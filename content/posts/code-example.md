---
title: "Code Example"
date: 2023-07-25T23:11:54-07:00
---

```python {linenos=table,hl_lines=["22-33","43-45"],linenostart=1}
#!/usr/bin/env python
from flask import Flask, render_template, request, redirect, url_for
import csv

app = Flask(__name__)
fieldnames = ['author', 'idea']
database = 'programs.csv'


@app.route("/")
@app.route("/index.html")
def home():
    return render_template('patrol.html')


@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)


@app.route('/program')
def list_programs():
    programs = []
    try:
        with open(database) as csvfile:
            csvreader = csv.DictReader(csvfile, fieldnames=fieldnames)
            for row in csvreader:
                programs.append(row)
    except FileNotFoundError:
        pass

    return render_template('program.html', programs=programs)


@app.route('/program/new', methods=['POST', 'GET'])
def new_program():
    if request.method == 'POST':
        program = {
            'author': request.form['author'],
            'idea': request.form['idea']
        }
        with open(database, 'a') as csvfile:
            csvwriter = csv.DictWriter(csvfile, fieldnames=fieldnames)
            csvwriter.writerow(program)

        return redirect(url_for('list_programs'))
    else:
        return render_template('new_program.html')


if __name__ == "__main__":
    app.run(debug=True, port=80, host="0.0.0.0")
```
