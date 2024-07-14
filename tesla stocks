import yfinance as yf
import requests
from bs4 import BeautifulSoup
import pandas as pd
import plotly.graph_objects as go
from plotly.subplots import make_subplots


def make_graph(stock_data, revenue_data, stock):
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=("Historical Share Price", "Historical Revenue"), vertical_spacing = .3)
    stock_data_specific = stock_data[stock_data.Date <= '2021--06-14']
    revenue_data_specific = revenue_data[revenue_data.Date <= '2021-04-30']
    fig.add_trace(go.Scatter(x=pd.to_datetime(stock_data_specific.Date), y=stock_data_specific.Close.astype("float"), name="Share Price"), row=1, col=1)
    fig.add_trace(go.Scatter(x=pd.to_datetime(revenue_data_specific.Date), y=revenue_data_specific.Revenue.astype("float"), name="Revenue"), row=2, col=1)
    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
    fig.update_layout(showlegend=False,
    height=900,
    title=stock,
    xaxis_rangeslider_visible=True)
    fig.show()


data=yf.Ticker("TSLA")
tesla_data=data.history(period="max")
tesla_data.reset_index(inplace=True)


url= "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm"
html_data=requests.get(url).text

soup = BeautifulSoup(html_data, 'html5lib')

tesla_revenue = pd.DataFrame(columns=["Date","Revenue"])
tables = soup.find_all('table')
relevant_table = None

for table in tables:
    if "Tesla Quarterly Revenue" in str(table):
        relevant_table = table
        break

for row in relevant_table.find_all('tr')[1:]:
    columns = row.find_all('td')
    if len(columns) >= 2:
        date = columns[0].get_text(strip=True)
        revenue = columns[1].get_text(strip=True)
        revenue = revenue.replace('$', '').replace(',', '')
        tesla_revenue = tesla_revenue._append({"Date": date, "Revenue": revenue}, ignore_index=True)

tesla_revenue['Revenue'] = pd.to_numeric(tesla_revenue['Revenue'], errors='coerce')
tesla_revenue.dropna(subset=['Revenue'], inplace=True)

make_graph(tesla_data,tesla_revenue,"Tesla Stocks")
