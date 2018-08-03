# Display The Create Page
Create ToDo
- Create a folder under components named “Create”
- Create a file inside of “Create” folder named “index.js” This file becomes the default when ‘components/Create’ is imported
- First, we import React. This pulls in the react library and is essential for creating our page: 
```
React, { Component } from 'react';
```

- Next we define a class named `CreatePage`:
  - `export default` says that when this page is imported, we're going to give whoever is calling it this class
  - `class CreatePage extends Component` says that this page is a type of a React Component. This gives us a ton of stuff for free out of the box, and is how we get the awesomeness of React in our app.
```
export default class CreatePage extends Component {

}
```
- Next, we need to add the `render()` method. This is where we define what HTML this page will display. For now, just mark something that let's us know we're on this page. A `<div>` is a generic HTML component to display info.
```
render() {
  return (
    <div> Welcome to the create page!</div>
  )
}
```

Altogether now, it will look like this:
```
import React, { Component } from 'react';
export default class extends Component {
    render () {
        return (
            <div> Welcome to the create page!</div>
        )
    }
}
```

# Link Up the Route

Go to the `routes.js` at `src/routes.js`
- Import the create page:
```
import CreatePage from '../components/Create';
```
Because of the `index.js`rule mentioned above, the page we just created will be pulled. Now we need to define when this page is displayed. This is done through `Routes`. *Above `<Route component={Home} />` put the following:
```
<Route path="/create" component={CreatePage} />
```
This says when we go to our website, `localhost:3000/create` /create, we're going to automatically go to our Create page. You should see the create page loaded
![alt text](https://s3.amazonaws.com/clc-images/CreatePage.png)

# Create a Form With A Submit
- Import a Button, a Form component, a FormInput, Yup, Formik, and Box. You'll see what all these do in practice soon.
```
import { Button } from 'semantic-ui-react'
import Form from '../Form';
import FormInput from '../FormInput'
import * as Yup from 'yup';
import { Formik } from 'formik';
import { Box } from 'rebass';
```

- First, let's set up a validator. A validator is used to make sure the user doesn't submit data they shouldn't. When you've submitted forms and you've gotten a "password isn't long enough" message, a validator is what is checking the logic. We're goign to use a validation library called Yup because it's super simple to use. Paste the following code, and we'll walk through it:

```
const FormSchema = Yup.object().shape({
    text: Yup.string()
        .required('Text for your To-Do is required')
});
```

- `const FormSchema` defines a variable named FormSchema
- `Yup.object().shape()...` defines the validation object. This says what our form is going to be expecting. We're only going to have one field for the text of our To-Do:
```
text: Yup.string()
    .required('Text for your To-Do is required')
```

Now, it's time to set up our form to put our validation in. Copy the following code for the `render()` method. I have put comments to explain what each does.
```
  render () {
      return (
          <Formik // A form library that takes care of a lot of magic for us
          initialValues={{text: ''}} // Defines what the initial text will be, a blank string ('')
          validationSchema={FormSchema} // Uses our schema we defined above, so 'text' can't be blank
          render={({ errors, touched, onSubmit }) => ( // Tells it what to draw
          <Form>
              <Box>
                  <Box p={10}>
                      <FormInput name="text" placeholder="Clean the basement" type="text" />
                  </Box>
                  <Box p={10}>
                      <Button type="submit">Create</Button>
                  </Box>
             </Box>
          </Form>
          )}
      />)    
  }
```
Save the file and watch the page reload. You just set up your first form! Try clicking the "Create" button without putting in any text. That's the FormSchema in action :).

# Link Up With MobX and Hit the Internet
- Now that we have our form good to go, we need something to happen when you click Create. We use MobX to initiate our network requests and load data. So let's get started creating what we call a MobX store.
- Go to `src/stores/` and create a new file called `toDoStore.js`
- At the top of `toDoStore.js` import the following:
```
import { observable, action } from 'mobx';
```

These are magic methods we'll be using later for MobX.

Define a class named `ToDoStore`:
```
class ToDoStore {

}
```

Finally export the store
```
export default new ToDoStore();
```

Altogether now:

```
import { observable, action, reaction } from 'mobx';

class ToDoStore {

}

export default new ToDoStore();
```

Go to `src/index.js`. We're now going to add this store we created to our app.

First, import it on line 13:
```
import toDoStore from './stores/toDoStore';
```

Then add it to the list of stores on line 23:
```
const stores = {
  authStore,
  commonStore,
  userStore,
  toDoStore <-- Add this one!
};
```
Great, now this new store is in your app! It doesn't do anything yet though. Let's change that!

First, we need to define our network request to make. All of our network requests are made in the `agent.js` file. Navigate to it (TIP: don't forget you can always search for a file in VSCode by searching CMD + P).

Below `Account` add a new variable named `ToDo`. This is where all the network requests around the `ToDo` model will go. This just keeps things organized and much easier to read.

```
const ToDo = {
};
```

Let's add a method called `create`. This method is going to create a new To-Do in our database by calling an endpoint /toods. We use the `.post` method when we want to create something. We'll be passing in the data, the `toDo` from the form directly. We'll see how that works in a bit.

```
const ToDo = {
  create: (toDo) =>
    requests.post(`/todos`, toDo),
};
```

