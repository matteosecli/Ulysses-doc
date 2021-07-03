# Ulysses-doc

The missing documentation for the cluster Ulysses @ SISSA


## How to contribute

This guide is written in reStructuredText (reST), a markup language (like e.g. Markdown). There's no need to know it in advance; you can just get started right away by looking at the existing syntax and you'll be writing like a pro in no time. In any case, when in doubt, you can have a look at the [reST Primer](https://docutils.sourceforge.io/docs/user/rst/quickstart.html) or at the [Quick Reference](https://docutils.sourceforge.io/docs/user/rst/quickref.html).

If you've noticed a typo, some outdated info or if you just want to quickly fix something or contribute with new info, you can just go ahead and edit the culprit file directly from GitHub. GitHub's workflow is fully walked-through and user-friendly, so you don't need to have any knowledge of Git; just have a look at their short guide about [editing files in another user's repository](https://docs.github.com/en/github/managing-files-in-a-repository/managing-files-on-github/editing-files-in-another-users-repository). If you are a more experienced Git user, feel free to fork the repo, make any edits you wish, and then open a pull request.

When you create the pull request, our continuous integration system will generate a new version of the HTML documentation in order to check your contribution, but it will not be published yet on the website. In order to take a look at it, click on the green tick (✔️) next to the latest commit in your pull request (a green tick indicates that your contribution built correctly; it'll be an orange dot while the compilation step is in progress or a red cross if there are issues). In the page that opens, click again on "CI/build" to be able to see the details of the automatic building step. Finally, by clicking on "Artifacts -> ulysses-doc-HTML" in the top-right menu, if the building step was successful you'll be able to download the built documentation with your contribution. Just unzip it locally and open "index.html" to preview the result of your contribution.


## How to build the documentation

The documentation is automatically built and published at [https://ulysses.readthedocs.io/](https://ulysses.readthedocs.io/).

If you want to build it locally on your machine, first install the prerequisites via the terminal (assuming you have Python):
```console
pip install sphinx recommonmark sphinx_rtd_theme
```

Then, in order to download and build, do the following:

```console
git clone https://github.com/matteosecli/Ulysses-doc.git
cd Ulysses-doc/doc
make html
```

If all goes well, you can then open `build/html/index.html` to browse the built docuemntation.
