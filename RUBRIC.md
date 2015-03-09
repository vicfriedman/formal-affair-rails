# Formal Affairs Review Rubric

## README

- Use the `form_for` helper for the babies forms, `form_tag` for the concretes forms, and `<form>` element for the search form.
- Learn more about how [form authentication works](http://guides.rubyonrails.org/security.html#csrf-countermeasures) in Rails, as well as the [Rails authenticity token](http://news-anand.blogspot.com/2013/05/include-csrf-token-in-form-ruby-on-rails.html).

## RSpec

- On the first `rspec` runthrough, I get an error about the baby form not using the `form_for` helper method - `uses the form_for helper (FAILED - 1)`. If you haven't already, read more about the `form_for` helper in the Rails documentation. Let's go ahead and start implementing the `form_for` helper:

    ```ruby
    # app/views/babies/_form.html.erb

    <%= form_for(@baby) do |f| %>
    <% end %>
    ```

- This passes the first test. However, we need to have our form generate a params hash that is structured correctly. So how do we do that? We have attributed a `@baby` object to the `form_for` helper, so we simply need to call the attributes that we're rendering in the form. Let's build it out below:

    ```ruby
    # app/views/babies/_form.html.erb

    <%= form_for(@baby) do |f| %>
      <%= f.label :first_name %>
      <%= f.text_field :first_name %>

      <%= f.label :last_name %>
      <%= f.text_field :last_name %>

      <%= f.label :weight %>
      <%= f.text_field :weight %>

      <%= f.label :birth_date %>
      <%= f.date_field :birth_date %>

      <%= f.submit %>
    <% end %>
    ```

- This gets both the tests for the `new` form and `edit` form passing. Why does this work? The `form_for` helper will dynamically determine the correct path and method to submit to based on whether the `@baby` object has an ID. If it's a new object, it will render a `post` method to the `"/babies"` path. If it's an existing object, it will render a `patch` method to the `"/babies/<baby_id>"` path. Pretty nifty.

- The next test failure we get is on the `concretes` form section - `uses the form_tag helper (FAILED - 1)`. Let's go ahead and start building the `form_tag` out.

    ```ruby
    <%= form_tag("/concretes") do %>
    <% end %>
    ```

- This generates the form element we're looking for, but it's not passing the tests for the correctly structured params. So how do we implement this for the `form_tag`? Let's finish it up for the `new` and `edit` form_tag templates.

    ```ruby
    # app/views/concretes/new.html.erb

    <%= form_tag "/concretes" do %>
      <%= label_tag "concrete[mix_type]", "Mix Type" %>
      <%= text_field_tag "concrete[mix_type]" %>

      <%= label_tag "concrete[color]", "Color" %>
      <%= text_field_tag "concrete[color]" %>

      <%= label_tag "concrete[psi]", "PSI" %>
      <%= text_field_tag "concrete[psi]" %>

      <%= label_tag "concrete[cost_per_yard]", "Cost Per Yard" %>
      <%= text_field_tag "concrete[cost_per_yard]" %>

      <%= submit_tag "Create Concrete" %>
    <% end %>
    ```

    ```ruby
    <%= form_tag "/concretes" do %>
      <%= label_tag "concrete[mix_type]", "Mix Type" %>
      <%= text_field_tag "concrete[mix_type]", @concrete.mix_type %>

      <%= label_tag "concrete[color]", "Color" %>
      <%= text_field_tag "concrete[color]", @concrete.color %>

      <%= label_tag "concrete[psi]", "PSI" %>
      <%= text_field_tag "concrete[psi]", @concrete.psi %>

      <%= label_tag "concrete[cost_per_yard]", "Cost Per Yard" %>
      <%= text_field_tag "concrete[cost_per_yard]", @concrete.cost_per_yard %>

      <%= submit_tag "Update Concrete" %>
    <% end %>
    ```

- Now we have the concretes form tests passing. The next error I get is `uses the <form> tag (FAILED - 1)` for the search form. Let's set up the search form:

    ```ruby
    <form action="/search" method="post">
    </form>
    ```

- This passes the first test for the search tests. Next we have to properly structure the form so that the test can find the correct element.

    ```ruby
    <form action="/searches" method="post">
      <label for="search-field">Search:</label>
      <input type="text" id="search-field" name="q">

      <input type="submit" value="Search">
    </form>
    ```

- We're going to get an `ActionController::InvalidAuthenticityToken` error. Why is this? The `form_for` and `form_tag` helpers automatically create an authenticity token for their respective form objects, and we're not passing that into our search form. Let's add that in now.

    ```ruby
    <form action="/searches" method="post">
      <input name="authenticity_token" value="<%= form_authenticity_token %>" type="hidden">
      <label for="search-field">Search:</label>
      <input type="text" id="search-field" name="q">

      <input type="submit" value="Search">
    </form>
    ```

- Sweet! All tests should be passing now.
