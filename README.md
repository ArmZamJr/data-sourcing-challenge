# data-sourcing-challenge

nyt_movie_review_articles_solution.ipynb
# Use the writers list to find the most recent articles by the same writer

# Empty list for results
results_list = []

# loop through the writers_list
for writer in writers_list:
    # Set up the query
    query_url = f"{url}api-key={api_key}&byline:{writer}&sort{sort}&fl={field_list}"
    
    # Get the results
    results = requests.get(query_url).json()
    
    # Add a 12 second interval between queries to stay within API query limits
    time.sleep(12)

    # Use a try-except clause to collect results
    try:
        # Loop through the "docs"
        for doc in results["response"]["docs"]:
            # Save byline.original, headline.main, snippet,
            # and web_url
            results_list.append({
                "byline": doc["byline"]["original"],
                "headline": doc["headline"]["main"],
                "snippet": doc["snippet"],
                "web_url": doc["web_url"]
            })
        print(f"Found articles {writer}")
    except:
        print(f"No articles {writer} found")
        
# Convert reviews_list to a Pandas DataFrame using json_normalize()
df = pd.json_normalize(reviews_list)
df.head()
TV_ratings_solution.ipynb
# Use json_normalize to convert all_results to a DataFrame
all_results_df = pd.json_normalize(all_results)
all_results_df