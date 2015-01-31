---
layout: post
title: "rack 技巧"
date: "2012-08-13T12:58:00+08:00"
comments: true
categories: 
---

最近比較常發佈基於 serve 的 rack app 到 heroku 上面, 通常是網站的 prototype.

常常因為網站不公開, 所以需要特定的人才看的到.

這時候只需要加一下 rack middleware 就可以達到這個需求


### 使用 Rack::Auth::Basic

``` ruby
use Rack::Auth::Basic, "Snack cabinet" do |username, password|
  username == 'admin' && password == 'foobar'
end
```

### 使用 Warden


``` 
require "warden"
class FailLoginApp
  def self.call(env)

    [200, { "Content-Type" => "text/html" }, [<<EOL
      <html>
      <body>
      <h1>Login</h1>
      <form action="/" method="post">
    username:<input type="text" name="username">

    password:<input type="password" name="password">
    <input type="submit">
    </form>
    </body>
    </html>
EOL
    ]]  
  end 
end

class CheckLogin

  def initialize(app, opts={})
    @app=app
    @opts=opts
  end

  def call(env)
    request = Rack::Request.new(env)

    env['warden'].logout if request.params["logout"]
    env['warden'].authenticate!

    @app.call(env)
  end
end

Warden::Strategies.add(:password) do
  def valid?
    params["username"] || params["password"]
  end

  def authenticate!
    if params["username"] == "admin" && params["password"] == "foobar"  
      success!({})
    else
      fail!("Could not log in")
    end
  end
end

use Rack::Session::Cookie

use Warden::Manager do |manager|
  manager.default_strategies :password
  manager.failure_app = FailLoginApp
end

use CheckLogin
```
