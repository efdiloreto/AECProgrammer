# Select elements from Model

There are several ways to get elements from the model using the Revit API. Here we are going to explore some of them.
Before starting, make sure you have installed <u>[Revit Python Shell](https://github.com/architecture-building-systems/revitpythonshell/releases)</u>.
Please note that Revit Python Shell already has imported all the necessary classes and objects from the Revit API, so you can run directly in your RPS Editor all the code used in this story.

## API Reference
- [FilteredElementCollector](https://www.revitapidocs.com/2022/16ee3d6e-a95d-0e58-3637-2ac1109f2f8d.htm)
- [Document](https://www.revitapidocs.com/2022/f4fb9f58-4933-f84e-8715-3ea73291b27f.htm)
- [BuiltInCategory](https://www.revitapidocs.com/2022/ba1c5b30-242f-5fdc-8ea9-ec3b61e6e722.htm)
- [ViewSheet](https://www.revitapidocs.com/2022/af2ee879-173d-df3a-9793-8d5750a17b49.htm)
- [UIDocument](https://www.revitapidocs.com/2022/295b48c8-0571-ad5c-eead-baea84a6787c.htm)
- [Selection](https://www.revitapidocs.com/2022/31b73d46-7d67-5dbb-4dad-80aa597c9afc.htm)
- [ISelectionFilter](https://www.revitapidocs.com/2022/d552f44b-221c-0ecd-d001-41a5099b2f9f.htm)

## Using FilteredElementCollector

### All elements in the model

``` py linenums="1"
# The variable doc is already instantiated in Revit Python Shell
elements = FilteredElementCollector(doc).WhereElementIsNotElementType()
for element in elements:
    print(element.Name)
```

### All elements in the active view
Here we are using the active view, but you can pass the Id of any view to the FilteredElementCollector constructor.

``` py linenums="1"
# The variable doc is already instantiated in Revit Python Shell
active_view = doc.ActiveView

elements = FilteredElementCollector(doc, active_view.Id).WhereElementIsNotElementType()
for element in elements:
    print(element.Name)
```

### All elements owned by a view
For example, an annotation element.

``` py linenums="1"
# The variable doc is already instantiated in Revit Python Shell
active_view = doc.ActiveView

elements = FilteredElementCollector(doc).OwnedByView(active_view.Id).WhereElementIsNotElementType()
for element in elements:
    print(element.Name)
```

### All elements of a specific category
In this example, we are getting all the elements from the model that have the category "Floors".

``` py linenums="1"
# The variable doc is already instantiated in Revit Python Shell
elements = FilteredElementCollector(doc).OfCategory(BuiltInCategory.OST_Floors).WhereElementIsNotElementType()
for element in elements:
    print(element.Name)
```

### All elements of a specific class
In this example, we are getting all the elements from the model that are instance of the class ViewSheet (Sheets in the model)

``` py linenums="1"
# The variable doc is already instantiated in Revit Python Shell
elements = FilteredElementCollector(doc).OfClass(ViewSheet).WhereElementIsNotElementType()
for element in elements:
    print(element.Name)
```


### All type elements of a specific category
In this example, we are getting all the type elements of category "Floors"

``` py linenums="1"
# The variable doc is already instantiated in Revit Python Shell
elements = FilteredElementCollector(doc).OfCategory(BuiltInCategory.OST_Floors).WhereElementIsElementType()
for element in elements:
    print(element.Name)
```

### Combining methods in FilteredElementCollector
Let's get all the elements of category floors from the current view. We can combine method inside the same 

``` py linenums="1"
FilteredElementCollector instance.
# The variable doc is already instantiated in Revit Python Shell
active_view = doc.ActiveView

elements = FilteredElementCollector(doc, active_view.Id).OfCategory(BuiltInCategory.OST_Floors).WhereElementIsNotElementType()
for element in elements:
    print(element.Name)
```

### Elements from linked models
The procedure is the same, the only difference is we have to use the "Document" from the linked model, for example:

``` py linenums="1"
# __revit__ is a instance of Autodesk.Revit.UI.UIApplication

for linked_doc in __revit__.Application.Documents:
    if linked_doc.IsLinked:
        elements = FilteredElementCollector(linked_doc).OfCategory(BuiltInCategory.OST_Floors).WhereElementIsNotElementType()
for element in elements:
        for element in elements:
            print(element.Name)
```

## Graphical Selection
### Selected elements in model

``` py linenums="1"
# The variables uidoc and doc already instantiated in Revit Python Shell

# Getting ids from selected objects
element_ids = uidoc.Selection.GetElementIds()
for id in element_ids:
    
# Getting element from id 
    element = doc.GetElement(id)
    print(element.Name)
```

### Pick elements by rectangle

``` py linenums="1"
# The variable uidoc is already instantiated in Revit Python Shell

# You can specify any prompt message
elements = uidoc.Selection.PickElementsByRectangle("Select elements")
for element in elements:
    print(element.Name)
```

### Pick elements by rectangle and a filter

``` py linenums="1"
from Autodesk.Revit.UI.Selection import ISelectionFilter

# Custom filter that takes a category name and filter the elements
# Alternatively you can create any type of filter
class CategoryISelectionFilter(ISelectionFilter):
    def __init__(self, category_name):
        self.category_name = category_name

    def AllowElement(self, e):
        if e.Category.Name == self.category_name:
            return True
        else:
            return False

    def AllowReference(self, ref, point):
        return False

# Instantiating the filter to allow only floors
# Alternatively you can instantiate it with other categories
floors_filter = CategoryISelectionFilter("Floors")

elements = uidoc.Selection.PickElementsByRectangle(floors_filter, "Select floors")
for element in elements:
    print(element.Name)
```