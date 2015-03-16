## Capturing Ruby on Rails Controller Methods ##

The following how-to was sent to me by Peter Vojtek:

There is an issue when controller method is captured via Rcapture -
this works quite nice in production mode, but not in development mode.
In development mode a controller is reloaded after each request which
means RCapture is invoked only in the first call of a controller
action, but all following calls of this action are without RCapture.

After some time I discovered quite simple solution how to enjoy
RCapture also in development mode and that is to put the AOP code into
following block (Rails 3.0+):

```
# config/application.rb
config.to_prepare do
  UserController.send :include, RCapture::Interceptable
  UserController.capture_pre :methods => [:login] do |cs|
    controller = cs.sender
    cs.predicate = false
    cs.return = controller.render :action => :go_somewhere_else
  end
end
```

In the same way it is possible to fix a similar problem - when a model
method captured by RCapture is called inside a controller.