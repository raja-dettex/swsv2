### Getting started

```rust
    let opts = ServerOpts { 
        host: "0.0.0.0".to_string(), 
        port: 5000
    };
    let ex_server = Server::new(opts, 4);
    let home_handler : Box<Handler> =  Box::new(move |val| {
        let req = val.downcast_ref::<Request>();
        match req {
            Some(request) => { 
                let param = request.path.trim_start_matches("/home/");
                println!("param is {}", param);
                if param == "" { 
                    return Box::new(CustomError{msg :"invalid path".to_string()});
                }
                Box::new(format!("welcome home {}", param))
            },
            None => { 
                Box::new(CustomError{msg: "internal server error".to_string()})
           },
        }
    });
    //define and initialize the router and map the handler to the routes
    let mut router = Router::init();
    router.add::<String, CustomError>("/home".to_string(), "GET".to_string(), home_handler);
    // start the server
    let result = ex_server.lock().unwrap().start(router);
    if let Err(err) = result {
        println!("error : {}", err.error)
    }
```

### storage interface

Guide to implementation of sws built in storage interface

```rust
    struct ExampleMemStore { 
        items : HashMap<String, ExampleItem>
    }

    impl ExampleMemStore { 
        pub fn new() -> ExampleMemStore { 
            ExampleMemStore { items : HashMap::new()}
        }
    }

    // storage trait for ExampleMemStore
    impl Store<ExampleItem> for ExampleMemStore {
        fn getAll(&self) -> Vec<ExampleItem> {
            todo!()
        }

        fn get(&self, key : String) -> Option<ExampleItem> {
            todo!()
        }

        fn add(&mut self, key: String, val : ExampleItem) -> Option<ExampleItem> {
            todo!()
        }

        fn update(&mut self, key: String, val : ExampleItem) -> Option<ExampleItem> {
            todo!()
        }

        fn delete(&mut self, key: String) -> Option<ExampleItem> {
            todo!()
        }
    }


    #[derive(Debug, Clone, Serialize, Deserialize)]
    struct ExampleItem { 
        ex_id : i32,
        item_name: String
    }
    // initialize mem store 
    let ex_mem_store_global = Arc::new(Mutex::new(ExampleMemStore::new())); // this is unified mutable global shared storage to persist the application state
    
```

## License

[MIT](https://choosealicense.com/licenses/mit/)
