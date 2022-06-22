![Image](HSG_logo.png?) <br>

# Investing@HSG

<b>Group Project <br>
Skills: Programming - Introduction Level & <br>
Skills: Programming with Advanced Computer Languages <br>
Spring Semester 2022 <br>
Dr. Mario Silic <br>
Submitted on 30th of June 2022 </b> <br>
<br>
<br>


![Image](Image.png?) <br>

The goal of this project is to provide the user an app to visualize stock data of selected companies and to analyze stocks to form an individual portfolio.
We want to incorporate this processes into a single tool to reduce the time consuming workload of every single investor. 

If you are curious to learn more about our approach, we encourage you to read on and experience our Web App "[Investing@HSG](https://thomas-weiss.shinyapps.io/portfolio_selection/)" by yourself!

1. [ Group Project Members ](#memb)
2. [ General Information ](#desc)
3. [ Technologies/Setup ](#usage)
4. [ Code Structure ](#code)
5. [ Screenshots ](#scrn)
6. [ Disclaimer ](#discl)
7. [ Appendix: Libraries Description ](#app)


<a name="memb"></a>
## 1. Group Project Members
- Begic Armin (20-614-582)
- Duerr Samuel (20-609-855)
- Tragust Sebastian (17-620-220)
- Weiss Thomas (17-620-360)

<a name="desc"></a>
## 2. General Information
This student project "Investing@HSG" is part of the courses "Programming - Introduction Level" & "Programming with Advanced Computer Languages" by Mario Silic at the University of St. Gallen (HSG). The purpose of this project is a fundamental analysis of publicly listed companies. You can also form an individual protfolio and get the analyst recommendations and forecasts for the specific stocks. <br>
<br>
**Here you can find the link to the online Web App**: https://thomas-weiss.shinyapps.io/portfolio_selection/
<br>

**Please note:** <br>
You can find the plain code in the [functions.R](https://github.com/tcweiss/Advanced-Pro/blob/90e500c360bcb408a23733337e2e963c987a5bb2/portfolio_selection/code/functions.R) file. <br>
You can find the *Shiny App* code in the [app.R](https://github.com/tcweiss/Advanced-Pro/blob/90e500c360bcb408a23733337e2e963c987a5bb2/portfolio_selection/app.R) file. <br>
The folder [app_data](https://github.com/tcweiss/Advanced-Pro/tree/main/portfolio_selection/app_data) includes all the data which is needed to correctly execute the program.

<a name="usage"></a>
## 3. Technologies/Setup
- R version: R 4.2.0
- RStudio: Please refer to https://www.rstudio.com/products/rstudio/download/ to install RStudio.
- Required libraries: ```shiny``` ```shinyWidgets``` ```shinythemes``` ```PerformanceAnalytics``` ```PortfolioAnalytics``` ```tidyquant``` ```tidyverse``` ```magrittr``` ```reactable``` ```arrow``` ```bslib``` ```qs``` ```timetk``` ```dygraphs``` ```rvest```





In order to properly use our "Investing@HSG"-App, it is essential to have installed the above listed libraries prior to running this program. To install the libraries, copy and paste the following code into your R console:

```
install.packages(c("shiny",                # Creating web app
                   "shinyWidgets",         # Web app widgets
                   "shinythemes",          # Web app themes
                   "bslib",                # Add-ins for web app themes
                   "PerformanceAnalytics", # Stock return calculcations
                   "PortfolioAnalytics",   # Portfolio optimization
                   "tidyquant",            # Accessing Yahoo API
                   "tidyverse",            # General data science toolkit
                   "magrittr",             # Better pipe operator
                   "reactable",            # Interactive tables
                   "arrow",                # Fast read/write of tabular data 
                   "qs",                   # Fast read/write of non-tabular data
                   "timetk",               # Converting dataframes to time-series formats
                   "dygraphs",             # Plotting 
                   "rvest"))               # Web scraping.
```


<a name="code"></a>
## 4. Code Structure

### Step 0: Framework
Prior to getting started it is vital to install and import all the required libraries that are listed in the chapters above. Disregarding this step will lead to an incorrect execution of this program.

### Step 1: Input

The first step is to enter the desired stock ticker (e.g. 'AAPL' for Apple Inc. or 'MSFT' for Microsoft Corporation). Please note that for some smaller companies there is not enough data available to value the stock based on a DCF valuation. In this case, the program will display a corresponding error message.

### Step 2: Descriptive Statistics & Stock Price Development

After the user has chosen a stock for the valuation the program provides some descriptive statistics such as the mean, standard deviation, variance, minimum and maximum of the stock price and stock trading volume during the last year. Additionally, the program visualizes the adjusted closing price development for the same time period.

### Step 3: Assumptions

In this section the program makes some assumptions that are essential for the excecution of the valuation process.

In order to determine the appropriate risk and the corresponding cost of capital for any company, the program requires the interest rate of a risk-free asset. For this purpose, it assumes a risk-free rate of ```1.60%``` in accordance with the 10 Year US Treasury Rate.

Furthermore, the program requires the perpetual growth rate as an assumption to calculate the terminal value of a company. The perpetual growth rate is the growth rate at which a company is expected to continue growing into eternity. Since it cannot realistically be assumed that companies will continue to grow into perpetuity at high rates, a perpetual growth rate in line with the average growth of the GDP is a reasonable assumption. The program therefore applies a rate of ```3.00%``` in accordance with the growth rate of the global GDP.

Finally, a time horizon of ```5 years``` is assumed for the projection of future free cashflows. The shorter the projection period, the larger is the contribution of the terminal value to the total value of the company. On the other hand, an excessively long projection period is also not desirable, as it is extremely difficult to reasonably estimate the individual cash flows for each of the future years. Hence, a time span of 5 years provides a reasonable approach in corporate valuations. This number can of course be customized as desired by the user.

### Step 4: Historical Data & Free Cashflows

The next step is to gather all the historical data of a company that is required for the valuation process. The program automatically collects all the necessary figures (such as historical EBIT, Tax expenses, D&A, Capex and changes in Net Working Capital) from the Yahoo! Finance API and calculates the free cashflows of the past three years.

### Step 5: WACC (Cost of Capital)

In the fourth step the program derives the cost of capital used for discounting future cashflows. The weighted average cost of capital (WACC) consists of cost of equity and cost of dept.

#### Cost of Equity
To calculate cost of equity the program uses the CAPM model, which is a widely used tool in Finance. The CAPM is a special regression analysis that plots the returns of the target company (which represent the dependent variable) against the average market returns of the target company's geographical market (which represents the independent variable). To determine a value for cost of equity, the program pulls the stock's appropriate beta (measure of the individual enterprise risk) from the Yahoo! Finance API. For average market returns the program identifies where the business is located and automatically calculates the average market returns of the corresponding market index.

#### Cost of Dept
There are multiple approaches to calculate a company's cost of dept. To ensure excellent results across a wide range of companies, we have decided to use the credit default risk as a measure of the company's cost of dept. As not all companies have an official credit rating available, we are calculating a synthetic credit rating for the target company based on its interest coverage ratio. Therefore, we put the average operating income before interest payments in relation to the average company's interest expenses. From this, we can derive the associated credit rating and based on that the corresponding credit spread. Now we can compute the company's cost of dept by adding the credit spread to the risk-free rate.

#### Combined Cost of Capital
To derive the total WACC, we multiply the respective cost of capital by the proportion of equity or debt in the company and offset the cost of debt against the tax shield. This provides us with the discount rate for the future cashflows.


### Step 6: Cashflow Growth Rate & Free Cashflow Projection

Since we do not care about hictorical data, as we are valuing the firm based on the expected future development of the company, we need to predict free cashflows for the projection horizon (in this case for the next 5 years). In order to do that, the program calculates historical cashflow growth rates based on the free cashflows of the past three years (which we calculated in Step 3). To avoid excessively high growth rates due to one-off events, the program takes the lower growth rate or averages out extreme values if necessary.

To take into account the difference between future and present value of free cashflows, the program discounts every single cashflow of each year in the projection horizon back to the present value, using the weighted average cost of capital (WACC) that we calculated in the last step.

### Step 7: Terminal Value

To account for the 'going concern principle' (the assumption that the company will continue to exist in the future), we need to calculate the terminal value of the firm. Therefore, the program takes the last projected free cashflow (in this case the cashflow of year 5), grows it by the perpetual growth rate and uses the 'Gordon growth formula' to calculate the perpetual value. Discounting this value back to the present value provides us with the terminal value of the company.

### Step 8: Implied Value per Share

To arrive at the implied value per share, we need to sum up the present values of the projected free cashflows and the terminal value. As we are interested in the equity value of the firm, the program adds the value of the cash from the company's balance sheet and deducts the value of total dept. Dividing this value by the number of shares outstanding we obtain the fair value per share based on our prediction of the development of the firm's future free cashflows.

### Step 9: Current Share Price

Collect the current market value of the share as basis for the recommendation of the "Stock Investing Advisor".

### Step 10: Recommendation

In the last step the program provides the user with a recommendation. Depending on the difference between the implied value per share and the current market value of the share, the program indicates whether it considers the stock to be undervalued, overvalued or efficiently priced. In addition, based on this calculation, the program recommends holding, selling or buying the share.


<a name="scrn"></a>
## 5. Screenshots
Below, you can find screenshots from the Stock Investig Advisor and example inputs for the program. You can click on the thumbnails to download the full size images. 

<img src="https://github.com/KRuschmann/Stock_Investing_Advisor/blob/e44c285ca081601dbd4896461484b13b91da1fe6/screenshots/Title.png" width="23%"> <img src="https://github.com/KRuschmann/Stock_Investing_Advisor/blob/d151f4685a3cdabaf2f918bf35e530fb2ed17bac/screenshots/Input.png" width="23%"> <img src="https://github.com/KRuschmann/Stock_Investing_Advisor/blob/d151f4685a3cdabaf2f918bf35e530fb2ed17bac/screenshots/Feedback.png" width="23%"> <img src="https://github.com/KRuschmann/Stock_Investing_Advisor/blob/d151f4685a3cdabaf2f918bf35e530fb2ed17bac/screenshots/Statistics.png" width="23%"> <img src="https://github.com/KRuschmann/Stock_Investing_Advisor/blob/d151f4685a3cdabaf2f918bf35e530fb2ed17bac/screenshots/Visualization.png" width="23%"> <img src="https://github.com/KRuschmann/Stock_Investing_Advisor/blob/d151f4685a3cdabaf2f918bf35e530fb2ed17bac/screenshots/Recommendation.png" width="23%"> <img src="https://github.com/KRuschmann/Stock_Investing_Advisor/blob/d151f4685a3cdabaf2f918bf35e530fb2ed17bac/screenshots/Geographics.png" width="23%"> <img src="https://github.com/KRuschmann/Stock_Investing_Advisor/blob/d151f4685a3cdabaf2f918bf35e530fb2ed17bac/screenshots/Example%20inputs.png" width="23%">


<a name="discl"></a>
## 6. Disclaimer
This valuation model is based on the anticipation of future free cash flows. As with any intrinsic valuation method, it is essential to bear in mind that valuations are not equally applicable to all businesses. While some companies do not even meet the required criteria (e.g. generating positive cash flows), other companies' values are not directly linked to the generation of free cash flows (e.g. Tesla and other companies that are experiencing hype for various reasons). Therefore, it is important to consider the individual context of each company in order to correctly implement the output of this DCF valuation. The delivered value should never be considered as an isolated basis in any decision-making process.


<a name="app"></a>
## 7. Appendix: Library Description

### shinyWidgets
```shinyWidgets```boffers custom widgets and other components to enhance your shiny applications. You can replace classical checkboxes with switch button, add colors to radio buttons and checkbox group, use buttons as radio or checkboxes. Each widget has an update method to change the value of an input from the server. [(1)](https://github.com/dreamRs/shinyWidgets)
### shinythemes
```shinythemes``` provides some Bootstrap themes for use with Shiny. The themes are from from https://bootswatch.com/. [(2)](https://github.com/rstudio/shinythemes)
### bslib
```bslib``` provides tools for customizing Bootstrap themes directly from R, making it much easier to customize the appearance of Shiny apps & R Markdown documents. [(3)](https://rstudio.github.io/bslib/)
### PerformanceAnalytics
```PerformanceAnalytics``` provides econometric functions for performance and risk analysis of financial instruments or portfolios. This package aims to aid practitioners and researchers in using the latest research for analysis of both normally and non-normally distributed return streams. [(4)](https://cran.r-project.org/web/packages/PerformanceAnalytics/PerformanceAnalytics.pdf)
### PortfolioAnalytics
```PortfolioAnalytics``` provides numerical solutions for portfolio problems with complex constraints and objective sets. The goal of the package is to aid practicioners and researchers in solving portfolio optimization problems with complex constraints and objectives that mirror real-world applications. [(5)](https://cran.r-project.org/web/packages/PortfolioAnalytics/PortfolioAnalytics.pdf)
### tidyquant
```tidyquant``` integrates the best resources for collecting and analyzing financial data with the tidy data infrastructure of the tidyverse allowing for seamless interaction between each. You can now perform complete financial analyses in the tidyverse. [(6)](https://cran.r-project.org/web/packages/tidyquant/index.html)
### tidyverse
```tidyverse``` is an opinionated collection of R packages designed for data science. All packages share an underlying design philosophy, grammar, and data structures. [(7)](https://cran.r-project.org/web/packages/tidyverse/index.html)
### magrittr
```magrittr``` has two aims: decrease development time and improve readability and maintainability of code. To achieve its humble aims, magrittr provides a new “pipe”-like operator, ```%>%```, with which you may pipe a value forward into an expression or function call; something along the lines of ```x %>% f```, rather than ```f(x)```. [(8)](https://cran.r-project.org/web/packages/magrittr/vignettes/magrittr.html)
### reactable
```reactable``` provides interactive data tables for R, based on the 'React Table' JavaScript library. It provides an HTML widget that can be used in 'R Markdown' documents and 'Shiny' applications, or viewed from an R console. [(9)](https://glin.github.io/reactable/)
### arrow
```arrow``` (Apache Arrow) is a cross-language development platform for in-memory data. It specifies a standardized language-independent columnar memory format for flat and hierarchical data, organized for efficient analytic operations on modern hardware. It also provides computational libraries and zero-copy streaming messaging and interprocess communication. [(10)](https://arrow.apache.org/docs/r/)
### qs
```qs``` provides functions for quickly writing and reading any R object to and from disk. [(11)](https://cran.r-project.org/web/packages/qs/index.html)
### timetk
```timetk``` is a package that is part of the modeltime ecosystem for time series analysis and forecasting. [(12)](https://business-science.github.io/timetk/)
### dygraphs
```dygraphs``` is an R interface to the dygraphs JavaScript charting library. It provides rich facilities for charting time-series data in R. [(13)](https://rstudio.github.io/dygraphs/)
### rvest
```rvest``` helps you scrape (or harvest) data from web pages. It is designed to work with magrittr to make it easy to express common web scraping tasks, inspired by libraries like beautiful soup and RoboBrowser. [(14)](https://github.com/tidyverse/rvest)

