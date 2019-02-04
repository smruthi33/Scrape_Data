---
#Front matter (metadata).
abstract:

authors:                # REQUIRED - Note: can be one or more
  - name: Smruthi Raj Mohan
    email: smrraj32@in.ibm.com

completed_date:         # REQUIRED - Note: date format is YYYY-MM-DD

components:
# For a full list of options see https://github.ibm.com/IBMCode/Definitions/blob/master/components.yml
# Use the "slug" value found at the link above to include it in this content.
# Example (remove the # to uncomment):
 # - "aix"

draft: true|false       # REQUIRED

excerpt:                # REQUIRED

keywords:               # REQUIRED - comma separated list

last_updated:           # REQUIRED - Note: date format is YYYY-MM-DD

primary_tag:          # REQUIRED - Note: Choose only only one primary tag. Multiple primary tags will result in automation failure. Additional non-primary tags can be added below.

pta:                    # REQUIRED - Note: can be only one
# For a full list of options see https://github.ibm.com/IBMCode/Definitions/blob/master/primary-technology-area.yml
# Use the "slug" value found at the link above to include it in this content.
# Example (remove the # to uncomment):
 # - "cloud, container, and infrastructure"

pwg:                    # REQUIRED - Note: can be one or many
# For a full list of options see https://github.ibm.com/IBMCode/Definitions/blob/master/portfolio-working-group.yml
# Use the "slug" value found at the link above to include it in this content.
# Example (remove the # to uncomment):
# - "containers"

related_content:        # OPTIONAL - Note: zero or more related content
  - type: announcements|articles|blogs|patterns|series|tutorials|videos
    slug:

related_links:           # OPTIONAL - Note: zero or more related links
  - title:
    url:
    description:

runtimes:               # OPTIONAL - Note: Select runtimes from the complete set of runtimes below. Do not create new runtimes. Only use runtimes specifically in use by your content.
# For a full list of options see https://github.ibm.com/IBMCode/Definitions/blob/master/runtimes.yml
# Use the "slug" value found at the link above to include it in this content.
# Example (remove the # to uncomment):
 # - "asp.net 5"

series:                 # OPTIONAL
 - type:
   slug:

services:               # OPTIONAL - Note: please select services from the complete set of services below. Do not create new services. Only use services specifically in use by your content.
# For a full list of options see https://github.ibm.com/IBMCode/Definitions/blob/master/services.yml
# Use the "slug" value found at the link above to include it in this content.
# Example (remove the # to uncomment):
# - "blockchain"

subtitle:               # REQUIRED

tags:
# Please select tags from the complete set of tags below. Do not create new tags. Only use tags specifically targeted for your content. If your content could match all tags (for example cloud, hybrid, and on-prem) then do not tag it with those tags. Less is more.
# For a full list of options see https://github.ibm.com/IBMCode/Definitions/blob/master/tags.yml
# Use the "slug" value found at the link above to include it in this content.
# Example (remove the # to uncomment):
 # - "blockchain"

title:                  # REQUIRED

translators:             # OPTIONAL - Note: can be one or more
  - name:
    email:

type: tutorial|howto    # REQUIRED

---

Web scraping is the use of a program or algorithm to extract and process large amounts of data from the web. This allows you to find data from the web, and there is no direct way to download it, web scraping using Python is a skill you can use to extract the data into a useful form that can be imported. In this tutorial, you will learn about Data extraction from the web using Watson Studio and further sent to Watson Natural Language Understanding for deriving important entities and keywords.

## Learning objectives

1. How to scrape from a Google search, with any search term of your choice.
2. Extracting the links and the summaries in the search results.
3. Extracted text from the results and process using Watson Natural Language Understanding for deriving important entities and keywords.

## Prerequisites

* [IBM Cloud Account](https://cloud.ibm.com/login)
* [Watson Studio Service Instance](https://cloud.ibm.com/catalog/services/watson-studio)
* [Watson Natural Language Understanding Service Instance](https://cloud.ibm.com/catalog/services/natural-language-understanding)

## Estimated time

Completing this tutorial should take about 30 minutes.

## Steps

1. [Create a Notebook on Watson Studio](#1-create-a-project-on-watson-studio)
2. [Set up the Notebook](#2-set-up-the-notebook)



### 1. Create a Notebook on Watson Studio

* Login to [IBM Cloud Dashboard](https://cloud.ibm.com/resources).
* Click the dropdown to `Services` and select on `Watson Studio`
* Click `Get Started` button at the bottom of the page.
* Select the `New Project` option from the Watson Studio landing page and choose the `Standard` option and create the project by giving a name.
* Upon a successful project creation, you are taken to a dashboard view of your project, click on the Assets and create a notebook.

### 2. Set up the Notebook

1. [Install the necessary package](#1-install-the-necessary-package)
2. [Import the necessary packages](#2-import-the-necessary-packages)
3. [Add the Natural Language Understanding service credentials](#3-add-the-natural-language-understanding-service-credentials)
4. [Scrape Data from Website](#4-scrape-data-from-website)

#### 1. Install the necessary package

  ```Python
  !pip install watson-developer-cloud==1.5
  ```
#### 2. Import the necessary packages
  ```Python
  from bs4 import BeautifulSoup
  from watson_developer_cloud import NaturalLanguageUnderstandingV1
  from watson_developer_cloud.natural_language_understanding_v1 \
  import Features, EntitiesOptions, KeywordsOptions, SemanticRolesOptions, SentimentOptions, EmotionOptions, ConceptsOptions, CategoriesOptions
  ```
#### 3. Add the Natural Language Understanding service credentials

* Open the Watson Natural Language Understanding service under `Service` dropdown in your [IBM Cloud Dashboard](https://cloud.ibm.com/resources).
* Once the service is open copy the `apikey` and `url` in the `Credentials` menu.
* Insert the following code and insert the `apikey` and `url` copied.

  ```Python
  apikey=''
  url=''
  natural_language_understanding = NaturalLanguageUnderstandingV1(
      version='2019-01-25',
      iam_api_key=apikey,
      url=url
  )
  ```

#### 4. Scrape Data from Website

* In a new cell define the function `scrape_google_summaries` as shown below:
  ```Python
  def scrape_google_summaries(s):
  time.sleep(randint(0, 2))  # relax and don't let google be angry
  r = requests.get("https://www.google.co.in/search?q="+s+"&oq="+s+"&aqs=chrome..69i57.14096j0j9&sourceid=chrome&ie=UTF-8")
  print(r.status_code)  # Print the status code
  content = r.text
  summary_items=[]

  soup = BeautifulSoup(content, "html.parser")

  for item in soup.find_all(class_='g'):
      summary_dict=dict()
      for link in item.find_all('a'):
          summary_dict['news_link']= link.get('href')
      for summary in item.find_all('span',class_="st"):
          summary_dict['summary']=summary.text
      summary_items.append(summary_dict)
  return summary_items
  ```
* Now, just call the function by passing any search term.
* If you wish to extract different information from a different website, enter a different url and in you browser (eg: Google Chrome) open, options> More Tools> Developer Tools. Navigate through the HTML and find the appropriate HTML tags you wish to extract and update it in the code using BeautifulSoup.

#### 5. Analyze Results in Watson NLU

* Add a new cell and define the function `analyze_using_NLU` as shown below:

  ```Python
  def analyze_using_NLU(analysistext):
    """ Extract results from Watson Natural Language Understanding for each news item
    """
    res=dict()
    response = natural_language_understanding.analyze(
        text=analysistext,
        features=Features(
                          sentiment=SentimentOptions(),
                          entities=EntitiesOptions(),
                          keywords=KeywordsOptions(),
                          emotion=EmotionOptions(),
                          concepts=ConceptsOptions(),
                          categories=CategoriesOptions(),
                          semantic_roles=SemanticRolesOptions()))
    res['results']=response
    return res
  ```
* Call the function by passing the scraped data and view the results returned as a json format.

## Summary

In this tutorial we learnt how to extract data from a given search term on a google search, get insights from Watson Natural Language Understanding.
