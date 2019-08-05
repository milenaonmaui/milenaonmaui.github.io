---
layout: post
title:      "Adventure Cruises Rails Portfolio Project"
date:       2019-08-05 02:21:18 +0000
permalink:  adventure_cruises_rails_portfolio_project
---


For my Rails portfolio project I decided to create a web application where people can book various adventure cruises such as Snorkeling trips, Whale Watches or dinner cruises. I often visit similar sites on the Internet to look for some good deals on cruises. Of course, my implementation is very simplified, but I decided it is good enough for "sprint 1" of this project and satisfies most of the requirements. I plan to further improve on it when I learn JavaScript, AJAX and possibly other front-end frameworks.
 
 My main models are User, Cruise and Booking. Booking serves as the joint table for User and Cruise, since User and Cruise have a many-to-many relationship. A User has many cruises through bookings and a Cruise has many users through bookings as well. The cruises can be classified into several different categories, such as Snorkel, Whalewatch,  Dinner, etc. Initially I had category as a string type attribute of the Cruise model. As I got further into implementation, esp. filtering of cruises by category, I found that to be really inconvenient, so I created a new Category model. A category has_many cruises and a cruise belongs_to a category. The one-to-many association between the models will allow me to easily find all cruises in a given category by using Category#cruises method. One caveat that tripped me for a while when  I changed to the Category model was that the previous seeds for the database would no longer work. When creating a new cruise, upon save the transaction would get rolled back.
 Upon deeper inspection via the console, I found the problem:
 
 `irb(main):024:0> cruise.errors
=> #<ActiveModel::Errors:0xad75f64 @base=#<Cruise id: nil, name: "Classic Whalewatch", capacity: 75, price_child: 50, price_adult: 80, min_age: 5, created_at: nil, updated_at: nil, category_id: nil>, @messages={:**category=>["must exist"**]}, @details={:category=>[{:error=>:blank}]}>
`

I examined by Cruise model to see if I had validation on the presence of Category field from my initial version of the model. There wasn’t any. Then I remembered that the belongs_to :category relationship would require category_id before saving the object.

In order to filer cruises by category I created a scope method in the Cruise model as follows:

`scope :by_category, ->(category_ids) { where(category_id: category_ids) }`

Now I can use something like `Cruise.by_category([13, 14])`, which will give me all cruises with category id 13 or 14.

Another unforeseen roadblock in my project turned out to be updating the number of seats left for a cruise with every create, update or delete action. A booking has num_adults and num_children attributes, and these two added together are the total number of seats that need to be subtracted upon booking from the seats left in the cruise. Similarly, if a booking is cancelled, we need to add those seats back. That seems pretty straightforward and in the case of create or destroy it is.

I added two callbacks to the Booking.rb model:

`
after_create :update_seats_left_new
before_destroy :update_seats_left_destroy
`

```
def update_seats_left_new
        self.cruise.seats_left -= self.seats_total
        self.cruise.save
    end

    def update_seats_left_destroy
        self.cruise.seats_left += self.seats_total
        self.cruise.save
    end
```

The updating of seats after creating a new booking should work pretty good, since there is validation that would ensure there is enough seats left for the booking. So after the booking is created successfully, the seats get subtracted. The updating of seats on destroy is not ideal, because it is done before the destroy has happened. So if the destroy operation fails those seats will be deducted anyway. However, after destroying the booking, I lose the information of how many seats were in it, and I didn't find any good way of storing that number, so it is available after destroying the object. The chance of failure to delete a booking is pretty slim. I have no validations that would prevent the deleting. So I think that adding back the seats just before destroy is a reasonable compromise.

With the updating of booking however, things got more complicated. During update, the number of required seats may go up or down, so I need to know the difference between the old number of seats and the updated one in order to keep the cruise seats_left  accurate. ActiveRecord provides a way of keeping track of attribute values of an object.

For example, if I have an existing booking 
```
irb(main):017:0> booking
=> #<Booking id: 12, cruise_id: 2, user_id: 3, num_adults: 1, num_children: 2, created_at: "2019-07-27 23:11:00", updated_at: "2019-07-27 23:20:16">

```
I can use booking.changed?  to see if any changes have been made.

```
irb(main):016:0> booking.changed?
=> false

```
an if I make some changes:

```
irb(main):019:0> booking.num_adults = 5
=> 5

```
there is an attribute_changed? method that can tell me if change was made and attribute_was that keeps the previous value:

```
irb(main):021:0> booking.num_adults_changed?
=> true

irb(main):023:0> booking.num_adults_was
=> 1

```
The tricky part is that once the object is saved, as in update, all the old values are lost:

```
irb(main):025:0> booking.save
irb(main):026:0> booking.num_adults_was
=> 5

```
Ideally I want to make the changes in the cruise seats left after the booking update is completed successfully. Update can fail for many reasons such as not enough seats, no adult, etc.  and if booking update fails I don't want the cruise to be updated. After doing research it seemed that the correct way of doing it will be to have both the booking update and the cruise update in a transaction, so either both of them succeed or the whole transaction gets rolled back.
However, at this time, implementing transactions seems to be a little out of scope for my project. I decided to go with a not perfect, but nevertheless working solution for my situation.
I added a callback before_save :recalculate_seats_left  that checks if there was a change of number of adults or children and if the booking object is valid (i.e. no errors) and then updates the cruise seats left just before saving the changed booking. Then the same callback can be used when creating too, since create involves a save.

```
def recalculate_seats_left
        if (self.num_adults_changed? || self.num_children_changed?) && self.valid?          
            diff = self.num_adults.to_i + self.num_children.to_i - self.num_adults_was.to_i - self.num_children_was.to_i
            self.cruise.seats_left -= diff
            self.cruise.save
        end
    end
```
This solution has weaknesses, such as despite being valid the save still may fail, or simultaneously another user may have made a booking leaving not enough seats left in which case there will be a discrepancy in the cruise seats left.
However for my simple project this solution seems to work pretty well. In the future as I progress in my studies, I may come up with something better.  As well as improve the user interface by adding some Java Script, AJAX and React.

