# Introduction
This study is aimed at cryptocurrency price prediction using market bid/ask pressures within orderbook data collected from Kaiku.

Repository navigation:

    Data_Gathering_API: Downloading Data from API using key provided by Kaiku
  
    Data_Undrestanding_Preperation: A run through the data and some preperation and cleaning.
    
    Modelling_Evaluation: Running baseline model as well as other models with conclusion on all models
    
    other models (redirected to seperate notebooks):
    
        DENSE: Dense neural network modelling

        LSTM: Long Short Term Memory modelling
  
# Business Understanding

**Guided walkthrough:**

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
date	type	price	amount

0	1567296016662	b	9600.85	0.058399

1	1567296016662	b	9600.01	0.225000

2	1567296016662	b	9600.00	0.104123

3	1567296016662	b	9600.00	0.021774

4	1567296016662	b	9600.00	1.000000


The sample csv contains snapshots of the order book at different timestamps found within the column 'date'. The order is identified as either a bid or an ask within the 'type' column. The price and amount are also present in the remaining two columns.

The timestamp is in unix format with a millisecond accuracy.

For reproduction: It would be necessary to obtain a similar format orderbook or rearrange it to the format above

# Data Preperation
Data preperation mainly revolved around the rebinning of data into price bins to emphasis the interest in high pressure price levels. Indicating a general market interest at that level.


# Modelling
Since the data was not in consistent time intervals modelling included running logistic to ask as baseline to whether the data could be looked at as discrete values rather than continuous.

-Dense Neural Network: A dense layered model yielded a validation accuracy of 0.705 which was lower than the training accuracy of 0.9687 indicating an over-fit model.

-Long Short Term Memory: Running the LSTM model yielded a validation accuracy of 0.8055 which was higher than the training accuracy of 0.7624 solving the over-fitting problem seen above.


# Evaluation

The buying and selling pressure as time moves indicates the level of interest within the markets.
This change of interest is directly translated to a change in price. 
The orderbook containing all this data should be capable of identifying the movement of the price as well as the price levels if prices were to move.

     -The logistic regression with no thresholding model was seen to be more or less incapable of accurately predicting the return expected in one time step as the accuracy of the model returned a 0.54 accuracy value
    
    -The Linear Dense neural network model that was run afterwards included three classfication outputs 0,1,2 : sell, hold, buy. The model training showed a very high accuracy of 0.96 when run at 100 epochs which seemed to be overfitting the data, since the validation accuracy was onlt 0.7 . Possible solutions include lower epoch number for a less over-fit model.
    
    -Finally the last model attempted which is the model that was chosen to be the best performing was the LSTM neural network model that returned a validation score of 0.805 which was higher than the training score of 0.762, the model was run using a lower epoch number to prevent over-fitting and was consequently run on the test values
   
   -The final testing of the model returned an accuracy equal to that of the validation which is always a good sign to see. 

# Other

**Future improvement ideas:**
More data manipulation
Rearranging the data for other models (maybe some techincal representation of the values as a whole)
Arima model/Sarimax model testing 
  
**Sourcing:**

Data source: https://www.kaiko.com/
Data source: Coinbase Pro API 

