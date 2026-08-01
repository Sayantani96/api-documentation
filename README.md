# TheMealDB API Documentation

TheMealDB is a free, crowd-sourced recipe database that exposes a RESTful JSON API for searching, browsing, and filtering meals from around the world. This document covers the V1 API, which is free to use with the shared test key.

## Base URL

```
https://www.themealdb.com/api/json/v1/1/
```

The `1` in the path is the API key. TheMealDB provides `1` as a shared test key for development and educational use. If you plan to publish an app publicly (for example, on an app store), you're expected to become a supporter and use your own key, which unlocks the premium V2 endpoints, higher result limits, and multi-ingredient filtering.

## Authentication

No authentication headers or tokens are required. The API key is passed directly in the URL path. For all examples below, `1` is used as the test key.

## Response Format

All endpoints return JSON. A successful lookup or search returns a `meals` array containing one or more meal objects. If no results are found, the API returns:

```json
{ "meals": null }
```

## Endpoints

### Search meal by name

Returns all meals matching the given name.

```
GET /search.php?s={name}
```

| Parameter | Type   | Description               |
|-----------|--------|----------------------------|
| `s`       | string | Full or partial meal name |

**Example**

```
GET https://www.themealdb.com/api/json/v1/1/search.php?s=Arrabiata
```

### List meals by first letter

Returns all meals whose name begins with the given letter.

```
GET /search.php?f={letter}
```

| Parameter | Type | Description                     |
|-----------|------|----------------------------------|
| `f`       | char | Single letter, a through z      |

**Example**

```
GET https://www.themealdb.com/api/json/v1/1/search.php?f=a
```

### Lookup full meal details by ID

Returns the complete recipe payload for a single meal, including ingredients, measures, instructions, and media links.

```
GET /lookup.php?i={id}
```

| Parameter | Type   | Description   |
|-----------|--------|----------------|
| `i`       | string | Meal ID |

**Example**

```
GET https://www.themealdb.com/api/json/v1/1/lookup.php?i=52772
```

**Sample response**

```json
{
  "meals": [
    {
      "idMeal": "52772",
      "strMeal": "Teriyaki Chicken Casserole",
      "strCategory": "Chicken",
      "strArea": "Japanese",
      "strInstructions": "Preheat oven to 350° F. Spray a 9x13-inch baking pan with non-stick spray...",
      "strMealThumb": "https://www.themealdb.com/images/media/meals/wvpsxx1468256321.jpg",
      "strTags": "Meat,Casserole",
      "strYoutube": "https://www.youtube.com/watch?v=4aZr5hZXP_s",
      "strIngredient1": "soy sauce",
      "strMeasure1": "3/4 cup",
      "strIngredient2": "water",
      "strMeasure2": "1/2 cup"
    }
  ]
}
```

Ingredients and measures are returned as numbered fields (`strIngredient1` through `strIngredient20`, `strMeasure1` through `strMeasure20`). Unused slots return empty strings or `null`. When consuming this response, loop through the numbered pairs and stop at the first empty ingredient.

### Lookup a random meal

Returns one randomly selected meal, in the same shape as the lookup response above.

```
GET /random.php
```

**Example**

```
GET https://www.themealdb.com/api/json/v1/1/random.php
```

### List all meal categories

Returns every category along with its description and thumbnail image.

```
GET /categories.php
```

**Example**

```
GET https://www.themealdb.com/api/json/v1/1/categories.php
```

### List categories, areas, or ingredients

Returns a lightweight list of names only, useful for populating filter dropdowns or navigation.

```
GET /list.php?c=list
GET /list.php?a=list
GET /list.php?i=list
```

| Parameter | Returns                     |
|-----------|-------------------------------|
| `c=list`  | All category names            |
| `a=list`  | All area (cuisine/region) names |
| `i=list`  | All ingredient names           |

### Filter by main ingredient

Returns a reduced meal list (ID, name, thumbnail only) for meals containing the given ingredient.

```
GET /filter.php?i={ingredient}
```

| Parameter | Type   | Description                                 |
|-----------|--------|----------------------------------------------|
| `i`       | string | Ingredient name, spaces replaced with `_`   |

**Example**

```
GET https://www.themealdb.com/api/json/v1/1/filter.php?i=chicken_breast
```

### Filter by category

```
GET /filter.php?c={category}
```

**Example**

```
GET https://www.themealdb.com/api/json/v1/1/filter.php?c=Seafood
```

### Filter by area

```
GET /filter.php?a={area}
```

**Example**

```
GET https://www.themealdb.com/api/json/v1/1/filter.php?a=Canadian
```

> Filter endpoints return a stripped-down object (`idMeal`, `strMeal`, `strMealThumb`). To get full recipe details, follow up with a `lookup.php?i={id}` call.

## Images

### Meal thumbnails

Meal images support size variants by appending a path segment to the base image URL:

```
{strMealThumb}/small
{strMealThumb}/medium
{strMealThumb}/large
```

### Ingredient thumbnails

Ingredient images follow a predictable naming pattern based on the ingredient name, with spaces replaced by underscores:

```
https://www.themealdb.com/images/ingredients/{ingredient}.png
https://www.themealdb.com/images/ingredients/{ingredient}-small.png
https://www.themealdb.com/images/ingredients/{ingredient}-medium.png
https://www.themealdb.com/images/ingredients/{ingredient}-large.png
```

## Premium (V2) Endpoints

Supporters get access to a beta V2 API at a separate base URL:

```
https://www.themealdb.com/api/json/v2/{key}/
```

V2 unlocks:

- **Random selection** (`randomselection.php`) — returns 10 random meals in one call instead of one
- **Latest meals** (`latest.php`) — returns the most recently added meals
- **Multi-ingredient filtering** (`filter.php?i=chicken_breast,garlic,salt`) — filter by more than one ingredient at once
- Higher result limits (the free tier caps list-style responses at 100 items)
- Ability to submit your own meals and images

These are not available on the free test key and require a supporter key issued after signing up.

## Error Handling

TheMealDB doesn't use conventional HTTP error codes for "no results" cases. Instead, check the response body:

| Response                  | Meaning                                  |
|----------------------------|--------------------------------------------|
| `{"meals": null}`          | No matches found for the query           |
| `{"meals": [...]}`         | One or more results returned             |

Always null-check the `meals` field before iterating over it, rather than relying on the HTTP status code alone.

## Rate Limits and Usage Notes

- The free test key (`1`) is intended for development, learning, and personal projects.
- Publicly released apps (app store listings, production traffic) require a supporter key.
- List-style endpoints on the free tier are capped at 100 results.
- There's no published hard rate limit for the free tier, but excessive automated traffic may be throttled. Cache responses where practical rather than re-fetching on every request.

## Quick Reference

| Task                          | Endpoint                              |
|-------------------------------|-----------------------------------------|
| Search by name                | `search.php?s={name}`                  |
| Search by first letter        | `search.php?f={letter}`               |
| Full meal lookup              | `lookup.php?i={id}`                   |
| Random meal                   | `random.php`                          |
| All categories                | `categories.php`                      |
| List category/area/ingredient names | `list.php?c=list` / `?a=list` / `?i=list` |
| Filter by ingredient          | `filter.php?i={ingredient}`           |
| Filter by category            | `filter.php?c={category}`             |
| Filter by area                | `filter.php?a={area}`                 |

---

*Documentation based on TheMealDB's public V1 API. Source: [themealdb.com/api.php](https://www.themealdb.com/api.php)*
