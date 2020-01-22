---
layout: post
title:      "Nutrition Calculator Using React/Redux and Rails API"
date:       2020-01-22 22:49:44 +0000
permalink:  nutrition_calculator_using_react_redux_and_rails_api
---


My daughter recently started to care a lot about nutrition. So whenever I cook something, inevitably the question comes up "How much enegry?" (she doesn't like to use the word calorie because her dad disapproves of that). I am sure there are tons of apps that calculate nutrition, but I don't want to deal with spam and advertisements, so I usually open the good old Excel spreadsheed and do a quick calculation. But that is pretty inconvenient, so naturally I came up with the idea to create my own nutrition calculator with my favorite recipes conveniently accessible.

The database schema consists of two main models - Meal and Ingredient - which have a many-to-many realtionship, i.e. a Meal can have many ingredients and an Ingredient can be a part of many meals. So I also created the joint table - MealIngredient, which belongs both to a Meal and to an Ingredient. In addition to the foreign keys for Meal and Ingredient, I have one additional property - servings - which indicates how many servings (e.g. cups, tablespoons, etc) are in the Meal. So this  is briefly what the database looks like. I created the serializers for Meal and Ingedient and added the `has_many :ingredients ` relationship to my Meal serializer. That way, whenever I fetch a meal, I will also fetch all its ingredients as an array of Ingredient objects. There was one additional issue, however: I wanted to fetch the number of servings for each ingredient withing the same json. I could have simply added the has_many :meal_ingredients relationship and get addtional meal_ingredients key in my json with the number of servings. But that was cumbersome and also required a lot of refactoring of my existing code.

So finally, after reserach, I came up with a solution that may not be the perfect solution, but nevertheless I got the json I wanted.  I serialized the Meal-Ingredients modeal as well. It is as simple as running the `rails g serializer meal_ingredient`  command. Then, since the meal_ingredient has access to all the ingredient properties via meal_ingredeint.ingredient.{property_name}, I added custom attributes for each ingredient property, e.g. 

```
class MealIngredientSerializer < ActiveModel::Serializer
  attribute :id do
    object.ingredient.id
  end
  
  attribute :name do
    object.ingredient.name
  end
	...
	
end
```
Nowthe MealIngredient serializer would return all the info about an ingredient which is part of a meal, including its servings in this meal. Next,  in order to fetch this ingredient info nested into the meal, in the MealSerializer I had to add
a custom method:

```
def ingredients
    ActiveModel::Serializer::CollectionSerializer.new(self.object.meal_ingredients, each_serializer: MealIngredientSerializer)
end

```
Again, all this is because I wanted all the info to be under the key `ingredients`, not meal_ingredients.

This was the main back end challenge. The front end challenges were many, but I will focus on only one recurring problem.
In React, I created two containers - MealsContainer, which woud be a parent of all meal-related components, and an IngredientsContainer for all ingredient-related components. The ingredients container renders IngredientList, , IngredientSearch and an IngredientInput components. Initially the IngredientContainer was meant to display all ingredients by themselves, not as part of showing a meal info. However I realized that a meal also has a list of ingredients and I could reuse the same components in the context of a meal. However, in that case, I always have to keep track of the meal_id. For example just adding an ingredient to the database is one thing, but adding it to a meal also requires the meal id. So I ended up doing a lot of conditional rendering and passing meal_id around as a prop.
For example, my IngredientsList component in addition to ingredients props, also receives a meal_id prop.
Then I found very handy using the following trick for conditional rendering

```
{props.meal_id ? <render on thing> : <render something else>
```
In my case if props.meal_id is true I render a button to add an ingredient.

In summary, these were some of the key challenges I encountered in my project. I have tons of ideas how to improve it and make it really useful. For example, I want to add a search box to query directly the USDA Nutritional Database API, which, coincidentally, is the example shown in [How to get "create-react-app" to work with your Rails API](https://www.newline.co/fullstack-react/articles/how-to-get-create-react-app-to-work-with-your-rails-api/), which I discovered only after I had started on my project. I thought for a while I may look guilty of stealing the idea, but actually my application is quite different, being focused mainly on the meals. However, being able to copy an ingredient directly from USDA to my local database would be great and I plan to implement it very soon. But for the time being, I think I have satisfied the requirements for my final portfolo project.
