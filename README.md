# Smart Pantry Manager

Smart Pantry Manager is an Android app built in Java for Mobile App Development 700. The idea is simple: you keep track of the ingredients you already have at home, and the app tells you which recipes you can cook with them right now, without a trip to the shop. The goal is to waste less food by actually using what's sitting in the cupboard.

## What it does

- **Pantry:** add, view, edit and delete ingredients, each with a name, quantity, unit and an optional expiry date.
- **Suggested recipes:** only shows recipes where you have *every* ingredient, in a big enough amount. If even one thing is missing, the recipe doesn't make the list.
- **Almost there (bonus):** a separate list of recipes that are short by exactly one ingredient, so you can see what one extra item would unlock.
- **Recipe details:** the full ingredient list, with a ✔ for what you have and a ✘ for what's missing, plus the method.
- **Settings:** turn expiry warnings on or off, and show or hide the Almost there list.
- **20 recipes** are loaded into the database the first time the app runs.
- The ingredient form checks your input before saving.
- A bottom navigation bar moves between Pantry, Recipes and Settings.

## How the matching works

The main logic is in `logic/RecipeMatcher.java`. Before comparing anything, it tidies up the data:

1. **Names are cleaned up:** lower case, trimmed, and simple plurals removed. So *Tomatoes* becomes *tomato*, *Berries* becomes *berry*, and *Eggs* becomes *egg*.
2. **Units are converted** to one base unit: kg to g, l to ml, and tsp, tbsp and cup to ml. Pieces stay as pieces.
3. **Duplicates are added together.** If you enter "Egg 2" and "Eggs 4", the app counts 6 eggs.
4. **Missing ingredients are counted.** A recipe is suggested only when nothing is missing. If exactly one ingredient is missing, it goes to Almost there instead.

I tested this logic with JUnit. The tests are in `app/src/test/.../RecipeMatcherTest.java`, and all 8 pass.

## Why I used SQLite

I chose SQLite through `SQLiteOpenHelper` because it fits what this app needs:

- It's built into Android, so there's no server to set up and no internet connection needed.
- The data is small and belongs to one person on one phone. A cloud database would only make sense if the pantry had to be shared between devices.
- The data stays on the phone after the app is closed and reopened.
- It's the approach we covered in the module, so I could focus on building the app instead of a backend.

The database has three tables: `pantry`, `recipes` and `recipe_ingredients`. One recipe can have many ingredients.

## Project structure

```
app/src/main/java/com/richfield/smartpantry/
├── data/     DatabaseHelper (SQLite CRUD), RecipeSeeder (20 starter recipes)
├── logic/    RecipeMatcher (strict matching), PantryUtils, AppSettings
├── model/    PantryItem, Recipe, RecipeIngredient
└── ui/       5 Activities, 2 Adapters, NavHelper
```

**Screens:**

1. Pantry List: `MainActivity`
2. Add / Edit Ingredient: `AddEditIngredientActivity`
3. Suggested Recipes: `SuggestedRecipesActivity`
4. Recipe Detail: `RecipeDetailActivity`
5. Settings: `SettingsActivity`

## How to run it

1. Install **Android Studio**. Any recent version works.
2. Clone the repo:
   ```
   https://github.com/Kgosiitumeleng/SmartPantryManager.git
   ```
3. In Android Studio, go to **File › Open** and select the `SmartPantryManager` folder.
4. Wait for Gradle to finish syncing. The first sync needs an internet connection.
5. Start an emulator (API 24 or higher) or plug in an Android phone with USB debugging turned on.
6. Press **Run ▶**.

To run the tests, right-click `RecipeMatcherTest` and choose **Run 'RecipeMatcherTest'**.

## Requirements

- Minimum SDK 24 (Android 7.0), target SDK 34
- Java 17, which comes with Android Studio
- No maps, GPS or location features are used, as the brief requires
