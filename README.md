# webapp-docker-k8s

## To-do app (Flask + MongoDB)

Create a virtual environment, install the dependencies and run the app
```bash
$ python -m venv .venv
$ source .venv/bin/activate
$ pip install -r requirements.txt
```
```bash
$ python app.py
 * Serving Flask app 'app'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:5000
```

[Install MongoDB](https://www.mongodb.com/docs/v4.2/tutorial/install-mongodb-on-os-x/) and run it
```bash
$ brew services start mongodb-community@4.4
==> Successfully started `mongodb-community@4.4` (label: homebrew.mxcl.mongodb-community@4.4)
```
