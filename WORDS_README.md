# Word List Configuration

## Overview
The `words.json` file contains all the word lists for each rainbow level. This file can be edited to customize the words without touching any code!

## File Format

```json
{
  "version": "1.0",
  "levels": [
    {
      "name": "Red",
      "color": "#ef4444",
      "badge": "🍓",
      "words": ["I", "said", "the", ...]
    }
  ]
}
```

## Editing the Word Lists

### To Add Words
1. Open `words.json`
2. Find the level you want to edit
3. Add words to the `words` array
4. Make sure to use proper JSON format (commas between words, quotes around each word)
5. Save and commit

### To Remove Words
1. Open `words.json`
2. Find the word you want to remove
3. Delete it (including the comma if needed)
4. Save and commit

### To Change Word Order
1. Open `words.json`
2. Rearrange words in the array
3. Save and commit

### To Add a New Level
1. Copy an existing level block
2. Change the name, color, badge, and words
3. Add it to the `levels` array
4. Save and commit

## Important Rules

### JSON Syntax
- ✅ Use double quotes `"` not single quotes `'`
- ✅ Add commas between array items
- ✅ NO comma after the last item in an array
- ✅ All words must be in quotes
- ✅ Validate JSON before committing (use jsonlint.com)

### Examples

#### ✅ Correct
```json
{
  "words": ["cat", "dog", "bird"]
}
```

#### ❌ Wrong (missing quotes)
```json
{
  "words": [cat, dog, bird]
}
```

#### ❌ Wrong (extra comma)
```json
{
  "words": ["cat", "dog", "bird",]
}
```

#### ❌ Wrong (single quotes)
```json
{
  'words': ['cat', 'dog', 'bird']
}
```

## Level Colors

Current rainbow colors (hex codes):
- **Red**: #ef4444
- **Orange**: #f97316
- **Yellow**: #eab308
- **Green**: #22c55e
- **Blue**: #3b82f6
- **Purple**: #a855f7
- **Pink**: #ec4899
- **Light Blue**: #38bdf8

## Badge Emojis

Current badges:
- 🍓 Red
- 🍊 Orange
- 🐤 Yellow
- 🍀 Green
- 💧 Blue
- 🔮 Purple
- 🦄 Pink
- ☁️ Light Blue

Feel free to change these to any emoji!

## Testing Your Changes

After editing `words.json`:
1. Commit and push to GitHub
2. Wait 1-2 minutes for GitHub Pages to update
3. Refresh the app (may need hard refresh: Ctrl+Shift+R or Cmd+Shift+R)
4. Check that words appear correctly
5. If you see an error, check the browser console for details

## Creating Custom Word Lists

You can create additional word list files for different purposes:

### Spanish Words
Create `words-spanish.json` with Spanish sight words

### Phonics Words
Create `words-phonics.json` with phonics-focused words

### Custom Lists
Create `words-custom.json` for any custom word set

Then update `index.html` to load from the different file by changing:
```javascript
const response = await fetch('words-spanish.json');
```

## Troubleshooting

### "Error loading word list"
- Check that `words.json` exists in the repository
- Validate JSON syntax at jsonlint.com
- Check browser console for specific error
- Make sure file is committed and pushed to GitHub

### Words not updating
- Hard refresh the page (Ctrl+Shift+R or Cmd+Shift+R)
- Clear browser cache
- Wait a few minutes for GitHub Pages to deploy
- Check that changes are pushed to main branch

### JSON validation errors
- Use jsonlint.com to validate your JSON
- Check for missing/extra commas
- Check for missing quotes
- Check for trailing commas

## Need Help?

If you're not comfortable editing JSON, you can:
1. Open an issue on GitHub with the words you want to change
2. Someone can make the changes for you
3. Or use the GitHub web interface to edit (it has syntax highlighting!)

Happy word list editing! 🌈
