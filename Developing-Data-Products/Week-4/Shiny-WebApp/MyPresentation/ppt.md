Interest Calculator (Developing Data Products - Week 4 Project)
========================================================
author: Ashwin Venkatesh Prabhu
date: 24 June, 2017
autosize: true

Overview
========================================================

This is an RStudio shiny application developed as a part of final project in the Developing Data Products course in Coursera Data Science Specialization track. The application is an interest calculator

The project includes:
- A number input to input the principal amount
- Slider inputs to select the interest rate and the time period
- A dropdown box to select the rate of caluclation (Yearly or Quarterly)

This app is hosted at: https://ashwinvprabhu.shinyapps.io/myfirstshinyapp/

Application widgets used
========================================================

This application uses four widgets listed below:

- numericInput: A field to enter numbers, in this case - the principal amount (in $)
- sliderInput: A slider bar
        1. First slider is to choose the yearly interest rate (in %)
        2. Second slider is to choose the number of time periods
- selectInput: A box with choices to select from, in this case - the type of time period: years, quarters
- actionButton: An Action Button, in this case to provide non-reactive reactivity to refresh and calculate

Operations inside the Shiny App and the Output
========================================================

The reactivity of the shiny application widgets is controlled by using an Action Button. Based on user inputs, and using the simple interest calculation equation, the application displays:
- Inputs entered by the user
- Calculated values
- Simple interest calculated
- Total amount calculated as principal + interest

To make it easy for a novice user, a Documentation tab in the Main Panel of the application lists the details of the calculation of simple interest.

Application - ui.R
========================================================
<font size="3">

```r
library(shiny)

shinyUI(fluidPage(
        titlePanel("Simple Interest Calculator"),
        sidebarLayout(
                sidebarPanel(
                        helpText("This app calculates simple interest based on your inputs."),            
                        numericInput("num_principal", label = h6("Enter the principal amount (in $)"), value = 100),
                        sliderInput("slider_intrate", label = h6("Choose the yearly interest rate (in %)"), min = 0, max = 30, value = 2),
                        sliderInput("slider_num", label = h6("Choose the number of time periods"), min = 0, max = 20, value = 5),
                        selectInput("select_time", label = h6(""), choices = list("Years" = 1, "Quarters" = 2), selected = 1),
                        actionButton("action_Calc", label = "Refresh & Calculate")        
                ),
                
                mainPanel(
                        tabsetPanel(
                                tabPanel("Output",
                                         p(h5("Your entered values:")), textOutput("text_principal"),
                                         textOutput("text_intrate"), textOutput("text_num"),
                                         textOutput("text_time"), p(h5("Calculated values:")),
                                         textOutput("text_int"), textOutput("text_amt")
                                ),
                                tabPanel("Documentation",
                                         p(h4("Simple Interest Calculator:")),
                                         helpText("This application calculates simple interest and total amount, i.e. principal + interest."),
                                         HTML("<u><b>Method for calculation: </b></u>
                                              <br> <br>
                                              <b> Amt = Prpl + Int = Prpl(1 + rate * time) ; R = rate * 100 </b>
                                              <br>
                                              where: <br>
                                              Amt = Total amount (Principal + Interest) <br>
                                              Prpl = Principal amount <br>
                                              Int = Interest amount <br>
                                              rate = Rate of interest per year, in decimal; r=R/100 <br>
                                              time = Time period invested in years/quarters")                
                                )
                        )
                )
        )
))
```
</font>

Application - server.R
========================================================
<font size="3">

```r
library(shiny)
shinyServer(function(input, output) {
        values <- reactiveValues()
        # Calculate the interest and amount    
        observe({
                input$action_Calc
                values$int <- isolate({
                        input$num_principal * input$slider_intrate *
                                recode(input$select_time, "1 = '1'; 2 = '0.25'")/100 * input$slider_num  
                })
                values$amt <- isolate(input$num_principal) + values$int
        })
        
        # Display values entered
        output$text_principal <- renderText({
                input$action_Calc
                paste("Principal Amount: [$]", isolate(input$num_principal))
        })
        
        output$text_intrate <- renderText({
                input$action_Calc
                paste("Interest Rate: ", isolate(input$slider_intrate), 
                      " % per year")
        })
        
        output$text_num <- renderText({
                input$action_Calc
                paste("Time Period ", isolate(input$slider_num),
                      recode(isolate(input$select_time),
                             "1 = 'Years'; 2 = 'Quarters'")
                )
        })

        output$text_int <- renderText({
                if(input$action_Calc == 0) ""
                else
                        paste("Simple Interest [$]:", values$int)
        })
        
        output$text_amt <- renderText({
                if(input$action_Calc == 0) ""
                else 
                        paste("Total Amount, i.e. Principal + Interest [$]:", values$amt)
        })
        
})
```
</font>
