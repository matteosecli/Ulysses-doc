# Ulysses-doc

The missing documentation for the cluster Ulysses @ SISSA


## How to contribute

**TODO:** first set up CI and then write a few words.


## How to build the documentation

The documentation is automatically built and published at [https://ulysses.readthedocs.io/](https://ulysses.readthedocs.io/).

If you want to build it locally on your machine, first install the prerequisites via the terminal (assuming you have Python):
```console
pip install sphinx recommonmark sphinx_rtd_theme
```

Then, in order to download and build, and do the following:

```console
git clone https://github.com/matteosecli/Ulysses-doc.git
cd Ulysses-doc/doc
make html
```

If all goes well, you can then open `build/html/index.html` to browse the built docuemntation.
