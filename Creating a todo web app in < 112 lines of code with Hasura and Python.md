---
Tags: hackathon
---

# Creating a todo web app in < 112 lines of code with Hasura and Python

## Introduction
I created this application for the Hashnode Hasura hackathon. I had some trouble thinking of an application to create. I got over my indecision by reading [this post](https://townhall.hashnode.com/project-ideas-and-tutorials-for-the-hasura-x-hashnode-hackathon). A todo app is a valid application so that is what I made.

## Built with
* Hasura
* Python
* Streamlit

## Setting up the backend
The backend of the application is developed with Hasura and Postgres. Doing this is really easy by following Hasura's official documentation [at this link here](https://hasura.io/learn/graphql/hasura/introduction/). It is really easy to follow along but let's summarize the steps we can take to get our app up and running.

### Hasura setup
* Create a Hasura account and create a new project

* Once the project has been created launch the project console. On the console click on the option to create a Heroku database.

* Create the Users table in the launched Hasura console [here](https://hasura.io/learn/graphql/hasura/data-modeling/1-users-table/)

* Create the Todos table in the Hasura console [here](https://hasura.io/learn/graphql/hasura/data-modeling/3-todos-table/)

* Create the foreign key relationships between the user and the todos [here](https://hasura.io/learn/graphql/hasura/relationships/)

### Connecting our app to Hasura
Once we have our Hasura backend set up we can connect our Streamlit app to it. To do so we will need a GraphQL library to allow us to make GraphQL requests from python. Install the following libraries.

- gql: Allows us to create GraphQL queries and mutations and requests from an API

- aiohttp: This allows us to create asynchronous methods

- dotenv: Allow us to read env files


```bash
pip install gql 
pip install aiohttp
```

Once installed create a main.py file and add the following code:

```python
import streamlit as st
import os
from gql import gql, Client
from gql.transport.aiohttp import AIOHTTPTransport
from dotenv import load_dotenv

load_dotenv()


headers = {'x-hasura-admin-secret': os.environ["HASURA_ADMIN_SECRET"] }
transport = AIOHTTPTransport(url="https://curious-sailfish-33.hasura.app/v1/graphql", headers=headers)
client = Client(transport=transport, fetch_schema_from_transport=True)
```

First import all the required modules from the libraries we have installed.

Create headers that will be used in all the requests we make. Use a Hasura admin secret that you can get from your Hasura dashboard.

We will use an async transport protocol in our client to interact with our GraphQL endpoint.

### CRUD operations
In our main.py we will create four methods to interact with our todos api.

#### Read Todos

```python
def get_todos():
    query = gql(
        """
        query getTodos {
            todos {
                id
                title
                is_completed
            }
        }
        """
    )
    result = client.execute(query)
    return result["todos"]

# Save all todos into our streamlit session for use in the application
if 'todos' not in st.session_state:
    st.session_state['todos'] = get_todos()
```

Create a query to get all todos from our database. Save them to our local state to be used in the Streamlit application.

#### Create Todos
```python
def create_todo(todo):
    query = gql(
        """
        mutation createTodo($title: String!, $user_id: String!) {
            insert_todos_one(object: {title: $title, user_id: $user_id }){
                id
                title
                is_completed
            }
        }
        """
    )
    params = {"title": todo, "user_id": "1"}

    result = client.execute(query, variable_values=params)
    todo = result['insert_todos_one']
    st.session_state.todos.append(todo)
    return result
```

Create a todo using a todo mutation and save the created todo into our session variable.

#### Update Todos
```python
def update_todo(todo):
    query = gql(
        """
        mutation updateTodo($id: Int!, $is_completed: Boolean!){
            update_todos(where: {id: {_eq: $id}}, _set: {is_completed: $is_completed}){
                affected_rows
            }
        }
        """
    )
    params = {"id": todo["id"], "is_completed": not todo["is_completed"]}

    result = client.execute(query, variable_values=params)
    return result
```

Update a todo using an update mutation.

#### Delete Todos
```python
def delete_todo(todo):
    query = gql(
        """
        mutation deleteTodo($id: Int!){
            delete_todos(where: {id: {_eq: $id}}){
                affected_rows
            }
        }
        """
    )
    params = {"id": todo["id"]}
    result = client.execute(query, variable_values=params)
    st.session_state.todos.remove(todo)
    return result
```

Finally a method to delete a todo and delete it from our local state as well.

## Setting up the frontend
To set up the web application frontend I used the [streamlit library](https://streamlit.io) . This library turns python scripts into a web application.

Let's set up the web front end:

```python
st.title("My todos")
with st.form(key="my_form", clear_on_submit=True):
	new_todo = st.text_input("Create a todo", value="")
	submit = st.form_submit_button("Create")
	if submit:
		create_todo(new_todo)

todos = st.session_state.todos
not_done = [todo for todo in todos if not todo["is_completed"]]
completed = [todo for todo in todos if todo["is_completed"]]
st.subheader("Not Completed")
for todo in not_done:
	col1, col2 = st.columns([3, 1])
	col1.checkbox(todo["title"], key=todo["id"])
	col2.button("delete", key=todo["id"], on_click=delete_todo, args=(todo, ))
st.subheader("Completed")
for todo in completed:
	st.checkbox(todo["title"], key=todo["id"], value=todo["is_completed"], on_change=update_todo, kwargs={"todo": todo})
```

Let us go through these lines of code:

```python 
st.title("My todos")
```

This creates a heading on the web page. We then create a form that will allow a user to input a new todo. To do this we use Streamlit's form component.

```python
# Clear on submit clears the input once a user 
with st.form(key="my_form", clear_on_submit=True)
	new_todo = st.text_input("Create a todo", value="")
	submit = st.form_submit_button("Create")
	# when a user submits a todo we call the function create todo
	if submit:
		create_todo(new_todo)
```

Streamlit allows for state management between each run of the web script. We will save all the todos that we call from our Hasura backend.

```python
todos = st.session_state.todos
# split the todos into done and incomplete
not_done = [todo for todo in todos if not todo["is_completed"]]
completed = [todo for todo in todos if todo["is_completed"]]
```

The final part to the UI is displaying the todos.

```python
st.subheader("Not Completed")
for todo in not_done:
	# Create a grid with streamlit
	col1, col2 = st.columns([3, 1])
	col1.checkbox(todo["title"], key=todo["id"])
	# A delete button calls the delete_todo method when clicked
	col2.button("delete", key=todo["id"], on_click=delete_todo, args=(todo, ))
st.subheader("Completed")
for todo in completed:
	st.checkbox(todo["title"], key=todo["id"], value=todo["is_completed"], on_change=update_todo, kwargs={"todo": todo})
```

## Putting it all together

Adding all the code snippets for the project:

```python
import streamlit as st
import os
from gql import gql, Client
from gql.transport.aiohttp import AIOHTTPTransport
from dotenv import load_dotenv

load_dotenv()


def get_gql_client(extra_headers=None):
    headers = {'x-hasura-admin-secret': os.environ["HASURA_ADMIN_SECRET"] }
    if extra_headers:
        headers += extra_headers
    transport = AIOHTTPTransport(url="https://curious-sailfish-33.hasura.app/v1/graphql", headers=headers)
    client = Client(transport=transport, fetch_schema_from_transport=True)
    return client

headers = {'x-hasura-admin-secret': os.environ["HASURA_ADMIN_SECRET"] }
transport = AIOHTTPTransport(url="https://curious-sailfish-33.hasura.app/v1/graphql", headers=headers)
client = Client(transport=transport, fetch_schema_from_transport=True)


def get_todos():
    query = gql(
        """
        query getTodos {
            todos {
                id
                title
                is_completed
            }
        }
        """
    )
    result = client.execute(query)
    return result["todos"]


if 'todos' not in st.session_state:
    st.session_state['todos'] = get_todos()


def create_todo(todo):
    query = gql(
        """
        mutation createTodo($title: String!, $user_id: String!) {
            insert_todos_one(object: {title: $title, user_id: $user_id }){
                id
                title
                is_completed
            }
        }
        """
    )
    params = {"title": todo, "user_id": "1"}

    result = client.execute(query, variable_values=params)
    todo = result['insert_todos_one']
    st.session_state.todos.append(todo)
    return result


def update_todo(todo):
    query = gql(
        """
        mutation updateTodo($id: Int!, $is_completed: Boolean!){
            update_todos(where: {id: {_eq: $id}}, _set: {is_completed: $is_completed}){
                affected_rows
            }
        }
        """
    )
    params = {"id": todo["id"], "is_completed": not todo["is_completed"]}

    result = client.execute(query, variable_values=params)
    return result


def delete_todo(todo):
    query = gql(
        """
        mutation deleteTodo($id: Int!){
            delete_todos(where: {id: {_eq: $id}}){
                affected_rows
            }
        }
        """
    )
    params = {"id": todo["id"]}
    result = client.execute(query, variable_values=params)
    st.session_state.todos.remove(todo)
    return result


st.title("My todos")
with st.form(key='my_form', clear_on_submit=True):
    new_todo = st.text_input("Create a todo", value="", key="d")
    submit = st.form_submit_button("Create")
    if submit:
        create_todo(new_todo)

todos = st.session_state.todos
not_done = [todo for todo in todos if not todo["is_completed"]]
completed = [todo for todo in todos if todo["is_completed"]]
st.subheader("Not Completed")
for todo in not_done:
    col1, col2 = st.columns([3, 1])
    col1.checkbox(todo["title"],key=todo["id"], value=todo["is_completed"], on_change=update_todo, args=todo)
    col2.button("Delete", key=todo["id"], on_click=delete_todo, args=(todo, ))
st.subheader("Completed")
for todo in completed:
    st.checkbox(todo["title"],key=todo["id"], value=todo["is_completed"], on_change=update_todo, kwargs={"todo": todo})
```

## Deploy the application

To deploy our application create a requirements.txt file in our folder and add the following:

```txt
aiohttp
streamlit
python-dotenv
gql
```

We then push this repo to Github.

Sign up for an account on Streamlit and link it to your GitHub account. Create a new application and select the repository that was pushed. Streamlit will allow you to pick the branch and the file that the application will run from.

![Create streamlit app](https://docs.streamlit.io/images/streamlit-cloud/deploy-an-app.png)

Click on Advanced settings to add our environment variables. This is where we will add the Hasura admin secret.

![Streamlit env variables settings](https://docs.streamlit.io/images/streamlit-cloud/advanced-settings.png)

Once saved hit deploy and viola your application is ready to be used and shared.

## What I learned
I learned quite a bit during this hackathon, it was my first time using Hasura. The following are a few things that I learned:

* Creating a Hasura GraphQL backend

* Connecting a Hasura API to python

* Local state in a python Streamlit application

* Auth0 for authentication purposes

## Things I struggled with
The main thing I struggled with was getting Hasura's authentication with Auth0 to play well with Streamlit. This was more a problem with me trying to fit a triangle peg in a square hole. Streamlit is not a conventional web framework so the concept of a redirect URL does not work with it. I tried to create an API backend authentication using a flask JWT API [tutorial here](https://hasura.io/docs/latest/graphql/core/actions/codegen/python-flask/).

I was able to get the sign-up route working fine but logging in was an issue because each JWT I generated was invalid. I'm sure there is something I missed in the documentation but for the life of me, I could not get it to work. I will revisit this and also push the code to my Github page.

## Try it out

Find the relevant links below:

* [Try out the live streamlit application](https://share.streamlit.io/kachamukabe/hasura-todo/main/main.py)
* [Github repo](https://github.com/kachaMukabe/hasura-todo)


