# Web-Scraping-in-R
# This repository contains an R script for scraping chef data from Cozymeal, including chef names, availability, reviews, and service descriptions. The scraped data is saved in a CSV file for further analysis or use.


library(rvest)
library(dplyr)

# Initialize an empty data frame to store results
chefs_data <- data.frame(
  chef_name = character(),
  next_date = character(),
  Number_Of_Reviews = character(),  
  synopsis = character(),
  stringsAsFactors = FALSE
)

# Loop through pages 1 to 8
for (page_result in seq(from = 1, to = 8, by = 1)) {
  # Construct the URL for the current page
  link <- paste0("https://www.cozymeal.com/nyc/personal-chefs?page=", page_result)
  
  # Print the URL to verify (optional)
  print(paste("Scraping:", link))
  
  # Scrape the webpage
  page <- read_html(link)
  
  # Extract chef names
  chef_name <- page %>% 
    html_nodes(".experiences-meal__chef-name") %>% 
    html_text(trim = TRUE)  # Use trim = TRUE to remove extra whitespace
  
  # Extract next available date
  next_date <- page %>% 
    html_nodes(".experiences-meal__next-date") %>% 
    html_text(trim = TRUE)
  
  # Extract number of reviews (using the new CSS selector)
  Number_Of_Reviews <- page %>% 
    html_nodes(".experiences-meal__reviews") %>%  # Updated selector
    html_text(trim = TRUE)
  
  # If no reviews are found, fill with NA
  if (length(Number_Of_Reviews) == 0) {
    Number_Of_Reviews <- rep(NA, length(chef_name))  # Fill with NA if no reviews are found
  }
  
  # Extract synopsis
  synopsis <- page %>% 
    html_nodes(".experiences-meal__description") %>% 
    html_text(trim = TRUE)
  
  # Append the data to the data frame
  chefs_data <- rbind(chefs_data, data.frame(
    chef_name = chef_name,
    next_date = next_date,
    Number_Of_Reviews = Number_Of_Reviews,  # Updated column name
    synopsis = synopsis,
    stringsAsFactors = FALSE
  ))
}

# View the final data frame
View(chefs_data)

# Export the data to a CSV file
write.csv(chefs_data, "chef.csv", row.names = FALSE)
 
 
