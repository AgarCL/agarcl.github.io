# AgarCL-docs

This folder contains the documentation for [AgarCL](https://github.com/AgarCL/AgarCL).

## Documentation

The documentation theme is based on the [Farama Foundation documentation theme](https://github.com/Farama-Foundation/Celshast?tab=readme-ov-file#egg=furo) and on the [Furo template](https://github.com/pradyunsg/furo).

## Instructions for modification

Install the required packages :

```
pip install -r requirements.txt
```

To build the documentation once:

```
make dirhtml
```

To rebuild the documentation automatically every time a change is made:

Install `sphinx-autobuild`:

```
pip install sphinx-autobuild
```

Build and serve using:

```
sphinx-autobuild -b dirhtml . docs
```

You can then open http://localhost:8000 in your browser to watch a live updated version of the documentation.
