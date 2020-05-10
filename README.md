# StockwithBarVolume
This is a basic stock line graph, with a trading volume bar graph below it. I have added an input box to type the ticker and date rage

import matplotlib.pyplot as plt
from pandas_datareader import data
import pandas as pd
import numpy as np
import yfinance as yahoo
from matplotlib import pyplot as plt
import tkinter as tk
from tkinter import filedialog, Text
import os
from matplotlib.offsetbox import AnchoredText


# Create Submit function

root = tk.Tk()


def submit1():
    global a1
    a1 = c_name1.get()
    return a1
    c_name1.delete(0, 100)
    root.destroy()


def submit3():
    global c1
    c1 = c_date1.get()
    c_date1.delete(0, 100)


def submit4():
    global d1
    d1 = c_date2.get()
    c_date2.delete(0, 100)
    root.destroy()


root.destroy()

#  Create input box
root = tk.Tk()
root.title('Stock Price Graph')
canvas = tk.Frame(root, height=700, width=700, bg='#263D42')


var1 = tk.StringVar()
var3 = tk.StringVar()
var4 = tk.StringVar()
c_name1 = tk.Entry(root, width=30, textvariable=var1)
c_name1.grid(row=0, column=1, padx=20)
c_name_label1 = tk.Label(root, text="Ticker")
c_name_label1.grid(row=0, column=0)


c_date1 = tk.Entry(root, width=30, textvariable=var3)
c_date1.grid(row=2, column=1, padx=20)
c_date_label1 = tk.Label(root, text="Start Date \'Year-Month-Day\'")
c_date_label1.grid(row=2, column=0)

c_date2 = tk.Entry(root, width=30, textvariable=var4)
c_date2.grid(row=3, column=1, padx=20)
c_date_label2 = tk.Label(root, text="End Date \'Year-Month-Day\'")
c_date_label2.grid(row=3, column=0)

submit_button = tk.Button(root, text='Graph Stock Prices',
                          command=lambda: [submit1(), submit3(),
                                           submit4()])
submit_button.grid(row=4, column=0, columnspan=2,
                   pady=10, padx=10, ipadx=100)

root.mainloop()

z3 = '\'{}\''.format(c1)
z4 = '\'{}\''.format(d1)

# Grabbing the data from yahoo
ticker1 = a1
start_date = z3
end_date = z4
# Dark Background formatting
plt.style.use(['dark_background'])

# 1st ticker
var1 = str(ticker1)[1:-1]
g1 = var1.strip('"\'')
lable1 = yahoo.Ticker(g1)
panel_data1 = data.DataReader(ticker1, 'yahoo', start_date, end_date)
panel_data1.head()
close1 = panel_data1['Close']
all_weekdays = pd.date_range(start=start_date, end=end_date, freq='B')
close1 = close1.reindex(all_weekdays)
close1 = close1.fillna(method='ffill')

listToStr = ' '.join([str(elem) for elem in ticker1])
company_name1 = lable1.info['longName']
bbf = yahoo.Ticker(ticker1)
ffb = bbf.info['longName']

# Define Variables
d = panel_data1['Volume']
c = panel_data1['Adj Close']
t = panel_data1['High']
s = panel_data1['Low']
vol = panel_data1['Volume']
vol3 = (vol/1000000)  # Trading volume graph adjustment
vol2 = vol3.values.tolist()
y = np.array(vol2)
bg = c.values.tolist()
big = np.array(bg)
x = np.array(panel_data1.index)

# Percent Change Box
pc = panel_data1['Adj Close'].pct_change()
bc = pc.cumsum()
g = (bc.iloc[-1])
j = (g*100)
pchange = round(j, 1)
plt.text(.83, .40, "Percent Change: {}%".format(pchange),
         bbox={'facecolor': 'k', 'pad': 5, },
         ha="left", va="bottom", color='limegreen' if pchange >= 0 else 'r',
         transform=plt.gca().transAxes)

# Plotting

plt.plot(x, big, label='Adjusted Close Price', color='lime', linewidth=1.50)
plt.plot(t, linestyle='--', label="Day High", color='deeppink', linewidth=0.75)
plt.plot(s, linestyle='--', label="Day Low", linewidth=0.75)
plt.bar(x, y, color='darkturquoise', label='Trading Volume (Millions)',
        alpha=0.8)


# Formatting
plt.legend(fancybox=True, framealpha=1, shadow=True, borderpad=1)
plt.grid(linestyle='--', linewidth=0.5, color='#2A3459')
plt.locator_params(axis='x', nbins=10)
linestyle_tuple = [('dotted',                (0, (1, 1))), ]
plt.fill_between(x, big, alpha=0.15, color='pink')
plt.ylim(auto=True)
plt.ylabel('Share Value ($)')
plt.title(ffb, fontname='bold')
plt.show()
