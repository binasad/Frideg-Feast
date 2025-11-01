 # Fridge Feast

Fridge Feast is a lightweight web app that generates recipe suggestions from ingredients you have on hand using AI-assisted flows.

Key concepts
- UI entry: [`RecipeGenerator`](src/components/app/recipe-generator.tsx)
- Ingredient input: [`IngredientManager`](src/components/app/ingredient-manager.tsx)
- Recipe list / cards: [`RecipeList`](src/components/app/recipe-list.tsx), [`RecipeCard`](src/components/app/recipe-card.tsx)
- Server/AI action: [`getRecipesAction`](src/lib/actions.ts) → [`generateRecipesFromIngredients`](src/ai/flows/generate-recipes-from-ingredients.ts)
- Toasts: [`Toaster`](src/components/ui/toaster.tsx) and hook [`useToast`](src/hooks/use-toast.ts)
- API proxy for geocoding (server-side): [src/pages/api/google-geocode.ts](src/pages/api/google-geocode.ts)

Architecture overview
- Next.js app (app directory) — see [`src/app/page.tsx`](src/app/page.tsx) and [`src/app/layout.tsx`](src/app/layout.tsx).
- UI primitives and components under [`src/components/ui`](src/components/ui).
- App-specific components under [`src/components/app`](src/components/app).
- AI flows implemented with Genkit in [`src/ai/flows`](src/ai/flows):
  - [`generateRecipesFromIngredients`](src/ai/flows/generate-recipes-from-ingredients.ts)
  - [`summarizeRecipeInstructions`](src/ai/flows/summarize-recipe-instructions.ts)
- Shared utilities: [`cn`](src/lib/utils.ts), placeholder images [`src/lib/placeholder-images.ts`].

Getting started (dev container / local)
1. Copy env example and add keys
   - Copy `.env.example` to `.env.local` and set at least:
     - `GOOGLE_API_KEY` (used by the proxy API route: [src/pages/api/google-geocode.ts](src/pages/api/google-geocode.ts))
2. Install dependencies
```bash
npm install
```
3. Run dev
```bash
npm run dev
```
- The dev container config includes a web preview command in `.idx/dev.nix`.
- Production build:
```bash
npm run build
npm start
```

How it works (brief)
- The client app collects an ingredients list via [`IngredientManager`](src/components/app/ingredient-manager.tsx).
- When you generate recipes, the client calls the server action [`getRecipesAction`](src/lib/actions.ts), which invokes the AI flow [`generateRecipesFromIngredients`](src/ai/flows/generate-recipes-from-ingredients.ts).
- Results are rendered via [`RecipeList`](src/components/app/recipe-list.tsx) and [`RecipeCard`](src/components/app/recipe-card.tsx).
- User feedback is surfaced through the toast system (`[`Toaster`](src/components/ui/toaster.tsx)` + [`useToast`](src/hooks/use-toast.ts)).

Important files
- Project entry: [`src/app/page.tsx`](src/app/page.tsx)
- Main generator UI: [`src/components/app/recipe-generator.tsx`](src/components/app/recipe-generator.tsx)
- Ingredients manager: [`src/components/app/ingredient-manager.tsx`](src/components/app/ingredient-manager.tsx)
- Recipe generation flow: [`src/ai/flows/generate-recipes-from-ingredients.ts`](src/ai/flows/generate-recipes-from-ingredients.ts)
- AI client config: [`src/ai/genkit.ts`](src/ai/genkit.ts)
- Toast hook: [`src/hooks/use-toast.ts`](src/hooks/use-toast.ts)
- Tailwind config: [`tailwind.config.ts`](tailwind.config.ts)
- Next config: [`next.config.ts`](next.config.ts)
- Utility helpers: [`src/lib/utils.ts`](src/lib/utils.ts)

Notes & tips
- To avoid exposing keys, the Google geocode usage is proxied via [src/pages/api/google-geocode.ts](src/pages/api/google-geocode.ts).
- UI components are built with Radix primitives and Tailwind classes; see the UI folder: [`src/components/ui`](src/components/ui).
- Placeholder images are stored in [`src/lib/placeholder-images.ts`](src/lib/placeholder-images.ts) and its JSON.

Development utilities
- There's a small dev loader that imports AI flows: [`src/ai/dev.ts`](src/ai/dev.ts). Use it to validate flow registration locally.

Contributing
- Fork, add tests, and open a PR. Follow existing component patterns under [`src/components/ui`](src/components/ui) and app components under [`src/components/app`](src/components/app).
// ...existing code...

## How it works

This section explains the runtime flow from user input to generated recipes and where key pieces live.

1. User input
   - The UI collects ingredients and optional preferences via the IngredientManager component (src/components/app/ingredient-manager.tsx).
   - The main interaction happens in RecipeGenerator (src/components/app/recipe-generator.tsx).

2. Client → Server
   - When the user requests recipes, the client invokes the server action getRecipesAction (src/lib/actions.ts).
   - The action validates input and forwards a request to the AI layer.

3. AI flows
   - The AI client is configured in src/ai/genkit.ts.
   - The main flow generateRecipesFromIngredients (src/ai/flows/generate-recipes-from-ingredients.ts) takes the ingredients and produces:
     - a set of candidate recipes with titles, ingredients, and instructions
     - optional metadata such as dietary labels and estimated prep times
   - Optionally, summarizeRecipeInstructions (src/ai/flows/summarize-recipe-instructions.ts) is used to produce condensed step-by-step instructions or shopping lists.

4. Server → Client
   - The server action returns the generated recipes to the client.
   - The client renders results using RecipeList (src/components/app/recipe-list.tsx) and RecipeCard (src/components/app/recipe-card.tsx).

5. UX & feedback
   - Toaster and useToast (src/components/ui/toaster.tsx, src/hooks/use-toast.ts) display progress, errors, and success messages.
   - Placeholder images are provided from src/lib/placeholder-images.ts when recipe images are not available.

6. External APIs & security
   - Any external geocoding requests use a server-side proxy (src/pages/api/google-geocode.ts) so API keys (e.g., GOOGLE_API_KEY) are never exposed to the browser.
   - Set environment variables in .env.local (copy from .env.example) before running locally.

7. Developer flow
   - Use src/ai/dev.ts to load and validate AI flows during development.
   - Components and UI primitives live under src/components/ui and src/components/app to keep presentation and logic separated.

<img width="1348" height="518" alt="fridgefeast" src="https://github.com/user-attachments/assets/cf314e6a-dd8b-40d8-9304-2b1435c9b0db" />

## Demo

A short demo video is available on YouTube:

- Demo video:  
  [https://www.youtube.com/watch?v=VIDEO_ID](https://youtu.be/xpJH4iNzAkQ)








