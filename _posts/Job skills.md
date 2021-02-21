## Mining Job Positions for Skills

### Which set of skills in the data science industry are the most important? 

#### Import Libraries


```python
import pandas as pd
import numpy as np
import requests
import urllib
from bs4 import BeautifulSoup 
import matplotlib.pyplot as plt
import seaborn as sns
```

Let's explore the current job market in various industries. 
To do this, we will use LinkedIn, a professional networking and job posting website. 


```python
def job_search(position):
    #Create LinkedIn Search from a predefined url formula. Here we have not set a location to search within
    # this could easily be added as another argument, but for now we want to return general results
    url = "https://www.linkedin.com/jobs/search/?keywords=" + urllib.parse.quote(position, safe='')
    result = requests.get(url)
    page_content = result.content
    soup = BeautifulSoup(page_content, 'lxml')
    
    #The "soup" contains the contents of the LinkedIn job search, using the method "find_all", we pass in "a" which
    # will return a list of links
    lists = soup.find_all("a")
    
    # We want to disgard everything but the links themselves, do do this we call the attribute method on 
    # each link and pass through "href"
    links = []
    for i in lists: 
        links = links + [i.attrs["href"]]
    
    #Because there are many links on the page, we need to narrow this list
    #We only want to capture the links that lead to job descriptions
    
    #LinkedIn job descriptions have the format "https://www.linkedin.com/jobs/view"
    l = []
    for li in links:
        if "https://www.linkedin.com/jobs/view" in li:
            l = l + [li] 
    
    #Lastly, we do not need all of the text on job descriptions. Instead, it's common for employers to list
    # the requirements for the position
    corpus = []
    for j in l:
        corpus = corpus + BeautifulSoup(requests.get(j).content, 'lxml').find_all("li")
        
    skills = []
    for items in corpus:
        if "_" not in items and "-" not in items and "https" not in items and "</h" not in items: 
            skills = skills + [items.text.lower()] 
    
    words = [word for p in list(dict.fromkeys(skills)) for word in p.split()]
    
    
    position_words = list(p.lower() for p in position.split())
    
    final_list = []
    for i in words:
        if len(i) < 12 and i not in ([input,'ability', 'skills', 'months', 'job','experience', 'new','years', 'york,','week', 'francisco,','ca2','ca3','ca1', 'diego,', 'days','weeks','jobs', "-","agoapply", "/",'ago', "angeles,", "," "ago", "&","Now","i", "me", "my", "myself", "we", "our", "ours", "ourselves", "you", "your", "yours", "yourself", "yourselves", "he", "him", "his", "himself", "she", "her", "hers", "herself", "it", "its", "itself", "they", "them", "their", "theirs", "themselves", "what", "which", "who", "whom", "this", "that", "these", "those", "am", "is", "are", "was", "were", "be", "been", "being", "have", "has", "had", "having", "do", "does", "did", "doing", "a", "an", "the", "and", "but", "if", "or", "because", "as", "until", "while", "of", "at", "by", "for", "with", "about", "against", "between", "into", "through", "during", "before", "after", "above", "below", "to", "from", "up", "down", "in", "out", "on", "off", "over", "under", "again", "further", "then", "once", "here", "there", "when", "where", "why", "how", "all", "any", "both", "each", "few", "more", "most", "other", "some", "such", "no", "nor", "not", "only", "own", "same", "so", "than", "too", "very", "s", "t", "can", "will", "just", "don", "should", "now"] + position_words):
            final_list = final_list + [i]
            
            
    # Creating an empty dictionary  
    freq = {} 
    
    for item in final_list: 
        if (item in freq): 
            freq[item] += 1
        else: 
            freq[item] = 1
            
    data = pd.DataFrame.from_dict(CountFrequency(final_list), orient='index', columns = ['Freq'])
    data = data.sort_values(by=['Freq'], ascending=False)
    data.index.name = 'Words'
    data.reset_index(inplace=True)
            
    return data
        
```


```python
input = "Machine Learning Engineer"
input2 = "Business Intelligence Analyst"
input3 = "Product Data Scientist"
MLE = job_search(input)
BIE = job_search(input2)
PDS = job_search(input3)

```


```python
MLE.head(10).plot.bar(x='Words', y='Freq', rot=0)
plt.xticks(rotation = 45) 
plt.show
```




    <function matplotlib.pyplot.show(*args, **kw)>




![png](output_7_1.png)



```python
BIE.head(10).plot.bar(x='Words', y='Freq', rot=0, color = 'red')
plt.xticks(rotation = 45) 
plt.show
```




    <function matplotlib.pyplot.show(*args, **kw)>




![png](output_8_1.png)



```python
PDS.head(10).plot.bar(x='Words', y='Freq', rot=0, color = 'green')
plt.xticks(rotation = 45) 
plt.show
```




    <function matplotlib.pyplot.show(*args, **kw)>




![png](output_9_1.png)



```python

```
