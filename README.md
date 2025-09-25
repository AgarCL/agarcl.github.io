# AgarCL-docs

This repository contains the documentation for [AgarCL](https://github.com/AgarCL/AgarCL).

## Documentation

The documentation theme is based on the [Farama Foundation documentation theme](https://github.com/Farama-Foundation/Celshast?tab=readme-ov-file#egg=furo) and on the [Furo template](https://github.com/pradyunsg/furo).

## Instructions for editing the documentation

The stucture of the repository is as follows:

```
root/  
├── README.md 
├── docs/                   # source files are here
│   ├── _build/             # html pages are being built here
│   ├── _static/            # put your imgs, gifs, etc here
│   ├── .doctrees
│   ├── introduction/       # .md sources of your future .html pages
│   ├── conf.py             # configs for building
│   ├── index.md            # .md sources of your index.html
│   ├── Makefile        
│   ├── README.md           # README for building the .html pages
│   └── requirements.txt    # requirements for building the .html pages
├── _images/
├── _static/
├── genindex/
├── introduction/
├── README/
├── search/
├── .nojekyll               # disables Jekyll so _static is served
├── index.html
├── objects.inv
└── searchindex.js
```

1. If you want to make any changes, go to `docs/`. 
2. Edit only the Markdown `docs/*.md` files for content updates.
3. Add any new images or other assets under `docs/_static/`. If you need to tweak layout, styling or build options, edit `docs/conf.py` and `docs/Makefile`.

4. Then follow `docs/README.md` for rebuilding the `*.html` pages.

5. Once the build finishes, copy everything from `docs/_build/dirhtml` into your repo’s root directory (overwriting the old files, but do not overwrite the `docs/`, you will lose everything).