---
chapter: Controllers
title: Routing
---

# Routing

Padrino provides advanced routing definition support to make routes and URL
generation much easier. This routing system supports named route aliases and
easy access to url paths. The benefits of this is that instead of having to
hard-code route URLs into every area of your application, now we can just define
the URLs in a single spot and then attach an alias which can be used to refer to
the URL throughout the application.

## Basic Routing Aliases

The routing system supports named aliases by using symbols instead of strings
for your routes:

```ruby
Demo::App.controllers :page do
  get :index do
    # url is generated as '/'
    # url_for(:index) => "/"
  end

  get :account, :with => :id do
     # url is generated as '/account/:id'
     # url_for(:account, :id => 5) => "/account/5"
     # access params[:id]
  end
end
```

These routes can then be referenced anywhere in the application:

```haml
= link_to "Index", url_for(:index)
= link_to "Account", url_for(:account, :id => 1)
```

## Inline Route Alias Definitions

The routing plugin also supports inline route definitions in which the explicit
URL and the named alias are both defined:

```ruby
Demo::App.controllers :acount do
  get :index, :map => '/index/example' do
    # url_for(:index) => "/index/example"
  end

  get :account, :map => '/the/accounts/:name/and/:id' do
    # url_for(:account, :name => "John", :id => 5) => "/the/accounts/John/and/5"
    # access params[:name] and params[:id]
  end
end
```

Routes defined inline this way can be accessed and treated the same way as traditional named aliases:

```haml
= link_to "Index Page", url_for(:index)
= link_to "Account Page", url_for(:account, :id => 1)
```

## Namespaced Route Aliases

There is also support for namespaced routes which are organized into a named
controller group:

```ruby
Demo::App.controllers :admin do
  get :index do
    # url is generated as '/admin/'
    # url_for(:admin, :index) => "/admin"
  end

  get :show, :map => "/admin/:id" do
     # url is generated as "/admin/#{params[:id]}"
     # url_for(:admin, :show, :id => 5) => "/admin/5"
  end
end
```

You can then reference these routes using the same `url_for` method:

```haml
= link_to 'admin show page', url_for(:admin, :index)
= link_to 'admin index page', url_for(:admin, :show, :id => 25)
```

If you prefer explicit URLs to named aliases, that is also supported within a
specified controller group:

```ruby
Demo::App.controllers "/admin" do
  get "/show", :name => :show do
    # url is generated as "/admin/show"
  end

  get "/other/:id", :name => :other do
    # url is generated as "/admin/#{params[:id]}"
  end
end
```
You can then reference these routes using the same `url_for` method:

```haml
= link_to 'admin show page', url_for(:admin, :show)
= link_to 'admin index page', url_for(:admin, :other, :id => 25)
```

## Named Parameters

With Padrino you can also specify named parameters within your route definition:

```ruby
Demo::App.controllers :admin do
  get :show, :with => :id do
    # url is generated as "/admin/show/#{params[:id]}"
    # url_for(:admin, :show, :id => 5) => "/admin/show/5"
  end

  get :other, :with => [:id, :name]  do
    # url is generated as "/admin/other/#{params[:id]}/#{params[:name]}"
    # url_for(:admin, :other, :id => 5, :name => "hey") => "/admin/other/5/hey"
  end
end
```

You can then reference the URLs using the same `url_for` method:

```haml
= link_to 'admin show page', url_for(:admin_show, :id => 25)
= link_to 'admin other page', url_for(:admin_other, :id => 25, :name => :foo)
```

### Nested Routes

You can specify parent resources in padrino with the `:parent` option on the
controller:

```ruby
Demo::App.controllers :product, :parent => :user do
  get :index do
    # url is generated as "/user/#{params[:user_id]}/product"
    # url_for(:product, :index, :user_id => 5) => "/user/5/product"
  end
  get :show, :with => :id do
    # url is generated as "/user/#{params[:user_id]}/product/show/#{params[:id]}"
    # url_for(:product, :show, :user_id => 5, :id => 10) => "/user/5/product/show/10"
  end
end
```

If need be the parent resource can also be specified on inline routes in
addition:

```ruby
Demo::App.controllers :product, :parent => :user do
  get :index, :parent => :project do
   # url is generated as "/user/#{params[:user_id]}/project/#{params[:project_id]}/product"
   # url(:product, :index, :user_id => 5, :project_id => 8) => "/user/5/project/8/product"
  end
end
```
