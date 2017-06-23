![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png)
# Rails View Templating

### Why is this important?
<!-- framing the "why" in big-picture/real world examples -->
*This workshop is important because:*

We want pages that users can see and interact with! And we want to take advantage of how easy Rails makes views... if we follow the "Rails way".

### Objectives
*After this lesson, developers will be able to:*

- Compare client-side and server-side templating.
- Describe how layouts, view templates, and partials fit together for Rails' server-side templating.

### Where should we be now?
*Before this workshop, developers should already be able to:*

- Start a Rails app with a route and controller for a home page view.
- Incorporate routes, a controller, and a model for a single resource.
- Read and write routes.
- Describe templating.

## Warmup: Client-side vs Server-side Template Rendering

Work with a partner to answer the following questions.  Write your answers on your table.

1. What is HTML "templating"?

2. We've been rendering templates on the client side with Angular, React, and/or ES6 string template literals.  This generally means the user will see part of the page, but the rest of the page is filled in after data comes back from an AJAX call.  List at least one benefit and one drawback of this approach. (Consider things like page load time, number of HTTP requests, user interaction, search engines reading pages.)

3. Rails will render templates on the server side, adding data into an HTML file before sending it to the client side.  List at least one benefit and one drawback of this approach. (Consider things like page load time, number of HTTP requests, user interaction, search engines reading pages.)


## Putting Together the Page

In Rails, the pages a user sees are rendered on the server side as a combination of a layout template and a view template. The view template, in turn, might be built up with one or more smaller "partial" templates. The server has stitches together these pieces into one HTML file before sending the completed file to the client side for display.

### Layout

When a Rails app is created, Rails will automatically add a main application layout file: **`app/views/layouts/application.html.erb`**.

We can use the main application layout for the lines of HTML that link site-wide CSS and JavaScript files. We can also use it for material like a footer that stays the same across all pages from multiple controllers.

<img src="https://raw.githubusercontent.com/sf-wdi-34/angular-routing/master/views_layouts_malcolm.png" alt="single goat show page with details" width="60%">

Templates are always rendered into a `<%= yield %>` tag in a layout. The `app/views/layouts/application.html.erb` layout already contains a `yield` statement, a `<head>` section, and links to the app's CSS and JavaScript files.

It is possible to use multiple layouts.

### View Templates

View templates contain all the content that's specific to one page.  

Each controller will have its own directory inside `app/views` to contain the view templates used by that controller. For fully CRUD-able RESTful resources, there will be views for:

 - index  
 - show  
 - new  
 - edit  


### Partials

Partials allow us to take a portion of a template and move it into a separate file.  

**Check for Understanding:** Why might we want to separate out portions of our HTML view templates?

<details><summary>Ideas</summary>

- A best practice is to always keep every template small. A rule of thumb would be to keep templates to about 50 lines for readability. If we wrote the bulk of our HTML directly in view templates, we'd quickly exceed this limit.


- We might have content that is repeated on more than one page associated with a controller - partials help us reuse HTML code for this content.


- Separating code into partials makes our views more modular and easier to change.
</details>


Partials are always named starting with an underscore: `app/views/products/_form.html.erb` or `app/views/shared/_menu.rb`.  

### Adding Partials to a View Template


Partials are included from inside a view file.  The syntax uses the view helper method `render`: `<%= render "form" %>`.  Note that you don't include the starting `_` when rendering a partial.

Here is syntax for some use cases:

1. Most common: In a view, render a partial from a file called `app/views/products/_product.html.erb`.

  ```erb
  <%- # inside app/views/products/show.html.erb %>
  <%= render "product" %>
  ```

2. In a view, render a partial from a file inside a directory, called `app/views/shared/_footer.html.erb`.
  ```erb
  <%- # inside app/views/products/show.html.erb %>
  <%= render "shared/footer" %>
  ```

3. Less common: You can also pass data into a partial.


 Pass a variable `product` to a partial to be used within the partial:

  ```erb
  <%- # inside app/views/products/index.html.erb %>
  <% @products.each do |product| %>
    <%= render partial: "product", locals: {product: product} %>
  <% end %>

  or

  <%= render partial: "customer", object: @customer %>

  ```

4. There's also a shorter syntax for the common pattern of repeating a partial for all items in a collection:

  ```erb
  <%- # inside app/views/products/index.html.erb %>
  <%= render partial: "product", collection: @products %>
  ```

  The Layouts and Rendering Rails guide section on [using partials](http://guides.rubyonrails.org/layouts_and_rendering.html#using-partials) has more information and examples.

#### Check for Understanding: Using Partials and Layouts

1)  How can we split up pages into layout and views if the website is structured as shown below?

  <img src="https://raw.githubusercontent.com/sf-wdi-34/angular-routing/master/goats_app.png" width="600px" alt="goat app screenshots">

  <hr>

  In Rails, the page `<head>` will already be in the application layout.  We can move the top menu and the footer into the layout to reduce repetition in the individual pages.

  **Layout:**

  ```html
  <!DOCTYPE html>
  <head>
    <!-- metadata, CSS link, JS link -->
  </head>
  <body>
    <header>
      <!-- all header & menu html.erb -->
    </header>
    <%= yield %>
    <footer>
      <!-- all footer html.erb -->
    </footer>
  </body>
  ```

  **Templates:**

  ```
  <!-- app/views/goats/show.html.erb -->
  <!-- show page main content -->
  <%= image_tag(@goat.image_url) %>
    <!-- continue single goat show page html.erb -->
  ```

  ```
  <!-- app/views/goats/index.html.erb -->
  <!-- index page main content -->
  <%= @goats.each do |goat| %>
     <!-- continue goat list page html.erb -->
  ```


  Remember: `yield` is where the current page content will be rendered.


We can also move the header and/or the footer into a partial.  This option would be best when we only use the menu on some pages, and other pages use different content for that portion of the page. For example, the home page might use a hero image in place of the header.

2)  What would our layout and view code look like if we split the header/menu section and the hero image into individual partials?


**Layout:**

  ```
  <!DOCTYPE html>
  <head>
      <!-- metadata, CSS link, JS link -->
  </head>
  <body>
    <%= yield %>
    <footer>
      <!-- all footer html.erb -->
    </footer>
  </body>
  ```

  **Templates:**

  ```
  <!-- app/views/wecome/about.html.erb -->
  <%= render "shared/header" %>
  <!-- continue current page content -->
  ```
  or, on another page,
  ```
  <!-- app/views/welcome/index.html.erb -->
  <%= render "shared/hero_image" %>
  <!-- continue current page content -->
  ```

Alternately, if the header is on every page, we could keep the main layout file short by adding a named `yield` and [`content_for`](http://guides.rubyonrails.org/layouts_and_rendering.html#using-the-content-for-method).

**Layout with named `yield` and `content_for`:**

 ```
 <!DOCTYPE html>
 <html>
   <head>
   <%= yield :head %>
   </head>
   <body>
   <%= yield %>
   </body>
 </html>
 ```

 **Templates**:

 ```
 <% content_for :head do %>
   <title>Goat Tracker - About</title>
 <% end %>

 <%= render "shared/header" %>
 <!-- rest of the about page content -->
 ```



> A very common example of using partials in Rails is moving code for a new/edit form into a partial.



## Views with Controllers

Now that we've gone over how layout templates, view templates, and partial templates come together to create the pages users see, let's dive into how our apps' controllers will trigger rendering these views.

Every route in Rails will execute a method inside a controller. In Rails, the basic logic for rendering an HTML view is straightforward.  When the method is executed:

1. Rails looks for a directory inside `views` corresponding to the controller's name (directory `posts` for `PostsController`).
2. Inside that folder, Rails looks for an `.html.erb` file with a name matching the method's name.

For example, if we visit `http://localhost:3000/posts` in the browser, that GET request should be routed to the PostsController#index method (because of RESTful conventions).  

By default, controller methods will render views files that match the controller method name. So when Rails executes the code in `index`, it will look for and render a template located in `app/views/posts/index.html.erb` unless we tell it to do something else.  

**Check for Understanding:** Which RESTful controller actions in Rails usually render an HTML view? What are their purposes?


<details><summary>click for a list</summary>

* `index` - display a list of all the records for one type of resource
* `show` - display details of one singe record
* `new` - display a form for users to create a new record
* `edit` - display a form for users to update a record

</details>
<br>

And which RESTful controller actions in Rails DO NOT render an HTML view? What are their purposes?
<details><summary>click for a list</summary>

  * `create` - work with the database to create a record (based on form input)
  * `update` - work with the database to update one record (based on form input)
  * `destroy` - work with the database to destroy one record

</details>


### Controller Redirect

Lets take a look at a `create` action:

```ruby
# inside app/controllers/PostsController.rb
def create
  # ...
  if post.save
    redirect_to "/posts/#{post.id}", notice: 'Post was successfully created.'
  else
    render :new
  end
end
```

**Check for Understanding:** What is happening in the code above?

<details><summary>click for explanation</summary>

  Based on the result of trying to save the post, the method will execute either the code in the "if" or in the "else".

  If the new post correctly saves, the response will be a redirect to that post's show page. If the new post doesn't save correctly, the new post form view is rendered again. Note that neither one of these options renders a <code>create.html.erb</code> file.

</details>

We've seen two ways to do something other than rendering the HTML view that matches our controller action name:
 - We can redirect to another action.  
 - We can use a `render` statement to specify a view to render that doesn't match the action name.


### JSON View Templates


Rails 5 comes bundled with a [`jbuilder`](https://github.com/rails/jbuilder) gem that helps us create JSON views instead of HTML. This gives us the flexibility to easily respond to requests for JSON content.

**Check for Understanding**

An app might have a directory `app/views/products` with the files listed below.  Explain in 6 or fewer words what each file is for.

```
_form.html.erb
edit.html.erb
index.html.erb
index.json.jbuilder
new.html.erb
show.html.erb
show.json.jbuilder
```

### Closing Thoughts

- Describe how layouts, view templates, and partials work together.


### Additional Resources

* [Glossary](glossary.md)  
* Rails Guides:
  * [Action View Overview](http://guides.rubyonrails.org/action_view_overview.html)
  * [Layouts and Rendering](http://guides.rubyonrails.org/layouts_and_rendering.html)
  * ActionController's [`render`](http://api.rubyonrails.org/classes/ActionController/Base.html#class-ActionController::Base-label-Renders)
  * ActionController's [`redirect_to`](http://api.rubyonrails.org/classes/ActionController/Base.html#class-ActionController::Base-label-Redirects)
