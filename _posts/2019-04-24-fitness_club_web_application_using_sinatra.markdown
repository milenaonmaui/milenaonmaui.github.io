---
layout: post
title:      "Fitness Club Web Application Using Sinatra"
date:       2019-04-25 02:49:26 +0000
permalink:  fitness_club_web_application_using_sinatra
---


Since I am a big fan of fitness studios, even though I don't have time to visit them, I decided to make a website for a fitness club, where instructors can manage their classes - add, edit, deletea class or view number of students, and students can sign up for a class or cancel it if already signed up.

So my models are Student, Instructor and a GroupClass (I didn't want to use the reserved keyword Class). An instructor can have many group classes and a group class belongs to an instructor. Instructor also has many students through groupclasses. So far, it is pretty straighforward:

```
class Instructor < ActiveRecord::Base
    has_many :groupclasses
    has_many :students, through: :groupclasses
end
```
class Groupclass < ActiveRecord::Base
    belongs_to :instructor
end

However, when it comes to students and groupclasses, the relationship is many-to-many. A groupclass can have many students and a student can have many groupclasses. So I had to add the join table groupclass_students which has two columns - groupclass_id and student_id.

So, my the affected Groupclass and Students  models evolved into:
```
class Groupclass < ActiveRecord::Base
    belongs_to :instructor
    has_many :groupclass_students
    has_many :students, through: :groupclass_students
end
```
and

```
class Student < ActiveRecord::Base
    has_many :groupclass_students
    has_many :groupclasses, through: :groupclass_students
end
```

To test my models I seeded the database with some data and in rake console tried the following: 

```
pry(main)> Groupclass.all.first.students
=> [#<Student:0x09319814
  id: 3,
  name: "Sophia",
  email: "sophia@yahoo.com",
  password_digest:
   "$2a$10$CCJaajGckn0jAZKLHsfJluf/aHZyotLWOHAAnHrPqe58qs2qxjUhW">,
 #<Student:0x09319760
  id: 4,
  name: "Violet",
  email: "violet@yahoo.com",
  password_digest:
   "$2a$10$7hhTrQkZQyxwLbgaB9RUfOTjHrzmQmWG5utH4joT90xuYkobFWjpu">,
 .....
 etc.
```
Successfully got all the students for my first groupclass.

Next, try in opposite direction:

```
pry(main)> Student.all.first.groupclasses
=> [#<Groupclass:0x09feb844
  id: 2,
  name: "Vinyasa",
  class_type: "yoga",
  max_students: 20,
  instructor_id: 4>,

.....
etc.

```
So that worked really well. In addition to that for an Instructor instance, say patty, I can get all groupclasses let by her:
```
pry(main)> patty.groupclasses
=> [#<Groupclass:0x0a0a15a4
  id: 7,
  name: "Cardio Barre",
  class_type: "Barre",
  max_students: 10,
  instructor_id: 3>,
  ....
	etc.

```

and vice-versa, for a groupclass instance cardio_barre, I can get the instructor:
```
pry(main)> barre.instructor
=> #<Instructor:0x091470a4
 id: 3,
 name: "Patty",
 email: "patty@yahoo.com",
 password_digest:
  "$2a$10$9ftRDRoB6FS0HnX/0Vs5KueJI.dsg6MTzdN1aUZzQfthrHSg0Lvt6",
 picture_url: nil>

```

Getting the models and getting the associations right was one of the most challenging parts of the project and I stumbled a few times and had to rollback migrations and migrate again. In my Groupclass model, I had an attribute "type" which was meant for the type of class - yoga, spin, barre, etc. However using this Ruby keyword caused some unforeseen conseqeunces, such as not being able to use the #groupclasses method for an instructor. So I had to change the attribute name to class_type, and that fixed several problems at once.

Another challenge for me was validations and displaying of validation messages. 

Some validations can be done on the client side and provide immediate feedback to the user instead of waiting for the server to process the data and return response.

For example, empty fields in a form can be prevented using the HTML required attribute such as in:

```
<input type="text" name="name" id="name" required>
```
The form cannot be submitted if a required field is blank.

Another validation that I wanted to do on the client side was password confirmation. If the password was mistyped, it seems wasteful to send all the information to the server. The form should not even be submitted. For that validation I used the HTMLSelectElement.setCustomValidity() method together with the form oninput attribute. If the setCustomValidity message is an empty string, that means that there is no validation error.
The solution was as follows:

```
<form action='/students/new' method = "POST"
oninput='password1.setCustomValidity(password1.value != password.value ? "Passwords do not match." : "")'>
<div >
    <label for="name">Name:</label>
    <input type="text" name="name" id="name" required>
</div>

    <div>
        <label for="email">Email:</label>
        <input type="email" name="email" id="email" required>
    </div>
    
    <div >
        <label for="password">Password:</label>
        <input type="password" name="password" required>
    </div>
    <div >
        <label for="password1">Confirm Password:</label>
        <input type="password" name="password1" required>
    </div>

<input type="submit" value="Submit">
</form>
```
Despite the fact that the new student form cannot be submitted with empty fields, I still validate it on the server side with the built-in ActiveRecord validations:
```
class Student < ActiveRecord::Base

    validates :name, :email, :password, presence: true
    validates :email, uniqueness: true
end
```
A new record will not be saved to the database if any of the validations fail. 
To test how the validations work I did the following experiment: create a student with duplicate email address:

```

[3] pry(main)> violet = Student.new(:name=>"Violet", :email=>"violet@yahoo.com", :password=>"hello"))
=> #<Student:0x08cfe1dc
 id: nil,
 name: "Violet",
 email: "violet@yahoo.com",
 password_digest: nil>

```

Now, test how the validations work:

```
pry(main)> violet.valid?
=> false
[6] pry(main)> violet.save
=> false
 [7] pry(main)> violet.errors.messages
=> {:email=>["has already been taken"]}
```

Furhtermore, more detailed information about what has been taken can be obtained by:

```
[18] pry(main)> violet.errors.full_messages
=> ["Email has already been taken"]

```
That validation message was pretty handy for displaying feedback messages on the screen via Rack::Flash.

In my students controller:

```
post '/students/new' do
        @student = Student.new(:name => params[:name], :email => params[:email], :password => params[:password])
        if  @student.save
            session[:user_id] = @student.id
            flash[:success] = "Student account created successfully!"
            redirect to '/students/show'
        else
            flash[:danger]=@student.errors.full_messages[0]
            redirect to '/students/new'
        end    
    end
```
The classification of messages as success or danger, etc. comes from the predefined bootstrap alert classes  .alert-success, .alert-danger, .alert-info and .alert-warning. I use the flash hash in my layout.erb as follows:

```
<% ["success" "danger" "warning" "info"].each do |key| %>
        <% if flash[key] %>
          <div class="alert alert-<%=key%>"
              <%=flash[key] %>
          </div>
      <% end %>
	<% end %>
```
This was I got my messages to display in colorful banners with minimal work on my side! May not be the most elegant solution, but it worked.

