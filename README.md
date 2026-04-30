# Requirements
You need to install a recent version of Python and
[MkDocs](https://www.mkdocs.org/getting-started/) (the version used is 1.6.1).
To install said package, you can simply use the following command:
```
pip install -r requirements.txt
```

# Developing
If you want to modify the docs, I recommend launching the server like this:
```
python -m mkdocs serve --livereload
```
Then open up http://127.0.0.1:8000/ in your browser. This way, if you modify the
documentations, your browser will automatically reload to show you the changes.

# Style conventions
In order to make a homogeneous documentation, we need to stick to some style
and conventions:

## Column limit
Each line should be no longer than 80 columns, unless it's inevitable, like when
you write tables or very long URLs.

## Doc structure
If you want to add documentation on file formats or lists of constants, add it
in their respective sections (`formats.md` and `constants.md`). If you want to
explain generic concepts like how items work, or how events work, make another
section (in other words, another `.md` file in the `docs` folder) called
`items.md` (which already exists) or `events.md`.

# Advices
To create tables faster, I suggest using the site
https://www.tablesgenerator.com/markdown_tables.

# Contributions
The whole idea of this documentation is incorporating new discoveries by anyone
in the community. Don't hesitate to open a PR with your changes, or creating an
issue. Make sure to read the style conventions above.