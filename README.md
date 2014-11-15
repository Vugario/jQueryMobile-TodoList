# Assignment - Homework app

Step 1. Create a new project and call it Homework. Start off with a basic template like the one below.

```html
  <!DOCTYPE html>
  <html>
      <head>
  		<meta name="viewport" content="width=device-width, initial-scale=1">
          <title>Homework</title>
          <script src="//ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
  		<script src="//ajax.googleapis.com/ajax/libs/jquerymobile/1.4.3/jquery.mobile.min.js"></script>
  		<link rel="stylesheet" href="//ajax.googleapis.com/ajax/libs/jquerymobile/1.4.3/jquery.mobile.min.css" />
      </head>
      <body>

      </body>
  </html>
```

Step 2. Set up a simple app structure like below. This will give your app a header with a title. Be sure to name the page "home" by setting the ID field, you will need this later on.

```html
  <div data-role="page" id="home">
    <div data-role="header">
      <h1>Homework</h1>
    </div>
    <div data-role="content">

    </div>
  </div>
```

Step 3. Inside the content we want to show a textfield to allow the user to type a new assignment. When the contents of the input field changes we call a function add();

```html
  <input type="text" name="item" placeholder="Add an item.." onchange="add();">
```

Step 4. When the user enters text in the input field, we want to storage it. Create a app.js file and link to inside the head tags.

```html
  <script src="app.js"></script>
```

Step 5. Inside app.js make a new method like below. Then run the application, enter some text in the input field and press enter.

```js
  function add()
  {
    // Find the element named "item"
    var textfield = $('[name="item"]');

    // Show the contents to the user
    alert(textfield.text());
  }
```

You will now see that the text you have entered is shown to the user via a alert message. We want to save this text as a new record in our LocalStorage. LocalStorage is a client-side database that can hold anything you want to save and is unique to the person using it as its stored in their browser.

Why do we use a database? Because when the user would leave our app and returns at a later moment, their items will still be there because we saved it on their harddrive.

Replace the method you just created with the code below.

```js
  function add()
  {
    // Retrieve the entered form data
    var title = $('[name="item"]').val();

    // Fetch the existing items
    items = getItems();

    // Push the new item into the existing list
    items.push({
      title: title
    });

    // Store the new list
    saveItems(items);

    // Reload the page to show the new item
    window.location.reload();
  }
```

When the enter key is pressed we look up the user's entered data. We then retrieve all items currently in our database and add this new one to the list. We now save the new list in the database and reload the page.

The getItems() and saveItems() functions we have to create ourselves. Use the code below.

```js
  function getItems()
  {
    // See if items is inside localStorage
    if (localStorage.getItem("items"))
    {
      // If yes, then load the existing courses
      items = JSON.parse(localStorage.getItem("items"));
    }
    else
    {
      // Make a new array of items
      items = new Array();

      // Save into local storage
      localStorage.setItem("items", JSON.stringify(items));
    }

    return items;
  }
```

This code will look inside our database for the key "items". If it does not yet exist it will make a new array. If it does exist, we transform the JSON string into a javascript array. Items saved in this database are saved using a JSON string and must therefore be converted when saving or retrieving.

```js
  function saveItems(items)
  {
    // Save the list into localStorage
    localStorage.setItem("items", JSON.stringify(items));
  }
```

The code as shown above takes a given array and turns it into a JSON string. It then store this inside the database with the key "items".

Now when you would press enter, a new item is stored in the database. Though you can not see it yet because we are not showing the existing items to the user yet. Lets build that now.

Make a list on the homepage that will hold all our items. Place this code below the <input> element we created earlier.

```html
  <ul id="items" data-role="listview" data-inset="true"></ul>
```

The code below is going to be executed every time the homepage is loaded. We load the existing items ready to be shown to the user. Then we clear the existing list to make sure we don't load items twice. Now we loop through every item and push it into the list. Items that are marked as "done" will have their opacity set to 50%.

```js
  function homepage()
  {
    // Fetch the existing items
    items = getItems();

    // Clear the list
    $('#items').find('li').remove();

    // Add every item to the items list
    $.each(items, function(index, item)
    {
      element = $('<li><a class="toggle" data-id="' + index + '" href="#"><h2>' + item.title + '</h2></a><a href="view.html?' + index + '"></li>');

      if (item.done)
      {
        element.css('opacity', .5);
      }

      $('#items').append(element);
    });

    // Let jQuery re-render our list
    $('#items').listview('refresh');
  }
```

Put the code below at the bottom of your app.js. This will tell the app to execute homepage() every time the homepage is loaded.

```js
  $(document).on('pagebeforeshow', '#home', function(event)
  {
    homepage();
  });
```

Run your application and see if it works. You can add new items and they will show up on the homepage!

Now lets build the ability to mark them as completed. Every item has a CSS class "toggle". Whenever we press a item with the class toggle we will launch a toggle() function with the ID of the item that was clicked on, like below.

```js
  $(document).on('click', '.toggle', function()
  {
    id = $(this).data('id');

    toggle(id);
  });
```

Make a new function called toggle(). This item will load all items and select one you clicked on. This item will then be marked as completed if it wasn't already so. If it was completed, we will mark it as not completed.

```js
  function toggle(id)
  {
    // Fetch all the items
    items = getItems();

    // Find the requested item
    item = items[id];

    // Toggle the status
    item.done = (item.done) ? false : true;

    // Write back to the item list
    items[id] = item;

    // Write back to the storage
    saveItems(items);

    // Reload the page
    window.location.reload();
  }
```

Run your application and test to see if it works.

We would like to have the ability to edit or delete a item. We will make a new view for this. Create a new file called view.html and set it up like below.

```html
  <!DOCTYPE html>
  <html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Homework</title>
    <script src="//ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
    <script src="//ajax.googleapis.com/ajax/libs/jquerymobile/1.4.3/jquery.mobile.min.js"></script>
    <link rel="stylesheet" href="//ajax.googleapis.com/ajax/libs/jquerymobile/1.4.3/jquery.mobile.min.css" />
    <script src="js/app.js"></script>
  </head>
  <body>

  <div data-role="page" id="view">
    <div data-role="header" data-add-back-btn="true">
      <h1>Homework</h1>
      <a id="remove" href="index.html" class="ui-btn-right">Delete</a>

    </div>
    <div data-role="content">
      <input type="text" id="title" name="title" placeholder="Enter a title">
      <textarea id="description" name="description" placeholder="Enter a description"></textarea>
      <a id="edit" href="index.html" data-role="button">Save</a>
    </div>
  </div>

  </body>
  </html>
```

This page contains a delete button, a save button, a title field and a description field. We will now create some code to interact with it.

```js
  $(document).on('pagebeforeshow', '#view', function(event)
  {
    // Retrieve the requested id from the URL
    id = window.location.href.split('?')[1];

    // Load the requested item
    view(id);
  });
```

The code as shown above will execute the view() function every time the #view page is requested.

The view() function is very simple. It simply loads the requested item and populates the title and description fields with the available data.

```js
  function view(id)
  {
    // Fetch all the items
    items = getItems();

    // Find the requested item
    item = items[id];

    // Populate the page
    $('#title').val(item.title);
    $('#description').val(item.description);
  }
```

When the user edits the form fields and presses save, we want to execute the code like below.

```js
  function edit()
  {
    // Retrieve the entered form data
    var id = window.location.href.split('?')[1];
    var title = $('#title').val();
    var description = $('#description').val();

    // Fetch the existing items
    items = getItems();

    // Push the new item into the existing list
    items[id] = {
      title: title,
      description: description
    };

    // Store the new list
    saveItems(items);
  }
```

This will take the new title and description and overwrites the currently available data in our database. Now also create the listener for the save button.

```js
  $(document).on('click', '#edit', function()
  {
    edit();
  });
```

To delete a item, we do pretty much the same thing. We create a listener

```js
  $(document).on('click', '#remove', function()
  {
    remove();
  });
```

Now we create a remove() function that finds the item and removes it from the database.

```js
  function remove()
  {
    // Find the requested id
    var id = window.location.href.split('?')[1];

    // Fetch the existing items
    items = getItems();

    // Remove the item from the list
    items.splice(id, 1);

    // Store the new list
    saveItems(items);
  }
```

Run your application and make sure that everything works. Create a new item and add a description to it. Then mark it as "completed" and finally delete it.

Did it work? Congratulations, you have finished your first database-driven application!
