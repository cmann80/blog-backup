---
title: "Validated User Movie Reviewing in JS React"
date: "2022-10-10"
categories: 
  - "software-engineering"
---

## Introduction

In making any kind of site where users post and see reviews of products such as movies, a fundamental problem arises: If the user is free to enter any information into the form they like, they may enter different titles that correpond to the same movie, either by a mistype or a different understanding of the title itself. If that information gets entered in a .json file as is, it may end up looking like this:

```
{
  "movies": [
    {
      "id": 45,
      "title": "Princess Mononoke",
      "reviews": [
        {
          "name": "Jay",
          "review": "call it what it is. treeson",
          "stars": 4
        },
      ]  
    },
    {
      "id": 46,
      "title": "Mononoke Hime",
      "reviews": [
          {
          "name": "D. Bolt",
          "review": "why everyone tryna kill the deer god he just                           chillin",
          "stars": 3
        }
      ]
    },
    {
      "id": 47,
      "title": "もののけ姫",
      "reviews": [
          {
          "name": "D. Bolt",
          "review": "I love Ghibli movies so much I learned Japanese to watch them!",
          "stars": 3
        }
      ]
}      
```

Princess Mononoke, Mononoke Hime and もののけ姫 are the same movie, only the English title, the Romanized Japanese title and the Japanese title, respectively. Depending on the user's understanding of the Japanese and English language, they may never even consider the other one as a search possibility, and thus would never see one or the other of the reviews when searching for it. The solution I came up with is to have the user search for the movie titles and have the search function poll a free API, The Movie Database API at [https://www.themoviedb.org/](https://www.themoviedb.org/) and return candidate movie titles. Then, the user can simply click on one and the movie title part of the data entry is already complete.

## Getting and Using the API Authorization Key

First, we visited [https://developers.themoviedb.org/4/getting-started/authorization](https://developers.themoviedb.org/4/getting-started/authorization) and made an account, which gave us access to a validation key. This key is part of the fetch request to get movie data from the database. I then created the fetch request like this:

```
fetch(`https://api.themoviedb.org/3/search/movie?api_key=<<api key>>&query=${event.target.value}`)
            .then(resource => resource.json())
            .then(movieSuggestions => (setSearchList(movieSuggestions)))
```

The API key is omitted here (replaced with<<api key>>) but you can see how it is inserted just before query= and then the value of the search field.

## Constructing the Rest of the Search Handler

We built the search handler around the fetch request like this:

```
// function to handle search and create suggestions for movie titles
function handleSearch(event){
        event.preventDefault()
        setSearchTerm(event.target.value)
        if(event.target.value==='') {
            setSearchList([])
            }
        
        fetch(`https://api.themoviedb.org/3/search/movie?api_key=<<api key>>&query=${event.target.value}`)
            .then(resource => resource.json())
            .then(movieSuggestions => (setSearchList(movieSuggestions)))
 }
```

## Turning Search Results into Routing in React

The API returns only the first 20 movie title candidates at the time, and that ended up being more than enough for our purposes. Then we set to work turning the results into links that would lead to the page which showed all the reviews for an individual movie. Some of the code for generating the React elements looks like this:

```
function SearchListItem({movieSuggestion, setCurrMovie}){

    function handleClick(){
        setCurrMovie(movieSuggestion)
    }

    return (
        <NavLink
        to={`/review/${movieSuggestion.id}`}
        exact
        >
    <li className="listitem"onClick={handleClick}>{movieSuggestion.title}</li>
    </NavLink>
    )
    
}
```

The NavLink is based off of the unique id tag of the movie from the database, which ensures that the user can't open the edit page with for a duplicate movie item.

## Conclusion

I hope that others looking for ways to eliminate duplicate entries in user review/comment systems consider this method in their projects. I would like to thank Justin Saborouh for his contributions to the code and sample movie reviews.
