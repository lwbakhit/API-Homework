# Initial imports
import os
import requests
import pandas as pd
from dotenv import load_dotenv
import alpaca_trade_api as tradeapi
from MCForecastTools import MCSimulation

%matplotlib inline

from matplotlib import pyplot as plt 
import numpy as np
In [2]:
# Load .env enviroment variables
load_dotenv()
Out[2]:
True
Part 1 - Personal Finance Planner
Collect Crypto Prices Using the requests Library
In [3]:
# Set current amount of crypto assets
# YOUR CODE HERE!
my_btc = 1.2
my_eth = 5.3
In [4]:
# Crypto API URLs
btc_url = "https://api.alternative.me/v2/ticker/Bitcoin/?convert=CAD"
eth_url = "https://api.alternative.me/v2/ticker/Ethereum/?convert=CAD"
In [5]:
# Fetch current BTC price
# YOUR CODE HERE!
btc_url = btc_url + "?format=json"

requests.get(btc_url)

response_data_btc = requests.get(btc_url)

response_content_btc = response_data_btc.content
In [6]:
# Formatting as json
import json
data_btc = response_data_btc.json()
print(json.dumps(data_btc, indent=4))
{
    "data": {
        "1": {
            "id": 1,
            "name": "Bitcoin",
            "symbol": "BTC",
            "website_slug": "bitcoin",
            "rank": 1,
            "circulating_supply": 18525975,
            "total_supply": 18525975,
            "max_supply": 21000000,
            "quotes": {
                "USD": {
                    "price": 13098.56,
                    "volume_24h": 19370001928,
                    "market_cap": 242581351449,
                    "percentage_change_1h": -0.0605302380496886,
                    "percentage_change_24h": 1.49671630404627,
                    "percentage_change_7d": 15.7209079080384,
                    "percent_change_1h": -0.0605302380496886,
                    "percent_change_24h": 1.49671630404627,
                    "percent_change_7d": 15.7209079080384
                }
            },
            "last_updated": 1603574365
        }
    },
    "metadata": {
        "timestamp": 1603574365,
        "num_cryptocurrencies": 1429,
        "error": null
    }
}
In [7]:
# Fetch current ETH price
eth_url = eth_url + "?format=json"
requests.get(eth_url)
response_data_eth = requests.get(eth_url)
response_content_eth = response_data_eth.content
data_eth = response_data_eth.json()
print(json.dumps(data_eth, indent=4))
{
    "data": {
        "1027": {
            "id": 1027,
            "name": "Ethereum",
            "symbol": "ETH",
            "website_slug": "ethereum",
            "rank": 2,
            "circulating_supply": 113142859,
            "total_supply": 113142859,
            "max_supply": 0,
            "quotes": {
                "USD": {
                    "price": 411.44,
                    "volume_24h": 9521200162,
                    "market_cap": 46457431525,
                    "percentage_change_1h": -0.273165693201108,
                    "percentage_change_24h": 0.813392054220548,
                    "percentage_change_7d": 12.4188291216044,
                    "percent_change_1h": -0.273165693201108,
                    "percent_change_24h": 0.813392054220548,
                    "percent_change_7d": 12.4188291216044
                }
            },
            "last_updated": 1603574228
        }
    },
    "metadata": {
        "timestamp": 1603574228,
        "num_cryptocurrencies": 1429,
        "error": null
    }
}
In [8]:
# Compute current value of my crpto
# YOUR CODE HERE!
btc_value = data_btc["data"]["1"]["quotes"]["USD"]['price']
eth_value = data_eth["data"]["1027"]["quotes"]["USD"]['price']

my_btc_value = btc_value * my_btc
my_eth_value = eth_value * my_eth


# Print current crypto wallet balance
print(f"The current value of your {my_btc} BTC is ${my_btc_value:0.2f}")
print(f"The current value of your {my_eth} ETH is ${my_eth_value:0.2f}")
The current value of your 1.2 BTC is $15718.27
The current value of your 5.3 ETH is $2180.63
Collect Investments Data Using Alpaca: SPY (stocks) and AGG (bonds)
In [18]:
# Current amount of shares
# YOUR CODE HERE!
SPY_shares = 50
AGG_shares = 200
In [19]:
# Set Alpaca API key and secret
# YOUR CODE HERE!
alpaca_api_key = os.getenv("ALPACA_API_KEY")
alpaca_secret_key = os.getenv("ALPACA_SECRET_KEY")

# Create the Alpaca API object
# YOUR CODE HERE!
api = tradeapi.REST(
    alpaca_api_key,
    alpaca_secret_key,
    api_version = "v2"
)
In [20]:
# Format current date as ISO format
today = pd.Timestamp("2020-10-23", tz="America/New_York").isoformat()

# Set the tickers
tickers = ["AGG", "SPY"]

# Set timeframe to '1D' for Alpaca API
timeframe = "1D"

# Get current closing prices for SPY and AGG
ticker_data = api.get_barset(
    tickers,
    timeframe,
    start=today,
    end=today
).df

# Preview DataFrame
ticker_data.head()
Out[20]:
AGG	SPY
open	high	low	close	volume	open	high	low	close	volume
2020-10-23 00:00:00-04:00	117.3	117.52	117.3	117.47	3482671	345.93	345.99	343.13	345.76	38718140
In [21]:
# Pick AGG and SPY close prices
spy_close_price = float(ticker_data["SPY"]["close"])
agg_close_price = float(ticker_data["AGG"]["close"])

# Print AGG and SPY close prices
print(f"Current AGG closing price: ${agg_close_price}")
print(f"Current SPY closing price: ${spy_close_price}")
Current AGG closing price: $117.47
Current SPY closing price: $345.76
In [22]:
# Compute the current value of shares
my_spy_value = spy_close_price * SPY_shares
my_agg_value = agg_close_price * AGG_shares

# Print current value of share
print(f"Current AGG value: ${my_spy_value}")
print(f"Current SPY value: ${my_agg_value}")
Current AGG value: $17288.0
Current SPY value: $23494.0
In [23]:
total_value_shares = my_spy_value + my_agg_value
total_value_crypto = my_btc_value + my_eth_value

print(f"Current Share value: ${total_value_shares}")
print(f"Current Crypto value: ${total_value_crypto}")
Current Share value: $40782.0
Current Crypto value: $17898.904
Savings Health Analysis
In [24]:
# Set monthly household income
monthly_income = 1200

# Create savings DataFrame
df_value = {
    "Current Share Value": [total_value_shares],
    "Current Crypto Value": [total_value_crypto]
}

df_savings = pd.DataFrame(df_value)

# Display savings DataFrame
display(df_savings)
Current Share Value	Current Crypto Value
0	40782.0	17898.904
In [25]:
# Plot savings pie chart
fig = plt.figure()
ax = fig.add_axes([0,0,1,1])
ax.axis('equal')
savings = ['Current Share Value', 'Current Crypto Value']
prices = [43131.4,17898.71]
ax.pie(prices, labels = savings,autopct='%1.2f%%')
plt.show()

In [17]:
# Set ideal emergency fund
emergency_fund = monthly_income * 3

# Calculate total amount of savings
total_savings = total_value_shares + total_value_crypto

# Validate saving health
if total_savings > emergency_fund:
    print("Congratualations! You have enough money in your emergency fund.")
Congratualations! You have enough money in your emergency fund.
Part 2 - Retirement Planning
Monte Carlo Simulation
In [86]:
# Set start and end dates of five years back from today.
# Sample results may vary from the solution based on the time frame chosen
start_date = pd.Timestamp('2015-08-07', tz='America/New_York').isoformat()
end_date = pd.Timestamp('2020-08-07', tz='America/New_York').isoformat()
In [87]:
# Get 5 years' worth of historical data for SPY and AGG
df_stock_data = api.get_barset(
    tickers,
    timeframe,
    start=start_date,
    end=end_date
).df

# Display sample data
df_stock_data.head()
Out[87]:
AGG	SPY
open	high	low	close	volume	open	high	low	close	volume
2015-08-07 00:00:00-04:00	109.14	109.2750	109.035	109.21	2041167.0	208.16	208.34	206.87	207.93	87669782
2015-08-10 00:00:00-04:00	109.15	109.1700	108.920	109.06	1149778.0	209.28	210.67	209.28	210.58	66755890
2015-08-11 00:00:00-04:00	109.42	109.5765	109.284	109.42	1420907.0	208.98	209.47	207.76	208.63	88424557
2015-08-12 00:00:00-04:00	109.55	109.7100	109.350	109.36	1468979.0	207.11	209.14	205.36	208.89	136171450
2015-08-13 00:00:00-04:00	109.36	109.3651	109.110	109.15	1465173.0	208.73	209.55	208.01	208.63	77197796
In [120]:
# Configuring a Monte Carlo simulation to forecast 30 years cumulative returns
MC_even_dist = MCSimulation(
    portfolio_data = df_stock_data,
    weights = [.40, .60],
    num_simulation = 500,
    num_trading_days = 252*30
)
In [121]:
# Printing the simulation input data
MC_even_dist.portfolio_data.head()
Out[121]:
AGG	SPY
open	high	low	close	volume	daily_return	open	high	low	close	volume	daily_return
2017-05-04 00:00:00-04:00	108.55	108.70	108.55	108.665	1789624.0	NaN	238.83	238.9225	237.78	238.75	46170233	NaN
2017-05-05 00:00:00-04:00	108.72	108.74	108.57	108.720	1287794.0	0.000506	239.18	239.7200	238.68	239.72	43733544	0.004063
2017-05-08 00:00:00-04:00	108.63	108.67	108.49	108.495	1579134.0	-0.002070	239.76	239.9200	239.17	239.67	32087919	-0.000209
2017-05-09 00:00:00-04:00	108.43	108.51	108.40	108.495	2395343.0	0.000000	239.96	240.1900	239.04	239.46	39723891	-0.000876
2017-05-10 00:00:00-04:00	108.60	108.66	108.44	108.520	1427511.0	0.000230	239.38	239.8700	239.15	239.85	34222778	0.001629
In [122]:
# Running a Monte Carlo simulation to forecast 30 years cumulative returns
MC_even_dist.calc_cumulative_return()
Running Monte Carlo simulation number 0.
Running Monte Carlo simulation number 10.
Running Monte Carlo simulation number 20.
Running Monte Carlo simulation number 30.
Running Monte Carlo simulation number 40.
Running Monte Carlo simulation number 50.
Running Monte Carlo simulation number 60.
Running Monte Carlo simulation number 70.
Running Monte Carlo simulation number 80.
Running Monte Carlo simulation number 90.
Running Monte Carlo simulation number 100.
Running Monte Carlo simulation number 110.
Running Monte Carlo simulation number 120.
Running Monte Carlo simulation number 130.
Running Monte Carlo simulation number 140.
Running Monte Carlo simulation number 150.
Running Monte Carlo simulation number 160.
Running Monte Carlo simulation number 170.
Running Monte Carlo simulation number 180.
Running Monte Carlo simulation number 190.
Running Monte Carlo simulation number 200.
Running Monte Carlo simulation number 210.
Running Monte Carlo simulation number 220.
Running Monte Carlo simulation number 230.
Running Monte Carlo simulation number 240.
Running Monte Carlo simulation number 250.
Running Monte Carlo simulation number 260.
Running Monte Carlo simulation number 270.
Running Monte Carlo simulation number 280.
Running Monte Carlo simulation number 290.
Running Monte Carlo simulation number 300.
Running Monte Carlo simulation number 310.
Running Monte Carlo simulation number 320.
Running Monte Carlo simulation number 330.
Running Monte Carlo simulation number 340.
Running Monte Carlo simulation number 350.
Running Monte Carlo simulation number 360.
Running Monte Carlo simulation number 370.
Running Monte Carlo simulation number 380.
Running Monte Carlo simulation number 390.
Running Monte Carlo simulation number 400.
Running Monte Carlo simulation number 410.
Running Monte Carlo simulation number 420.
Running Monte Carlo simulation number 430.
Running Monte Carlo simulation number 440.
Running Monte Carlo simulation number 450.
Running Monte Carlo simulation number 460.
Running Monte Carlo simulation number 470.
Running Monte Carlo simulation number 480.
Running Monte Carlo simulation number 490.
Out[122]:
0	1	2	3	4	5	6	7	8	9	...	490	491	492	493	494	495	496	497	498	499
0	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	...	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000
1	0.980674	1.001481	0.996300	1.003902	0.988963	1.002596	1.002873	0.994484	1.002651	1.000784	...	0.988966	0.999908	0.998515	0.990012	1.006602	1.001400	0.990985	1.004529	1.015185	0.999988
2	0.983447	0.994926	0.994084	1.014283	0.986992	1.007029	1.011586	0.996340	0.976128	0.995427	...	0.994921	1.014054	0.987969	0.989917	0.998578	1.008023	0.979144	1.018651	1.011390	1.003220
3	0.975824	1.002515	0.999438	1.014767	0.992504	1.010794	1.018583	1.003910	0.985815	0.986517	...	1.008832	1.010948	0.992308	0.988365	0.981652	1.012749	0.973819	1.015787	1.018661	1.000269
4	0.968987	1.006887	1.002395	1.011216	0.987244	0.998124	1.022072	1.000537	0.989642	0.987729	...	1.006819	0.997542	0.988493	0.997812	0.971552	1.013803	0.969273	1.014890	1.028313	1.007368
...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...
7556	17.532903	3.936265	9.072207	5.061623	11.109742	10.222144	3.887087	6.115339	4.296899	4.913455	...	5.453840	3.953150	3.490449	2.011166	10.875541	9.676332	2.679734	2.300697	2.315715	1.236896
7557	17.563170	3.913994	9.043520	5.047309	11.241016	10.304813	3.898369	6.180149	4.272433	4.866521	...	5.425879	3.913874	3.436777	2.002221	10.921116	9.697007	2.685187	2.291924	2.327735	1.226435
7558	17.645411	3.955485	8.926526	5.067081	11.320722	10.194775	3.885971	6.167531	4.278389	4.866638	...	5.475716	3.949833	3.369351	1.990583	11.032819	9.606236	2.703764	2.304251	2.338132	1.207965
7559	17.499487	3.913644	8.824074	5.060812	11.270364	10.182928	3.927986	6.250978	4.219149	4.893420	...	5.489103	3.944512	3.359002	1.991885	11.101687	9.523170	2.721080	2.291684	2.318453	1.209413
7560	17.549036	3.867834	8.803430	5.028786	11.240518	10.276117	3.923276	6.203043	4.239852	4.890392	...	5.450769	3.902085	3.348968	1.963511	11.060325	9.762843	2.729497	2.276453	2.320194	1.224383
7561 rows × 500 columns

In [123]:
# Plot simulation outcomes
line_plot = MC_even_dist.plot_simulation()

In [124]:
# Plot probability distribution and confidence intervals
dist_plot = MC_even_dist.plot_distribution()

Retirement Analysis
In [125]:
# Fetch summary statistics from the Monte Carlo simulation results
even_tbl = MC_even_dist.summarize_cumulative_return()

# Print summary statistics
print(even_tbl)
count           500.000000
mean              5.417247
std               4.110698
min               0.472362
25%               2.628708
50%               4.241905
75%               6.975438
max              28.653615
95% CI Lower      1.060960
95% CI Upper     17.200637
Name: 7560, dtype: float64
Calculate the expected portfolio return at the 95% lower and upper confidence intervals based on a $20,000 initial investment.
In [153]:
# Set initial investment
initial_investment = 20000

# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes of our $20,000
ci_lower = round(even_tbl[8]*20000,2)
ci_upper = round(even_tbl[9]*20000,2)

# Print results
print(f"There is a 95% chance that an initial investment of ${initial_investment} in the portfolio"
      f" over the next 30 years will end within in the range of"
      f" ${ci_lower} and ${ci_upper}")
There is a 95% chance that an initial investment of $20000 in the portfolio over the next 30 years will end within in the range of $21219.19 and $344012.75
Calculate the expected portfolio return at the 95% lower and upper confidence intervals based on a 50% increase in the initial investment.
In [152]:
# Set initial investment
initial_investment2 = 20000 * 1.5

# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes of our $30,000
ci_lower = round(even_tbl[8]*30000,2)
ci_upper = round(even_tbl[9]*30000,2)

# Print results
print(f"There is a 95% chance that an initial investment of ${initial_investment2} in the portfolio"
      f" over the next 30 years will end within in the range of"
      f" ${ci_lower} and ${ci_upper}")
There is a 95% chance that an initial investment of $30000.0 in the portfolio over the next 30 years will end within in the range of $31828.79 and $516019.12
Optional Challenge - Early Retirement
Five Years Retirement Option
In [133]:
# Configuring a Monte Carlo simulation to forecast 5 years cumulative returns
MC_even_dist2 = MCSimulation(
    portfolio_data = df_stock_data,
    weights = [.25, .75],
    num_simulation = 500,
    num_trading_days = 252*5
)
MC_even_dist2.portfolio_data.head()
Out[133]:
AGG	SPY
open	high	low	close	volume	daily_return	open	high	low	close	volume	daily_return
2017-05-04 00:00:00-04:00	108.55	108.70	108.55	108.665	1789624.0	NaN	238.83	238.9225	237.78	238.75	46170233	NaN
2017-05-05 00:00:00-04:00	108.72	108.74	108.57	108.720	1287794.0	0.000506	239.18	239.7200	238.68	239.72	43733544	0.004063
2017-05-08 00:00:00-04:00	108.63	108.67	108.49	108.495	1579134.0	-0.002070	239.76	239.9200	239.17	239.67	32087919	-0.000209
2017-05-09 00:00:00-04:00	108.43	108.51	108.40	108.495	2395343.0	0.000000	239.96	240.1900	239.04	239.46	39723891	-0.000876
2017-05-10 00:00:00-04:00	108.60	108.66	108.44	108.520	1427511.0	0.000230	239.38	239.8700	239.15	239.85	34222778	0.001629
In [134]:
# Running a Monte Carlo simulation to forecast 5 years cumulative returns
MC_even_dist2.calc_cumulative_return()
Running Monte Carlo simulation number 0.
Running Monte Carlo simulation number 10.
Running Monte Carlo simulation number 20.
Running Monte Carlo simulation number 30.
Running Monte Carlo simulation number 40.
Running Monte Carlo simulation number 50.
Running Monte Carlo simulation number 60.
Running Monte Carlo simulation number 70.
Running Monte Carlo simulation number 80.
Running Monte Carlo simulation number 90.
Running Monte Carlo simulation number 100.
Running Monte Carlo simulation number 110.
Running Monte Carlo simulation number 120.
Running Monte Carlo simulation number 130.
Running Monte Carlo simulation number 140.
Running Monte Carlo simulation number 150.
Running Monte Carlo simulation number 160.
Running Monte Carlo simulation number 170.
Running Monte Carlo simulation number 180.
Running Monte Carlo simulation number 190.
Running Monte Carlo simulation number 200.
Running Monte Carlo simulation number 210.
Running Monte Carlo simulation number 220.
Running Monte Carlo simulation number 230.
Running Monte Carlo simulation number 240.
Running Monte Carlo simulation number 250.
Running Monte Carlo simulation number 260.
Running Monte Carlo simulation number 270.
Running Monte Carlo simulation number 280.
Running Monte Carlo simulation number 290.
Running Monte Carlo simulation number 300.
Running Monte Carlo simulation number 310.
Running Monte Carlo simulation number 320.
Running Monte Carlo simulation number 330.
Running Monte Carlo simulation number 340.
Running Monte Carlo simulation number 350.
Running Monte Carlo simulation number 360.
Running Monte Carlo simulation number 370.
Running Monte Carlo simulation number 380.
Running Monte Carlo simulation number 390.
Running Monte Carlo simulation number 400.
Running Monte Carlo simulation number 410.
Running Monte Carlo simulation number 420.
Running Monte Carlo simulation number 430.
Running Monte Carlo simulation number 440.
Running Monte Carlo simulation number 450.
Running Monte Carlo simulation number 460.
Running Monte Carlo simulation number 470.
Running Monte Carlo simulation number 480.
Running Monte Carlo simulation number 490.
Out[134]:
0	1	2	3	4	5	6	7	8	9	...	490	491	492	493	494	495	496	497	498	499
0	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	...	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000
1	0.998381	0.986198	0.977255	1.012358	1.001757	0.996180	0.991139	0.984987	1.004632	1.012967	...	0.982392	1.012601	0.995245	0.995212	0.992751	0.998605	0.999902	1.002943	1.009689	1.002669
2	0.976868	0.983891	0.983649	1.013315	1.003622	1.006377	0.980740	0.975583	1.000870	0.999607	...	0.985948	1.007091	0.993235	0.998500	1.009176	1.011352	1.012490	0.998604	1.015358	1.014913
3	0.949144	0.972791	0.978600	1.017486	1.011331	1.014657	0.984736	0.994689	1.015511	0.987866	...	0.984667	1.005078	0.985374	1.025212	0.992561	1.019153	1.013045	0.999260	1.009177	1.015599
4	0.961223	0.966144	0.986918	1.013266	1.004276	1.018372	0.990402	0.983314	1.025516	0.963422	...	0.968215	1.014199	0.990323	1.035478	1.010765	1.018728	1.018467	1.005826	1.012662	1.021614
...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...
1256	1.409882	1.100224	0.985481	1.527296	0.590297	1.230245	1.517798	0.997507	1.185878	1.174868	...	0.957137	2.503381	0.973257	1.955606	1.773656	1.222098	1.467315	1.585613	0.767941	1.911127
1257	1.422262	1.109641	0.992224	1.531215	0.584889	1.225508	1.492189	0.998837	1.179426	1.161690	...	0.958002	2.510520	0.973928	1.934678	1.782598	1.231994	1.461624	1.575430	0.774677	1.918023
1258	1.406609	1.104265	1.005512	1.542476	0.582277	1.243779	1.500595	1.006758	1.186910	1.155338	...	0.968096	2.478034	0.975311	1.912237	1.792239	1.224049	1.466280	1.551302	0.777466	1.908150
1259	1.404361	1.118985	1.006224	1.526207	0.585091	1.229208	1.533145	0.995084	1.193186	1.143746	...	0.967116	2.462323	0.968863	1.947190	1.811522	1.226587	1.486785	1.554132	0.794547	1.885164
1260	1.418956	1.134303	1.010460	1.532156	0.586370	1.228880	1.528360	1.002776	1.182578	1.147133	...	0.965015	2.520147	0.967397	1.948694	1.808291	1.231060	1.470235	1.561645	0.803965	1.893460
1261 rows × 500 columns

In [135]:
# Plot simulation outcomes
line_plot2 = MC_even_dist2.plot_simulation()

In [136]:
# Plot probability distribution and confidence intervals
dist_plot2 = MC_even_dist2.plot_distribution()

In [137]:
# Fetch summary statistics from the Monte Carlo simulation results
even_tbl2 = MC_even_dist2.summarize_cumulative_return()

# Print summary statistics
print(even_tbl2)
count           500.000000
mean              1.363972
std               0.493465
min               0.432051
25%               1.002502
50%               1.278798
75%               1.615819
max               3.274789
95% CI Lower      0.647963
95% CI Upper      2.592722
Name: 1260, dtype: float64
In [151]:
# Set initial investment
initial_investment3 = 60000

# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes of our $60,000
ci_lower_five = round(even_tbl2[8]*60000,2)
ci_upper_five = round(even_tbl2[9]*60000,2)

# Print results
print(f"There is a 95% chance that an initial investment of ${initial_investment3} in the portfolio"
      f" over the next 5 years will end within in the range of"
      f" ${ci_lower_five} and ${ci_upper_five}")
There is a 95% chance that an initial investment of $60000 in the portfolio over the next 5 years will end within in the range of $38877.77 and $155563.31
Ten Years Retirement Option
In [140]:
# Configuring a Monte Carlo simulation to forecast 10 years cumulative returns
MC_even_dist3 = MCSimulation(
    portfolio_data = df_stock_data,
    weights = [.25, .75],
    num_simulation = 500,
    num_trading_days = 252*10
)
MC_even_dist3.portfolio_data.head()
Out[140]:
AGG	SPY
open	high	low	close	volume	daily_return	open	high	low	close	volume	daily_return
2017-05-04 00:00:00-04:00	108.55	108.70	108.55	108.665	1789624.0	NaN	238.83	238.9225	237.78	238.75	46170233	NaN
2017-05-05 00:00:00-04:00	108.72	108.74	108.57	108.720	1287794.0	0.000506	239.18	239.7200	238.68	239.72	43733544	0.004063
2017-05-08 00:00:00-04:00	108.63	108.67	108.49	108.495	1579134.0	-0.002070	239.76	239.9200	239.17	239.67	32087919	-0.000209
2017-05-09 00:00:00-04:00	108.43	108.51	108.40	108.495	2395343.0	0.000000	239.96	240.1900	239.04	239.46	39723891	-0.000876
2017-05-10 00:00:00-04:00	108.60	108.66	108.44	108.520	1427511.0	0.000230	239.38	239.8700	239.15	239.85	34222778	0.001629
In [141]:
# Running a Monte Carlo simulation to forecast 10 years cumulative returns
MC_even_dist3.calc_cumulative_return()
Running Monte Carlo simulation number 0.
Running Monte Carlo simulation number 10.
Running Monte Carlo simulation number 20.
Running Monte Carlo simulation number 30.
Running Monte Carlo simulation number 40.
Running Monte Carlo simulation number 50.
Running Monte Carlo simulation number 60.
Running Monte Carlo simulation number 70.
Running Monte Carlo simulation number 80.
Running Monte Carlo simulation number 90.
Running Monte Carlo simulation number 100.
Running Monte Carlo simulation number 110.
Running Monte Carlo simulation number 120.
Running Monte Carlo simulation number 130.
Running Monte Carlo simulation number 140.
Running Monte Carlo simulation number 150.
Running Monte Carlo simulation number 160.
Running Monte Carlo simulation number 170.
Running Monte Carlo simulation number 180.
Running Monte Carlo simulation number 190.
Running Monte Carlo simulation number 200.
Running Monte Carlo simulation number 210.
Running Monte Carlo simulation number 220.
Running Monte Carlo simulation number 230.
Running Monte Carlo simulation number 240.
Running Monte Carlo simulation number 250.
Running Monte Carlo simulation number 260.
Running Monte Carlo simulation number 270.
Running Monte Carlo simulation number 280.
Running Monte Carlo simulation number 290.
Running Monte Carlo simulation number 300.
Running Monte Carlo simulation number 310.
Running Monte Carlo simulation number 320.
Running Monte Carlo simulation number 330.
Running Monte Carlo simulation number 340.
Running Monte Carlo simulation number 350.
Running Monte Carlo simulation number 360.
Running Monte Carlo simulation number 370.
Running Monte Carlo simulation number 380.
Running Monte Carlo simulation number 390.
Running Monte Carlo simulation number 400.
Running Monte Carlo simulation number 410.
Running Monte Carlo simulation number 420.
Running Monte Carlo simulation number 430.
Running Monte Carlo simulation number 440.
Running Monte Carlo simulation number 450.
Running Monte Carlo simulation number 460.
Running Monte Carlo simulation number 470.
Running Monte Carlo simulation number 480.
Running Monte Carlo simulation number 490.
Out[141]:
0	1	2	3	4	5	6	7	8	9	...	490	491	492	493	494	495	496	497	498	499
0	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	...	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000
1	1.002219	1.015573	1.001815	1.014466	1.002517	1.003830	0.986645	0.995791	1.008770	0.996069	...	1.002944	0.997328	0.996746	0.988316	0.986833	1.006737	0.999890	1.014434	0.986674	0.998050
2	0.986068	1.023798	0.978840	1.010581	1.005896	1.008760	0.979565	0.986163	0.992453	0.996248	...	1.000167	0.991164	0.981999	1.001913	1.005946	1.015728	0.980191	1.004879	0.994867	1.001760
3	0.981391	1.027070	0.974041	1.014644	1.003202	0.995082	0.974642	0.996492	0.990923	1.006371	...	0.999056	0.997962	0.976198	1.000871	1.006237	1.004204	0.995025	1.023865	1.004642	1.000281
4	0.989417	1.033157	0.981197	1.019859	1.011573	1.001601	0.980880	0.992013	0.991588	1.012974	...	0.980113	0.999585	0.981158	0.990404	1.004933	0.992153	0.993014	1.018381	1.008534	0.991543
...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...
2516	2.798913	1.804331	0.682731	1.617316	3.193903	2.469874	1.729369	3.623501	1.693827	2.431793	...	1.869696	2.370441	2.149712	7.554817	3.609288	2.975995	1.852120	2.039779	0.840036	1.532325
2517	2.845827	1.840781	0.669755	1.605783	3.169801	2.456893	1.732576	3.566699	1.730964	2.426518	...	1.881886	2.364498	2.130233	7.470154	3.574616	3.003537	1.829767	2.048915	0.846855	1.534366
2518	2.819367	1.822698	0.672524	1.599869	3.111372	2.484985	1.737835	3.577863	1.721213	2.423327	...	1.870367	2.328541	2.090199	7.419409	3.614524	2.998238	1.852307	2.062701	0.841698	1.512154
2519	2.847081	1.800219	0.680459	1.598565	3.085814	2.443083	1.750196	3.614436	1.738035	2.365533	...	1.858169	2.393240	2.099344	7.426743	3.596107	2.984386	1.860874	2.054668	0.835883	1.499622
2520	2.893582	1.816569	0.697065	1.607348	3.118478	2.475600	1.725758	3.594816	1.754755	2.377874	...	1.867095	2.421257	2.119273	7.404481	3.571108	2.993673	1.849198	2.045402	0.834294	1.494226
2521 rows × 500 columns

In [142]:
# Plot simulation outcomes
line_plot3 = MC_even_dist3.plot_simulation()

In [143]:
# Plot probability distribution and confidence intervals
dist_plot3 = MC_even_dist3.plot_distribution()

In [144]:
# Fetch summary statistics from the Monte Carlo simulation results
even_tbl3 = MC_even_dist3.summarize_cumulative_return()

# Print summary statistics
print(even_tbl3)
count           500.000000
mean              1.910282
std               1.072303
min               0.469152
25%               1.160758
50%               1.666575
75%               2.388296
max               7.404481
95% CI Lower      0.628879
95% CI Upper      4.658836
Name: 2520, dtype: float64
In [150]:
# Set initial investment
initial_investment4 = 60000

# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes of our $60,000
# YOUR CODE HERE!
ci_lower_ten = round(even_tbl3[8]*60000,2)
ci_upper_ten = round(even_tbl3[9]*60000,2)

# Print results
print(f"There is a 95% chance that an initial investment of ${initial_investment4} in the portfolio"
      f" over the next 10 years will end within in the range of"
      f" ${ci_lower_ten} and ${ci_upper_ten}")
There is a 95% chance that an initial investment of $60000 in the portfolio over the next 10 years will end within in the range of $37732.71 and $279530.15
