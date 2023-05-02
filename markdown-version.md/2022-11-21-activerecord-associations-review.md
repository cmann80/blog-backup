---
title: "ActiveRecord Associations Review"
date: "2022-11-21"
categories: 
  - "software-engineering"
---

Deciding how to associate database tables with each other can be an intense part of early project planning, especially when considering future growth. To help software engineeers focus on the important, big picture questions, ActiveRecord has many pre-built ways to associate database tables with each other. In this blog I will present a few database schemas that would be necessary for common kinds of web applications and which ActiveRecord association macros to use.

## A list of user-created objects

Be they recipes, movie reviews, arts and crafts for sale, plans to take over the earth or anything else, users love creating things and putting them on the internet. This behavior can be represented by a one-to-many table association, where a user has many objects, but the objects each have only one user. We can accomplish this in Rails by first creating a User model with the following association macro:

```
class User < ApplicationRecord
  has_many :world_conquest_plans

end
```

This simple declarative statement shows the app that we intend for one user to have many ways to take over the world. We can get away with not describing _how_ the database will accomplish this because ActiveRecord is a _declarative_ web application framework, not an _imperative_ language like Javascript or most others. Likewise, on the ways to take over the world side, the code looks like this:

```
class WorldConquestPlan < ApplicationRecord
  belongs_to :user

end
```

Every plan to conquer the world belongs to one user, but only to them. Notice especially how in the first code snippet, world\_conquest\_plan is plural but in the second, user is singular. This naming convention is enforced by ActiveRecord, and you need to follow it as well. Incidentally, ActiveRecord already knows the appropriate spelling patterns of English, like that "flies" is the plural of "fly".

## Events and schedules

Many web apps need to create a relationships between users and some kind of service they offer, such as college courses, events, or appointments. Many of them, such as an advising schedule between students and professors at a university, involve many to many relationships that can be hard to keep track of. Things can be made simpler with _join table_ which has reference to one element of the other two tables. In terms of student/instructor advising scheduling, the student can be set up like this:

```
class Student < ApplicationRecord
  has_many :meetings
  has_many :instructors, through: :meetings

end
```

The instructor model is the opposite:

```
class Instructor < ApplicationRecord
  has_many :meetings
  has_many :students, through: :meetings

end
```

The fact that instructors will have far, far more students than students have instructors is not important here, only that they have a many to many relationship with each other.

The meeting model looks very different than the others:

```
class Meeting < ApplicationRecord
  belongs_to :student
  belongs_to :instructor
end
```

This indicates that the meeting involves only one student and one instructor at a time. Once this relationship is established, you can create queries that reveal important trends, such as which instructors get visited the most for help, or which students make the most use of instructor time.

## What about has\_and\_belongs\_to\_many?

In the situation above, the relationship between students and instructors (or almost any other many to many relationship), I did not use Rails' has\_and\_belongs\_to\_many association macro, which automatically creates a join table that allows relationships between each element of the other tables. I searched and searched for examples where it would be superior to making a join table of your own, but failed to find any. The problem is that conceptually, the join table it creates (which will be automatically given a name based on the two other tables being joined) doesn't represent anything. Imagine the example above, but created that way:

```
class Instructor < ApplicationRecord
  has_and_belongs_to_many: students

end
```

It would create a schema that looks like this:

```
  create_table "student_instructor", force: :cascade do |t|
    t.integer "student_id", null: false
    t.integer "instructor_id", null: false
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end
```

Just what is a "student\_instructor"? Even if you know that it just refers to the connection between students and instructors, future software engineers who look at your work may not. If you decide to put extra data in this table, such as meeting times, it will only add to the confusion.

## Conclusion

Rails makes it easy to just think about the highest level issues in table associations and queries. Besides choosing tables and associations that mean something to you and represent tangible things in the world, make sure to make choices that future people who look at your tables will be able to read and expand on. The world is a neverending deluge of new data and as a database engineer, your job is to bring order to it even in the face of future data the likes of which you can't imagine.
