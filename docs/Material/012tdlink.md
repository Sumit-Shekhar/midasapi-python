# Time-Dependent Material Link

The TDLink class links materials with their time-dependent properties (creep/shrinkage and compressive strength).

### Class Attributes
*TDLink.mats* -> Dictionary of all material links.

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