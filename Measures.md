# Measures

## Sales and Orders KPIs

- Total Revenue = CALCULATE(SUM(orders[order_value]), orders[order_status] IN {"Delivered", "Delayed"})
- Total Orders = COUNT(orders[order_id])
- Completed Orders = CALCULATE([Total Orders], orders[order_status] IN {"Delivered","Delayed"})
- Avg Order Value = DIVIDE([Total Revenue], [Completed Orders])

## Customer KPIs

- Total Customers = DISTINCTCOUNT(orders[customer_id])
- Repeat Customers = 
  COUNTROWS(
      FILTER(
          VALUES(orders[customer_id]),
          CALCULATE(COUNT(orders[order_id]), orders[order_status]<>"Cancelled") > 2
      )
  )
- Repeat Customer % = DIVIDE([Repeat Customers], [Total Customers])
- Orders Per Customer = DIVIDE([Completed Orders], [Total Customers])
- One Time Customers = [Total Customers] - [Repeat Customers]
- New Customers = COUNTROWS(customers)
- Average Customer Spend = DIVIDE([Total Revenue], [Total Customers])

## Restaurant KPIs

- Total Restaurants = DISTINCTCOUNT(restaurants[restaurant_id])
- Revenue per Restaurant = DIVIDE([Total Revenue], [Total Restaurants])
- Orders per Restaurant = DIVIDE([Completed Orders], [Total Restaurants])
- Avg Votes = AVERAGE(restaurants[total_votes])
- Avg Rating = AVERAGE(restaurants[rating])


# Calculated Column
- customer_segment = 
  var completed_orders = CALCULATE(
                          COUNTROWS(Orders),
                          FILTER(
                              orders,
                              orders[customer_id] = customers[customer_id]
                                  && orders[order_status] <> "Cancelled"
                              )
                          )
  RETURN 
  SWITCH(
      TRUE(),
      completed_orders <= 2, "One-Time",
      completed_orders <= 5, "Regular",
      "Loyal"
  )
