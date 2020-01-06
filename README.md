# W-DN-17002 User Manuals

[![Build Status](https://travis-ci.org/waterlinked/docs.svg?branch=master)](https://travis-ci.org/waterlinked/docs)

We are using mkdocs to manage our documentation.

# Contributing

We're really happy if you want to contribute to make the documentation better!
This is done by creating a pull request.

1. Download, install dependencies

```
git clone --recursive https://github.com/waterlinked/docs
cd docs

virtualenv venv
source ven/bin/activate
pip install -r requirements.txt

mkdocs serve
```

2. Make changes using your favorite editor

3. Test them

* Fire up your browser and go to localhost:8000

## Deploy changes to server

After the changes have been tested and they work, push the changes to the master branch and Github will do the rest of the job for you!
