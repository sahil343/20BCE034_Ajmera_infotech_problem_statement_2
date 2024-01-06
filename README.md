# 20BCE034_Ajmera_infotech_problem_statement_2

# What are the Steps Involved in Web Scraping?
 To Fetch the data using Web Scraping using Python, we need to go through the following steps:

1) Find the URL that you want to scrape.
2) Inspecting the Page.
3) Find the data you want to extract.
4) Write the code.
5) Run the code & extract the data.
6) Finally, Store the data in the required format.

# Project Demonstration

# Importing Libraries
Let’s start by installing the required packages.
import csv
from bs4 import BeautifulSoup
from selenium import webdriver
import pandas as pd

# Starting up the WebDriver
We begin by first creating an object of Webdriver by importing the webdriver class from docs and then we can use this object to perform any operation(s) we want. For example, here we have created a Chrome object.

driver = webdriver.Chrome()

Now we use the getfunction of webdriver.Chrome() to open the Flipkart website page in our Chrome driver object.

url = 'https://flipkart.com'

driver.get(url)

# Now there are a few ways we can conduct a product search :

The first way is to automate the browser by finding the input element and then insert a text and hit enter key on the keyboard. The image is like below and keeps doing this.

However, this kind of automation is unnecessary and it creates a potential for program failure. The Rule of thumb for automation is to only automate what you absolutely need to when Web Scraping.

Let’s search the input inside the search area and hit enter. You’ll notice that the search term has now been embedded into the URL site. Now we can use this pattern to create a function that will build the necessary URL for our driver to retrieve. This will be much more efficient in the long term and less prone to program failure. The image is like below.


def get_url(search_item):


    '''
    This function fetches the URL of the item that you want to search
    '''
    
    template = 'https://www.flipkart.com/search?q={}&as=on&as-show=on&otracker=AS_Query_HistoryAutoSuggest_1_4_na_na_na&otracker1=AS_Query_HistoryAutoSuggest_1_4_na_na_na&as-pos=1&as-type=HISTORY&suggestionId=mobile+phones&requestId=e625b409-ca2a-456a-b53c-0fdb7618b658&as-backfill=on'
    
    # We'are replacing every space with '+' to adhere with the pattern
    
    search_item = search_item.replace(" ","+")
    return template.format(search_item)

Now we have a function that will generate a URL based on the search term we provide.

url = get_url('iphones')

print(url)

then we can change the url of driver

url = 'https://flipkart.com'](https://www.flipkart.com/search?q={}&as=on&as-show=on&otracker=AS_Query_HistoryAutoSuggest_1_4_na_na_na&otracker1=AS_Query_HistoryAutoSuggest_1_4_na_na_na&as-pos=1&as-type=HISTORY&suggestionId=mobile+phones&requestId=e625b409-ca2a-456a-b53c-0fdb7618b658&as-backfill=on' 

search_item = search_item.replace(" ","+")

driver.get(url)
