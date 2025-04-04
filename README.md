# dwdm-implementation


install.packages(c("quantmod", "RMySQL", "tidyverse", "forecast", "shiny", "prophet"))


library(quantmod)
getSymbols("AAPL", src = "yahoo", from = "2024-01-01")

# View data
head(AAPL)


library(RMySQL)

# Replace with your actual password
con <- dbConnect(MySQL(), user='root', password='mypassword', dbname='mysql', host='localhost', port = 3306)

# Check connection
dbListTables(con)

# Create a new database for your project
dbSendQuery(con, "CREATE DATABASE stock_data")


# Reconnect using the new database
con <- dbConnect(MySQL(), user='root', password='mypassword', dbname='stock_data', host='localhost', port = 3306)


library(prophet)
df <- data.frame(ds = index(AAPL), y = as.numeric(Cl(AAPL)))
model <- prophet(df)
future <- make_future_dataframe(model, periods = 30)
forecast <- predict(model, future)
plot(model, forecast)


library(shiny)
ui <- fluidPage(
  titlePanel("Stock Price Prediction"),
  plotOutput("plot")
)
server <- function(input, output) {
  output$plot <- renderPlot({
    plot(model, forecast)
  })
}
shinyApp(ui = ui, server = server)
