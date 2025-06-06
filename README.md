# PyBootstrapGUI

A Python module for generating HTML using Bootstrap CSS.

PyBootstrapGUI simplifies the creation of Bootstrap-powered HTML from Python code. It is ideal for static web pages, lightweight GUIs using [pywebview](https://pywebview.flowrl.com/), and generating dynamic content with minimal boilerplate.

Used Bootstrap version: 5.3.3

## Installation

```python
pip install PyBootstrapGUI
```

## Usage

```python
from pybootstrapgui import Page
from pybootstrapgui.components import (
    Body,
    Button,
    Container,
    Head,
    Heading,
    Script,
    Style,
)
from pybootstrapgui.tag import Div, Tag
from pybootstrapgui.utils import B, C, D, H, R, V


class MyPage(Page):
    def __init__(self):
        super().__init__()

    def compose(self):
        with Head(title="Title"):
            internal_css = """
            body {
                background-color: red;
            }
            """
            yield Style(css=internal_css)
            internal_script_js = """
            console.log('Hello World');
            """
            yield Script(js=internal_script_js)
        with Body(style="font-size: 14px"):
            yield Heading(h=1, text="Hello World", v_align="center")
            with Container(display="flex", class_=f"{V.CENTER} {H.CENTER} vh-100"):
                button = Button(" Click", id="mybutton", icon="circle")
                button.listener("click", "() => {alert('Button clicked')}")
                yield button
            with Div():
                # This component can be anything
                yield Tag("div", "I am div", class_="text-center")


if __name__ == "__main__":
    page = MyPage()
    page.save("index.html", indent=True, short_empty_elements=False)
    page.open()

```

### Offline Bootstrap Support

To enable offline Bootstrap support, serve static files locally

```python
# Other imports ..
# Important import
from pybootstrapgui.utils import copy_bootstrap_static_to

class MyPage(Page):
    def __init__(self):
        super().__init__()
        # Dir where to copy bootstrap static files
        copy_bootstrap_static_to(str(Path(__file__).parent.absolute() / "static"))

    def compose(self):
        # Path to static bootstrap files
        with Head(title="Title", bootstrap_css="static/css/bootstrap.min.css", bootstrap_icons_css="static/css/bootstrap-icons.min.css"):
            internal_script_js = """console.log('Hello World');"""
            yield Script(js=internal_script_js)
        with Body(style="font-size: 14px; background-color: #f0f0f0", bootstrap_js="static/js/bootstrap.bundle.min.js"):
            pass
```

### Extensions

#### Jinja templates

To render such templates custom `render_template` function should be used

Requires `short_empty_elements=False`

```python
from pybootstrapgui.extensions.jinja_components import JinjaCondition, JinjaLoop

with JinjaCondition("{% if icon %}"):
    yield Image(src="data:image/png;base64,{{ icon }}", alt="image")
```

```python
from pybootstrapgui.extensions.jinja_components import render_template

page = MyPage()
html = page.build(indent=True, short_empty_elements=False)
html = render_template(html, icon="...")
```

### Custom components

You can create your own custom components and use them.
Limitations: custom components cannot use with context manager.

```python
from pybootstrapgui.base import CustomComponent

class MyComponent(CustomComponent):

    def __init__(self):
        super().__init__()

    def compose(self):
        with Div():
            yield Button("Some button", color="warning", v_align="center")
            
```

```python
yield MyComponent()
```

## Simple pywebview + PyBootstrapGUI app example

```bash
pip install PyBootstrapGUI pywebview
```

[simple_webview_app.py](examples/simple_webview_app/simple_webview_app.py)

## Included low level html builder

This module uses low level html builder that can be used like this

```python
from pybootstrapgui.html_builder import Doc

doc = Doc()

with doc.tag("head"):
    doc.tag("meta", {"charset": "utf-8"})()
    with doc.tag("title") as e:
        e.text = "Title"
    doc.tag(
        "link",
        {"rel": "stylesheet", "href": "https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css"},
    )()

    with doc.tag("script", {"type": "text/javascript"}) as script:
        inline_script_js = """console.log('Hello World');"""
        script.text = inline_script_js
with doc.tag("body"):
    with doc.tag("div") as div:
        div.text = "{% if title %}"
        doc.tag("button", {"class": "btn btn-primary", "type": "button"})("Submit")
        div[-1].tail = "{% endif %}"
# print(doc.build(indent=True, short_empty_elements=False))
doc.save(indent=True, short_empty_elements=False)
```

## Resources

**Bootstrap**
<https://getbootstrap.com/docs/5.3/getting-started/download/>

**Bootstrap icons**
<https://icons.getbootstrap.com/#install>

**Inline source highlighter**
<https://marketplace.visualstudio.com/items?itemName=jurooravec.python-inline-source-2>
