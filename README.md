## Linked Movie Search
#### Linked Data Search Engine for Movies using Wikidata and Sparql  
  
## Introduction
Movie Search allows the user to search for movies, casts and directors. Simply select the filter
you want to use and enter the search term. The app uses [SPARQL](https://www.w3.org/TR/rdf-sparql-query/) via the SPARQLWrapper library to query the [Wikidata](https://www.wikidata.org/wiki/Wikidata:Main_Page) Knowledge base and displays the search results in a nice manner.The back-end is implemented in [Python3](https://www.python.org/download/releases/3.0/) using the [Flask](https://www.palletsprojects.com/p/flask/) framework while the front-end is implemented using [BulmaCSS](https://bulma.io/) library. Images for cast and directors are provided by Wikidata but unfortunately posters for movie are not avaiable in Wikidata. So,movie posters are obtained from [The Movie Database](https://www.themoviedb.org/) using their Free API. 

## Running the App
Since the app is written in Python, running it is pretty simple. All you need is a relatively new version of Python3 and its package
manager [pip](https://pip.pypa.io/en/stable/). To run the app, run the following commands  
```bash
git clone https://github.com/saivarshith2000/LinkedMovieSearch  # Clone this repository
cd LinkedMovieSearch                # move into the app directory (this directory contains main.py)
python3 -m venv venv                # create a python virtual container for the app
source venv/bin/activate            # activate the virtual environment
pip3 install -r requirements.txt    # install the required dependencies in this container
python main.py                      # run the app (it runs on the address http://127.0.0.1:5000/)
```
**Note:** In the above commands if python3 or pip3 commands don't work just try them again without the 3.

## SPARQL Details
SPARQL Details are explained in the [Report.pdf](#). Please refer to it.

## Recommendation System(Or lack thereof)
The app was originally supposed to be a **Search-Cum-Recommendation** system. But, unfortunately due to certain limitations it was not implemented. Instead, we have designed the system to implement it. The limitations are explained below:  
1. **Wikidata:** Wikidata has ton of data stored in RDF format. The SPARQL used in the app is quite simple but non-trivial interms of computation power required for inferencing. For some queries it takes pretty long time or it just gives up with empty results. Since its a free service and computation is not trivial Wikidata has implemented some restrictions on their Free API service. For example, we cannot send more than 3 requests per minute from the same IP. In a recommendation system it is very important to be able to fire off a few requests in multi-threaded asynchronously and then process them on return. But, since we cannot send that many requests it is not possible to have such system.
   
2. **Network:** Some queris take pretty long(40s-1min).This means the network socket must be open for a longer time than standard. Since, the API uses HTTP protocal neither wikidata nor the Campuse network keeps a socket open for that long. Therefore, we have an IncompleteRead Error. This too prohibits the implementation of a recommendations system.

## Possible Solutions for a Recommendation System
1. **Static File Caching System:** This is a pretty common form of caching used by most of the online services. Its as follows, fetch results from the API, save them in a file based or SQL based database(File based is preferred for portable apps), on the next query check if this data is useful. This eliminates the need to send multiple queries to some extent. But, we need to have a very large cache of data to do this. This method would really impressive results if the app was implemented as a single server and multiple user model.

2. **LIMIT SPARQL:** Use the `LIMIT` SPARQL construct to limit the number of triples returned. Although, the data returned is _correct_ it is not _enough_. We cannot run any meaningful recommendation algorithm on _limited_ data.