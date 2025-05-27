# Load
The module provides functionality to create, manage, and synchronize load cases and various types of loads (Self-Weight, Nodal, and Beam loads) in the model.

!!! info "Note."
    All the codes below assumes the initial import and MAPI Key definition.

```py
from midasapi import *
MAPI_KEY('eyJ1ciI6InN1bWl0QG1pZGFzaXQuY29tIiwicGciO252a81571d')
```

## Load Case

### Constructor
To create load cases, use the Load_Case constructor.

**<font color="green">`Load_Case(type, *name)`</font>**

Creates load cases with specified type and names.

#### Parameters
* `type`: Load case type
* `*name`: Variable number of load case names

#### Class Attributes
*Load_Case.cases* -> List of all load cases.   

```py
# Create load cases
lc1 = Load_Case("D", "Dead Load", "Additional Dead")
lc2 = Load_Case("L", "Live Load")

for lc in Load_Case.cases:
    print(f'Load Case IDs: {lc.ID} | Names: {lc.NAME} | Type: {lc.TYPE}')

# Output:
# Load Case IDs: [1, 2] | Names: ('Dead Load', 'Additional Dead') | Type: D
# Load Case IDs: [3] | Names: ('Live Load',) | Type: L
```

### Methods

#### json
Returns a JSON representation of all Load Cases defined in python.

```py
lc1 = Load_Case("D", "Dead Load", "Additional Dead")
lc2 = Load_Case("L", "Live Load")

print(Load_Case.json())

# Output:
# {'Assign': {1: {'NAME': 'Dead Load', 'TYPE': 'D'}, 2: {'NAME': 'Additional Dead', 'TYPE': 'D'}, 3: {'NAME': 'Live Load', 'TYPE': 'L'}}}
```

#### create
Sends the current load case list to Civil NX using a PUT request.

```py
lc1 = Load_Case("D", "Dead Load")
lc2 = Load_Case("L", "Live Load")

Load_Case.create()
```

#### get
Fetches load cases from Civil NX and returns the JSON representation.

```py
print(Load_Case.get())
# Output
# {'STLD': {'1': {'NAME': 'Dead Load', 'TYPE': 'D'}, '2': {'NAME': 'Live Load', 'TYPE': 'L'}}}
```

#### sync
Retrieves Load Case data from Civil NX and rebuilds the internal load case list.

```py
Load_Case.sync()
for lc in Load_Case.cases:
    print(f'Load Case: {lc.NAME} | Type: {lc.TYPE}')
```

#### delete
Deletes all load case data from both Python and Civil NX.

```py
Load_Case.delete()
```

---

## Load

The Load class provides a unified interface to create different types of loads and includes nested classes for specific load types.

### Methods

#### create
Creates all defined loads (Self-Weight, Nodal, and Beam loads) in Civil NX.

```py
Load.create()
```

---

## 1. SELF-WEIGHT LOAD

A nested class within Load used to create self-weight loads.

### Constructor
**<font color="green">`Load.SW(load_case, dir = "Z", value = -1, load_group = "")`</font>**

Creates a self-weight load for the specified load case.

### Parameters
* `load_case`: Name of the load case
* `dir (default="Z")`: Direction of self-weight ("X", "Y", or "Z")
* `value (default=-1)`: Magnitude of self-weight (can be int or list [FX, FY, FZ])
* `load_group (default="")`: Load group name

### Examples
```py
# Simple self-weight in Z direction
for i in range(2):
    Node(i*10,0,0)
    Node.create()

Element.Beam(1,2)
Element.create()
    
#Load Case
Load_Case("D","SW Load")
Load_Case.create()

Load.SW("SW Load","Z",-1)
Load.SW.create()

```

### Methods

#### json
Returns JSON representation of all self-weight loads.

```py
sw1 = Load.SW("Dead Load", "Z", -1)
print(Load.SW.json())
```

#### create
Sends self-weight loads to Civil NX.

```py
Load.SW.create()
```

#### get
Fetches self-weight loads from Civil NX.

```py
print(Load.SW.get())
```

#### sync
Synchronizes self-weight loads from Civil NX.

```py
Load.SW.sync()
```

---

## 2. NODAL LOADS

A nested class within Load used to create nodal loads.

### Constructor
**<font color="green">`Load.Nodal(node, load_case, load_group = "", FX = 0, FY = 0, FZ = 0, MX = 0, MY = 0, MZ = 0, id = "")`</font>**

Creates nodal loads (forces and moments) at specified nodes.

### Parameters
* `node`: Node ID where load is applied
* `load_case`: Name of the load case
* `load_group (default="")`: Load group name
* `FX, FY, FZ (default=0)`: Force components in X, Y, Z directions
* `MX, MY, MZ (default=0)`: Moment components about X, Y, Z axes
* `id (default="")`: Manual ID assignment (auto-assigned if empty)

### Examples
```py
#Nodal Load Example
for i in range(2):
    Node(i*10,0,0)
    Node.create()

Element.Beam(1,2)
Element.create()
    
#Load Case
Load_Case("L","Nodal Load")
Load_Case.create()

#Define Nodal Load

Load.Nodal(1,"Nodal Load","",FX=100,FY=200,FZ=-50,id=1)
Load.Nodal(2,"Nodal Load","",MX=10,MY=20,MZ=-5,id=2)
Load.Nodal.create()
```

### Methods

#### json
Returns JSON representation of all nodal loads.

```py
nl1 = Load.Nodal(101, "Live Load", FZ=-50)
print(Load.Nodal.json())
```

#### create
Sends nodal loads to Civil NX.

```py
Load.Nodal.create()
```

#### get
Fetches nodal loads from Civil NX.

```py
print(Load.Nodal.get())
```

#### sync
Synchronizes nodal loads from Civil NX.

```py
Load.Nodal.sync()
```

#### delete
Deletes all nodal loads from both Python and Civil NX.

```py
Load.Nodal.delete()
```

---

## 3. BEAM LOADS

A nested class within Load used to create beam loads with comprehensive options for distributed loads, concentrated loads, and eccentricity.

### Constructor
**<font color="green">`Load.Beam(element, load_case, value, load_group = "", direction = "GZ", id = "", D = [0, 1, 0, 0], P = [0, 0, 0, 0], cmd = "BEAM", typ = "UNILOAD", use_ecc = False, use_proj = False, eccn_dir = "LZ", eccn_type = 1, ieccn = 0, jeccn = 0.0000195, adnl_h = False, adnl_h_i = 0, adnl_h_j = 0.0000195)`</font>**

Creates beam loads with various distribution patterns and advanced options.

### Parameters
* `element`: Element number where load is applied
* `load_case`: Load case name
* `value`: Load magnitude
* `load_group (default="")`: Load group name
* `direction (default="GZ")`: Load direction ("GX", "GY", "GZ", "LX", "LY", "LZ")
* `id (default="")`: Manual ID assignment (auto-assigned if empty)
* `D (default=[0, 1, 0, 0])`: Relative distance array (4 values based on element length)
* `P (default=[0, 0, 0, 0])`: Load magnitude at corresponding D positions
* `cmd (default="BEAM")`: Load command ("BEAM", "LINE", "TYPICAL")
* `typ (default="UNILOAD")`: Load type ("CONLOAD", "CONMOMENT", "UNILOAD", "UNIMOMENT", "PRESSURE")
* `use_ecc (default=False)`: Enable eccentricity
* `use_proj (default=False)`: Enable projection
* `eccn_dir (default="LZ")`: Eccentricity direction
* `eccn_type (default=1)`: Eccentricity from offset (1) or centroid (0)
* `ieccn, jeccn (default=0, 0.0000195)`: Eccentricity values at i-end and j-end
* `adnl_h (default=False)`: Consider additional height for pressure loads
* `adnl_h_i, adnl_h_j (default=0, 0.0000195)`: Additional height values at ends

### Examples

##### Simple Uniform Distributed Load
```py
#UDL Load Example
for i in range(2):
    Node(i*10,0,0)
    Node.create()

Element.Beam(1,2)
Element.create()
    
#Define Load Case
Load_Case("L","UDL Load")
Load_Case.create()

#Apply UDL Load

Load.Beam(1,"UDL Load",-50,"","GZ")
Load.Beam.create()

```

##### Trapezoidal Load

```py
#Trapezoidal Load Example
for i in range(2):
    Node(i*10,0,0)
    Node.create()

Element.Beam(1,2)
Element.create()
    
#Define Load Case
Load_Case("L","Trapezoidal Load")
Load_Case.create()

#Apply Trapezoidal Load

Load.Beam(1,"Trapezoidal Load",0,"","GZ","",[0,0.3,0.7,1],[0,-20,-50,0])
Load.Beam.create()
```

##### Concentrated Load
```py
#Concentrated Load Example
for i in range(2):
    Node(i*10,0,0)
    Node.create()

Element.Beam(1,2)
Element.create()
    
#Load Case
Load_Case("L","Test Load")
Load_Case.create()

#Apply Concentrated Load

Load.Beam(1,"Test Load",0,"","GZ",1,[0.3,0.5,0.7],[-20,-30,-40],"BEAM","CONLOAD")
Load.Beam.create()
```

##### Load with Eccentricity
```py
#Eccentric Load Example
for i in range(2):
    Node(i*10,0,0)
    Node.create()

Element.Beam(1,2)
Element.create()
    
#Define Load Case
Load_Case("L","Test Load")
Load_Case.create()

# Apply Load with 2.5 m eccentricity at i-end

Load.Beam(1, "Test Load", -100, use_ecc=True, ieccn=2.5)
Load.Beam.create()
```

##### Concentrated Moment/Torsion
```py
#Concentrated Moment/Torsion Example
for i in range(2):
    Node(i*10,0,0)
    Node.create()

Element.Beam(1,2)
Element.create()
    
#Define Load Case
Load_Case("L","Test Load")
Load_Case.create()

# Apply Concentrated Moment/Torsion

Load.Beam(1,"Test Load",0,"","GZ","",[0.3,0.7],[-20,-50],"BEAM","CONMOMENT")
Load.Beam.create()
```

##### Uniform & Trapezoidal Moment/Torsion
```py
#Uniform & Trapezoidal Moment/Torsion Example
for i in range(3):
    Node(i*10,0,0)
    Node.create()

Element.Beam(1,2)
Element.Beam(2,3)
Element.create()
    
#Define Load Case
Load_Case("L","Test Load 1","Test Load 2")
Load_Case.create()

#Uniform Moment/Torsion

Load.Beam(1,"Test Load 1",0,"","GZ","",[0,1],[-20,-20],"BEAM","UNIMOMENT")

#Trapezoidal Moment/Torsion

Load.Beam(2,"Test Load 2",0,"","GZ","",[0.3,0.7],[-30,-50],"BEAM","UNIMOMENT")
Load.Beam.create()
```

### Methods

#### json
Returns JSON representation of all beam loads.

```py
bl1 = Load.Beam(115, "Live Load", -50.0)
print(Load.Beam.json())
```

#### create
Sends beam loads to Civil NX.

```py
Load.Beam.create()
```

#### get
Fetches beam loads from Civil NX.

```py
print(Load.Beam.get())
```

#### sync
Synchronizes beam loads from Civil NX.

```py
Load.Beam.sync()
```

#### delete
Deletes all beam loads from both Python and Civil NX.

```py
Load.Beam.delete()
```

---

## Complete Example

```py
from midasapi import*

MAPI_KEY("eyJ1ciI6IklOMjQwN0ZZVDIiLCJwZyI6ImNpdmlsIiwi") #Paste your Mapi Key

for j in range(6):
    for i in range(2):
        Node(i*10,j*2,0)
        Node.create()
j = 0
for k in range(6):   
    for i in range(1,2):
        Element.Beam(i +j,i+1 +j)
        Element.create()
    j = j + 2

#Load Case
Load_Case("D","SW")
Load_Case("L","Nodal Load")
Load_Case("USER","Test Load 1","Test Load 2","Test Load 3","Test Load 4","Test Load 5","Test Load 6")

Load_Case.create()  #Create Load Case in Civil NX

#Self Weight Load

Load.SW("SW","Z",-1)

Load.SW.create() #Apply Self weight Load in Civil NX


#Nodal Load

Load.Nodal(1,"Nodal Load","",FX=10,FY=20,FZ=-50,id=1)
Load.Nodal(3,"Nodal Load","",MX=100,MY=20,MZ=-5,id=2)
Load.Nodal.create() #Apply Nodal Load in Civil NX

#Concentrated Load

Load.Beam(1,"Test Load 1",0,"","GZ",1,[0.3,0.5,0.7],[-20,-30,-40],"BEAM","CONLOAD")

#UDL Load

Load.Beam(2,"Test Load 2",-50,"","GZ")

#Trapezoidal load

Load.Beam(3,"Test Load 3",0,"","GZ","",[0,0.3,0.7,1],[0,-20,-50,0])

#Concentrated Moment/Torsion

Load.Beam(4,"Test Load 4",0,"","GZ","",[0.3,0.7],[-20,-50],"BEAM","CONMOMENT")

#Uniform Moment/Torsion

Load.Beam(5,"Test Load 5",0,"","GZ","",[0,1],[-20,-20],"BEAM","UNIMOMENT")

#Trapezoidal Moment/Torsion

Load.Beam(6,"Test Load 6",0,"","GZ","",[0.3,0.7],[-30,-50],"BEAM","UNIMOMENT")

#Create this Beam load in Civil NX
Load.Beam.create()

```