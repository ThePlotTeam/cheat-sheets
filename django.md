# **Django CheatSheet**

## 1. Built-in template tags

---

- Include the `name.html` fine in the template:

```django
{% include name.html %}
```

- Wrap the all template in the `name.html` one (to include in top of a template, ususally):

```django
{% extends name.html %}
```

- Replace a part of the *extended* content with current template code (same marker should be used in the *extended* file):

```django
{% block name %}
...
{% enblock %}
```

- Include data and variables from the current context and add a filter to it:

```django
{{ myvar.param|filter }}
```

- Iterate through context's lists with template:

```django
<ul> 
{% for item in context_list %}
<li> {{ item }} </li> 
{{ forloop.counter }} <!--Give the index of iteration in the loop-->
{% endfor %}
</ul>
```

- Template condition:

```django
{% if condition %}
<!-- show some stuff -->
{% elif condition_2 %}
<!-- show some stuff -->
{% else %}
<!-- show some other stuff -->
{% endif %}
```

=> [List of all template tags and filters (Django official Doc)](https://docs.djangoproject.com/en/3.1/ref/templates/builtins/)

## 2. Views

---
Basic render function:

```py
def some_view(request):
 # Define context
 ctxt = ...

 return render(request, "path_to_template.html", ctxt)
```

### Create a model form rendering using Django ModelForm

`views.py`

```py
def model_create_view(request):
 form = ModelForm(request.POST or None)
 if form.is_valid(): # check if the form is correctly filled
  form.save()
  form = ModelForm()pip #when submitted, reset blank form
 
 ctxt = { 'form' = form }
 return render(request, "path_to_template.html", ctxt)
```

`template.html`

```django
<!--POST is because we make changes on the server-->
<form method='POST' action='path-to-redirect-if-any'>
 
 <!--For security measures when using POST-->
 {% csrf_token %} 

 <!--transform the form in context in a series of paragraphs-->
 {{ form.as_p }} 
 
 <!--Validation button-->
 <input type='submit' value='Save'/>
</form>
```

### Create a model form rendering using Django Basic Forms

`forms.py`

```py
class MyForm(forms.Form):
 value1 = forms.CharField()
 value2 = forms.NumberField()
```

`views.py`

```py
from .forms import MyForm

def model_create_view(request):
 form = MyForm()

 if request.method == 'POST':
  form = MyForm(request.POST)
  if form.is_valid():
   # If the form is complete and valid, then create the 
   Model.object.create(**form.clean_data)
 
 ctxt = { 'form' = form }
 return render(request, "path_to_template.html", ctxt)
```

`template.html`

```django
<!--POST is because we make changes on the server-->
<form method='POST' action='path-to-redirect-if-any'>
 
 <!--For security measures when using POST-->
 {% csrf_token %} 

 <!--transform the form in context in a series of paragraphs-->
 {{ form.as_p }} 
 
 <!--Validation button-->
 <input type='submit' value='Save'/>
</form>
```

### Create a model form rendering using raw HTML

`views.py`

```py
def model_create_view(request):
 
 if request.method == 'POST':
  elm1 = request.POST.get('E1')
  elm2 = request.POST.get('E2')
  ...
  # elements are the attributes of the model
  Model.object.create(element1=elm1, element2=elm2, ...) 
 
 ctxt = { }
 return render(request, "path_to_template.html", ctxt)
```

`template.html`

```django
<!--POST is because we make changes on the server-->
<form method='POST' action='path-to-redirect-if-any'>
 
 <!--For security measures when using POST-->
 {% csrf_token %} 

 <!--Input fields-->
 <input type='text' name='E1' placeholder='element1'/>
 <input type='number' name='E2' placeholder='element2'/>
 
 <!--Validation button-->
 <input type='submit' value='Save'/>
</form>
```

## 3. Model Manager

---

- `Model.objects.create(**args)` : create a **Model** object

- `Model.objects.all()` : return all the instances of **Model**

- `Model.objects.get(**args)` : return the (unique) **Model** objects matching the arguments

- `Model.objects.filter(**args)` : same as get() but with any number of matching records

- `Model.objects.exclude(**args)` : opposite of filter (include unmatched records)

### One-To-Many Relations

Quick Example:

```py
from django.db import models

class A(models.Model):
 name = models.CharField(...)

class B(models.Model):
 name = models.CharField(...)
 a_link = models.ForeignField(A, models.CASCADE)
```

Here, there is a one-to-many relation from **A** to **B**.

We can get the instance of model A from B with `B_instance.a_link`.

Conversely, we can get all the instances of B connected to A with `A_instance.b_set.all()`. 'b_set' is internally created by Django and depends on the name of the class **B** (but always full lowercase).

It is also possible to create a instance of B from an instance of A with `A_instance.b_set.create(...)` (no need to give the *a_link* attribute though).

## 4. Form Validation

---

```py
class MyForm(forms.Form):
 value1 = forms.CharField()
 value2 = forms.NumberField()

 # Validation methods
 def clean_value1(self, *args, **kwargs):
  value = self.cleaned_data.get("value1")
  if condition:
   raise forms.ValidationError("condition is not fullfilled")
 
 def clean_value2(...):
  <...>
 
```
