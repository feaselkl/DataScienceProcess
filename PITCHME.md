<style>
.reveal section img { background:none; border:none; box-shadow:none; }
</style>

## Cleaning Is Half The Battle:  Launching A Data Science Project

<a href="https://www.catallaxyservices.com">Kevin Feasel</a> (<a href="https://twitter.com/feaselkl">@feaselkl</a>)
<a href="https://csmore.info/on/datascience">https://CSmore.info/on/datascience</a>

---

@title[Who Am I?]

@snap[west splitscreen]
<table>
	<tr>
		<td><a href="https://csmore.info"><img src="https://www.catallaxyservices.com/media/Logo.png" height="133" width="119" /></a></td>
		<td><a href="https://csmore.info">Catallaxy Services</a></td>
	</tr>
	<tr>
		<td><a href="https://curatedsql.com"><img src="https://www.catallaxyservices.com/media/CuratedSQLLogo.png" height="133" width="119" /></a></td>
		<td><a href="https://curatedsql.com">Curated SQL</a></td>
	</tr>
	<tr>
		<td><a href="https://wespeaklinux.com"><img src="https://www.catallaxyservices.com/media/WeSpeakLinux.jpg" height="133" width="119" /></a></td>
		<td><a href="https://wespeaklinux.com">We Speak Linux</a></td>
	</tr>
</table>
@snapend

@snap[east splitscreen]
<div>
	<a href="https://www.twitter.com/feaselkl"><img src="https://www.catallaxyservices.com/media/HeadShot.jpg" height="358" width="315" /></a>
	<br />
	<a href="https://www.twitter.com/feaselkl">@feaselkl</a>
</div>
@snapend

---?image=presentation/assets/background/2_1_philosophy.jpg&size=cover&opacity=15

### Data Adages

* "Clean" data is an aspiration, not a reality
* If data is stored in two places, there will inevitably be disagreements
* You will always have more questions than data
* Decision-makers will not know their questions until you have enough information to give them; their questions almost invariably cannot be answered by your system
* Data necessarily abstracts the particulars of time and place and therefore explains a fragment of the underlying reality

Nevertheless, our job is to figure something out.

---

### Process

@div[left-50]
![Microsoft Team Data Science Process](presentation/assets/image/tdsp-lifecycle2.png)
@divend

@div[right-50]
To deal with these considerations, we build and implement processes.  These processes give us structure and help us navigate some of the difficulties in managing data.

In today's talk, we will look at one particular process:  the Microsoft Team Data Science Process.
@divend

---?image=presentation/assets/background/2_2_motivation.jpg&size=cover&opacity=15

### Motivation

We will use the Team Data Science Process to uncover interesting information from the <a href="https://www.brentozar.com/archive/2018/01/2018-data-professionals-salary-survey-results/">2018 Data Professionals Salary Survey</a>.

At each step of the way, we will walk through implementation details and achieve a better understanding of data professional salaries.

---

@title[Business Understanding]

## Agenda
1. **Business Understanding**
2. Data Processing
3. Modeling
4. Deployment
5. What's Next?

---

### Business Understanding

Somebody higher up (typically on the business side of the house) wants a data science project done.  Your mission is to figure out as much about that person's vision as possible.

The first clue is in understanding the domain. What does the company do? Which industry are we in? What is the common parlance? Which technical terms do people use and what do they mean?

Even if you have a deep knowledge of the domain, you should still find the champion(s) of your project and understand best their vision of success.

---

### Ask Questions

When interviewing your project champion or people on the business side, listen for the following types of questions:

* How much / how many?
* Which category does this belong to?
* How can we segment this data?
* Is this weird?
* Which option should I choose?

Each of these is a different type of problem with its own set of statistical techniques and rules. Figuring out which of these is on your champion's mind is critical to delivering the right product.

---

### Get A Specific Problem

Your goal is to nail down a **specific** problem with a <strong>specific</strong> answer, narrowing the scope to something achievable.

* Find a model which predicts quarterly sales to within 5% no later than 30 days into that quarter.
* Given a title and description for a product, tell me with at least 90% probability a listing category that Amazon will consider valid for this product.
* Determine which factors most affect the number of years the first owner holds onto our mid-range sedan.

With a specific problem to solve, you can start looking for relevant data. Note that you might need to modify the scope of this problem over time but this at least gives you a starting point for what success looks like.

---

### Looking For Data

Once you have an interesting question, start looking for data. Some of this data will be in-house and could be in databases, Excel, flat files, accessible through APIs, or even in reports.

Your champion will hopefully be able to point you in the right direction, but this part of the process requires legwork.

---

### Data Dictionary

Once you have a compendium of data sources, you will want to build a <strong>data dictionary</strong>, which helps you explain what different pieces of data actually mean.  A data dictionary typically tells you:

* Data type:  numeric, string, categorical, binary
* Data format:  CSV, SQL Server table, Hive table, etc.
* Size of data and number of records
* Enumeration of valid values (if categorical)
* Other domain rules (if known)

---

### End Result

Another thing to determine is, where does your end data need to be? Will there be a different engineering team expecting to call a microservice API?  Will you get a set of files each day and dump results into a warehouse?

This is generally a technical problem that an Engineering group will architect, although your champion might have insight here depending upon how the business side will need to use your results.

---

### Example

We work for Data Platform Specialists, a company dedicated to providing DBAs and other data platform professionals with valuable market knowledge. We have come into possession of a survey of data professionals and want to build insights that we can share with our client base.

---

![Sample survey data](presentation/assets/image/Survey.png)

---

### Example

Questions we can ask given our domain knowledge:

* **How much** money does a DBA make?
* **Which category** of DBA (junior, mid-level, senior) does this particular work?
* **How can we segment** the DBAs in our survey?
* Is this many hours per week **weird**?
* **Which option should I choose** as a career path? DBA? Data science? BI?

---

### Example

Narrowing this down with our champion and other stakeholders, we can get to the following question which we will endeavour to answer:

**How much money should we expect a data professional will make?**

---

### Example

As we review the survey data, we can start to see different shapes of data and begin to build a data dictionary.

For example, TelecommuteDaysPerWeek has six options:  less than 1, 1, 2, 3, 4, 5 or more. These are ordered options, meaning the numbers inherently matter.

By contrast, number of hours worked per week is an integer ranging from 5 to 200 (?!).

---

### Example

Our company wants to build a small website and allow people who have built profiles to get an estimate of how much they could be making in different roles.  Our job is to build a microservice API which returns a dollar amount based on inputs.

---

@title[Data Processing]

## Agenda
1. Business Understanding
2. **Data Processing**
3. Modeling
4. Deployment
5. What's Next?

---

### Data Processing

Data processing is made up of a few different activities:

* Data Gathering
* Data Cleansing
* Data Analysis

---

### Data Gathering

Data gathering will likely be an iterative process. At first, you won't know exactly what data you need. But as you flesh out your models and gain a better understanding of the problem, you will go back to the well several times and probably will end up getting data from other sources. These sources can include:

* Internal proprietary data
* Open data sources (often governmental or academic)
* Paid APIs or data sources from third parties
* Survey data

---

### Data Gathering Example

In this example, we will stick to just the data professional survey. But if you want to take this further, a few additional data sources could be:

* PPP GDP per capita to normalize salaries across countries.
* A geocoding data set to visualize results on a map.
* Cost of living by ZIP code to normalize salaries within the US.
* Census information to build out data by ZIP code.
* Data from other surveys to add more to the sample.

---

### Data Cleansing

Data cleansing is half the battle.  That's the title, after all!

![Knowledge is half the battle.](presentation/assets/image/Knowing.jpg)

---

### Data Cleansing

Well, actually...

![I lied](presentation/assets/image/Sully.jpg)

---

### Data Cleansing

Most of your time, you'll be a data plumber.

![Data Plumber](presentation/assets/image/SuperMario.jpg)

---

### Data Cleansing

General estimates that you will hear from data scientists is that they spend approximately 80% of their time cleaning data. If anything, this is an underestimation--based on my experiences, that number might be closer to 90%.

Simply getting the data is a start, but there's a long journey ahead.

---

### Data Cleansing

After grabbing relevant-looking data sets, you will want to join them together to gain insight from the mashup of different data sets. Common join activities include:

* Changing the grain of at least one data set
* Definining join criteria (because there is no obvious natural join key)
* Reshaping data to fit join criteria

---

### Data Cleansing

You will quickly find problems with your data sets, including (but not limited to):

* Mismatched, mislabeled, and incorrect data
* Records with missing data
* Duplicated records
* Data inconsistencies:  records conflicting with other records
* Misshapen flat files

---

### Data Cleansing - Mismatches

**Mislabeled** data:  when the label is incorrect.  Ex: on the data professional survey, a person who earns $50K per year accidentally typing $500K per year. If you can fix the label, this data becomes useful. If you cannot, it may throw off your analysis.

**Mismatched** data:  data joined together which should not have been.
							
**Incorrect** data: when data other than the label is incorrect. Ex: a person works 200 hours per week?

---

### Data Cleansing - Missing Data

People don't always fill out the entirety of every form. Sometimes, they don't even give us important information. When we're missing data, we have a few options available to us:

* Delete the row (okay with a small percent)
* Substitute with a dummy value
* Substitute with the mean, median, or mode
* Use regression and replace with the regressed value

None of these options is perfect, but the last three can help salvage incomplete records.

---

### Data Cleansing - Duplicate Data

If your data source does not have duplicate protection in place, you might end up with multiple entries representing the same thing. Sometimes it's easy to catch those:  you might be able to use a DISTINCT clause to remove duplicates. Other times, you will have to dig further.

---

### Data Cleansing - Inconsistent Data

Independent systems may end up with inconsistent data. Sometimes people forget to fill out both systems the same.  Sometimes there are typos in data. Sometimes there are subtle differences in data sets which lead to differing results.

Regardless of the reason, we need to be able to handle those differences. One way to handle differences is to make one data set canonical and trust it over the other set. Another way to handle differences is to institute a rule--for example, maybe the lower number between data sources is more likely to be correct so you go with it.

---

### Data Cleansing - Misshapen Data

Data stored in flat files or textual format can end up misshapen--some rows may not have enough delimiters (or maybe too many), there could be newlines in the middle of a record, or the file cuts off in the middle of a record.

This is a problem with flat files and certain semi-structured data formats. It is not a problem with relational databases, where data shape is enforced.

---

### Data Shaping

There are several techniques we can use to reshape data to make it easier to analyze:

* Vectorize words (turn each word into a number)
* Turn strings into factors (categorical data)
* Normalize values (transform numeric data to have a mean of 0 and standard deviation of 1)
* Bin data, converting continuous variables to discrete

---?image=presentation/assets/background/4_5_demo.jpg&size=cover&opacity=15

### Demo Time

---

### Data Analysis

The most common technique for data analysis at this point is Exploratory Data Analysis (EDA). The purpose of Exploratory Data Analysis is to summarize the data in a way that gives you a better understanding of the data. Examples of EDA techniques include:

* Getting count and cardinality of features
* Building out 5-number summaries
* Visualizing data, especially with box plots and histograms
* Determining correlation between variables

---

### Data Analysis - Cardinality

@div[left-50]
![Cardinality](presentation/assets/image/Cardinality.png)
@divend

@div[right-50]
The cardinality of a feature is the number of unique values.
@divend

---

### Data Analysis - 5-Number Summary

@div[left-50]
![Five-Number Summary](presentation/assets/image/FiveNumber.png)
@divend

@div[right-50]
The five-number summary of a feature tells us the minimum value, value at the 25% mark, value at the 50% mark, value at the 75% mark, and maximum value. The mean technically is not part of the five-number summary, but can be useful to know as well.
@divend

---

### Data Analysis - Box Plot

@div[left-50]
![Box Plot](presentation/assets/image/BoxPlot.png)
@divend

@div[right-50]
Box plots show us the five-number summary by group.
@divend

---

### Data Analysis - Histogram

@div[left-50]
![Box Plot](presentation/assets/image/Histogram.png)
@divend

@div[right-50]
Histograms show us the spread of a single variable.
@divend

---

### Data Analysis - Correlation

@div[left-50]
![Box Plot](presentation/assets/image/Correlation.png)
@divend

@div[right-50]
Here we have two comparisons, depth vs table and x vs y. Depth and table are mildly negatively correlated; this isn't a problem. The x and y variables, however, are very highly correlated. Some models, such as linear regression, may exhibit problems if you include both x and y in your analysis.
@divend

---?image=presentation/assets/background/4_5_demo.jpg&size=cover&opacity=15

### Demo Time

---

@title[Modeling]

## Agenda
1. Business Understanding
2. Data Processing
3. **Modeling**
4. Deployment
5. What's Next?

---

### Modeling

Modeling has five major steps:

* Feature Engineering
* Feature Selection
* Model Selection & Training
* Model Evaluation
* Model Tuning

---

### Feature Engineering

Feature engineering involves creating relevant features from raw data. These can include things like:

* Indicator flags (Age >= 21, Income >= $100K)
* Calculations (ClickThroughRate = Clicks / Impressions)
* Geocoding latitude and longitude from a street address
* Aggregating data (by day, by hour, by 36-hour period)
* Text processing -- turning words into arbitrary numbers for numeric analysis (TF-IDF, Word2Vec)

---

### Feature Selection

We use feature selection to winnow down the available set of features, removing redundant, unnecessary, or highly correlated features.  There are several reasons to do this:

* Collinearity -- one explanatory variable can predict another, which makes analysis less precise.
* Make analysis easier for a human to understand by removing irrelevant or redundant features.
* More efficient training with fewer variables.
* Reduce the risk of an irrelevant or redundant feature causing spurious correlation.

---

### Spurious Correlation

![My favorite example of spurious correlation](presentation/assets/image/SpuriousCorrelation.png)

(<a href="http://www.tylervigen.com/spurious-correlations">Source</a>)

---

### Model Training

We take some percentage of our total data and designate it for training / validation, and the remainder is for evaluation. There are no hard rules on percentages, but typically, we reserve 70-80% for training.

There are four major branches of algorithms:
* Supervised learning
* Unsupervised learning
* Self-supervised learning
* Reinforcement learning

---

### Supervised Learning

Supervised learning models require known answers (labels). We train a model to map input data to those labels in order to have the model predict the correct answer for unlabeled records. Major classes of supervised learning models and their pertinent driving questions include:

* Regression -- How much?
* Classification -- Which?
* Recommendation -- What next?

---

### Unsupervised Learning

With unsupervised learning, we do not know the answers beforehand and try to derive answers. We can use unsupervised learning to drive toward a supervised problem by giving data analysts insight into the nature of the problem. Major classes of unsupervised learning models and their pertinent driving questions include:

* Clustering -- How can we segment?
* Dimensionality reduction -- What of this data is useful?

---

### Self-Supervised Learning

This is a subset of supervised learning, but with the popularity of neural networks, has come into its own. We use heuristics to guesstimate labels and train the model that way. An example of self-supervised learning is predicting the next word in a document based on previous words.

Self-supervised learning typically happens with neural networks.

---

### Reinforcement Learning

@div[left-50]
![MariFlow -- A neural network which solves Mario Kart levels.](presentation/assets/image/MariFlow.png)
@divend

@div[right-50]
Reinforcement learning is where we train an agent to observe its environment and use those environmental clues to make a decision.
@divend

---

### Choose An Algorithm

Once you understand the nature of the problem, you can choose an algorithm.  There are often several potential algorithms which can solve the problem, so you will want to try different algorithms and compare. The major trade-offs between algorithms typically include:

* Accuracy
* Training time
* Ability to understand the result
* Number of parameters
* Number of features allowed

---

![A sampling of algorithms available](presentation/assets/image/Algorithms.png)

---

### Algorithms

@div[left-50]
![An example of linear regression](presentation/assets/image/LinearRegression.png)
@divend

@div[right-50]
Once you have an algorithm, features, and labels (if supervised), you can train the algorithm. Training a model is solving a system of equations, minimizing a loss function.
@divend

---

### Validate The Model

Instead of using up all of our data for training, we typically want to perform some level of validation within our training data set to ensure that we are on the right track and are not overfitting.

Overfitting happens when a model latches on to the particulars of a data set, leaving it at risk of not being able to generalize to new data. The easy way to tell if you are overfitting is to test your model against unseen data, and if there is a big dropoff in model accuracy between training and testing data, you are likely overfitting.

---

### Cross-Validation

Cross-validation is a technique where we slice and dice the training data, training our model with different subsets of the total data. The purpose here is to find a model which is fairly robust to the particulars of a subset of training data, thereby reducing the risk of overfitting.

---

### Tune The Model

Most models have **hyperparameters**. For neural networks, the number of training epochs is a hyperparameter. For random forests, hyperparameters include things like the size of each decision tree and the number of trees.

We tune hyperparameters using our validation data set.

---

### Evaluate The Model

Model evaluation happens when we send new data to the model that it has never before seen. This is data that we did not use at all during the training and validation process. We have to be careful not to let any information leak into the training data, meaning that we should never feed aggregates of training + evaluation data to a model.

If we fail to safeguard this data, we can end up overfitting our model to the test data, leaving it less suited for the real-world data outside of our sample.

---

### Evaluate The Model

@div[left-50]
![MarIO -- Using genetic algorithms to train a neural network.](presentation/assets/image/MarIO.png)
@divend

@div[right-50]
You can also build a fitness function to evaluate certain types. Genetic algorithms are a common tool for this.
@divend

---?image=presentation/assets/background/4_5_demo.jpg&size=cover&opacity=15

### Demo Time

---

@title[Deployment]

## Agenda
1. Business Understanding
2. Data Processing
3. Modeling
4. **Deployment**
5. What's Next?

---

### Deployment

Back in the day, one team would build a solution in an analytics language (e.g., R) but you would not go to production with that. Instead, an implementation team would rewrite your model in C++ or some other fast language. Those days of research versus implementation teams using completely different languages are now (mostly) gone.

---

### Deployment

Welcome to the era of the microservice:  many small services dedicated to providing a single answer to a single problem. Most microservices are exposed via web calls, although other forms of interoperation are still possible. The big benefit to web calls is that I can write my service in R, you can call it in Python, and then someone can call your service from .NET.

---

### Deployment

Once you have a model ready to go, there are tools which make it relatively easy to deploy scalable predictive services.  For example, DeployR:

![The DeployR process.](presentation/assets/image/deployrworkflow.png)

---

### Deployment

You can also build your own services.  Stacks that I've put into production include:

* WebAPI (C#) ==> SQL Server Machine Learning Services ==> Microsoft Machine Learning with R
* WebAPI (C#) ==> Nginx ==> Flask/Gunicorn ==> Keras with Python

With a microservices architecture, you're trying to plug in these new APIs while not forcing everybody else to change their skills.

---

### Deployment

SQL Server Machine Learning Services, in particular, is great when the input data is already stored in SQL Server. With certain types of models, you can make "real-time" predictions. Our scenario was complex enough that we pre-trained a large number of models and stored the results in SQL Server for later prediction.

---

### Deployment

@div[left-50]
![Jupyter notebooks](presentation/assets/image/Jupyter.png)
@divend

@div[right-50]
Another option is notebooks, which help you record your work for subsequent review. Jupyter (Julia + Python + R) and Apache Zeppelin are two great examples of notebooks.
@divend

---

### Deployment

@div[left-50]
![Shiny, a visualization library in R](presentation/assets/image/Shiny.png)
@divend

@div[right-50]
Shiny is an interactive visualization product combining JavaScript and R. This is more for visualizing data rather than integrating with other services.
@divend

---

@title[What's Next?]

## Agenda
1. Business Understanding
2. Data Processing
3. Modeling
4. Deployment
5. **What's Next?**

---

### What's Next?

After go-live, the job is not complete.

It is important to keep checking the efficacy of models. Model shift happens, where a model might have been good at one point in time but becomes progressively worse over time as circumstances change.

You may also find out that your training/testing data was not truly indicative of real-world data.

---

### What's Next?

Occasionally, you will take new production data and retrain the model.  This works best if you keep track of your model's predictions and actual outcomes so you can tell the actual efficacy of the model.

Depending upon your choice of algorithm, you might be able to update the existing model with the latest information. If you create a process whereby the service you've created automatically feeds the data back to your model, you have true machine learning.

Some algorithms, however, require you to retrain from scratch.

---

### What's Next?

Regardless of model efficacy, you will want to confer with those stakeholders and ensure that your model fits their needs. Then, repeat the process.

In a production scenario, you will start with a Minimum Viable Product (MVP) to gauge interest. If there is sufficient interest, you can add incremental improvements.  That could include expanding the base of predictions, making more accurate predictions, improving the speed of your service, or adding new functionality.

---

### What's Next?

The moral of the story:  a data plumber's work is never done.

![A data plumber's work is never done](presentation/assets/image/MarioGames.jpg)

---

@title[Wrapping Up]

### Wrapping Up

Over the course of today's talk, we have covered the Microsoft Team Data Science Process as well as a pragmatic implementation of a data science workflow. Following a stable process allows you to maximize the chances of developing a high-quality and effective model.

---

### Wrapping Up

To learn more, go here:  <a href="https://csmore.info/on/datascience">https://CSmore.info/on/datascience</a>

And for help, contact me:  <a href="mailto:feasel@catallaxyservices.com">feasel@catallaxyservices.com</a> | <a href="https://www.twitter.com/feaselkl">@feaselkl</a>