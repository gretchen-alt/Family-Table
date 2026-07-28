# Editing Recipes — The Family Table

All recipe content lives in one file: **`recipes-data.js`**. There are no more individual `.md` files per recipe — everything (ingredients, steps, notes) is in this one JavaScript file, and both `recipe.html` and `planner.html` read from it directly.

This means editing or adding a recipe never requires touching `recipe.html`, `planner.html`, or `style.css`. Those stay as-is.

---

## Editing an existing recipe

1. Open `recipes-data.js` in GitHub.
2. Find the recipe by its id, e.g. `"muhammara": {`.
3. Edit whatever you need — title, description, ingredients, steps, notes — and commit.

That's the whole process. The recipe page and the grocery planner both pick up the change automatically.

### Ingredient format

Each ingredient is one line like this:

```js
{ "amount": 1, "unit": "cup", "name": "walnuts, lightly toasted", "location": "Pantry", "category": "Nuts & Seeds" }
```

| Field | Notes |
|---|---|
| `amount` | A plain number. Halves and quarters are decimals — `0.5`, `0.25`, `0.333` — not fractions. It displays back as ½, ¼, ⅓ automatically on the recipe page. |
| `unit` | e.g. `"cup"`, `"tbsp"`, `"tsp"`, `"oz"`, `"lb"`, `"can"`. Use `null` if the ingredient is just counted (e.g. "3 garlic cloves") — put the counting word in `name` instead. |
| `name` | The ingredient description, including any prep note (e.g. `"garlic cloves, minced"`). |
| `location` | Where it lives in the house. Must be exactly one of: `Fridge`, `Freezer`, `Pantry`, `Counter`, `Spice Rack`. |
| `category` | The subgrouping used in the grocery planner. See the list below — reuse one exactly (spelling matters) rather than inventing a new one. |

**No real amount** (e.g. "salt and pepper to taste")? Set `"amount": null`.

**Plain water** or anything that shouldn't appear on a grocery list? Add `"skip": true` to that ingredient — the planner will ignore it. (Search `recipes-data.js` for existing `"skip": true` entries to see examples, like water in the tahini dressing or the granolas.)

### Existing categories

Use one of these for `category`, so ingredients group correctly in the planner:

`Produce` · `Dairy & Eggs` · `Milk & Alternatives` · `Protein` · `Frozen` · `Grains` · `Beans & Legumes` · `Nuts & Seeds` · `Dried & Freeze-Dried Fruit` · `Protein Powders & Supplements` · `Condiments & Sauces` · `Oils & Vinegars` · `Canned & Jarred` · `Baking & Sweeteners` · `Spices`

### Steps format

```js
{ "title": "Sauté the onion", "text": "In a wide pot..." }
```

Steps are just an ordered array — add, remove, reorder, or reword freely. `title` shows as the bold step label; `text` is the instruction body.

### Notes format

`notes` is a single string. For multiple paragraphs, separate them with `\n` inside the string — each becomes its own paragraph on the page.

---

## Adding a brand new recipe

Two steps — one in the data file, one on the homepage.

### 1. Add the recipe to `recipes-data.js`

Copy an existing recipe entry as a template and give it a new id — short, lowercase, hyphenated (e.g. `"lentil-koftas"`). This id becomes the URL: `recipe.html?r=lentil-koftas`.

Fill in:

```js
"your-new-id": {
  "title": "...",
  "description": "...",
  "origin": "...",
  "time": "...",
  "servings": 4,
  "instant_pot": false,
  "section": "Dinner",          // Breakfast, Stews, Sauces & Dips, Beans, or Dinner
  "tags": ["Dinner", "Vegetarian"],
  "ingredients": [ /* ... */ ],
  "steps": [ /* ... */ ],
  "notes": "..."
}
```

Don't forget the comma after the closing `}` if it's not the last entry in the file.

### 2. Add a card to `index.html`

Find the section matching your recipe (search for `<!-- DINNER -->` etc.), copy an existing `<a class="recipe-card">` block within it, and update:

- `href="recipe.html?r=your-new-id"` — must match the id from step 1
- the origin, title, and description text
- `data-tags` — space-separated tags used by the tag filter buttons
- `data-search` — space-separated keywords used by the search box (include ingredient names, origin, alternate names — anything someone might type)
- the card's time and tag pills in `card-footer`

That's it — the recipe page itself needs no separate file or edit; it looks up whatever id is in the URL automatically.

---

## A note on the grocery planner

The planner combines ingredients across recipes by matching on `name` + `unit`. If two recipes call the same ingredient slightly different things — `"garlic cloves, minced"` vs. `"garlic clove, minced"` — they won't merge into one line; they'll just appear as two separate entries on the list. Not a big problem, just something to keep half an eye on for common ingredients (garlic, onion, olive oil, salt) when writing new recipes — reusing the exact wording from an existing recipe when it's the same ingredient keeps the list clean.
