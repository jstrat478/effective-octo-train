# Get quantmod
if (!require("quantmod")) {
    install.packages("quantmod")
    library(quantmod)
}

start <- as.Date("2016-01-01")
end <- as.Date("2016-10-01")

# Let's get Apple stock data; Apple's ticker symbol is AAPL. We use the
# quantmod function getSymbols, and pass a string as a first argument to
# identify the desired ticker symbol, pass 'yahoo' to src for Yahoo!
# Finance, and from and to specify date ranges

# The default behavior for getSymbols is to load data directly into the
# global environment, with the object being named after the loaded ticker
# symbol. This feature may become deprecated in the future, but we exploit
# it now.

getSymbols("AAPL", src = "yahoo", from = start, to = end)

##     As of 0.4-0, 'getSymbols' uses env=parent.frame() and
##  auto.assign=TRUE by default.
## 
##  This  behavior  will be  phased out in 0.5-0  when the call  will
##  default to use auto.assign=FALSE. getOption("getSymbols.env") and 
##  getOptions("getSymbols.auto.assign") are now checked for alternate defaults
## 
##  This message is shown once per session and may be disabled by setting 
##  options("getSymbols.warning4.0"=FALSE). See ?getSymbols for more details.

## [1] "AAPL"

# What is AAPL?
class(AAPL)

## [1] "xts" "zoo"

# Let's see the first few rows
head(AAPL)

##            AAPL.Open AAPL.High AAPL.Low AAPL.Close AAPL.Volume
## 2016-01-04    102.61    105.37   102.00     105.35    67649400
## 2016-01-05    105.75    105.85   102.41     102.71    55791000
## 2016-01-06    100.56    102.37    99.87     100.70    68457400
## 2016-01-07     98.68    100.13    96.43      96.45    81094400
## 2016-01-08     98.55     99.11    96.76      96.96    70798000
## 2016-01-11     98.97     99.06    97.34      98.53    49739400
##            AAPL.Adjusted
## 2016-01-04     102.61218
## 2016-01-05     100.04079
## 2016-01-06      98.08303
## 2016-01-07      93.94347
## 2016-01-08      94.44022
## 2016-01-11      95.96942
plot(AAPL[, "AAPL.Close"], main = "AAPL")
candleChart(AAPL, up.col = "black", dn.col = "red", theme = "white")
# Let's get data for Microsoft (MSFT) and Google (GOOG) (actually, Google is
# held by a holding company called Alphabet, Inc., which is the company
# traded on the exchange and uses the ticker symbol GOOG).
getSymbols(c("MSFT", "GOOG"), src = "yahoo", from = start, to = end)

## [1] "MSFT" "GOOG"

# Create an xts object (xts is loaded with quantmod) that contains closing
# prices for AAPL, MSFT, and GOOG
stocks <- as.xts(data.frame(AAPL = AAPL[, "AAPL.Close"], MSFT = MSFT[, "MSFT.Close"], 
    GOOG = GOOG[, "GOOG.Close"]))
head(stocks)

##            AAPL.Close MSFT.Close GOOG.Close
## 2016-01-04     105.35      54.80     741.84
## 2016-01-05     102.71      55.05     742.58
## 2016-01-06     100.70      54.05     743.62
## 2016-01-07      96.45      52.17     726.39
## 2016-01-08      96.96      52.33     714.47
## 2016-01-11      98.53      52.30     716.03

# Create a plot showing all series as lines; must use as.zoo to use the zoo
# method for plot, which allows for multiple series to be plotted on same
# plot
plot(as.zoo(stocks), screens = 1, lty = 1:3, xlab = "Date", ylab = "Price")
legend("right", c("AAPL", "MSFT", "GOOG"), lty = 1:3, cex = 0.5)
plot(as.zoo(stocks[, c("AAPL.Close", "MSFT.Close")]), screens = 1, lty = 1:2, 
    xlab = "Date", ylab = "Price")
par(new = TRUE)
plot(as.zoo(stocks[, "GOOG.Close"]), screens = 1, lty = 3, xaxt = "n", yaxt = "n", 
    xlab = "", ylab = "")
axis(4)
mtext("Price", side = 4, line = 3)
legend("topleft", c("AAPL (left)", "MSFT (left)", "GOOG"), lty = 1:3, cex = 0.5)
# Get me my beloved pipe operator!
if (!require("magrittr")) {
    install.packages("magrittr")
    library(magrittr)
}

## Loading required package: magrittr

stock_return % t %>% as.xts

head(stock_return)

##            AAPL.Close MSFT.Close GOOG.Close
## 2016-01-04  1.0000000  1.0000000  1.0000000
## 2016-01-05  0.9749407  1.0045620  1.0009975
## 2016-01-06  0.9558614  0.9863139  1.0023994
## 2016-01-07  0.9155197  0.9520073  0.9791734
## 2016-01-08  0.9203607  0.9549271  0.9631052
## 2016-01-11  0.9352634  0.9543796  0.9652081

plot(as.zoo(stock_return), screens = 1, lty = 1:3, xlab = "Date", ylab = "Return")
legend("topleft", c("AAPL", "MSFT", "GOOG"), lty = 1:3, cex = 0.5)
stock_change % log %>% diff
head(stock_change)

##              AAPL.Close    MSFT.Close   GOOG.Close
## 2016-01-04           NA            NA           NA
## 2016-01-05 -0.025378648  0.0045516693  0.000997009
## 2016-01-06 -0.019763704 -0.0183323194  0.001399513
## 2016-01-07 -0.043121062 -0.0354019469 -0.023443064
## 2016-01-08  0.005273804  0.0030622799 -0.016546113
## 2016-01-11  0.016062548 -0.0005735067  0.002181138

plot(as.zoo(stock_change), screens = 1, lty = 1:3, xlab = "Date", ylab = "Log Difference")
legend("topleft", c("AAPL", "MSFT", "GOOG"), lty = 1:3, cex = 0.5)
candleChart(AAPL, up.col = "black", dn.col = "red", theme = "white")
addSMA(n = 20)
start = as.Date("2010-01-01")
getSymbols(c("AAPL", "MSFT", "GOOG"), src = "yahoo", from = start, to = end)

## [1] "AAPL" "MSFT" "GOOG"

# The subset argument allows specifying the date range to view in the chart.
# This uses xts style subsetting. Here, I'm using the idiom
# 'YYYY-MM-DD/YYYY-MM-DD', where the date on the left-hand side of the / is
# the start date, and the date on the right-hand side is the end date. If
# either is left blank, either the earliest date or latest date in the
# series is used (as appropriate). This method can be used for any xts
# object, say, AAPL
candleChart(AAPL, up.col = "black", dn.col = "red", theme = "white", subset = "2016-01-04/")
addSMA(n = 20)
candleChart(AAPL, up.col = "black", dn.col = "red", theme = "white", subset = "2016-01-04/")
addSMA(n = c(20, 50, 200))
