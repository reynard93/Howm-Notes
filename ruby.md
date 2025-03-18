pThis is another ruby note
erb tags has two flavors the one with = at the end shows the result in the template after evaluating
erb blocks ends with %

special erb blocks like if and loops end with <% end %> block

to create a link helper can use 'as' kw on a route

rails is smart enough to not require you to pass id 
`link_to event.name, event_path(event)`
in fact also smart enough that you donat need event_path,  can just use event
even if you just use event you still need the 'as' to create the path helper
`link_to event.name, event`

ruby has a helper form builder called form_with which you ,pass in a model

By prefixing the model name with '@', it indicates that the variable is referencing a model from an external module or file. This helps in maintaining code clarity and avoiding naming conflicts

n Rails, the form builder can determine whether to use a "submit" or "patch" button based on the HTTP method specified for the route in the routes file. When you define a route using resources :controller_name, Rails automatically sets up RESTful routes, including the appropriate HTTP methods for each action (e.g., GET for show, POST for create, PATCH/PUT for update, DELETE for destroy).

When you use form_for @model_object in your view to generate a form, Rails inspects the object to determine if it is a new record (not yet saved to the database) or an existing record. If it's a new record, Rails will use the POST method to create a new record upon form submission. If it's an existing record, Rails will use the PATCH method to update that existing record.

So, in summary, Rails uses both the routes file and the model object to decide whether to use a "submit" or "patch" button in the form.

Partials are prefixed with '_' when they are used you cannot reach outside the partial file, you have to pass in a local variable like this `<%= render "form", event: @event %>`
they are passed with the model on the view pages

nto overwrite the method in a link_to erb tab you use data: turbo_method , like this
`link_to "Delete", @event, class: "button", data: { turbo_method: :delete, turbo_confirm: "Really!?" }`

# custom query
 - where '?' represents the placeholder and Time is inbuilt to ruby
 `Event.where("starts_at > ?", Time.now)`

 in a model class the model i.e. 'Event' is implicit in the queries

if we use redirect in a controller we will lose the valid pieces of data when we reshow the form, to not lsoe those data we use render instead for example `render :new` this does not call the new action
this renders the new template and prepopulate the form with what is in the current current

to_sentence method on an array of strings join those strings together

Calling .create! on an associated model within another model creates a new record for the associated model and associates it with the original model in a single step. This method is commonly used in ActiveRecord associations in Ruby on Rails to create and associate records simultaneously.
can also use .create, the difference with using .create! is that the ! version will return exception instead of false if valdiation error occurs/

you can use a through association to traverse through models (avoid N + 1 queries), for example a event has likes, likes has users, in ordder to get all users who liked a event need to loop through event.likes making a query on each like to find the user.

where likes is the join table that belongs to both event and user
in the user model you can write `has_many :events, through: :likes` or for better name `has_many :liked_events, through: :likes, source: :events`
in the events model you can write `has_many :users, through: :likes` or for a better name `has_many :likers, through: :likes, source: :user`

then you can do `user.events` and also `event.users`

to create checkboxes:
- this is an example inside of a form builder
```
<%= f.collection_check_boxes(:category_ids, Category.all, :id, :name)
```

respectively according to the order of the args, 
1. the model attribute to refer
2. get the list from the model
3. get the value to set
4. what to display next to the checkbox

in the params permit section to permit the checkboxes values is bit different, you explicitly have to pass in an empty array

`permit(..., category_ids: [])`

when u see the submit event and there is an empty string for category_id no need to worry, will be stripped out automatically, it just represents no category checked

what a scope does:: dynamically defines a class-level method
the scope name (as a symbol) becomes the method name
 scope is simply a way to name a custom query

you dont want to query code to be reevaluated whenever u call the scope therefore this wont work:
`where('starts_at < ?', Time.now)`
i.e. the Time.now needs to be dynamic (when query execute)
to reeval the query whenevr the scope is called, the second param needs to be a callable obj
sol is to use a lambda and put the query within '{'
`lambda { where('starts_at < ?', Time.now)}`
this converts what is inside into a callable object (also Proc object)

`scope :past, lambda {}`

instead of lambda u can also use '->'

to get a helper method u need to name the route using 'as' passing in a symbol


if you are on the console and not the view template to call a route helper method u need to call it on app
`app.event_path(event)`
-> "/events/10"
the reason why u get 10 is because of 'event.to_param'
we can overwrite the `to_param` method inside your model

`def to_param
  name.parameterize # to make it url friendly, called on a sting, where *name* here is a string attribute, parameterize is called on a string
end
`

# create more friendly urls through slugs

  we need to save the parameterized name on the database col named slug
  `Event.find_by!(slug: params[:id])`

  use model callback to auto generate a slug when a new model instance is created
  use specifically the before_save callback

  the self is important when assigning not impt when getting
  put the following snippet in a private method within the model and call it on before_save
  `self.slug = name.parameterize`

  `before_save :set-slug`


