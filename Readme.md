# My Phoenix pet project
https://hexdocs.pm/phoenix/contexts.html

# Install phoenix
https://hexdocs.pm/phoenix/installation.html

```
https://hexdocs.pm/phoenix/installation.html
```

# Install the Phoenix application generator:

```
mix archive.install hex phx_new
```
# Install PostgreSQl

https://postgresapp.com/

# Initialize
| Param   | Value
|---------|------------------------|
| Host    | localhost              |
| Port    | 5432                   |
| User    | ilya                   |
| Database| ilya                   |
| Password| none                   |
| URL     | postgresql://localhost |

# pgAdmin

https://www.pgadmin.org/

Congigure:
| Param   | Value
|---------|------------------------|
| User    | ilya                   |
| URL     | postgresql://localhost |

# Bootstrap our Phoenix application
```
mix phx.new hello
```

# Go to project dir
```
cd hello
```

# Create database
``` 
mix ecto.create
```

# Start Phoenix app:
```
mix phx.server
```

or

```
iex -S mix phx.server
```

# Stop Phoenix app:
```ctrl-c``` twice

# Dev Tools
## Investigate routes
```
mix phx.routes
```
## Path helpers

https://hexdocs.pm/phoenix/routing.html

```
iex -S mix
```

# Database
## Create
```
mix phx.gen.schema User users name:string email:string bio:string number_of_pets:integer
```

## Migrate
```
mix ecto.migrate
```

## Dev test
```
iex -S mix
alias Hello.User
changeset = User.changeset(%User{}, %{})
changeset.valid?
changeset.errors
```
## Update changes
```
recompile()
```
## Test record
```
params = %{name: "Joe Example", email: "joe@example.com", bio: "An example to all", number_of_pets: 5, random_key: "random value"}
changeset = User.changeset(%User{}, params)
changeset.valid?
changeset.changes
```

## Data persistence
```
iex -S mix
alias Hello.{Repo, User}
Repo.insert(%User{email: "user1@example.com"})
Repo.insert(%User{email: "user2@example.com"})
```

## Get data
```
Repo.all(User)
```

## Get partial data
```
import Ecto.Query
Repo.all(from u in User, select: u.email)
Repo.one(from u in User, where: ilike(u.email, "%1%"), select: count(u.id))
```

## Map of all user id's to their email
```
Repo.all(from u in User, select: %{u.id => u.email})
```

# Context
## Add Product
```
mix phx.gen.html Catalog Product products title:string description:string price:decimal views:integer
```
then ```in lib/hello_web/router.ex```:
```
  scope "/", HelloWeb do
    pipe_through :browser

    ...

    resources "/products", ProductController
  end
```
then
```
    mix ecto.migrate
```
then
http://localhost:4000/products

## Add Category
```
mix phx.gen.context Catalog Category categories title:string:unique
```

## Many-to-Many
```
mix ecto.gen.migration create_product_categories
```
update migration file:
```
defmodule Hello.Repo.Migrations.CreateProductCategories do
  use Ecto.Migration

  def change do
    create table(:product_categories, primary_key: false) do
      add :product_id, references(:products, on_delete: :delete_all)
      add :category_id, references(:categories, on_delete: :delete_all)
    end

    create index(:product_categories, [:product_id])
    create index(:product_categories, [:category_id])
    create unique_index(:product_categories, [:product_id, :category_id])
  end
end
```
then
```
mix ecto.migrate
```
then create content priv/repo/seeds.exs:
```
for title <- ["Home Improvement", "Power Tools", "Gardening", "Books"] do
  {:ok, _} = Hello.Catalog.create_category(%{title: title})
end
```
then:
```
mix run priv/repo/seeds.exs
```
add many-to-manyy relation in lib/hello/catalog/product.ex
```
many_to_many :categories, Category, join_through: "product_categories", on_replace: :delete
```