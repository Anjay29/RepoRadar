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
```
import requests
from bs4 import BeautifulSoup

url = "https://github.com/topics"
res = requests.get(url)
doc = BeautifulSoup(page_content, 'html.parser')
```

Add some explanation

Let's create some helper functions to parse information from the page.

To get topic titles, we can pick `p` tags with the `class` ...
![image](https://github.com/user-attachments/assets/088e4ac1-d12b-45c5-8eac-69dedc9f553c)
![image](https://github.com/user-attachments/assets/1cf1467f-e747-423c-bf7a-502a4e38e2b3)

```
def get_topic_title(doc):
    selection_class = "f3 lh-condensed mb-0 mt-1 Link--primary"
    topic_title_tags = doc.find_all('p', {'class' : selection_class})

    topic_titles = []
    for tag in topic_title_tags:
        topic_titles.append(tag.text)
    return topic_titles
```
`get_topic_titles` can be used to get the list of titles
Similarly we have defined functions for descriptions and URLs.

```
def get_topic_des(doc):
    topic_desc_tag = doc.find_all('p', {'class' : 'f5 color-fg-muted mb-0 mt-1'})
    topic_descriptions = []
    for tag in topic_desc_tag:
        topic_descriptions.append(tag.text.strip())
    return topic_descriptions

def get_topic_url(doc):
    topic_link_tags = doc.find_all('a', {'class' : 'no-underline flex-1 d-flex flex-column'})
    topic_urls = []
    base_url = "https://github.com"
    for tag in topic_link_tags:
        topic_urls.append(base_url + tag["href"])
    return topic_urls
```
Let's put this all together into a single function

```
def scrape_topics():
    topic_url = "https://github.com/topics"
    res = requests.get(topic_url)
    if(res.status_code != 200):
        raise Exception('Failed to load page {}'.format(topic_url))

    doc = BeautifulSoup(res.text, 'html.parser')
    topic_dict = {
        'title' : get_topic_title(doc),
        'description' : get_topic_des(doc),
        'url' : get_topic_url(doc)
    }
    return pd.DataFrame(topic_dict)
```

## Get the top 25 repositories from a topic page

TODO - explanation and step
```
def get_topic_page(topic_url):
     # Download the page
    res = requests.get(topic_url)

    # checking the status code
    if res.status_code != 200:
        raise Exception('Failed to load page {}'.format(topic_url))

    # Parse using beautifulsoup
    topic_doc = BeautifulSoup(res.text, 'html.parser')
    return topic_doc
```

```
doc = get_topic_page('https://github.com/topics/3d')
```
TODO - talk about the h3 tags


