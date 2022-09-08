---
title: Parallel parameterized quarto documents
author: Carlos Rodriguez
date: '2022-09-01'
slug: parameterized-and-parallelized-quarto-reports
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: "September 08, 2022"
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
---

The built-in  R dataset "iris", contains the length and width measurements of the sepal and petal of various iris species. 

```r
kableExtra::kable(
  head(iris)
)
```

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> Sepal.Length </th>
   <th style="text-align:right;"> Sepal.Width </th>
   <th style="text-align:right;"> Petal.Length </th>
   <th style="text-align:right;"> Petal.Width </th>
   <th style="text-align:left;"> Species </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 5.1 </td>
   <td style="text-align:right;"> 3.5 </td>
   <td style="text-align:right;"> 1.4 </td>
   <td style="text-align:right;"> 0.2 </td>
   <td style="text-align:left;"> setosa </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4.9 </td>
   <td style="text-align:right;"> 3.0 </td>
   <td style="text-align:right;"> 1.4 </td>
   <td style="text-align:right;"> 0.2 </td>
   <td style="text-align:left;"> setosa </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4.7 </td>
   <td style="text-align:right;"> 3.2 </td>
   <td style="text-align:right;"> 1.3 </td>
   <td style="text-align:right;"> 0.2 </td>
   <td style="text-align:left;"> setosa </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4.6 </td>
   <td style="text-align:right;"> 3.1 </td>
   <td style="text-align:right;"> 1.5 </td>
   <td style="text-align:right;"> 0.2 </td>
   <td style="text-align:left;"> setosa </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5.0 </td>
   <td style="text-align:right;"> 3.6 </td>
   <td style="text-align:right;"> 1.4 </td>
   <td style="text-align:right;"> 0.2 </td>
   <td style="text-align:left;"> setosa </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5.4 </td>
   <td style="text-align:right;"> 3.9 </td>
   <td style="text-align:right;"> 1.7 </td>
   <td style="text-align:right;"> 0.4 </td>
   <td style="text-align:left;"> setosa </td>
  </tr>
</tbody>
</table>

Let's suppose we wanted to create a separate report for each species. Furthermore, let's suppose that we wanted to do this in parallel. In his blog, [Matt Roumaya demonstrated](https://www.mattroumaya.com/post/using-foreach-to-speed-up-parameterized-rmarkdown-reports/)
 one approach to this problem using the foreach and doParallel packages. 
 
While Matt's approach works well for knitting RMarkdown documents, I was unsuccessful in adapting it to render Quarto documents in parallel [footnote]. As a result, I developed a different approach that relies on the furrr package which is akin to the purrr package with parallel processing capabilities. 

The overall gist of my approach is to 1) create a primary layout .qmd file and 2) create a script that contains a function that renders .qmd files. The caveat here is that the rendering function in the script must contain code that creates copies of the .qmd file intended for parallel rendering. 

## 1. Create a quarto file to render a document
The .qmd file shown here is a modified version of the .Rmd file from [Matt Roumaya's post](https://www.mattroumaya.com/post/using-foreach-to-speed-up-parameterized-rmarkdown-reports/). The .qmd file here has modifications in the YAML header to reflect Quarto execution parameters and in the dyplyr `filter()` accept a parameterized input for Species.

```` markdown
  ---
  title: "Iris Demo"
  format: pdf
  params: 
    species: ""
  ---
    
  ```{r}
  knitr::opts_chunk$set(
    echo = FALSE,
    message = FALSE,
    warning = FALSE
    )

  library(tidyverse)

  df <- iris %>% 
  filter(Species == params$species)
  ```
````

```` markdown
  ```{r}
  df %>% 
    filter(Species == params$species) %>% 
    ggplot(aes(Sepal.Width)) +
    geom_histogram() +
    ggtitle(paste0("Distribution of Sepal.Length for ", params$species))
  ```
````
## 2. Create an R script that will render multiple documents in parallel
The next step is to create a script that renders quarto documents in parallel. The script needs two main parts, the first contains the rendering function, while the second implements the rendering function in parallel.

### a. Create a report rendering function
The first section of the report rendering function establishes which files should be used as input and what the final report should be named. Critically, this section also creates a copy of the .qmd file and adds the iris species name as a suffix in the file name. For reasons that are not entirely clear to me, separate copies of the .qmd file were needed for parallelization to work properly with this on my Windows machine. My suspicion is that the .qmd file cannot be used simultaneously in parallel with this approach. Thus, my solution was to copy the main .qmd file and append a suffix to the file name. This results in the availability of multiple files that contain the exact same code and can be used simultaneously as inputs for each parallel process. In the second section, the `quarto_render()` function is used with the corresponding copy of the .qmd file, the execution parameters for the YAML header, and an output file name. 

### b. Specify options and render .qmd files in parallel
To complete the document rendering script, just a couple of a couple more code chunks are needed. First, specify the number of cores to use during parallel processing with the `plan()` function. Since there are only three species of iris, this choice was easy. However, in the wild it may be best to use no more than the number of cores in your CPU minus 1 (n_cpu - 1). Lastly, we can pipe the vector of unique iris species to the `future_walk()` where it will apply the `render_reports()` function in parallel.


```r
library(tidyverse)
library(furrr)

render_reports <- function(species_name){
  
  # Section 1: Create copies of main layout .qmd file
  # 1.a Create new names for .qmd copies by appending the species name to the
  # layout file name.
  file_in <- str_c("report_layout_", species_name, ".qmd")
  
  # 1.b Create copies of the layout files using the modified file names
  file.copy(
    from = "report_layout.qmd",
    to = file_in,
    overwrite = TRUE
    )
  
  # Section 2: Render reports using .qmd copies.
  quarto::quarto_render(
    input = file_in,
    execute_params = list(species = species_name),
    output_file = str_c("report_for_", species_name, ".pdf")
    )
    
  # Section 3: Remove .qmd copies
  file.remove(
    str_c("report_layout_", species_name, ".qmd")
    )
  }

# Set options and cores  
plan(cluster, workers = 3)

# future_walk the create_reports function
as.character(unique(iris$Species)) %>%
  future_walk(~ render_reports(.x))
```

## Footnote
In case you're wondering, I tried Matt's approach replacing the rmarkdown::render() function with the quarto::quarto_render() function with the appropriate options, and could not get it to work. I already had several .qmd file that I had created for multiple projects and was looking for a solution that would work with .qmd files instead of trying to port my .qmd files to .Rmd.
