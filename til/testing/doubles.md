# Test Doubles

Keeping track of the different kinds of tests doubles has long confused me. Mocks, stubs, fakes, spies, dummies—sounds like I'm describing a cocktail party of pirates, medieval jesters and MI6 agents.

Up front, thanks to [Michal Lipski](https://twitter.com/milipski) for his article, ["Test Doubles - Fakes, Mocks and Stubs."](https://dev.to/milipski/test-doubles---fakes-mocks-and-stubs), which went a long way toward clarifying my thinking on the subject. Also thanks to [Martin Fowler's piece "Mocks Aren't Stubs"](https://martinfowler.com/articles/mocksArentStubs.html).

## Dummies
Dummies are probably the simplest form of test double, as you aren't expected to actually use them in the lifecycle of the test. We can incorporate dummies when we need additional parameters for a function call, but we never actually use them. That's what makes them dumb.

## Stubs
Stubs are intermediate objects (or functions that return objects) that live between your application logic and an external dependency (like a database). I like how Martin Fowler's piece calls them "canned answers," as they don't actually respond to external elements.

Instead of querying a database or fetching from a third-party API, I can simply instantiate the object in my test as a stub, run logic against it, and assert that the object is in a certain state after my exercise.

## Mocks
Mocks, by contrast, allow us to test the _behavior_ of our applications. We can make assertions about the way our application interacts with third-party entities.

One of my favorite mocking libraries in Elixir is [Bypass](https://github.com/PSPDFKit-labs/bypass).

```elixir
defmodule MyApp.MyModuleTest do
  use ExUnit.Case

  setup do
    bypass = Bypass.open()
    url    = "localhost:#{bypass.port}"
    config = Application.get_env :my_app, MyModule

    new_config = Keyword.put :my_app, :foreign_url, url

    Application.put_env :my_app, MyModule, new_config

    on_exit fn ->
      Application.put_env :my_app, MyModule, config

      :ok
    end

    {:ok, bypass: bypass}
  end

  test "fetches users", %{bypass: bypass} do
    Bypass.expect bypass, fn conn ->
      # Testing behavior
      assert conn.method       == "GET"
      assert conn.request_path == "/users"

      users    = [%{"name" => "Jane Doe"}, %{"name" => "John Doe"}]
      response = Poison.encode! %{"data" => users}

      Plug.Conn.resp conn, 200, response
    end

    assert {:ok, users} = MyModule.fetchUsers()

    assert users == [
      %{"name" => "Jane Doe"},
      %{"name" => "John Doe"}
    ]
  end
end
```

In the above example, we make two behavioral assertions about `fetchUsers/0`. First, we assert that it sends a GET request to the endpoint. Secondly, we assert that it makes its request on the `/users` endpoint.

With Bypass, we can also return a response, which allows us to make assertions about our functions handling of that response. Our final assertion here, for example, demands that our function extract the `data` value from the response and return an `{:ok, users}` tuple on success, which is idiomatic Elixir.
