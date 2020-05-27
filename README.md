# Introduction
This study is aimed at cryptocurrency price prediction using market bid/ask pressures within orderbook data collected from Kaiku.

The correct prediction of the price change in future timestamps is especially important to traders as it gives an idea of the price movement and whether is could be used to initiate a profitable trade.

The use of such a predictive system could easily be wrapped within a trading bot for automated use, if able of consistently producing reliable predictions.

Repository navigation:

    Data_Gathering_API: Downloading Data from API using key provided by Kaiku
  
    Data_Undrestanding_Preperation: A run through the data and some preperation and cleaning.
    
    Modelling_Evaluation: Running baseline model as well as other models with conclusion on all models
    
    other models (redirected to seperate notebooks):
    
        DENSE: Dense neural network modelling

        LSTM: Long Short Term Memory modelling
  
# Business Understanding

**Guided walkthrough:**

When trading any asset one should be aware of the general sentiment around that asset; that is generally obtainable through news outlets as well as some further investigation into the general market and dependant factors. After coming to the decision on the expected performance a trader can then implement some technical analysis (short term/long term analysis on the chart) to determine whether to buy/sell and at what price he would be looking to buy/sell. People don’t generally wait by the computer to initiate their ideal trade, but rather put in limit orders that help automate this step ensuring you buy or sell at your chosen price even if you are not behind the computer. Of course for your computer to execute that trade on your behalf it needs to be pre-funded and that amount held in the case this trade executes. All the limit orders placed by all the traders on the market are stored in what is called an orderbook. In other terms all trader’s sentiment around the asset are now digitalised in numeric form and include the following key features: Buy(bid) or Sell(ask)? How much of the asset (amount)? At what price level (price)? 

When trading on a specific exchange there exists two differnet orders a trader can set:

1- **Market Orders:** These are orders that are settled immediately at the current market price.

    Example: BTC is trading at 9800USD setting a maket order of 0.2 BTC is settled immediately at a cost of (9800*0.2)1960USD in exchange for 0.2 BTC


2- **Limit Orders:** Include orders that are set to trigger at specific price levels to either buy/sell an asset.

    Example: BTC is trading at 9800USD setting a limit order of 0.2 BTC at 9700USD is settled when/if the price were to drop to 9700 at which the tranasction is settled at a cost of (9700*0.2)1940USD for 0.2 BTC

What is the current price?

-Sounds like a weird question to ask but in just in case. The current price is the least priced ask on the market when the trade is settled if you are a buyer. Otherwise, if you are a seller you would be selling at the highest bid at that time. 

Using the orderbook:
"The term order book refers to an electronic list of buy and sell orders for a specific security or financial instrument organized by price level. An order book lists the number of shares being bid on or offered at each price point, or market depth." https://www.investopedia.com/terms/o/order-book.asp

Using this data the prediction of the price movement can be monitored and predicted through changes in the orderbook reflecting buying support or selling resistance.
****
# Data Understanding
The structure of the dataframe is as follows:

|   | date          | type | price   | amount   |
|---|---------------|------|---------|----------|
| 0 | 1567296016662 | b    | 9600.85 | 0.058399 |
| 1 | 1567296016662 | b    | 9600.01 | 0.225000 |
| 2 | 1567296016662 | b    | 9600.00 | 0.104123 |

The sample csv contains snapshots of the order book at different timestamps found within the column 'date'. The order is identified as either a bid or an ask within the 'type' column. The price and amount are also present in the remaining two columns.

The timestamp is in unix format with a millisecond accuracy.

For reproduction: It would be necessary to obtain a similar format orderbook or rearrange it to the format above

# Data Preperation
Data preperation mainly revolved around the rebinning of data into price bins to emphasis the interest in high pressure price levels. Indicating a general market interest at that level.

Because the price of buying something is what another person is willing to sell at, the price of buying the asset is essentially the lowest ask order in the orderbook. And the price at which you would sell an asset likely depends on the price someone is willing to buy at, essentially the highest bid in the orderbook.

The difference between them is called the spread. In other terms it is the difference between the lowest price to sell something and highest price you could buy it at . In practice if you were to sell X amounts of an asset and then buy the same X amount back you will be paying the spread.

The data we are looking through is the orderbook with data of 10% above and below the price, 
If the entire orderbook is df the data we have available is: df.loc[math.abs((df[‘price’] - current price )/current_price)<= 10/100]

and the main aim is to figure out what price levels a price could move to based on the bid/ask pressure.
Data after binning:

    -Columns 0-999: bins of bid data (below the highest bid): 10% — 9.99% — 9.98% — ….. — 0.02% — 0.01% 

    -Columns 1000-1999: bins of ask data (above the lowest ask): 0.01% — 0.02% — ….. — 9.98% — 9.99% — 10%

The actual values in the columns is the sum of the amounts (15BTC etc..) located within X% (refer to above) of the current price.

The amounts in these bins either below or above the price is what constitutes these bid/ask pressures. A lot of people feel it is best to sell at this level —> they don’t think it’ll pass that price level —> they would rather get rid of it and cash out than keep holding onto it —> price will go down.

# Modelling
Since the data was not in consistent time intervals modelling included running logistic to ask as baseline to whether the data could be looked at as discrete values rather than continuous.

-Dense Neural Network: A dense layered model yielded a validation accuracy of 0.705 which was lower than the training accuracy of 0.9687 indicating an over-fit model.

-Long Short Term Memory: Running the LSTM model yielded a validation accuracy of 0.8055 which was higher than the training accuracy of 0.7624 solving the over-fitting problem seen above.


# Evaluation

The buying and selling pressure as time moves indicates the level of interest within the markets.
This change of interest is directly translated to a change in price. 
The orderbook containing all this data should be capable of identifying the movement of the price as well as the price levels if prices were to move.

 -The logistic regression model was run without thresholding on a binary price change of either positive/negative. It was seen to be incapable of accurately predicting a one step forward price change as the accuracy of the model returned 0.54
    
-The Linear Dense neural network model that was run afterwards included three classfication outputs 0,1,2 : sell, hold, buy. The model **training showed a very high accuracy of 0.96** when run at 100 epochs which seemed to be overfitting the data, since the **validation accuracy was 0.7** . Possible solutions include lower epoch number for a less over-fit model.
    
 -Finally the last model attempted which is the model that was chosen to be the best performing was the LSTM neural network model that returned a **validation score of 0.805** which was higher than the **training score of 0.762**, the model was run using a lower epoch number to prevent over-fitting and was consequently run on the test values.
   
   -The final testing of the model returned a **test score of 0.805** which is equal to that of the validation which is always a good sign to see. 

# Other

**Future improvement ideas:**

More data manipulation

Rearranging the data for other models (maybe some techincal representation of the values as a whole)

Arima model/Sarimax model testing 
  
**Sourcing:**

Data source: https://www.kaiko.com/

Data source: Coinbase Pro API 

