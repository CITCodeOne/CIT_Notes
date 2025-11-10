
| Function            | Purpose                      | Parameters                          | Returns | Side effects           | Notes                                              | Example call                                                     |
| ------------------- | ---------------------------- | ----------------------------------- | ------- | ---------------------- | -------------------------------------------------- | ---------------------------------------------------------------- |
| ```register_user``` | Create a minimal user record | ```uconst, name, email, password``` | void    | ```user_info insert``` | No auth scope; ```ON CONFLICT DO NOTHING```allowed | ```select register_user('u000000001','alex','a@ex.com','pw');``` |
|                     |                              |                                     |         |                        |                                                    |                                                                  |
