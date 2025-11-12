import yfinance as yf
import pandas as pd
import numpy as np
import math

yr_day=248
TW_PickStockNo=100
start_date='2025-10-13'
end_date='2025-11-13'

TW_stock_ticker=pd.read_html('https://www.taifex.com.tw/cht/9/futuresQADetail' , encoding="utf-8")[0]['證券名稱'][:TW_PickStockNo]

TW_stock_list=[]

for i in TW_stock_ticker:
    TW_stock_list.append(str(i)+'.TW')

TW_100= yf.download(TW_stock_list, start=start_date, end=end_date,progress=False)['Close'].dropna(axis=1,how='all')

R_TW_100=np.log(TW_100/TW_100.shift(1)).dropna()
R_TW_100_annual=R_TW_100.mean()*yr_day
std_TW_100_annual=R_TW_100.std()*math.sqrt(yr_day)
sr_TW_100=R_TW_100_annual/std_TW_100_annual

TW_100_best_sharpe=sr_TW_100.idxmax()
TW_100_best_return=R_TW_100_annual.idxmax()

TW_100_vs=pd.DataFrame({"年化報酬率":R_TW_100_annual,"年化標準差":std_TW_100_annual,"Sharpe ratio":sr_TW_100})
TW_100_vs.to_excel('FA102a_股票數據分析.xlsx')
print(f"\年化報酬率最高：{TW_100_best_return}")
print(f"\sharpe ratio最好:{TW_100_best_sharpe}")
