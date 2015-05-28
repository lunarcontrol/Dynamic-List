#!/usr/bin/env python

from flask import Flask, render_template, request, url_for, redirect
from flask.ext.sqlalchemy import SQLAlchemy

from datetime import date 

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///news.db'
db = SQLAlchemy(app)

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(255))
    link = db.Column(db.String(255))
    upvotes = db.Column(db.Integer)
    downvotes = db.Column(db.Integer)
    time = db.Column(db.Integer)

    def __init__(self, title, link):
        self.title = title
        self.link = link
        self.upvotes = self.downvotes = 0
        self.time = date.today().ctime() 

    def __repr__(self):
        return '<Post %r(%r)>' % (self.title, self.score())

    def score(self):
        try:
            return self.upvotes # purely based on upvotes right now
            #return (self.upvotes) / (self.upvotes + self.downvotes)
        except ZeroDivisionError:
            return 0

@app.route('/')
def index():
    posts = sorted(Post.query.all(), key=lambda x: x.score(), reverse=True)
    print posts
    return render_template('front.html', posts=posts)

@app.route('/submit', methods=['GET', 'POST'])
def submit():
    if request.method == 'POST':
        submission = Post(request.form['title'], request.form['link'])
        db.session.add(submission)
        db.session.commit()
        return redirect(url_for('index'))
    else:
        return render_template('submit.html')

@app.route('/vote/<int:pid>', methods=['GET'])
def vote(pid):
    direction = request.args.get('d','up')

    p = Post.query.filter_by(id=pid).all()[0]
    if direction == 'up':
        p.upvotes += 1
    elif direction == 'down':
        p.downvotes += 1

    db.session.add(p)
    db.session.commit()
    return redirect(url_for('index'))


if __name__=='__main__':
    import sys
    if '--create-db' in sys.argv:
        db.create_all()
        sys.exit(0)
    app.run(debug=True)
