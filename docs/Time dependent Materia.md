# Time-Dependent Properties
The module provides functionality to create, manage, and synchronize time-dependent properties including creep and shrinkage, compressive strength, and material linking in the model.

!!! info "Note."
    All the codes below assumes the initial import and MAPI Key definition.

```py
from midasapi import *
MAPI_KEY('eyJ1ciI6InN1bWl0QG1pZGFzaXQuY29tIiwicGciO252k81571d')
```

## Creep and Shrinkage

The CreepShrinkage class manages time-dependent creep and shrinkage properties for concrete materials.

### Constructor
**<font color="green">`CreepShrinkage(data, id=0)`</font>**

Creates creep and shrinkage properties with specified data.

#### Parameters
* `data`: Creep and shrinkage data dictionary
* `id (default=0)`: Manual ID assignment (auto-assigned if 0 or ID already exists)

#### Class Attributes
*CreepShrinkage.mats* -> List of all creep and shrinkage instances.   

### Methods

#### json
Returns a JSON representation of all Creep and Shrinkage properties defined in python.

```py
print(CreepShrinkage.json())
# Output:
# {'Assign': {1: {'NAME': 'CS_M25', 'CODE': 'INDIA_IRC_112_2011', ...}}}
```

#### create
Sends creep and shrinkage data to Civil NX using a PUT request.

```py
CreepShrinkage.create()
```

#### get
Fetches creep and shrinkage data from Civil NX and returns the JSON representation.

```py
print(CreepShrinkage.get())
```

#### sync
Retrieves Creep and Shrinkage data from Civil NX and rebuilds the internal list.

```py
CreepShrinkage.sync()
```

#### delete
Deletes all creep and shrinkage data from both Python and Civil NX.

```py
CreepShrinkage.delete()
```

### ⭕ IRC
**<font color="green">`CreepShrinkage.IRC(name='', code="INDIA_IRC_112_2011", fck=0, notionalSize=1, relHumidity=70, ageShrinkage=3, typeCement='NR', id=0)`</font>**

Creates IRC standard creep and shrinkage properties.

#### Parameters
* `name (default='')`: Property name
* `code (default="INDIA_IRC_112_2011")`: IRC code standard
* `fck (default=0)`: Characteristic compressive strength (MPa)
* `notionalSize (default=1)`: Notional size (mm)
* `relHumidity (default=70)`: Relative humidity (%)
* `ageShrinkage (default=3)`: Age at start of shrinkage (days)
* `typeCement (default='NR')`: Type of cement ('R' for Rapid, 'NR' for Normal)
* `id (default=0)`: Manual ID assignment

#### Examples
```py
# Create IRC creep and shrinkage properties
cs1 = CreepShrinkage.IRC("CS_M25", "INDIA_IRC_112_2011", fck=25, notionalSize=150, relHumidity=75, ageShrinkage=7, typeCement='R', id=1)
cs2 = CreepShrinkage.IRC("CS_C30", "INDIA_IRC_112_2011", fck=30, notionalSize=200, relHumidity=70, ageShrinkage=3, typeCement='NR', id=2)

CreepShrinkage.create()
```

---

## Compressive Strength

The CompStrength class manages time-dependent compressive strength properties for concrete materials.

### Constructor
**<font color="green">`CompStrength(data, id=0)`</font>**

Creates compressive strength properties with specified data.

#### Parameters
* `data`: Compressive strength data dictionary
* `id (default=0)`: Manual ID assignment (auto-assigned if 0 or ID already exists)

#### Class Attributes
*CompStrength.mats* -> List of all compressive strength instances.   

### Methods

#### json
Returns a JSON representation of all Compressive Strength properties defined in python.

```py
print(CompStrength.json())
# Output:
# {'Assign': {1: {'NAME': 'Comp_M25', 'TYPE': 'CODE', ...}}}
```

#### create
Sends compressive strength data to Civil NX using a PUT request.

```py
CompStrength.create()
```

#### get
Fetches compressive strength data from Civil NX and returns the JSON representation.

```py
print(CompStrength.get())
```

#### sync
Retrieves Compressive Strength data from Civil NX and rebuilds the internal list.

```py
CompStrength.sync()
```

#### delete
Deletes all compressive strength data from both Python and Civil NX.

```py
CompStrength.delete()
```

### ⭕ IRC
**<font color="green">`CompStrength.IRC(name, code="INDIA(IRC:112-2020)", fckDelta=0, typeCement=1, typeAggregate=0, id=0)`</font>**

Creates IRC standard compressive strength properties.

#### Parameters
* `name`: Property name (required)
* `code (default="INDIA(IRC:112-2020)")`: IRC code standard
* `fckDelta (default=0)`: Compressive strength difference (MPa)
* `typeCement (default=1)`: Type of cement (1=Normal, 2=Rapid)
* `typeAggregate (default=0)`: Type of aggregate (0=Normal, 1=Lightweight)
* `id (default=0)`: Manual ID assignment

#### Examples
```py
# Create IRC compressive strength properties
comp1 = CompStrength.IRC("Comp_M25", "INDIA(IRC:112-2020)", fckDelta=5, typeCement=1, typeAggregate=0, id=1)
comp2 = CompStrength.IRC("Comp_C30", "INDIA(IRC:112-2020)", fckDelta=5, typeCement=2, typeAggregate=1, id=2)

CompStrength.create()
```

---

## Time-Dependent Material Link

The TDLink class links materials with their time-dependent properties (creep/shrinkage and compressive strength).

### Constructor
**<font color="green">`TDLink(matID, CnSName='', CompName='')`</font>**

Links a material ID with creep/shrinkage and compressive strength property names.

#### Parameters
* `matID`: Material ID to link (required)
* `CnSName (default='')`: Creep and shrinkage property name
* `CompName (default='')`: Compressive strength property name

#### Class Attributes
*TDLink.mats* -> Dictionary of all material links.

```py
# Link material with time-dependent properties
link1 = TDLink(1, "CS_M25", "Comp_M25")
link2 = TDLink(2, "CS_C30", "Comp_C30")

for mat_id, properties in TDLink.mats.items():
    print(f'Material {mat_id}: Creep/Shrinkage={properties["TDMT_NAME"]}, Compressive={properties["TDME_NAME"]}')
```

### Methods

#### json
Returns a JSON representation of all Time-Dependent Material Links defined in python.

```py
print(TDLink.json())
# Output:
# {'Assign': {'1': {'TDMT_NAME': 'CS_M25', 'TDME_NAME': 'Comp_M25'}, ...}}
```

#### create
Sends material links to Civil NX using a PUT request.

```py
TDLink.create()
```

#### get
Fetches material links from Civil NX and returns the JSON representation.

```py
print(TDLink.get())
```

#### sync
Retrieves Time-Dependent Material Link data from Civil NX and rebuilds the internal dictionary.

```py
TDLink.sync()
```

#### delete
Deletes all material links from both Python and Civil NX.

```py
TDLink.delete()
```

#### Examples
```py
# Link materials with their time-dependent properties
link1 = TDLink(1, "CS_M25", "Comp_M25")  # Link material ID 1 with M25 properties
link2 = TDLink(2, "CS_C30", "Comp_C30")  # Link material ID 2 with C30 properties
link3 = TDLink(3, "CS_M25", "")          # Link material ID 3 with only creep/shrinkage

TDLink.create()
```

---

## Complete Example

```py
from midasapi import *

MAPI_KEY("eyJ1ciI6IklOMjQwN0ZZVDIiLCJwZyI6ImNpdmlsIiwi") #Paste your MAPI Key

# Create IRC creep and shrinkage properties
cs1 = CreepShrinkage.IRC("CS_M25", "INDIA_IRC_112_2011", fck=25, notionalSize=150, relHumidity=75, ageShrinkage=7, typeCement='R', id=1)
cs2 = CreepShrinkage.IRC("CS_C30", "INDIA_IRC_112_2011", fck=30, notionalSize=200, relHumidity=70, ageShrinkage=3, typeCement='NR', id=2)
cs3 = CreepShrinkage.IRC("CS_M40", "INDIA_IRC_112_2011", fck=40, notionalSize=250, relHumidity=65, ageShrinkage=28, typeCement='R', id=3)

# Create IRC compressive strength properties
comp1 = CompStrength.IRC("Comp_M25", "INDIA(IRC:112-2020)", fckDelta=5, typeCement=1, typeAggregate=0, id=1)
comp2 = CompStrength.IRC("Comp_C30", "INDIA(IRC:112-2020)", fckDelta=0, typeCement=2, typeAggregate=1, id=2)
comp3 = CompStrength.IRC("Comp_M40", "INDIA(IRC:112-2020)", fckDelta=-2, typeCement=1, typeAggregate=0, id=3)

# Link materials with time-dependent properties
# Assuming materials with IDs 1, 2, 3 exist
link1 = TDLink(1, "CS_M25", "Comp_M25")  # Link material 1 with M25 properties
link2 = TDLink(2, "CS_C30", "Comp_C30")  # Link material 2 with C30 properties
link3 = TDLink(3, "CS_M40", "Comp_M40")  # Link material 3 with M40 properties

# Create all time-dependent properties in Civil NX
CreepShrinkage.create()
CompStrength.create()
TDLink.create()

print("All time-dependent properties and links created successfully!")

# You can also create them all at once using Material.createAll() if materials exist
# Material.createAll()  # This creates materials, creep/shrinkage, compressive strength, and links
```