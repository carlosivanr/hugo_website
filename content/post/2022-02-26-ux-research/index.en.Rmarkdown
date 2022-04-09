---
title: UX Research
author: Carlos Rodriguez
date: '2022-02-26'
slug: ux-research
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2022-02-26T10:28:53-07:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
draft: TRUE
---

***Example Case Study:*** Imagine that people are using a certain app initially, but they stop returning after a few months. Your team would like to understand which people are likely to stop returning to the app and what factors predict this behavior.

•	How you would propose research questions (methods selection)?

•	How you would scope the research plan?

•	Why did you proposed that?

---
The case study describes what is called a churn study. In a churn study, the objective is to determine why people are abandoning an app or a product. These studies are important for understanding the needs and preferences of different user types. Additionally they may offer an opportunity to learn valuable information about an app and how it fits in within the wider ecosystem of apps.


1. In the case study, we are interested in knowing which people are likely to stop returning to the app.

2. Additionally, we are interested in understanding which factors predict churn?


### 1. Which people are likely to stop returning to the app?
1. I would begin tackling this question by tapping into colleagues to see what type of information is out there. I would be interested in seeing what kind of data is available such as logs, satisfcation surveys, reviews, to begin getting an idea as to what clues are available that can reveal which users are likely to stop returning to the app. 


2. This data will be particulary important to perform some exploratory data analysis (EDA). It would be interesting to plot a retention rates, because this would give us information about which users are dropping out. Is it bran new users or users that have been around for a while?
  - When do people stop?
  - Is there a particular demographic or user characteristics that is related to this behavior?
    - Age, income, gender, country, language, phone operating system (iOS, Android, Windows), screen size, phone horsepower (RAM & CPU), load times.
  - Are there common themes or characteristics that pop up?
  - Could it be hardware/performance/size on device related?

3. Performing this typ of analysis may go a long way in helping us understand what type of variables are associated with retention/chure.

### 2. Which factors predict churn?
If we have some of this data in hand, my natural inclination is to build a logistic regression model. This may be a bit crude in the outcome variable by just splitting up the two groups into returners and deserters, but it would serve as a simple model of the type of analysis that could be done.

We have the Dependent variable of retained or not retained, which is binary variable that serves as the dependent variable.

The independent variables can consist of age, income, race/ethnicity, country, language, phone characteristics, operating system, years using apps, session time (duration), number of visits to the app (frequency), interval between app visits, duration of keeping the app, time spent on the onboarding.

If the sample size were large enough I would like to perform a machine learning style approach where we standardize the variables and one-hot encoding, which is kind of like a contrast in ANOVA. Split the data into a 60/40 plit for trainingn and testing.  The training will give us an idea of whether or not the independent variables are significantly associated with retention. The test data tells if this model actually generalized to data that was not used in the model building, also known as cross validation. 

If the sample size were small, you could potentially do a leave one out cross validation approach.

This would be a short term type of approach.

You may need to run different models, lets just look at the first 30 days, and have that as a starting point. Who stays and who goes to see what behaviors are between the two groups that are being studied.

If I had more time, I would actually implement a survey to be able to gather some additional features that could go into our model. The survey could ask question on a 5 point likert scale.

1. Perceived ease of use.
2. Rate the number of friends on the app.
3. The number of people they follow
4. The number of people that follow them
5. Rate your satisfaction on a scale of 1 to 5

To build off a study like this, we can get a little more sophisticated with the DV. Instead of binary logistic variable, we could come up with more than two. Say people who quit early within 30 days, people who quit between 31 days and 1 year, and people who quick after 1 year of using the app.

25% of new users will log in to the new app only once.
80% of new users will decided whether to continue using the app within three minutes of use.

We could do a PCA to reduce dimensionality, but my concern is that we would lose some interpretatbility, which would make it a bit tougher to make recommendations or actionable solutions.

We can enroll people in a study, and then as they are using the app, we can ask them questions regarding how they perceive the app, what sorts of things have they done on the app. If we have app features, we can simly count the number of posts they've viewed, the number of post they have posted, who have they followed.

I'm curious if there are some people that you can follow that increase the likelihood of sticking around? I thought about cat videos as a silly example. What if you get a new user to follow a cat-video and then all of a sudden, it's like oh my gosh this app is great! Look at all these cute cats!
----

### Part 1: Method Selection
Idiothetic: Qualitative research, gets at the why some users behave. Advantages is that you may not need as many participants and you can get in depth knowledge. Disadvantage is that it may not generalize. It may be quicker to conduct than a larger quantitative study

Nomothetic: Quantitative research, can be better equipped to generalize, but care must be taken to select a sample that is representative.


### Part 2: Paricipant Criteria
Users who quit within 7 days of using the app.
Users who quit after using the app for greater than 6 weeks. If we have data available on those individuals, I would randomly sample those, would need help on clustering or stratfied sampling.

Qualitative may be a semi-structured interview.

Qualitative:
Alternative hypothesis: The type of device OS is driving users to give up the app.
Null hypothesis: The type of device OS does not drive the user to give up the app.
Because it's the Null Hypothesis Significance Testing (NHST) and the logistic regression method I would use falls under this framework. For this question, POLR would not be needed. We could do this with a multinomial regression 
Variables: 
DV is the Device OS
IV:
age categorical ranges
income categorical ranges
gender
years using apps
satisfaction using app
estimated device use: could be a confound, 


### Part 3: Reporting
Let's say that the device OS turned out to be the case. I think this would spark additional research to further investigate the particular issues with this type of format. Recommendations would be do more research. It could be that there are bugs in that OS, maybe that version of the app is resource hungry and it runs slower. We could do tests to see how fast the apps run and if that has something to do with churn. That could be done with t-tests, ANOVA framework and I know how to do that easy peasy.

---
Tell me about yourself
- I was born in Mexico but raised in the US. I grew up constantly having to code switch and juggle the values and wishes of my family with those of mainstream society that sometimes in contrast. This has led me to become open minded and to see things from multiple angles.
- Aside from my cultural identity, I'm a father to a bright 12 year old girl which drives a lot of my aspirtations and motivations.
- And I would also say that I'm a person who possesses strengths in developing ideas, learning, and who likes to think about the factors that caused certain situations.


Describe your latest project.
- My latest project has been to perform a secondary data analysis of a longitudinal study designed to better understand the relationship between couple of neurophysiological characteristics and recovery from alcohol use disorder. You know how your heart rate has its own natural rhythm? It turns out that our brains have their own natural rhythms too and they differ according to whether your eyes are closed or open, whether your day dreaming, or trying to solve a puzzle. There's one particular rhythm that's called alpha, and it turns out that in long term alcohol users, this rhythm just isn't as "loud." And it turns out that when alcohol dependent users drink, this rhythm gets louder. There has been some evidence to show that after stopping alcohol use, that this rhythm returns to baseline, but most of these studies have been done with people undergoing some type of treatment, and the project I'm working on is aimed to looking at people who are not undergoing any treatment, which is actually how most people recovery from alcohol use. We hope that this type of research may offer us clues as to how brain activity changes in recovery and whether or not it is predictive of recovery rats.

What part of a research project do you enjoy the least?
- I think the part of research that I enjoy the least is deciding what research question to pursue. For me I get ideas constantly, and of course some ideas are better than others, but I can say that I don't enjoy deciding what question to pursue, because it means saying no to all of these other potentially interesting questions.

What are you looking for on your next job?
- First and foremost I'm looking for fit. I want an organizational culture where I can bring my full self to work and recognizes the strengths that I bring as a first generation person of color. I'm looking for colleagues who support and encourage one another and who strive towards excellence. And I'm looking for a position where I can leverage my desire to solve problems and apply my research skills.

What kind of manager do you enjoy working with the most?
- I think the kind of manager that I will enjoy working with the most will have a few different qualities. First, they are going to know how to give feedback, especially positive feedback. It's my experience that a lot of managers aren't as skilled in giving feedback, and I know for myself, that a little positive feedback can go a long way. In an ideal world, the manager that I'm going to enjoy working with the most, is the one who is going to establish a safe space to hear out ideas. One of my strengths as an individual is to formulate ideas. I love to brain storm to just throw stuff out there and see what sticks. The person who recognizes this strength and can foster that strength will be fun to work with.

What skills are you looking to develop next?
- The skills that I'm looking to develop next are going to be on two fronts. One I'm interested in learning how to become more of a coach or guide on the side to others in research. I did some work in this area as a graduate student and it was very fulfilling, but I haven't had much of the opportunity to do this in a professional setting. Next, I'm interested in growing in my capabilities as a researcher but also in the statistical techniques. I love stats and I would love to be that guy that gets called in to provide input on what could be the best techniques to apply to a given question. There are a few techniques I'm interested in learning longitudinal analyses, general estimating equations for longitudinal data, and maybe general additive models.



---
**Thoughts to consider**
- Data Camp Survey Weights

- UX Analytics

<!--
Draft of Part 1:
3. Churned users may fall under one of two types, those that stop relatively soon after using the app, say 7 days, and those that stop even they have been users for years (maybe the app sucked, and something kept them there). It might be a good idea to perform some qualitative research on a small sample of these folks. Now I'm not a qualitative researcher, so here is where I would definitely want to collaborate with someone to learn more about this app churn. This could be an example of idiographic research because we are studying a few but in depth.

4. Next we turn to more nomothetic approaches such as a survey with a much larger sample. Now survey design isn't necessarily my forte either, but I do know that it's important to ask questions that reduce bias and that don't nudge the user to respond in one way or another. I also know that sampling is incredibly important here, because we want our results to be generalizable to the entire population of app users. Ideally, we want a random sample that is representative of the population of users. We would want that sample to be representative acros age, gender, income level, perhaps device os or type. A survey can be used to quantitatively assess which people are likely to stop returning. 

Draft of Part 2:
A survey could also let us understand what factors predict churn with proporotional odds logistic regression (POLR).

Typical binary logistic regression is a statistical technique used when our dependent variable, the outcome that we are interested in investigating, falls under one of two options. It can be whether or not someone survived a hospital stay. It's either a yes or no answer. 
Logistic regression could be an option to determine which factors predict churn, we could use the status either kept using or stoped using as a crude measure to make it a binary variable.

POLR is another technique that builds off of this to included situations where the dependent variable has three or more outcomes. The special case of POLR is when the responses are ordered, such as low, medium, high. Survey responses of this type can be analyzed with POLR. This could be something like stopped using, stopped using within 30 days, stopped using after 90 days. We could use POLR to split it up this way. 

POLR has a few assumptions, mainly that the response of one participant will not influence the response of another. This is called independence. The other assumption is that the independent variables are uncorrelated, just like in regular linear regression. Finally, POLR has an assumption of parallel regression or proportional odds. This is kind of like homogeneity of variance assumption in ANOVA. In the HOV assumption, we want equal variances between groups, and in POLR we want the relationships between all possible pairwise outcomes to be equal. So we want the relationship between low and medium + high, to be equal to low + medium and high. This can be done visually or with the Brant test.

One issue with POLR is that you need to have some large samples, and the smalles cell in a given group with a certain set of independent variables needs to be 10 times larger than the number of predictor variables. So let's say we want to make a model with 5 predictor variables such as (age, gender, nicotine use, cannabis use, and years drinking), then we would want the smalles group to be at least 50. We really need some large samples for this type of model.

While these models can be tricky at first to wrap your mind around because we start dealing with odds, which is not the same as probability, and the log of odds, they can be straight forward to interprete when we exponentiate the coefficients. That'll give us the odds ratios which have a straightforward interpretation anything above 1 is a positive relationship in which an increase in the independent variable will lead to an increase in the odds of falling into that category. A value below one would indicate the opposite, as the independent variable increases the odds of falling into a categore decreases. Remember, the odds is just the probability of something happening over the probability of that same thing not happening. To get smaller odds, that would mean we have a larger denominator and smaller numerator indicating the likelihood of something happening (say selecting on of the categories in the survey response) being smaller. Conversely, to get larger odds, that would mean we have a larger numerator and a smaller denominator indicating the likelihoo of something happening being larger.

Now the alternative to POLR in case the assumptions don't hold is to use a multinomial logistic regression, which is the rigth technique to use when either the categorical dependent responses are not ordered, or when the assumptions of POLR don't hold, mainly the parallel regression assumption. One could use nnet, or mlogit for those analyses in R.

Other idea,
What if you did an experiment, where you brought people into the lab. Brand new users that are representative of the population of users. They get a phone so we know they all have the same hardware, we then can track measures such as on-boarding, followers, etc, and then track these individuals to see which ones return. Lets say they each get an iphone at $800.00 and we track them for two-years, we'd have to give them the phone otherwise they would keep using so that we don't take it away. For a large sample of say 1,000 people, that study would cost at least $8,000. Which propably won't work. 

Collection of tidbits
1. https://ieeexplore.ieee.org/abstract/document/8631433?casa_token=fpIXj7ucZPUAAAAA:PUQcgwDhaYoUKwZ1NGfDxw9S2qy_dWvJmfVdGt8o1J2RWpmD28OWRpknpNydKxRwprI1P__D

2. https://www.mdpi.com/2071-1050/11/8/2432
Early in 1998, Pin and Gilmore claimed that “from now on, leading-edge companies—whether they sell to consumers or businesses—will find that the next competitive battleground lies in staging experiences”

AttrakDiff
- Could be used with MANOVA
Pragmatic quality (PQ): Technical–Human; Complicated–Simple; Impractical–Practical; Cumbersome– Straightforward; Unpredictable–Predictable; Confusing–Clearly structured; Unruly–Manageable.
Hedonic—identity quality (HQ-I): Isolating–Connective; Unprofessional–Professional; Tacky–Stylish; Cheap–Premium; Alienating–Integrating; Separates me–Bring me closer; Unpresentable–Presentable.
Hedonic—stimulation quality (HQ-S): Conventional–Inventive; Unimaginative– Creative; Cautious–Bold; Conservative–Innovative; Dull–Captivating; Undemanding–Challenging; Ordinary–Novel. Attractiveness (ATT): Unpleasant–Pleasant; Ugly–Attractive; Disagreeable–Likeable;
Rejecting–Inviting; Bad–Good; Repelling–Appealing; Discouraging–Motivating
The questionnaire has been widely used by practitioners and researchers, and has been suggested
to be an easy-to-use and effective UX assessment method.
-->
