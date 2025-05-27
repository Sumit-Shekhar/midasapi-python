# Group
The module provides functionality to create, manage, and synchronize different types of groups (Structure, Boundary, Load, and Tendon groups) in the model.

!!! info "Note."
    All the codes below assumes the initial import and MAPI Key definition.

```py
from midasapi import *
MAPI_KEY('eyJ1ciI6InN1bWl0QG1pZGFzaXQuY29tIiwicGciO252k81571d')
```

## Group Class

The Group class provides a unified interface to create different types of groups and includes nested classes for specific group types.

### Methods

#### create
Creates all defined groups (Structure, Boundary, Load, and Tendon groups) in Civil NX.

```py
Group.create()
```

#### sync
Synchronizes all group types from Civil NX and rebuilds internal group lists.

```py
Group.sync()
```

#### delete
Deletes all group data from both Python and Civil NX.

```py
Group.delete()
```

---

## 1. STRUCTURE GROUPS

A nested class within Group used to create and manage structure groups containing nodes and elements.

### Constructor
**<font color="green">`Group.Structure(name, nlist=[], elist=[])`</font>**

Creates a structure group with specified name and optional node/element lists.

### Parameters
* `name`: Name of the structure group
* `nlist (default=[])`: List of node IDs to include in the group
* `elist (default=[])`: List of element IDs to include in the group

#### Class Attributes
*Group.Structure.Groups* -> List of all structure groups.  

### Examples
```py
# Create nodes and elements first
for i in range(3):
    Node(i*10, 0, 0)
Node.create()

Element.Beam(1, 2)
Element.Beam(2, 3)
Element.create()

# Create structure groups
sg1 = Group.Structure("Main Girder", nlist=[1, 2, 3], elist=[1, 2])
sg2 = Group.Structure("Secondary", nlist=[1], elist=[1])

for sg in Group.Structure.Groups:
    print(f'Group ID: {sg.ID} | Name: {sg.NAME} | Nodes: {sg.NLIST} | Elements: {sg.ELIST}')

# Output:
# Group ID: 1 | Name: Main Girder | Nodes: [1, 2, 3] | Elements: [1, 2]
# Group ID: 2 | Name: Secondary | Nodes: [1] | Elements: [1]
```

### Methods

#### update
Updates an existing structure group with new node/element lists.

**<font color="green">`Group.Structure.update(name, operation="r", nlist=[], elist=[])`</font>**

##### Parameters
* `name`: Name of the group to update
* `operation (default="r")`: Operation type ("r" for replace, "a" for add)
* `nlist (default=[])`: List of node IDs
* `elist (default=[])`: List of element IDs

```py
# Replace existing lists
Group.Structure.update("Main Girder", "r", nlist=[1, 2, 3, 4], elist=[1, 2, 3])

# Add to existing lists
Group.Structure.update("Main Girder", "a", nlist=[5], elist=[4])
```

#### json
Returns a JSON representation of all Structure Groups defined in python.

```py
sg1 = Group.Structure("Main Girder", nlist=[1, 2], elist=[1])
print(Group.Structure.json())

# Output:
# {'Assign': {1: {'NAME': 'Main Girder', 'P_TYPE': 0, 'N_LIST': [1, 2], 'E_LIST': [1]}}}
```

#### create
Sends the current structure group list to Civil NX using a PUT request.

```py
Group.Structure.create()
```

#### get
Fetches structure groups from Civil NX and returns the JSON representation.

```py
print(Group.Structure.get())
```

#### sync
Retrieves Structure Group data from Civil NX and rebuilds the internal group list.

```py
Group.Structure.sync()
for sg in Group.Structure.Groups:
    print(f'Structure Group: {sg.NAME} | Nodes: {sg.NLIST} | Elements: {sg.ELIST}')
```

#### delete
Deletes all structure group data from both Python and Civil NX.

```py
Group.Structure.delete()
```

---

## 2. BOUNDARY GROUPS

A nested class within Group used to create and manage boundary groups.

### Constructor
**<font color="green">`Group.Boundary(name)`</font>**

Creates a boundary group with specified name.

### Parameters
* `name`: Name of the boundary group

#### Class Attributes
*Group.Boundary.Groups* -> List of all boundary groups.  

### Examples
```py
# Create boundary groups
bg1 = Group.Boundary("Support Group")
bg2 = Group.Boundary("Fixed Ends")

for bg in Group.Boundary.Groups:
    print(f'Boundary Group ID: {bg.ID} | Name: {bg.NAME}')

# Output:
# Boundary Group ID: 1 | Name: Support Group
# Boundary Group ID: 2 | Name: Fixed Ends
```

### Methods

#### json
Returns JSON representation of all boundary groups.

```py
bg1 = Group.Boundary("Support Group")
print(Group.Boundary.json())

# Output:
# {'Assign': {1: {'NAME': 'Support Group', 'AUTOTYPE': 0}}}
```

#### create
Sends boundary groups to Civil NX.

```py
Group.Boundary.create()
```

#### get
Fetches boundary groups from Civil NX.

```py
print(Group.Boundary.get())
```

#### sync
Synchronizes boundary groups from Civil NX.

```py
Group.Boundary.sync()
```

#### delete
Deletes all boundary groups from both Python and Civil NX.

```py
Group.Boundary.delete()
```

---

## 3. LOAD GROUPS

A nested class within Group used to create and manage load groups.

### Constructor
**<font color="green">`Group.Load(name)`</font>**

Creates a load group with specified name.

### Parameters
* `name`: Name of the load group

#### Class Attributes
*Group.Load.Groups* -> List of all load groups.  

### Examples
```py
# Create load groups
lg1 = Group.Load("Dead Load Group")
lg2 = Group.Load("Live Load Group")

for lg in Group.Load.Groups:
    print(f'Load Group ID: {lg.ID} | Name: {lg.NAME}')

# Output:
# Load Group ID: 1 | Name: Dead Load Group
# Load Group ID: 2 | Name: Live Load Group
```

### Methods

#### json
Returns JSON representation of all load groups.

```py
lg1 = Group.Load("Dead Load Group")
print(Group.Load.json())

# Output:
# {'Assign': {1: {'NAME': 'Dead Load Group'}}}
```

#### create
Sends load groups to Civil NX.

```py
Group.Load.create()
```

#### get
Fetches load groups from Civil NX.

```py
print(Group.Load.get())
```

#### sync
Synchronizes load groups from Civil NX.

```py
Group.Load.sync()
```

#### delete
Deletes all load groups from both Python and Civil NX.

```py
Group.Load.delete()
```

---

## 4. TENDON GROUPS

A nested class within Group used to create and manage tendon groups.

### Constructor
**<font color="green">`Group.Tendon(name)`</font>**

Creates a tendon group with specified name.

### Parameters
* `name`: Name of the tendon group

#### Class Attributes
*Group.Tendon.Groups* -> List of all tendon groups. 

### Examples
```py
# Create tendon groups
tg1 = Group.Tendon("PT Group 1")
tg2 = Group.Tendon("PT Group 2")

for tg in Group.Tendon.Groups:
    print(f'Tendon Group ID: {tg.ID} | Name: {tg.NAME}')

# Output:
# Tendon Group ID: 1 | Name: PT Group 1
# Tendon Group ID: 2 | Name: PT Group 2
```

### Methods

#### json
Returns JSON representation of all tendon groups.

```py
tg1 = Group.Tendon("PT Group 1")
print(Group.Tendon.json())

# Output:
# {'Assign': {1: {'NAME': 'PT Group 1'}}}
```

#### create
Sends tendon groups to Civil NX.

```py
Group.Tendon.create()
```

#### get
Fetches tendon groups from Civil NX.

```py
print(Group.Tendon.get())
```

#### sync
Synchronizes tendon groups from Civil NX.

```py
Group.Tendon.sync()
```

#### delete
Deletes all tendon groups from both Python and Civil NX.

```py
Group.Tendon.delete()
```

---

## Complete Example

```py
from midasapi import *

MAPI_KEY("eyJ1ciI6IklOMjQwN0ZZVDIiLCJwZyI6ImNpdmlsIiwi")  # Paste your Mapi Key

# Create nodes and elements
for i in range(6):
    Node(i*5, 0, 0)
Node.create()

for i in range(5):
    Element.Beam(i+1, i+2)
Element.create()

# Create Structure Groups
Group.Structure("Main Span", nlist=[1, 2, 3, 4], elist=[1, 2, 3])
Group.Structure("Side Span", nlist=[4, 5, 6], elist=[4, 5])

# Update structure group
Group.Structure.update("Main Span", "a", nlist=[5], elist=[])

# Create other group types
Group.Boundary("Support Boundary")
Group.Boundary("Expansion Joint")

Group.Load("Dead Load Group")
Group.Load("Live Load Group")

Group.Tendon("PT Cable Group 1")
Group.Tendon("PT Cable Group 2")

# Create all groups in Civil NX
Group.create()

# Display group information
print("Structure Groups:")
for sg in Group.Structure.Groups:
    print(f'  {sg.NAME}: Nodes={sg.NLIST}, Elements={sg.ELIST}')

print("\nBoundary Groups:")
for bg in Group.Boundary.Groups:
    print(f'  {bg.NAME}')

print("\nLoad Groups:")
for lg in Group.Load.Groups:
    print(f'  {lg.NAME}')

print("\nTendon Groups:")
for tg in Group.Tendon.Groups:
    print(f'  {tg.NAME}')
```