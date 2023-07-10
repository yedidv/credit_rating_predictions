## Introduction

A company’s credit rating is an “opinion of an independent agency regarding the likelihood that a corporation will meet its financial obligations as they come due” (investopedia, 2020). The company’s credit is categorically ranked by a few different credit rating providers, such as the Standard and Poor (S&P), Moody, and Fitch. Unfortunately, the formula for determining credit differs by company, and the formula for finding a company’s credit rating isn’t 100% clear. 

Because of this, companies have a hard time assessing how different decisions might impact their credit ratings, and their subsequent ability to secure loans. Being able to accurately predict their future credit ratings would put companies at an advantage over the competition, as it allows them to optimize financial planning and future decision making (Ca’ Foscari University, 2020). Studies have been done by the Ca’ Foscari University (Ca’ Foscari University, 2020), the Stevens Institute of Technology (SIT, 2020), and Bond University (Bond University, 2019). The Ca’ Foscari study used American company financial data to predict the credit rating for Italian companies, which didn’t give a high level of accuracy because of the differences in the structure of the companies in the two countries. The SIT study claimed that while their models could predict the credit score based on financial data, it might be more important from a business perspective to be able to detect changes in credit score based on changes in the income statement, considering it’s these changes that would most impact the decisions a company might make, more than the credit rating itself. The Bond University study also predicted just credit ratings, and they said in the future they would like to incorporate non-traditional data in their future research to predict credit ratings, such as sentiment analysis of annual reports and business news.

## Problem Statement

Drawing inspiration particularly from the SIT study, the goal of this project is to try and predict future changes in a company’s credit score based on changes in current financial data found in a company’s balance sheet. 

Variable Selection
Data was collected from all the companies in the S&P 500. This was done since the list was easily accessible, since in order to make it into the S&P 500 the company must be one of the largest in the world, and more than 50% of its stocks must be made public (Investopedia, 2021). This means these companies are the most likely to contain information for all the data needed for the models being created, and the companies that make up the S&P 500 are in a wide variety of industries, making models created in this case much more broadly applicable. 

A large list of 26 independent variables were selected to be looked at initially, based on intuition of what variables might most impact the credit score. It was quickly found out that much of the data contained an extraordinary amount of zeros or missing values, making them very difficult to use. So these features were filtered out, and 10 were selected which contained much less sparse data, so to more accurately create predictive models. The final features selected are as follows: 


_Net Income:_ Indicator of a company’s profitability. Normally seen as a company’s revenue minus its expenses, taxes, etc. Having a more positive net income might have a positive impact on the health of the company, and subsequently the credit score. 

_Total Revenue:_ Total amount of money that a company receives from selling goods and/or services. This is tied in with the net income; and the higher it is might mean we would see a higher credit score. 

_Accounts Receivable:_ Amount of money owed to the company for their goods and/or services from another customer/company. Usually paid in a certain time frame. Having a higher accounts receivable looks like it might lead to a higher credit score. 

_Gross Profit:_ Profit that a company makes after removing the company’s costs of goods or cost of services. Normally seen as a company’s revenue minus its costs of goods sold. Having a higher gross profit might mean the company will have a higher credit score. 

_Total Assets:_ Total amount of assets owned by the company. Assets are seen as items of value that the company owns such as cash, property, etc. Having a higher total asset value might lead to a higher credit score. 

_Accounts Payable:_ Amount of money owed by the company to another company or a supplier. Similar to Accounts Receivable where it is usually paid in a certain time frame. It is seen as a liability to the company. Having a higher accounts payable might lead to a lower credit score. 

_Total Liabilities:_ Total amount of liabilities that a company owes. Examples of liability are: loans, accounts payable, etc. Could be short-term or long-term. Having more liabilities might lead to a lower credit score. 

_Total Debt:_ Total amount of debt that a company has. Debt is typically money owed from one party to another. Having more debt might lead to a lower credit score. 

_Total Equity:_ Total amount of equity that a company has. Equity is normally seen as a company’s total assets minus its total liabilities.  Having more equity means the company might have a higher credit score. 

_Cash/Cash Equivalent:_ Cash and cash equivalents are a company’s more liquid assets. Cash equivalents can be any short-term investment like treasury bills. Having more cash reduces the risk of the company, which would increase the credit score. 

_Cash from Financing:_ A subset of the company’s cash flow. This records the net flows of cash directly used to fund the company. As said before, having more cash would reduce the risk of the company, which would increase the credit score. 

As can be seen, all these variables have the potential to have an impact on the credit score of a company. Having more assets, more cash on hand, more accounts receivable, and more cash on hand should in theory lead to a higher credit rating, while more liabilities, debt, and accounts payable should have an inverse impact on the credit score, as the risk associated with the company ends up going up. 

## Data Collection
Capital IQ is a dashboard created by S&P Global, “one of the world’s largest providers of ratings, data, and research” (investopedia, 2021). It contains detailed information of public companies, such as information found on these company income statements. Their web portal contains an Excel plugin that can be used to feed company information into an Excel document over a certain time period, as long as the information is collected by S&P Global. Unfortunately the plugin only allows us to access information for one company at a time. In order to work around this, a Python library was created, “capiq-excel” (pypi.org, 2021), in which the ticker symbols for multiple companies can be inputted, along with the names of the values we would like to look at. 

The ticker symbols from all the companies in the S&P 500 as of 3/23/2021 were scraped from Wikipedia (Wikipedia, 2021) using BeautifulSoup, a Python library for web-scraping. All of the information was outputted to CSV files, so it could be later accessed for data pre-processing and modeling. 

Capital IQ did not have historical credit rating data, only the most recent credit rating for each company. As a result, this data was given to us by the professor. It was given as a numerical value, representing the monthly average S&P long-term debt rating over the respective year. The higher the number corresponded to a lower credit rating. 


## Data Formatting
To begin with, the data was looked at to determine whether there were null values or infinite values. There were no infinite values, but 8 rows containing all null values. These were dropped since they contained no information for any of the independent variables. 

Since the data extracted from Capital IQ was quarterly, and the credit rating data was yearly, the independent variables were annualized. This was done by taking the average value for the independent variables by year. 

For most businesses, it is very valuable for the businesses to be able to predict future changes in credit ratings based on current and past data. This is best predicted by looking at the changes in the independent variables. So for each company, the differences were taken for all the variables including the credit rating between years. The dependent variable “lead_rating” was then created by shifting the credit rating changes by one year, so each row of values can be used to predict the changes in credit rating for the next year. 

The changes in credit rating were then put into 3 bins, in order to make the prediction models easier. If the credit rating didn’t change, it was classified into bin “0”. If it changed positively, meaning the rating went down, the bin was classified at “-1”. If the rating changed negatively, meaning the rating went up, it was put into bin “1”. This means the prediction made was only on if the credit changed, and whether it was positive or negative, rather than trying to predict how much the rating changed, which would be much more difficult, less accurate, and only marginally more valuable to businesses, since changes in credit rating usually end up being very small(plus or minus 1 step) with few exceptions. 

It was then found that the data was extraordinarily imbalanced. As can be seen in Figure 1, there are over 2000 (2049) instances of the credit not changing. This is more than triple the instances of positive change (642) and negative change (524). This imbalance can lead to overfitted models, but it can be solved by down-sampling the instances of no credit change. A random sample of 650 instances of no credit change was taken, which created a more balanced dataset, as seen in Figure 2. 

Finally, independent features were normalized by total assets. According to Morningstar, another company that performs risk evaluation for companies, credit rating is measured by looking at different values on the balance sheet relative to their assets (MorningStar, 2017). This more accurately represents the financial health of a company, since other values on the balance sheet tend to be more inflated when a company holds more assets. For example, Google might have more debt obligations than a startup company, but in terms of financial health, it wouldn’t matter since Google would have far more assets to liquidate if it needs to pay off these obligations. In order to account for this, all the independent variables were divided by the total assets variable. The total assets column was then normalized by the total assets for each company (subtract assets by the mean assets of its respective company, divide this by the standard deviation), so as to eliminate differences in units of measurement between variables. 



## Data Exploration
An autocorrelation matrix can be used to see if there is a linear relationship between variables after it has been processed. As can be seen in Figure 3, there are correlations between gross profit and total revenue. There is also a relationship between total liabilities and total debt. These relationships are pretty self-explanatory, but the one relationship that was interesting was the slight linear correlation between the rating change of the current period and the rating change of the next period. This shows that credit ratings tend to follow trajectories, so it is possible to use the previous rating to predict the next. 



Tables 1 and 2 show the descriptive statistics for each of the variables. Figure 4 shows a graphical representation of the descriptive stats. As can be seen, the mean change in all the dependent variables is around 0. But the standard deviations are different between variables, with the standard deviation in total assets being the highest at 0.98. Nearly all the variables have outliers, as can be seen that the minimum value is significantly lower than the 25th percentile in nearly all cases. This could have a small impact on the final model, as Support Vector Machines are very susceptible to outliers in training samples (Cornell, 2014). 

## Models
The models used to try and fit this processed data were support vector classifiers, random forests and neural networks.  For the support vector classifiers, linear, polynomial, sigmoid and radial kernels were used to see how different transformations of the data could improve accuracy.  Then for both the SVC and the random forest, 5 fold time series cross-validation was used, along with a hyperparameter tuning function to find the best hyperparameters and the optimal models for each model type. This was all implemented using Python’s sklearn libraries (Scikit Learn, 2021). Additionally, the sklearn python implementation of these algorithms was used to solve these tasks.  For the Neural net, a Tensorflow implementation was used with an architecture of one hidden layer with 128 nodes and a relu activation function was used along with a 20% dropout rate for that hidden layer.  This was run for 1000 epochs on the training set finishing with a training accuracy around 73%.

To evaluate these models the main component analyzed was accuracy on the test set that had been downsampled the same way as the training set. This showed that all of these models fell within one percentage point of each other at around 69% accuracy.  The accuracies for each of the models are shown in Figure 5.

In order to further break down the results of these models, confusion matrices were constructed to better understand where each model was getting predictions wrong and are shown in Figure 6.  Doing this it can be seen that no changes are predicted with the most success across all models and then predicting that credit will get worse is easier than predicting it will improve.  It is also worth noting that for the most part, error predictions are a result of expecting no change when there is a change.  This suggests that the variables chosen do separate increases from decreases but the problem comes from trying to split the changes from the no changes.  Lastly, we see that no model struggles in one particular area of classification significantly more than any other model.

	

## Conclusion 
Based on the results of testing the various models, the model that had the greatest success was the Support Vector Classifier with a Radial kernel.  This was able to achieve an accuracy of 69.9% beating the remainder of the models but at the same time, the penalty for having a complex model is not as high as it is for some of the other models such as the random Forest or the Neural Net.

## Future Work
There are several things that could be tried moving forward to see if the accuracy can be improved.  First, more neural net architectures should be tried to see if narrowers and/or deeper architectures are capable of producing better results when it comes to predicting the latest data.  Additionally, this could be a scenario where recurrent Neural Networks would make sense as the change in credit rating in one period is likely impacted by the previous time period as well.  This report was conducted using yearly averages of the changes between quarters, but in the future it might make sense to look at the changes between quarterly reports independently as the average may work to hide factors that could be instrumental in predicting a credit rating change.

## Appendix
All data and Jupyter Notebooks containing code can be found on github. The link can be found below. 

https://github.com/yedidv/credit_rating_predictions


### Appendix A: Figures


Figures 1 and 2: Figure 1 shows the value counts of the dependent labels before downsampling, and figure 2 shows the value counts after downsampling. 



Figure 3: An autocorrelation matrix for all the variables. This matrix was created after the data was processed, and normalized by assets. 



Figure 4: Plot of the descriptive stats for each of the variables. 

Figure 5: Accuracies of each of the 5 models run

Figure 6: Confusion Matrices for each of the models run


### Appendix B: Tables



Tables 1 and 2: Descriptive Stats 

