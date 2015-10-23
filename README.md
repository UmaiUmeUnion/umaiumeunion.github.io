# UmaiUme webpages

This is the source of UmaiUme's webpages made with the help of Jekyll.

## How to edit (for contributors)

### Writing dev news (posts)
Just create a new .md file into `_posts` folder. Use the existing posts as examples.
Full GitHub flavoured Markdown is supported, but you can also add HTML and Liquid tags.
**Rember to add the front matter (2x --- on separate lines) in the beginning**. Otherwise Jekyll will not render the files.

### Adding items to the navbar
Edit `header.html` in `_includes` folder. Use existing links as examples and guides to Bootstrap and Jekyll for more advanced commands.

### Creating a new tutorial
1. Create a new collection in `_config.yml` (use the definition of `guides_reipatcher`) as example.
2. Create the folder for tutorial posts (again, use `guides_reipatcher` as an example).
3. Create a new layout in `_layouts` folder for your guide. Use `guiderei.html` layout as an example. Especially remember to replace `site.guides_reipatcher` with the appropriate tutorial collection.
4. Create your first post in the new folder. Remember to add reference the layout in the front matter. You can use ReiPatcher's guide as an example.

### Creating new pages (generally)
You can either create pages with any styling language Jekyll can process. Use existing pages as examples.
