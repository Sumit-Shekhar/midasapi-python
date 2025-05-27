# Boundary
The module provides functionality to create, manage, and synchronize boundary conditions including supports, elastic links, and rigid links in the model.

!!! info "Note."
    All the codes below assumes the initial import and MAPI Key definition.

```py
from midasapi import *
MAPI_KEY('eyJ1ciI6InN1bWl0QG1pZGFzaXQuY29tIiwicmciO252k81571d')
```

## Boundary Class

The Boundary class provides a unified interface to create different types of boundary conditions and includes nested classes for specific boundary types.

### Methods

#### create
Creates all defined boundary conditions (Supports, Elastic Links, and Rigid Links) in Civil NX.

```py
Boundary.create()
```

#### delete
Deletes all boundary conditions from both Python and Civil NX.

```py
Boundary.delete()
```

#### sync
Synchronizes all boundary conditions from Civil NX to Python.

```py
Boundary.sync()
```

---

## 1. SUPPORT

A nested class within Boundary used to create nodal supports with various constraint conditions.

### Constructor
**<font color="green">`Boundary.Support(node, constraint, group = "")`</font>**

Creates support conditions at specified nodes with defined constraints.

### Parameters
* `node`: Node ID where support is applied
* `constraint`: Constraint definition (string of 1s and 0s, or predefined keywords)
* `group (default="")`: Boundary group name

#### Constraint Options
* **String format**: "1110000" (7 characters for DOF: DX, DY, DZ, RX, RY, RZ, WARP)
* **Predefined keywords**:
  - `"pin"`: Pinned support (translational constraints only)
  - `"fix"`: Fixed support (all DOF constrained)
  - `"roller"`: Roller support (vertical constraint only)
  - `"free"`: Free condition (no constraints)

#### Class Attributes
*Boundary.Support.sups* -> List of all support instances.

### Examples
```py

#Create Beam
for i in range(3):
    Node(i*10,0,0)
    Node.create()

Element.Beam(1,2)
Element.Beam(2,3)
Element.create()
    
#Apply Support

Boundary.Support(1,"1111111","") 
Boundary.Support(3,"pin","") 

#create Support 
Boundary.Support.create()

#Note: "" represents the absence of a boundary group. By default, it is set to "". 
# Therefore, the following two commands are equivalent:Boundary.Support(3, "pin") and Boundary.Support(3, "pin", "").
```

### Methods

#### json
Returns JSON representation of all supports.

```py
sup1 = Boundary.Support(101, "fix")
print(Boundary.Support.json())
```

#### create
Sends support data to Civil NX.

```py
Boundary.Support.create()
```

#### get
Fetches support data from Civil NX.

```py
print(Boundary.Support.get())
```

#### sync
Synchronizes supports from Civil NX to Python.

```py
Boundary.Support.sync()
```

#### delete
Deletes all supports from both Python and Civil NX.

```py
Boundary.Support.delete()
```

---

## 2. ELASTIC LINK

A nested class within Boundary used to create elastic connections between nodes with various spring properties and link types.

### Constructor
**<font color="green">`Boundary.ElasticLink(i_node, j_node, group = "", id = None, link_type = "GEN", sdx = 0, sdy = 0, sdz = 0, srx = 0, sry = 0, srz = 0, shear = False, dr_y = 0.5, dr_z = 0.5, beta_angle = 0, dir = "Dy", func_id = 1, distance_ratio = 0)`</font>**

Creates elastic links between two nodes with specified spring properties and behavior.

### Parameters
* `i_node`: First node ID
* `j_node`: Second node ID
* `group (default="")`: Boundary group name
* `id (default=None)`: Manual ID assignment (auto-assigned if None)
* `link_type (default="GEN")`: Type of elastic link
* `sdx, sdy, sdz (default=0)`: Translational spring stiffness in X, Y, Z directions
* `srx, sry, srz (default=0)`: Rotational spring stiffness about X, Y, Z axes
* `shear (default=False)`: Consider shear effects
* `dr_y, dr_z (default=0.5)`: Distance ratios for Y and Z directions
* `beta_angle (default=0)`: Rotation angle in degrees
* `dir (default="Dy")`: Direction for specialized link types
* `func_id (default=1)`: Function ID for specialized link types
* `distance_ratio (default=0)`: Distance ratio for specialized link types

#### Link Types
* **"GEN"**: General elastic link with full stiffness matrix
* **"RIGID"**: Rigid connection (infinite stiffness)
* **"TENS"**: Tension-only link (works only in tension)
* **"COMP"**: Compression-only link (works only in compression)
* **"MULTI LINEAR"**: Multi-linear behavior with function definition
* **"SADDLE"**: Saddle-type connection
* **"RAIL INTERACT"**: Rail track interaction link

#### Class Attributes
*Boundary.ElasticLink.links* -> List of all elastic link instances.

### Examples

##### General Elastic Link
```py
#General Elastic Link Example

#Create Beam and Node
for j in range(2):
    for i in range(2):
        Node(i*10,0,-1*j)
        Node.create()
Element.Beam(1,2)
Element.create()

#Create General Elastic Link    
Boundary.ElasticLink(1, 3, "", 1, "GEN", 1000, 1000, 1000, 100, 100, 100)
Boundary.ElasticLink.create()
```

##### Rigid Link
```py
#Rigid Link Example

#Create Beam and Node
for j in range(2):
    for i in range(2):
        Node(i*10,0,-1*j)
        Node.create()
Element.Beam(1,2)
Element.create()

#Create Rigid Link    
Boundary.ElasticLink(2, 4, "", 1, "RIGID")
Boundary.ElasticLink.create()

```

##### ension-Onl & Compression-Only Link
```py
#Tension-Onl & Compression-Only Link Example

#Create Beam and Node
for j in range(2):
    for i in range(2):
        Node(i*10,0,-1*j)
        Node.create()
Element.Beam(1,2)
Element.create()

#Tension-Onl & Compression-Only Link    
Boundary.ElasticLink(1, 3, "", 1, "TENS",500)
Boundary.ElasticLink(2, 4, "", 2, "COMP",600)
Boundary.ElasticLink.create()
```

##### Saddle type Link
```py
#Saddle type Link Example

#Create Beam and Node
for j in range(2):
    for i in range(2):
        Node(i*10,0,-1*j)
        Node.create()
Element.Beam(1,2)
Element.create()


#Create Saddle type Link    
Boundary.ElasticLink(1, 3, "", 1, "SADDLE")
Boundary.ElasticLink.create()

```

##### Multi-Linear Link
```py
#Multi-linear link Example

#Create Beam and Node
for j in range(2):
    for i in range(2):
        Node(i*10,0,-1*j)
        Node.create()
Element.Beam(1,2)
Element.create()


# Multi-linear link
Boundary.ElasticLink(1, 3, "", 1, "MULTI LINEAR", dir="Dy", func_id=1)
Boundary.ElasticLink.create()

#Note: Before running this code, the Force-Deformation function must be created in Civil NX to avoid any errors.

```

##### Rail Interaction Link
```py
# Rail track interaction link Example

#Create Beam and Node
for j in range(2):
    for i in range(2):
        Node(i*10,0,-1*j)
        Node.create()
Element.Beam(1,2)
Element.create()


# Rail track interaction link
Boundary.ElasticLink(2, 4, "", 1, "RAIL INTERACT", dir="Dy", func_id=1)
Boundary.ElasticLink.create()

#Note: Before running this code, the Rail Interaction function must be created in Civil NX to avoid any errors.
```

### Methods

#### json
Returns JSON representation of all elastic links.

```py
link1 = Boundary.ElasticLink(1, 2, "Group1", 1, "GEN", 1000, 1000, 1000)
print(Boundary.ElasticLink.json())
```

#### create
Sends elastic link data to Civil NX.

```py
Boundary.ElasticLink.create()
```

#### get
Fetches elastic link data from Civil NX.

```py
print(Boundary.ElasticLink.get())
```

#### sync
Synchronizes elastic links from Civil NX to Python.

```py
Boundary.ElasticLink.sync()
```

#### delete
Deletes all elastic links from both Python and Civil NX.

```py
Boundary.ElasticLink.delete()
```

---

## 3. RIGID LINK

A nested class within Boundary used to create rigid connections between a master node and multiple slave nodes.

### Constructor
**<font color="green">`Boundary.RigidLink(master_node, slave_nodes, group = "", id = None, dof = 111111)`</font>**

Creates rigid links connecting a master node to one or more slave nodes with specified degrees of freedom.

### Parameters
* `master_node`: Master node ID (controls the motion)
* `slave_nodes`: List of slave node IDs (follow master node motion)
* `group (default="")`: Boundary group name
* `id (default=None)`: Manual ID assignment (auto-assigned if None)
* `dof (default=111111)`: Degrees of freedom constraint (6-digit integer)

#### DOF Constraint Format
* **6-digit format**: DXDYDZ RXRYRZ (e.g., 111111 for all DOF, 111000 for translations only)
* **1**: Constrained (rigid connection)
* **0**: Free (no constraint)

#### Class Attributes
*Boundary.RigidLink.links* -> List of all rigid link instances.

### Examples

##### Single Slave Node
```py
# Create nodes
for i in range(5):
    Node(i*5, 0, 0)
Node.create()

# Rigid link between master and single slave
rlink1 = Boundary.RigidLink(1, [2], "Group1", 1, 111111)
Boundary.RigidLink.create()
```

##### Multiple Slave Nodes
```py
# Create nodes
for i in range(5):
    Node(i*5, 0, 0)
Node.create()

# Rigid link with multiple slave nodes
rlink2 = Boundary.RigidLink(3, [4, 5], "Group2", 2, 111111)
Boundary.RigidLink.create()
```

##### Partial DOF Constraint
```py
# Create nodes
for i in range(5):
    Node(i*5, 0, 0)
Node.create()

# Rigid link with only translational constraints
rlink3 = Boundary.RigidLink(1, [2, 3], "Group3", 3, 111000)
Boundary.RigidLink.create()
```

### Methods

#### json
Returns JSON representation of all rigid links.

```py
rlink1 = Boundary.RigidLink(1, [2, 3], "Group1", 1, 111111)
print(Boundary.RigidLink.json())
```

#### create
Sends rigid link data to Civil NX.

```py
Boundary.RigidLink.create()
```

#### get
Fetches rigid link data from Civil NX.

```py
print(Boundary.RigidLink.get())
```

#### sync
Synchronizes rigid links from Civil NX to Python.

```py
Boundary.RigidLink.sync()
```

#### delete
Deletes all rigid links from both Python and Civil NX.

```py
Boundary.RigidLink.delete()
```

---

## Complete Example

```py
from midasapi import *

MAPI_KEY("eyJ1ciI6IklOMjQwN0ZZVDIiLCJwZyI6ImNpdmlsIiwi") # Paste your MAPI Key

#Create Beam and Node
for j in range(6):
    for i in range(2):
        Node(i*10,j*2,0)
        Node.create()

for j in range(6):
    for i in range(2):
        Node(i*10,j*2,-3)
        Node.create()

j = 0
for k in range(6):   
    for i in range(1,2):
        Element.Beam(i +j,i+1 +j)
        Element.create()
    j = j + 2

#Support

Boundary.Support(13,"fix")
Boundary.Support(14,"1111111")

Boundary.Support(15,"fix")
Boundary.Support(16,"1111000")

Boundary.Support(17,"pin")
Boundary.Support(18,"roller")

Boundary.Support(19,"1110000")
Boundary.Support(20,"1111000")

Boundary.Support(21,"free")
Boundary.Support(22,"roller")

Boundary.Support(23,"1110101")
Boundary.Support(24,"0101011")

Boundary.Support.create()

#Elastic Link

#Create General Elastic Link    
Boundary.ElasticLink(1, 13, "", 1, "GEN", 1000, 1000, 1000, 100, 100, 100)
Boundary.ElasticLink(2, 14, "", 2, "GEN", 1000, 1000, 1000, 100, 100, 100)

#Rigid Link
 
Boundary.ElasticLink(3, 15, "", 3, "RIGID")

#Create Saddle type Link    

Boundary.ElasticLink(4, 16, "", 4, "SADDLE")

#Tension-Onl & Compression-Only Link    
Boundary.ElasticLink(5, 17, "", 5, "TENS",500)
Boundary.ElasticLink(6, 18, "", 6, "COMP",600)



# Rail track interaction link
Boundary.ElasticLink(7, 19, "", 7, "RAIL INTERACT", dir="Dy", func_id=1)

#Multi-linear link
Boundary.ElasticLink(8, 20, "", 8, "MULTI LINEAR", dir="Dy", func_id=1)
# Note: Before running this code, the Rail Interaction & Force-Deformation function must be created in Civil NX to avoid any errors.

#Create all the Elastic link
Boundary.ElasticLink.create()


#Rigid Link

Boundary.RigidLink(9,[21],"",1,111111)
Boundary.RigidLink(10,[22,24],"",2,111111)

Boundary.RigidLink.create()


print("All boundary conditions created successfully!")
```