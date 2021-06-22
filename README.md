1. Create docker container of postgresql
```
---
services:
  databases:
    image: postgres
    volumes:
      - /home/user/docker_compose/pgdata:/var/lib/postgresql/data/
    ports:
      - "5432:5432"
    restart: always
    environment:
      - POSTGRES_DB=demodb
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
...
```
2. mkdir pgdata
3. docker-compose up -d
4. docker-compose ps
5. mkdir flask_db
6. cd flask_db
7. touch app.py
8. touch db_ops.py
9. vim app.py
```
rom flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://postgres:postgres@172.17.0.1:5432/demodb'
app.config['SECRET_KEY'] = "secret"

db = SQLAlchemy(app)

class Students(db.Model):
    id = db.Column('student_id', db.Integer, primary_key = True)
    name = db.Column(db.String(100))
    city = db.Column(db.String(50))

    def __init__(self, name, city):
        self.name = name
        self.city = city
```
10.  vim db_ops.py
```
from app import db, Students


if not db.engine.has_table('students'):
    db.create_all()

db.session.add(Students('John', 'New York'))
db.session.add(Students('Harry', 'California'))
db.session.add(Students('Kylie', 'Hamburg'))
db.session.commit()

# op = Students.query.all()
# for val in op:
#     print(val.id, val.name, val.city)

```
11.  python3 db_ops.py
12.  psql -h 172.17.0.1 -p 5432 -d demodb -U postgres
