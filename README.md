# Live Project


## Introduction

For the past two weeks of training at the tech academy, I worked on developing a full scale CRUD Web Application in Python. The App I choice to design and build was a Movie Lists app which sorted Movies based off their ratings. This was a great learning expierence using Django, Debugging, adding requested features. Through this sprint, One of the things I enjoyed doing the most was working on the [back end stories](#back-end-stories). I also got a great deal of expience from working on the [front end stories](#front-end-stories). I honestly believe the skills i learned from using Agile project managemnent will translate well and will be used greatly in my future careers. 


## Back End Stories
* [Basic CRUD](#basic-crud)
* [TMDB API](#tmdb-api)


### Basic CRUD
```Python
This is my views.py which enables to to complete the CRUD functionality
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
