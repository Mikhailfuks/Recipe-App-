using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;

public class Recipe
{
    public string Name { get; set; }
    public List<string> Ingredients { get; set; }
    public List<string> Instructions { get; set; }

    public override string ToString()
    {
        return $"Recipe: {Name}";
    }

    public string ToDetailedString()
    {
       string detailed =  $"Recipe: {Name}\nIngredients:\n";
        foreach (var ingredient in Ingredients)
         {
             detailed += $" - {ingredient}\n";
         }

       detailed += "Instructions:\n";
        foreach(var instruction in Instructions)
        {
           detailed += $" - {instruction}\n";
        }
        return detailed;
    }
}

public class RecipeApp
{
    private static readonly string DATA_FILE = "recipes.txt";

    private static List<Recipe> recipes = new List<Recipe>();


    public static void AddRecipe()
    {
        Console.WriteLine("Enter recipe name:");
        string name = Console.ReadLine();

        List<string> ingredients = new List<string>();
        while (true)
        {
             Console.WriteLine("Enter an ingredient (or type 'done' to finish):");
             string ingredient = Console.ReadLine();
            if (ingredient.ToLower() == "done") break;
             ingredients.Add(ingredient);
        }


        List<string> instructions = new List<string>();
        while (true)
         {
            Console.WriteLine("Enter an instruction (or type 'done' to finish):");
             string instruction = Console.ReadLine();
             if (instruction.ToLower() == "done") break;
             instructions.Add(instruction);
         }


        Recipe recipe = new Recipe
        {
            Name = name,
            Ingredients = ingredients,
            Instructions = instructions
        };


        recipes.Add(recipe);
        Console.WriteLine("Recipe added successfully!");
        SaveRecipes();
    }


    public static void DisplayRecipeList()
    {
         if (recipes.Count == 0) {
            Console.WriteLine("No recipes found.");
            return;
         }
        Console.WriteLine("Recipe List:");
        for (int i = 0; i < recipes.Count; i++)
        {
            Console.WriteLine($"{i + 1}. {recipes[i]}");
        }
    }


    public static void DisplayRecipeDetails()
    {
        Console.WriteLine("Enter recipe number to view details:");
        if(!int.TryParse(Console.ReadLine(), out int recipeNumber) || recipeNumber < 1 || recipeNumber > recipes.Count)
         {
            Console.WriteLine("Invalid recipe number.");
             return;
          }

         Console.WriteLine(recipes[recipeNumber-1].ToDetailedString());
    }

    public static void SearchRecipesByIngredients()
    {
        Console.WriteLine("Enter ingredients to search (comma separated):");
        string ingredientsSearchString = Console.ReadLine();

         string[] searchIngredientsArray = ingredientsSearchString.Split(",").Select(str => str.Trim()).ToArray();


        var searchResults = recipes.Where(recipe => searchIngredientsArray.All(ing => recipe.Ingredients.Any(recipeIng => recipeIng.Contains(ing, StringComparison.OrdinalIgnoreCase)))).ToList();


          if (searchResults.Count == 0) {
            Console.WriteLine("No matching recipes found.");
            return;
          }

         Console.WriteLine("Matching Recipes:");
          for(int i = 0; i < searchResults.Count; i++)
         {
            Console.WriteLine($"{i + 1}. {searchResults[i]}");
        }

    }

     public static void SaveRecipes()
     {
        try {
          List<string> lines = new List<string>();
          foreach(var recipe in recipes)
          {
             lines.Add(recipe.Name);
              lines.Add(string.Join(",", recipe.Ingredients));
              lines.Add(string.Join("###", recipe.Instructions));
           }

          File.WriteAllLines(DATA_FILE, lines);

        } catch (Exception ex)
        {
            Console.WriteLine($"An error occurred saving recipes: {ex.Message}");
         }
     }

     public static void LoadRecipes()
    {
        try
        {
            if (File.Exists(DATA_FILE)) {

              string[] lines =  File.ReadAllLines(DATA_FILE);

            for (int i = 0; i < lines.Length; i += 3)
            {

                string name = lines[i];
                string[] ingredientsArray = lines[i + 1].Split(',');
                List<string> ingredients = ingredientsArray.ToList();
                 string[] instructionsArray = lines[i + 2].Split("###");
                 List<string> instructions = instructionsArray.ToList();

                Recipe recipe = new Recipe { Name = name, Ingredients = ingredients, Instructions = instructions };
                recipes.Add(recipe);
            }
         }


        } catch (Exception ex)
        {
              Console.WriteLine($"An error occurred loading recipes: {ex.Message}");
        }

    }
    public static void Main(string[] args)
    {
        LoadRecipes();


        while (true)
        {
            Console.WriteLine("\nMenu:");
            Console.WriteLine("1. Add Recipe");
            Console.WriteLine("2. List Recipes");
            Console.WriteLine("3. View Recipe Details");
            Console.WriteLine("4. Search Recipes by Ingredients");
             Console.WriteLine("5. Exit");

            Console.WriteLine("Enter your choice:");
            if (!int.TryParse(Console.ReadLine(), out int choice)) {
                Console.WriteLine("Invalid input. Please enter a number.");
                continue;
            }
            switch (choice)
            {
                case 1:
                    AddRecipe();
                    break;
                case 2:
                    DisplayRecipeList();
                    break;
                case 3:
                    DisplayRecipeDetails();
                    break;
                case 4:
                    SearchRecipesByIngredients();
                    break;
                 case 5:
                     Console.WriteLine("Exiting...");
                     return;
                default:
                   Console.WriteLine("Invalid choice.");
                    break;
            }
        }
    }
}
