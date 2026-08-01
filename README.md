# TheMealDB API Documentation

TheMealDB provides a REST API for searching, browsing, filtering, and retrieving meal and recipe data from around the world.

This documentation covers the **V1 API**, which can be accessed using TheMealDB's shared test key for development and educational use.

## Contents

- [Getting Started](#getting-started)
  - [Base URL](#base-url)
  - [Make Your First Request](#make-your-first-request)
- [Authentication](#authentication)
- [Response Format](#response-format)
- [Endpoints](#endpoints)
  - [Search Meal by Name](#search-meal-by-name)
  - [List Meals by First Letter](#list-meals-by-first-letter)
  - [Lookup Full Meal Details by ID](#lookup-full-meal-details-by-id)
  - [Lookup a Random Meal](#lookup-a-random-meal)
  - [List All Meal Categories](#list-all-meal-categories)
  - [List Categories, Areas, or Ingredients](#list-categories-areas-or-ingredients)
  - [Filter by Main Ingredient](#filter-by-main-ingredient)
  - [Filter by Category](#filter-by-category)
  - [Filter by Area](#filter-by-area)
- [Images](#images)
- [Error Handling](#error-handling)
- [Rate Limits and Usage Notes](#rate-limits-and-usage-notes)
- [Premium V2 Endpoints](#premium-v2-endpoints)
- [Quick Reference](#quick-reference)

---

## Getting Started

### Base URL

All V1 API requests use the following base URL:

```text
https://www.themealdb.com/api/json/v1/1/
```

The `1` in the URL path is the API key. TheMealDB provides `1` as a shared test key for development and educational use.

If you plan to publish an application publicly, for example through an app store, TheMealDB expects you to become a supporter and use your own API key.

### Make Your First Request

You can make your first request without creating an account or generating an authentication token.

The following request searches for meals containing `Arrabiata` in the meal name:

```bash
curl "https://www.themealdb.com/api/json/v1/1/search.php?s=Arrabiata"
```

Alternatively, send the following `GET` request using an API client such as Postman:

```http
GET https://www.themealdb.com/api/json/v1/1/search.php?s=Arrabiata
```

A successful request returns a JSON response containing a `meals` array:

```json
{
  "meals": [
    {
      "idMeal": "52771",
      "strMeal": "Spicy Arrabiata Penne"
    }
  ]
}
```

> **Note:** The actual response contains additional meal properties. The example above has been shortened for readability.

If no matching meal is found, the `meals` property is `null`:

```json
{
  "meals": null
}
```

---

## Authentication

The V1 API does not require authentication headers or access tokens.

The API key is included directly in the URL path:

```text
https://www.themealdb.com/api/json/v1/{api-key}/
```

All examples in this documentation use `1` as the shared test key:

```text
https://www.themealdb.com/api/json/v1/1/
```

---

## Response Format

TheMealDB returns responses in JSON format.

A successful lookup or search returns a `meals` array containing one or more meal objects:

```json
{
  "meals": [
    {
      "idMeal": "52772",
      "strMeal": "Teriyaki Chicken Casserole"
    }
  ]
}
```

If the request does not match any meals, the API returns:

```json
{
  "meals": null
}
```

Always check the value of the `meals` property before processing the returned data.

---

# Endpoints

## Search Meal by Name

Returns meals matching a full or partial meal name.

### Request

```http
GET /search.php?s={name}
```

### Query Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `s` | string | Yes | Full or partial meal name |

### Example Request

```http
GET https://www.themealdb.com/api/json/v1/1/search.php?s=Arrabiata
```

### Example using cURL

```bash
curl "https://www.themealdb.com/api/json/v1/1/search.php?s=Arrabiata"
```

---

## List Meals by First Letter

Returns all meals whose names begin with the specified letter.

### Request

```http
GET /search.php?f={letter}
```

### Query Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `f` | character | Yes | Single letter from `a` through `z` |

### Example Request

```http
GET https://www.themealdb.com/api/json/v1/1/search.php?f=a
```

---

## Lookup Full Meal Details by ID

Returns the complete recipe information for a single meal, including its ingredients, measurements, instructions, category, area, and available media links.

### Request

```http
GET /lookup.php?i={id}
```

### Query Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `i` | string | Yes | Unique meal ID |

### Example Request

```http
GET https://www.themealdb.com/api/json/v1/1/lookup.php?i=52772
```

### Sample Response

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

> **Note:** The sample response has been shortened for readability.

### Working with Ingredients and Measurements

Ingredients and measurements are returned as numbered property pairs:

```text
strIngredient1
strMeasure1

strIngredient2
strMeasure2

...

strIngredient20
strMeasure20
```

Unused ingredient and measurement fields can contain empty strings or `null`.

When consuming the response, iterate through the numbered ingredient and measurement pairs and ignore empty ingredient values.

---

## Lookup a Random Meal

Returns one randomly selected meal.

The response uses the same meal object structure as the [Lookup Full Meal Details by ID](#lookup-full-meal-details-by-id) endpoint.

### Request

```http
GET /random.php
```

### Example Request

```http
GET https://www.themealdb.com/api/json/v1/1/random.php
```

### Example using cURL

```bash
curl "https://www.themealdb.com/api/json/v1/1/random.php"
```

---

## List All Meal Categories

Returns all available meal categories along with their descriptions and thumbnail images.

### Request

```http
GET /categories.php
```

### Example Request

```http
GET https://www.themealdb.com/api/json/v1/1/categories.php
```

---

## List Categories, Areas, or Ingredients

Returns lightweight lists that can be used to populate filters, dropdown menus, or application navigation.

### Requests

List all categories:

```http
GET /list.php?c=list
```

List all areas:

```http
GET /list.php?a=list
```

List all ingredients:

```http
GET /list.php?i=list
```

### Query Parameters

| Parameter | Returns |
| --- | --- |
| `c=list` | All category names |
| `a=list` | All area or cuisine names |
| `i=list` | All ingredient names |

---

## Filter by Main Ingredient

Returns meals containing the specified main ingredient.

### Request

```http
GET /filter.php?i={ingredient}
```

### Query Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `i` | string | Yes | Ingredient name. Replace spaces with underscores (`_`) where required. |

### Example Request

```http
GET https://www.themealdb.com/api/json/v1/1/filter.php?i=chicken_breast
```

Filter responses contain a reduced set of meal properties:

```json
{
  "meals": [
    {
      "strMeal": "Meal name",
      "strMealThumb": "https://example.com/image.jpg",
      "idMeal": "12345"
    }
  ]
}
```

To retrieve the complete recipe information for a returned meal, pass its `idMeal` value to the [Lookup Full Meal Details by ID](#lookup-full-meal-details-by-id) endpoint.

---

## Filter by Category

Returns meals belonging to the specified category.

### Request

```http
GET /filter.php?c={category}
```

### Query Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `c` | string | Yes | Meal category |

### Example Request

```http
GET https://www.themealdb.com/api/json/v1/1/filter.php?c=Seafood
```

> **Note:** Filter endpoints return a reduced meal object containing `idMeal`, `strMeal`, and `strMealThumb`. Use the meal ID with the lookup endpoint to retrieve the complete recipe.

---

## Filter by Area

Returns meals associated with the specified cuisine or geographical area.

### Request

```http
GET /filter.php?a={area}
```

### Query Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `a` | string | Yes | Cuisine or geographical area |

### Example Request

```http
GET https://www.themealdb.com/api/json/v1/1/filter.php?a=Canadian
```

> **Note:** To retrieve complete recipe information for a returned meal, use its `idMeal` value with the [Lookup Full Meal Details by ID](#lookup-full-meal-details-by-id) endpoint.

---

# Images

## Meal Thumbnails

Meal objects can contain a `strMealThumb` property containing the URL of the meal image.

Meal images support size variants by appending a path segment to the base image URL:

```text
{strMealThumb}/small
{strMealThumb}/medium
{strMealThumb}/large
```

## Ingredient Thumbnails

Ingredient images follow a predictable URL structure based on the ingredient name.

```text
https://www.themealdb.com/images/ingredients/{ingredient}.png
```

Size variants are also available:

```text
https://www.themealdb.com/images/ingredients/{ingredient}-small.png
https://www.themealdb.com/images/ingredients/{ingredient}-medium.png
https://www.themealdb.com/images/ingredients/{ingredient}-large.png
```

Replace spaces in ingredient names with underscores where required.

---

# Error Handling

TheMealDB does not use conventional HTTP error responses to indicate every "no results" scenario.

For search and lookup requests, check the response body to determine whether matching meals were found.

| Response | Meaning |
| --- | --- |
| `{"meals": null}` | No matching meals were found |
| `{"meals": [...]}` | One or more meals were returned |

For example:

```json
{
  "meals": null
}
```

Always check whether the `meals` property is `null` before attempting to iterate through the returned data.

---

# Rate Limits and Usage Notes

Keep the following considerations in mind when using the V1 API:

- The shared test key (`1`) is intended for development, learning, and personal projects.
- Publicly released applications require a supporter key.
- List-style responses on the free tier are capped at 100 results.
- TheMealDB does not publish a hard rate limit for the free tier.
- Excessive automated traffic may be throttled.

Where appropriate, cache responses instead of repeatedly requesting data that does not change frequently.

---

# Premium V2 Endpoints

TheMealDB supporters can access the V2 API using a supporter key.

### Base URL

```text
https://www.themealdb.com/api/json/v2/{key}/
```

V2 provides additional functionality, including:

- **Random selection** — returns multiple random meals in a single request.
- **Latest meals** — returns recently added meals.
- **Multi-ingredient filtering** — filters meals using multiple ingredients.
- Higher result limits.
- Ability to submit meals and images.

### Random Selection

```http
GET /randomselection.php
```

Returns 10 random meals in a single request.

### Latest Meals

```http
GET /latest.php
```

Returns recently added meals.

### Multi-Ingredient Filtering

```http
GET /filter.php?i=chicken_breast,garlic,salt
```

Filters meals using multiple ingredients.

> **Note:** V2 endpoints are not available using the shared V1 test key. A supporter key is required.

---

# Quick Reference

| Task | Endpoint |
| --- | --- |
| Search by meal name | `search.php?s={name}` |
| Search by first letter | `search.php?f={letter}` |
| Get complete meal details | `lookup.php?i={id}` |
| Get a random meal | `random.php` |
| List all categories | `categories.php` |
| List category names | `list.php?c=list` |
| List area names | `list.php?a=list` |
| List ingredient names | `list.php?i=list` |
| Filter by ingredient | `filter.php?i={ingredient}` |
| Filter by category | `filter.php?c={category}` |
| Filter by area | `filter.php?a={area}` |

---

## About This Documentation Sample

This documentation is an **independent technical writing portfolio project** created using TheMealDB's publicly available API.

It demonstrates API documentation practices including:

- Developer onboarding
- REST endpoint documentation
- Request and response examples
- Query parameter documentation
- cURL examples
- JSON response documentation
- Error-handling guidance
- Cross-referencing and documentation navigation

This is **not official TheMealDB documentation**.

For official API information, refer to [TheMealDB API documentation](https://www.themealdb.com/api.php).
