# Back-testing-a-value-investing-strategy

Do value stocks outperform growth stocks?

We will measure value and growth using book-to-market ratios. Book-to-market ratios are a useful measure of where a company is in its lifecycle. A growth company derives most of its valuation from investors' expectation of future performance. The company may not be that profitable yet and has not generated much book value, but its market value (market cap) may be high because investors expect it to be profitable in the future. Such a company will have a low ratio of book value to market value. On the other hand, a value company, has already generated a lot of book value, but investors do not expect it to grow much in the future. So its market value and book value are closer together, and this its book-to-market ratio is high.

Fama and French found that stocks with high book-to-market ratios (value stocks) have historically generated higher returns than stocks with low book-to-market ratios (growth stocks). In this project, we will examine whether this has been true recently.

### Logistics: 

1. Get accounting data to obtain book values (book value of shareholder's equity)
2. Get stock data to obtain market values (market cap)
3. Merge the two datasets to compute book-to-market ratios
4. Use end of 2012 data to sort companies into portfolios based on book-to-market
5. Compute the returns on the portfolios over the subsequent decade

### Part A: Get Data

We will obtain data from WRDS

#### Accounting Data

To get accounting data, on WRDS go to CRSP -> CRSP/Compustat Merged -> Fundamentals Annual. Select Data Date from Dec 2012 to Dec 2021.

#### Stock Data

To get stock price data, on WRDS go to CRSP -> Stock/Security Files -> Monthly Stock File. Select Data Date from Dec 2012 to Dec 2021

### Part B: Construct the Portfolio

1. Clean the data.
    - Accounting data: keep only U.S. based companies (`fiq` is `'USA'`) and those with non-missing shareholder equity (`seq`).
    - Stock data: keep only common equity (`SHRCD` of 10 or 11) only trading on the three major U.S. exchanges -- NYSE, Nasdaq, and AMEX (`EXCHCD` of 3 or less).  Keep only data with non-missing prices, shares outstanding, and non-missing **numeric** returns.
2. Calculate book-to-market ratios as of December 31, 2012.
    - Book equity is `seq` (shareholder's equity) minus preferred stock `pstkrv` from the accounting data. Many comapnies don't have preferred stock so if that variable is missing, replace its values with 0 before subtracting. Otherwise you'll end up with missing book equity.
    - Market equity is `prc` (price) times `shrout` (shares outstanding) from the stock data. Note: CRSP denotes quoted but not traded prices with a negative numbers. So take the absolute value of price.
    - Book-to-market is book equity divided by market equity. To compute it, you need to merge the two datasets, matching on `LPERMNO` and `datadate` in Compustat with `PERMNO` and `date` from CRSP.
3. Identify the top 25% and bottom 25% of companies by book-to-market ratio. These are the value and growth companies, respectively. Label them as such in a new column.
4. Print out the 10 largest (by market equity) value and 10 largest growth companies. Are they what you expected? Anything interesting you notice about either list?

### Part C: Compute and Plot Returns

1. Calculate the monthly returns on equal-weighted value and growth portfolios. Recall, an equal-weighted portfolio invests an equal fraction into each stock.
2. Compute cumulative returns to represent the value of $1 invested at the end of 2012 in each portfolio over the 2013-2021 period. You can do this by multiplying sucessive gross returns together. For example, if you invest $1 at the start of January 2013 and the portfolio returns 1% in January, 5% in February, and -3% in March, then the value of your portfolio at the end of March is $1 * (1.01 * 1.05 * 0.97).
3. Plot the cumulative returns on the two portfolios over time. Which portfolio has performed better over the past decade?

### Part D: Consequences of Not Rebalancing

We did not rebalance this portfolio annually. As time went on, book-to-market ratios changed. Some stocks that were growth stocks have since stopped being growth stocks and may have become value. The opposite is less likely but still possible. Let's see how often this happens.

1. Sort stocks into value and growth portfolios at the end of 2021 following the approach you took in Part B.
2. Combine the 2012 and 2021 portfolios into a single dataset so that you have one column for the stocks' 2012 portfolio (value, growth, or neither) and another column for their 2021 portfolio. Keep in mind -- some companies may have been delisted or merged with other companies over the past decade, and new ones have been created. Keep those in the dataset so we can see how the two universes of stocks compare.
3. Create and display a cross-tabulation of the 2012 and 2021 portfolios. This table should have a row for each possible stock category in 2012 and a column for each possible category in 2021, categories being Value, Growth, Neither, and Doesn't Exist. Hint: you can set the `dropmissing` argument of `groupby` to `False` to keep missing categories in the table.
