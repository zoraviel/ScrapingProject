# Scrape Multiple Pages a Table, and write them into a csv file.
 First, you have to import a few things so that your code can run correctly. Here's what's needed:
 ```
 from bs4 import BeautifulSoup
 import requests
 import time
 import csv
 from selenium import webdriver
```
*Note: you have to have BeautifulSoup, selenium and a driver installed before this code can work*
## Getting the table data
Get the specific page that you are looking for by using its url, then requests and soup:
 ```
 url = "https://www.cbpp.org/research/family-income-support/tanf-benefits-still-too-low-to-help-families-especially-black"
page = requests.get(url)
soup = BeautifulSoup(page.text,'html.parser')
```
Now, this code gets rid of unwanted stuff, like footnotes in this case:
```
for match in soup.findAll('sup'):
    match.replace_with('')
```
Now, it's time to open a write a new csv file and create a variable:
```
csvfile = open('tanf.csv', 'w', newline='', encoding='utf-8')

z = csv.writer(csvfile)
```
Use writerow() to make columns with your csv file:
```
z.writerow( ['Tanf State Name','Tanf Monthly Benefit', 'Low Cost City & State', ' Median Home Value']
```
Get the specific table you want by finding all the tables on the page using soup. Then, make a variable containing the specific table you want using index. Then, create another variable that finds all the rows in the table:
```
table = soup.find_all('table')
benefits = table[2]
rows = benefits.find_all('tr')
```
Make a for loop that creates a list that appends the data from all the rows into an empty list. Use try/excpet to get specific data from each cell. Then create another for loop that appends to that writes to the csv file. Printing the list is optional:
```
for data in rows:
    try:
        my_list=[]
        cells = data.find_all('td')
        cellz = cells[0].text,cells[6].text
        my_list.append(cellz)
        for item in my_list:
        # write one row to csv — item MUST BE a LIST
            z.writerow(item)
            print(my_list)
    except:
        pass
  ```
  Finally, close the csv file:
```
csvfile.close
```
## Scraping Multiple Pages using Selenium
Make a variable names driver, and using webdriver.Chrome create a local path to your driver on your computer. Then, get the url of the first page you want to scrape. Use page and soup to get the source, and finally close the driver:

```
driver = webdriver.Chrome(r'C:\Users\zorav\OneDrive\Documents\python\scraping\chromedriver_win32\chromedriver.exe')

driver.get('https://www.niche.com/places-to-live/search/cities-with-the-lowest-cost-of-living/')
page = driver.page_source

soup = BeautifulSoup(page, "html.parser")
driver.quit()
```
Use soup to find all of the links you wish to scrape and put it into a variable. Print the length of that variable:
```
city_list = soup.find_all('li', class_='search-results__list__item')
len(city_list)

```

Create an empty list and use a for loop to append the data of your previous variable to that list. Print the list to make sure it was appended:
```
link_list = []

for city in city_list:
    yay = (city.find('a').attrs['href'])
    link_list.append(yay)

print(link_list)
```

Once you have that data, use get the second page using the same method that got the first one:
```
driver = webdriver.Chrome(r'C:\Users\zorav\OneDrive\Documents\python\scraping\chromedriver_win32\chromedriver.exe')

driver.get('https://www.niche.com/places-to-live/search/cities-with-the-lowest-cost-of-living/?page=2')
page = driver.page_source

soup = BeautifulSoup(page, "html.parser")
driver.quit()
```

Create a second variable, and repeat the same steps to get the links information:
```
second_list = soup.find_all('li', class_='search-results__list__item')
len(second_list)
```

Create another for loop with the variable that you just created, and append that data into the list with the links from the first page:
```
for second in second_list:
    yep = (second.find('a').attrs['href'])
    link_list.append(yep)
print(link_list)
```

Create a for loop that gets rid of unwanted things:
```
for man in soup.findAll('div', class_='scalar__national__value'):
    man.replace_with('')
```
Now, use a function to find specific elements on each page, and write them into the csv file. Start by opening and closing the driver again:
 ```
 def get_info(link_list):
    driver = webdriver.Chrome(r'C:\Users\zorav\OneDrive\Documents\python\scraping\chromedriver_win32\chromedriver.exe')

    driver.get(link_list)
    page = driver.page_source

    soup = BeautifulSoup(page, "html.parser")
```

Open the same csv file you used for the table, but append it instead of writing a new file:
```
csvfile = open('tanf.csv', 'a', newline='', encoding='utf-8')

    z = csv.writer(csvfile)
```

Use try/except to get the specific elements. You can use a for loop to get read of unwanted elements. Make an empty list to write the elements into. After you find all the elements, append them into the empty list. Then, use a for loop to write that into the csv file:
```
try:
        for man in soup.findAll('div', class_='scalar__national__value'):
            man.replace_with('')
        third_list = []
        stuff = soup.find('h1')
        city = soup.find_all('li', class_='postcard__attr postcard-fact')
        citystate =(stuff.text + ',' + ' ' + city[0].text)

        cash = soup.find_all('div', class_='scalar__value')
        medianval = (cash[1].text)
        together = citystate,medianval
        third_list.append(together)
        for now in third_list:
            z.writerow(now)

        driver.quit()
    except:
        pass
```
Use a for loop to get the urls from the previous list, then, close the csv file:
```
for link in link_list:
    get_info(link)


csvfile.close()
```
