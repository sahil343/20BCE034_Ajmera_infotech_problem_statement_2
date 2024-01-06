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

# Extracting the collection

Now we are going to extract the contents of the webpage from which we want to extract the information.

To do that we need to create a BeautifulSoup object which will parse the HTML content from the page source.

Creating a soup object using driver.page_source to retrieve the HTML text and then we’ll use the default HTML parser to parse the HTML.

soup = BeautifulSoup(driver.page_source, 'html.parser')


Now that we have identified that the above card/record indicated by the box containing all the information that we need for a mobile phone. So let’s find out all the tags for these boxes/cards which contain information we want to extract.

We’ll be extracting — Model, stars, number of ratings, number of reviews, RAM, Storage capacity, Expandable option, display, camera information, battery, processor, warranty & Price information.


# Inspecting the tags

The data is usually embedded in tags. So we need to inspect the page to see, under which tag the data we want to scrape is embedded. To inspect the page, right-click on the element and select ‘Inspect’.

# Generalizing the Pattern

Now let create a function that will extract all the information at once from a single page.

def extract_phone_model_info(item):
    
    """
    This function extracts model, price, ram, storage, stars , number of ratings, number of reviews, 
    storage expandable option, display option, camera quality, battery , processor, warranty of a phone model at flipkart
    """
    # Extracting the model of the phone from the 1st card
    model = item.find('div',{'class':"_4rR01T"}).text
    # Extracting Stars from 1st card
    star = item.find('div',{'class':"_3LWZlK"}).text
    # Extracting Number of Ratings from 1st card
    num_ratings = item.find('span',{'class':"_2_R_DZ"}).text.replace('\xa0&\xa0'," ; ")[0:item.find('span',{'class':"_2_R_DZ"}).text.replace('\xa0&\xa0'," ; ").find(';')].strip()
    # Extracting Number of Reviews from 1st card
    reviews = item.find('span',{'class':"_2_R_DZ"}).text.replace('\xa0&\xa0'," ; ")[item.find('span',{'class':"_2_R_DZ"}).text.replace('\xa0&\xa0'," ; ").find(';')+1:].strip()
    # Extracting RAM from the 1st card
    ram = item.find('li',{'class':"rgWa7D"}).text[0:item.find('li',{'class':"rgWa7D"}).text.find('|')]
    # Extracting Storage/ROM from 1st card
    storage = item.find('li',{'class':"rgWa7D"}).text[item.find('li',{'class':"rgWa7D"}).text.find('|')+1:][0:10].strip()
    # Extracting whether there is an option of expanding the storage or not
    expandable = item.find('li',{'class':"rgWa7D"}).text[item.find('li',{'class':"rgWa7D"}).text.find('|')+1:][13:]
    # Extracting the display option from the 1st card
    display = item.find_all('li')[1].text.strip()
    # Extracting camera options from the 1st card
    camera = item.find_all('li')[2].text.strip()
    # Extracting the battery option from the 1st card
    battery = item.find_all('li')[3].text
    # Extracting the processir option from the 1st card
    processor = item.find_all('li')[4].text.strip()
    # Extracting Warranty from the 1st card
    warranty = item.find_all('li')[-1].text.strip()
    # Extracting price of the model from the 1st card
    price = item.find('div',{'class':'_30jeq3 _1_WHN1'}).text
    result = (model,star,num_ratings,reviews,ram,storage,expandable,display,camera,battery,processor,warranty,price)
    return result

Putting all the data from one page into one list.

#Now putting all the information from all the cards/phone models and putting them into a list
records_list = []
results = soup.find_all('a',{'class':"_1fQZEK"})
for item in results:
    records_list.append(extract_phone_model_info(item))


Viewing how does our data frame looks like for the 1st page by creating a DataFrame using the list we above created

pd.DataFrame(records_list,columns=['model',"star","num_ratings"   ,"reviews",'ram',"storage","expandable","display","camera","battery","processor","warranty","price"])

# Putting all the pieces together
Let’s consolidate what we have done so far by combining everything. At the end of this cell below, we write a main function that will take the search query and give us a DataFrame as a result after extracting from 457 pages giving us a total of roughly 11,000 mobile phones data.

# Importing necessary Libraries

import csv

from bs4 import BeautifulSoup

from selenium import webdriver

import pandas as pd

def get_url(search_item):
    
    '''
    This function fetches the URL of the item that you want to search
    '''
    template = 'https://www.flipkart.com/search?q={}&as=on&as-show=on&otracker=AS_Query_HistoryAutoSuggest_1_4_na_na_na&otracker1=AS_Query_HistoryAutoSuggest_1_4_na_na_na&as-pos=1&as-type=HISTORY&suggestionId=mobile+phones&requestId=e625b409-ca2a-456a-b53c-0fdb7618b658&as-backfill=on'
    search_item = search_item.replace(" ","+")
    # Add term query to URL
    url = template.format(search_item)
    # Add term query placeholder
    url += '&page{}'
    return url

def extract_phone_model_info(item):
    
    """
    This function extracts model, price, ram, storage, stars , number of ratings, number of reviews, 
    storage expandable option, display option, camera quality, battery , processor, warranty of a phone model at flipkart
    """
    # Extracting the model of the phone from the 1st card
    model = item.find('div',{'class':"_4rR01T"}).text
    # Extracting Stars from 1st card
    star = item.find('div',{'class':"_3LWZlK"}).text
    # Extracting Number of Ratings from 1st card
    num_ratings = item.find('span',{'class':"_2_R_DZ"}).text.replace('\xa0&\xa0'," ; ")[0:item.find('span',{'class':"_2_R_DZ"}).text.replace('\xa0&\xa0'," ; ").find(';')].strip()
    # Extracting Number of Reviews from 1st card
    reviews = item.find('span',{'class':"_2_R_DZ"}).text.replace('\xa0&\xa0'," ; ")[item.find('span',{'class':"_2_R_DZ"}).text.replace('\xa0&\xa0'," ; ").find(';')+1:].strip()
    # Extracting RAM from the 1st card
    ram = item.find('li',{'class':"rgWa7D"}).text[0:item.find('li',{'class':"rgWa7D"}).text.find('|')]
    # Extracting Storage/ROM from 1st card
    storage = item.find('li',{'class':"rgWa7D"}).text[item.find('li',{'class':"rgWa7D"}).text.find('|')+1:][0:10].strip()
    # Extracting whether there is an option of expanding the storage or not
    expandable = item.find('li',{'class':"rgWa7D"}).text[item.find('li',{'class':"rgWa7D"}).text.find('|')+1:][13:]
    # Extracting the display option from the 1st card
    display = item.find_all('li')[1].text.strip()
    # Extracting camera options from the 1st card
    camera = item.find_all('li')[2].text.strip()
    # Extracting the battery option from the 1st card
    battery = item.find_all('li')[3].text
    # Extracting the processir option from the 1st card
    processor = item.find_all('li')[4].text.strip()
    # Extracting Warranty from the 1st card
    warranty = item.find_all('li')[-1].text.strip()
    # Extracting price of the model from the 1st card
    price = item.find('div',{'class':'_30jeq3 _1_WHN1'}).text
    result = (model,star,num_ratings,reviews,ram,storage,expandable,display,camera,battery,processor,warranty,price)
    return result


def main(search_item):
    
    '''
    This function will create a dataframe for all the details that we are fetching from all the multiple pages
    '''
    
    driver = webdriver.Chrome() 
    
    records = []
    url = get_url(search_item)
    for page in range(1,464):
        driver.get(url.format(page))
        soup = BeautifulSoup(driver.page_source,'html.parser')
        results = soup.find_all('a',{'class':"_1fQZEK"})
        for item in results:
            records.append(extract_phone_model_info(item))
    driver.close()
    # Saving the data into a csv file
    with open('Flipkart_results.csv','w',newline='',encoding='utf-8') as f:
        writer = csv.writer(f)
        writer.writerow(['Model','Stars','Num_of_Ratings','Reviews','Ram','Storage','Expandable','Display','Camera','Battery','Processor','Warranty','Price'])
        writer.writerows(records)
