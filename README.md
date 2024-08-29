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

## Get the top repositories from a topic page

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

```
def parse_star_count(stars_str):
    stars_str = stars_str.strip()
    if stars_str[-1] == 'k':
        return int(float(stars_str[:-1])*1000)
    return int(stars_str)
```
```
def get_repo_info(h_tag,star_tag):
    a_tag = h_tag.find_all('a')
    username = a_tag[0].text.strip()
    repo_name = a_tag[1].text.strip()
    repo_url = base_url + a_tag[1]["href"]
    star = parse_star_count(star_tag.text)
    return username, repo_name, repo_url, star
```
TODO - show a example
```
def get_topic_repos(topic_doc):
    
    # repo_tags containing repo_name, repo_url and username
    repo_tags = topic_doc.find_all('h3', {'class' : 'f3 color-fg-muted text-normal lh-condensed'})
    star_tag = topic_doc.find_all('span',{'id' : 'repo-stars-counter-star'})

    topic_repos_dict = {
        'username' : [],
        'repo_name' : [],
        'stars' : [],
        'repo_url' : [],
    }

    for i in range(len(repo_tags)):
        repo_info = get_repo_info(repo_tags[i],star_tag[i])
        topic_repos_dict['username'].append(repo_info[0])
        topic_repos_dict['repo_name'].append(repo_info[1])
        topic_repos_dict['stars'].append(repo_info[3])
        topic_repos_dict['repo_url'].append(repo_info[2])

    return pd.DataFrame(topic_repos_dict)
```

TODO - show an example
```
def scrape_topic(topic_url, topic_name):
    fname = topic_name + '.csv'
    if os.path.exists(fname):
        print("The file {} already exists. Skipping..".format(fname))
        return
        
    topic_df = get_topic_repos(get_topic_page(topic_url))
    topic_df.to_csv(fname, index=None)
```
## Putting it all together

- We have a funciton to get the list of topics
- We have a function to create a CSV file for scraped repos from a topics page
- Let's create a function to put them together

```
def scrape_topic_repos():
    print("Scraping list of topics")
    topics_df = scrape_topics()
    for index, row in topics_df.iterrows():
        print('Scraping top reops for "{}"'.format(row['title']))
        scrape_topic(row['url'],row['title'])
```
Let's run it to scrape the top repos for the all the topics on the first page of https://github.com/topics
```
scrape_topic_repos()
```


