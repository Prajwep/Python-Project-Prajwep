import requests
from bs4 import BeautifulSoup
import pandas as pd

# Fetch top 5 movies by genre
def get_top_movies_by_genre(genre):
    # IMDb's Top Rated Movies by Genre URL
    url = f"https://www.imdb.com/search/title/?genres={genre}&sort=user_rating,desc&title_type=feature"

    # Send an HTTP GET request to the URL
    response = requests.get(url)

    if response.status_code == 200:
        # Parse the HTML content of the page
        soup = BeautifulSoup(response.text, "html.parser")

        
        movie_containers = soup.find_all("div", class_="lister-item-content")

        top_movies = []

        # Iterate movie containers to extract top 5 movie titles
        for i, container in enumerate(movie_containers):
            title = container.find("h3").find("a").get_text()
            top_movies.append(title)

            # Top 5 movies
            if len(top_movies) == 5:
                break

        return top_movies

    else:
        print("Failed to retrieve IMDb data. Check your internet connection or the IMDb website.")
        return []

# genre input from the user
genre = input("Enter the genre you're interested in: ")

# Store the top 5 movies of the specified genre 
top_movies = get_top_movies_by_genre(genre)
if top_movies:
    df = pd.DataFrame(top_movies, columns=["Top Movies"])
    print(f"Top 5 {genre} Movies on IMDb:")
    print(df)
else:
    print(f"No top-rated movies found for the genre: {genre}")
