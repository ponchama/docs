# W-DN-17002 User Maunals

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

After the changes have been completed run the command "mkdocs gh-deploy". This updates the gh-pages branch which is the one github pages reads the documentation from.
