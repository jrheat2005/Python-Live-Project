# Live Project


## Introduction

For the past two weeks of training at the tech academy, I worked on developing a full scale CRUD Web Application in Python. The app I choice to design and build was a Movie Lists app which sorted movies based off their ratings. This was a great learning expierence using Django, debugging, adding requested features. Through this sprint, One of the things I enjoyed doing the most was working on the [back end stories](#back-end-stories). I honestly believe the skills i learned from using Agile project managemnent will translate well and will be used greatly in my future careers. 


## Back End Stories
* [Basic CRUD](#basic-crud)
* [TMDB API](#tmdb-api)
* [IMDB Webscraping](#imdb-webscraping)


### Basic CRUD

For these stories, I implemented full CRUD (create, read, update, delete) functionality for movies on the website. This included:

* Building forms to allow users to add new movies with details like ratings, genres, and descriptions.
* Displaying the list of movies sorted by rating, allowing viewers to browse through the catalog.
* Adding edit forms and links so users can update or modify existing movie entries if needed.
* Adding delete buttons so users have the ability to remove movies from the list completely

Below i have listed my views.py code created to accomplish that task

```Python
def MovieLists_Create(request):
    if request.method == 'POST':
        form = ListForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('MovieLists_home')
    else:
        form = ListForm()
    return render(request, 'MovieLists/MovieLists_Create.html', {'form': form})


def MovieLists_List(request):
    search_form = MovieListSearchForm()
    search_query = request.GET.get('search_query')
    movie_lists = List.objects.order_by('-ratings')
    if search_query:
        movie_lists = movie_lists.filter(title__icontains=search_query)
    return render(request, 'MovieLists/MovieLists_List.html', {'movie_lists': movie_lists, 'search_form': search_form})

def MovieLists_Details(request, movie_list_id):
    # Retrieve the movie list based on its ID or identifier
    movie_list = get_object_or_404(List, id=movie_list_id)
    content = {'movie_list': movie_list}
    return render(request, 'MovieLists/MovieLists_Details.html', content)

def MovieLists_Edit(request, movie_list_id):
    movie_list = get_object_or_404(List, id=movie_list_id)
    form = ListForm(request.POST or None, instance=movie_list)
    if request.method == 'POST':
        if form.is_valid():
            form.save()
            return redirect('MovieLists_List')
    content = {'form': form, 'movie_list': movie_list}
    return render(request, 'MovieLists/MovieLists_Edit.html', content)

def MovieLists_Delete(request, movie_list_id):
    movie_list = get_object_or_404(List, id=movie_list_id)
    if request.method == 'POST':
        movie_list.delete()
        return redirect('MovieLists_List')
    content = {'movie_list': movie_list}
    return render(request, 'MovieLists/MovieLists_Delete.html', content)
```

### TMDB API

To keep in theme with my website i utlized a API to pull data from TMDB (The Movie DataBase) to display realtime to my website.

```Python
def MovieLists_Api(request, tv_id):
    base_url = 'https://api.themoviedb.org/3'
    endpoint = f'{base_url}/tv/{tv_id}?api_key={api_key}'
    response = requests.get(endpoint)
    tv_data = response.json()
    print(tv_data)
    return render(request, 'MovieLists/MovieLists_Api.html', {'tv_data': tv_data})
```
### IMDB Webscraping
I was also tasked with using Beautiful Soup to create a webscraper that grabbed data from IMDB displaying the list of A24 movies to my website.
```Python
def MovieLists_BS(request):
    headers = {"User-Agent": "Mozilla/5.0"}
    page = requests.get('https://www.imdb.com/list/ls024372673/', headers=headers)
    soup = BeautifulSoup(page.content, 'html.parser')

    data = []
    try:
        for movie in soup.select('.lister-item'):
            name = movie.find('h3', class_="lister-item-header").a.text
            rank = movie.find('span', class_="lister-item-index unbold text-primary").text
            star = movie.find('span', class_="ipl-rating-star__rating").text
            metascore = movie.find('div', class_="inline-block ratings-metascore").span.text
            score = movie.find('div', class_="list-description").text if movie.find('div', class_="list-description") else None
            genre = movie.find('span', class_="genre").text.strip()
            about = movie.find('p', class_="").text

            data.append({
                'name': name,
                'rank': rank,
                'star': star,
                'genre': genre,
                'about': about,
            })

        print(data)
    except Exception as e:
        print(e)

    return render(request, 'MovieLists/MovieLists_BS.html', {'data': data})
```
