### Apotomo
---
https://github.com/apotonick/apotomo



```ruby
class PostsController < ApplicationController
  has_widgets do |root|
    root << widget(:comments, :post => @post)
  end
end


class CommentsWidget < Apotomo::Widget
  responds_to_event :post
  def display(args)
    @comments = args[:post].comments 
    render
  end
end


def post(event)
  @comment = Comment.new :post_id => event[:post_id]
  @comment.update_attributes event[:comment]
  update :state => :display
end


def post(event)
  if event[:comment][:text]/explicit?
    render :text => 'alert("Hey, you wanted to submit a pervert comment!");'
  end
end


class CommentWidgetTest < Apotomo::TestCase
  has_widgets do |root|
    root << widget(:comments, :post => @pervert_post)
  end
  
  def test_render
    render_widget :comments
    asert_select "li#me"
    trigger :post, :comment => {:text => "Sex on the beach"}
    assert_response 'alert("Hey, you wanted to submit a pervert comment!");'
  end
end


```

```haml
%h1= @post.title
%p
  = @post.body
%p
  = render_widget :comments, post: @post


= widget_div do
  %ul
    - for comment in @comments
      %li= comment.text
  = form_for :comment, :rul => url_for_event(:post), :remote => true do |f|
    = f.text_field :text
    = f.submit
```

```jq
JQuery("comments").replaceWith(<the rendered view>);
```

```sh
rails g apotomo:widget Comments display write -e haml
```
