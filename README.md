# RepoRadar
## Scraping Top Repositories for Topics on GitHub
TODO (Intro):

- Introduction about web scraping
- Introduction about GitHub and the problem statement
- Mention the tools you're using (Python, requests, Beautiful Soup, Pandas)

## Scrape the list of topics from Github

Explain how you'll do it.

- use requests to downlaod the page
- user BS4 to parse and extract information
- convert to a Pandas dataframe

Let's write a function to download the page.
import requests
from bs4 import BeautifulSoup

def get_topics_page():
    # TODO - add comments
    topics_url = 'https://github.com/topics'
    response = requests.get(topics_url)
    if response.status_code != 200:
        raise Exception('Failed to load page {}'.format(topic_url))
    doc = BeautifulSoup(response.text, 'html.parser')
    return doc
