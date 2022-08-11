# Documentation Setup

To develop documentation locally, you need to install `mkdocs` which is a Python package. You can create a Python virtual enviroment using [Anaconda Navigator](https://www.anaconda.com/products/distribution).

Next create a new virutal env named USAHello-docs and initialize the env.

Then install mkdocs in conda
```sh
conda install -c conda-forge mkdocs
```

## MKdocs
Navigate to the `docs/` folder of this repository and run:

```sh
mkdocs build
```

to build the documentation into a browseable HTML format that can be uploaded to a server, or run:

```sh
mkdocs serve
```

if you want to run a local version of the documentation that will be available at: [http://localhost:8005](http://localhost:8005). This will live-reload any changes.

MKdocs Commands

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.

## Repo & Hosting
This project is housed in [GitHub](https://github.com/usahello/usahello-docs) and hosted as a static site on Netlify.
