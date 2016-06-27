---
date: 2016-06-27T12:09:16-07:00
draft: false
title: Full Stack Development with Ember and Go
tags: [Tutorial, EmberJS, Go]
---

One of my personal 2016 resolution was learning Go, and get familiar with
writing restful API using Go. After couple of months learning Go, I started a
simple application that use Go, and Ember to develop a Todo app. Yes, you read
it right, I'm also creating another Todo app. Yet this one is different from the
other you have seen because it's built with Go for backend api, and Ember for
frontend.

The source code can be found here:
https://github.com/pmkhoa/restful-api-go-and-ember

### Backend overview
- *model*: Todo model includes `Id`, `Name`, `Completed`, `Due`
        
        package main
        import "time"
        type Todo struct {
            Id          string `json:"id"`
            Name        string `json:"name"`
            Completed   bool `json:"completed"`
            Due         time.Time `json:"due"`
        }
        type Todos []Todo
We create type Todo, and Todos to hold our Todo object, and Todo list

- *server*: bootstrap http server, response to request made from the client.

    For our server code, we use `httprouter` to for our api handler, and enable CORS
so that our frontend app can talk to the server when they're on different
domains.

        package main
        import (
            ... // import our dependencies here
        )
        func main() {
            router := httprouter.New()
            router.GET("/todos", TodosIndex)
            router.GET("/todos/:todoId", ShowTodo)
            // More routes are defined similarly

            // Enable the CORS
            c := cors.New(cors.Options{
                AllowedOrigins: []string{"*"},
                AllowedMethods: []string{"GET", "POST", "DELETE", "PUT", "PATCH", "OPTIONS"}
            })
        }

        func TodosIndex(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {
            w.Header().Set("Content-Type", "application/json; charset=UTF-8")
            w.WriteHeader(http.StatusOK)
            err := json.NewEncoder(w).Encode(todos)
            if err != nil {
                panic(err)
            }
        }

        func ShowTodo(w http.ResponseWriter, r *http.Request, params httprouter.Params) {
            todoId := params.ByName("todoId")
            todo := RepoFindTodo(todoId)
            w.Header().Set("Content-Type", "application/json; charset=UTF-8")
            w.WriteHeader(http.StatusOK)
            err := json.NewEncoder(w).Encode(todo)
            if err != nil {
                panic(err)
            }
        }
        // more handlers can be defined with other httprouter 


- *Data fixture*: findTodo, updateTodo, deleteTodo, createTodo.
    
    I'm using a simple data fixture to handle model operations. There will be
    another blog post that integrate this application with Postgres for data
    persistent.
        
        // repo.go
        package main
        import (
            "fmt"
            "strconv"
        )
        var currentId int // keep track of our current todo
        var todos Todos
        func init() {
            // When initialize the app, we will create two sample todos
            RepoCreateTodo(Todo{Name: "Test Todo 1", Completed: false})
            RepoCreateTodo(Todo{Name: "Test Todo 2", Complete: false})
        }
        func RepoCreateTodo(t Todo) Todo {
            currentId += 1
            t.Id = strconv.Itoa(currentId)
            todos = append(todos, t)
            return t
        }
        func RepoFindTodo(id string) Todo {
            for _, todo := range todos {
                if todo.Id == id {
                    return todo
                }
            }
        }


### Frontend overview
- *webapp*: Simple Ember app that lists all todos, and all user to create todo.
    If you're interested in learning Ember for building frontend. Feel free to
    check out the github repo for this.

### Future development
Eventhough this was a very simple app, I have learnt a lot from building it from
the ground up. Using Go for backend API is quite astonishing and simple, I think
it's a great choice for anyone who is looking for a fast, simple way to build
API. There are a lot of things the can improve from this app.

- Write README
- Better handling errors for both backend & frontend
- Write integration test & unit tests
- Use Postgres for data persistent.

If you're interested in all the source code, feel free to check it out here:
https://github.com/pmkhoa/restful-api-go-and-ember


