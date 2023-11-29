# First_project
import requests
from bs4 import BeautifulSoup
import pandas as pd
import matplotlib.pyplot as plt

# Functions
def get_hn_page(page):
'''Downloads page content'''
res = requests.get(f'https://news.ycombinator.com/news?p={page}')
soup = BeautifulSoup(res.text, 'html.parser')
return soup

def scrape_hn_titles(soup):
'''Scrapes titles from a page'''
titles = []
for title in soup.select('.storylink'):
titles.append(title.text)
return titles

def scrape_hn_votes(soup):
'''Scrapes vote counts from a page'''
votes = []
for score in soup.select('.score'):
votes.append(int(score.text.split()[0]))
return votes

# Main script
pages = 2 # number of pages to scrape
hn_titles, hn_votes = [], []
for page in range(1, pages+1):
soup = get_hn_page(page)
titles = scrape_hn_titles(soup)
votes = scrape_hn_votes(soup)
hn_titles.extend(titles)
hn_votes.extend(votes)

# Create dataframe
hn_df = pd.DataFrame({'title': hn_titles, 'votes': hn_votes})

# Analyze data
print(hn_df.describe())
hn_df.plot.scatter(x='votes', y='title', figsize=(10, 8))

# Export
hn_df.to_csv('hn_data.csv', index=False)
