# data-sourcing-challenge

nyt_movie_review_articles_solution.ipynb
# Use the writers list to find the most recent articles by the same writer

# Empty list for results
results_list = []

# loop through the writers_list
for writer in writers_list:
    # Set up the query
    query_url = f"{url}api-key={api_key}&byline:{writer}&sort{sort}&fl={field_list}"
    I had issues with my Api not connecting to URL due to api-key being at the end of the query_url f string.
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

## chatgpt.com
# Fix the "keywords" column by converting cells from a list to a string

Explanation:
extract_keywords Function:

Iterates through each dictionary in the keyword_list.
Extracts the 'name' and 'value' from each dictionary and formats them as "name: value;".
Concatenates these formatted strings into a single string (extracted_keywords).
Removes any trailing spaces using .strip().
.apply() Method:

The function is applied to each row in the "keywords" column, transforming the list of dictionaries into a single string.
Result:
After running the code, the "keywords" column in your DataFrame will be converted from a list of dictionaries to a string, formatted as desired.

'''# Extract the title from the "headline.main" column and
def extract_title(headline):
    start = headline.find("\u2018")
    end = headline.find("\u2019")
    if start != -1 and end != -1:
        title = headline[start + 1:end] 
        return title
    else:
        return None
# save it to a new column "title"
# Title is between unicode characters \u2018 and \u2019. 
df['title'] = df['headline.main'].apply(extract_title)
# End string should include " Review" to avoid cutting title early
df'''
# stick to the script lambda st: st[st.find("\u2018")+1:st.find("\u2019 Review")]

df['title'] = df['headline.main'].apply(lambda st: st[st.find("\u2018")+1:st.find("\u2019 Review")])
df
This one gave me issues after I remembered there was a lambda function that was given to use.  Not sure why it messed up the rest of my cells.

# Create an empty list to store the results
tmdb_movies_list = []
# Create a request counter to sleep the requests after a multiple
# of 50 requests
request_counter = 1
# Loop through the titles

for title in titles:
    # Check if we need to sleep before making a request
    if request_counter % 50 == 0:
        print("Sleeping at 50 requests")
        time.sleep(1)
    # Add 1 to the request counter
    request_counter += 1
    
    # Perform a "GET" request for The Movie Database
    '''
    search_parameters = {
        "query": title,
        "api_key": tmdb_api_key
    }
    search_response = request.get(search_url, params=search_params) 
    '''
    query_url = f"{url}{title}{tmdb_key_string}"
    search_response = requests.get(query_url)
    search_data = search_response.json()
  
    
    # Include a try clause to search for the full movie details.
    try:
        movie_id = search_data["results"][0]["id"]
    # Use the except clause to print out a statement if a movie
    # is not found.

        # Get movie 
      


        # Make a request for a the full movie details
        #details_response + requests>get(f"{details_url}{movie_id}", params={"api_key": tmdb_api_key})
        details_url = f"https://api.themoviedb.org/3/movie/{movie_id}?api_key={tmdb_api_key}"
        details_response = requests.get(details_url)
        # Execute "GET" request with url
        details_data = details_response.json()
    
        # Extract the genre names into a list
        genres = [genre['name'] for genre in details_data.get("genres", [])]

        # Extract the spoken_languages' English name into a list
        spoken_languages = [language['english_name'] for language in details_data.get("spoken_languages",[])]

        # Extract the production_countries' name into a list
        production_countries = [country['name'] for country in details_data.get("production_countries", [])]

        # Add the relevant data to a dictionary and
       
        movie_info = {
            "title": details_data.get("title"),
            "original_title": details_data.get("original_title"),
            "budget": details_data.get("budget"),
            "original_language": details_data.get("original_language"),
            "homepage": details_data.get("homepage"),
            "overview": details_data.get("overview"),
            "popularity": details_data.get("popularity"),
            "runtime": details_data.get("runtime"),
            "revenue": details_data.get("revenue"),
            "release_date": details_data.get("release_date"),
            "vote_average": details_data.get("vote_average"),
            "vote_count": details_data.get("vote_count"),
            "genres": genres,
            "spoken_languages": spoken_languages,
            "production_countries": production_countries
        }
            
        # append it to the tmdb_movies_list list
        tmdb_movies_list.append(movie_info)
        
        # Print out the title that was found
        #print(f"Found {details_data.get('title')}")   
        print(f"Found {title}")
   # except:
    except Exception as myerro :
        #print(myerro)
        print(f"{title} not found.")
        #print(f"{title} not found.") 

My Latest Resolved Questions:
ASK-313900 | Python \ API | Assignment
Submitted Aug 20 8:16 AM
Answered Aug 20 9:20 AM
I have code that works when it wants to.  API key works, just code is intermittent.
ASK-313898 | Python / API | Assignment
Submitted Aug 20 12:30 AM
Answered Aug 20 1:07 AM
retrieve_movie_data.ipynb issues with printing tmdb_movies_list
ASK-313881 | Python / API | Assignment
Submitted Aug 19 2:32 PM
Answered Aug 19 3:09 PM
"retrieve_movies_data.ipynb" not able to include full movie_info dict
I had issues with the API querey and it connecting intermittently.  Wheather is was environment or kernal issues or coding I don't know.  But after 3 BCS Learning assistants and 2 TAs and 1 instructor and 3 days I finnally got it resolved.