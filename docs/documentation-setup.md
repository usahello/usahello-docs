# Documentation Setup

## Repo & Hosting
This project is built using [MKdocs markdown](https://www.mkdocs.org/getting-started/?#getting-started-with-mkdocs), housed in [GitHub](https://github.com/usahello/usahello-docs), and hosted as a static site on [Netlify](https://app.netlify.com/sites/usahello-docs/overview).

To develop documentation locally, you need to install `mkdocs` which is a Python package. You can create a Python virtual enviroment using [Anaconda Navigator](https://www.anaconda.com/products/distribution).

Next create a new virutal env named USAHello-docs and initialize the env.

Then install mkdocs in conda
```sh
conda install -c conda-forge mkdocs
```

Clone the repo from [GitHub](https://github.com/usahello/usahello-docs)

Navigate to the `docs/` folder of this repository and run:

```sh
mkdocs build
```

This will build the documentation into a browseable HTML format that can be uploaded to a server or run.


If you want to run a local version of the documentation that will live-reload any changes run:
```sh
mkdocs serve
```
The dev site is availabe at [http://localhost:8005](http://localhost:8005).


## Deployment
To deploy your changes simply run:
```sh
mkdocs build
```
And then push your changes to the GitHub repository.
Netlify will then integrate the changes and build the site.

