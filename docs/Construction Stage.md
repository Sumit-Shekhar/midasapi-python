# Construction Stage (CS)
The module provides functionality to create, manage, and synchronize construction stages for staged construction analysis in the model. It handles activation and deactivation of structure groups, boundary groups, and load groups across different construction phases.

!!! info "Note."
    All the codes below assumes the initial import and MAPI Key definition.

```py
from midasapi import *
MAPI_KEY('eyJ1ciI6InN1bWl0QG1pZGFzaXQuY29tIiwicGciO252k81571d')
```

## Construction Stage

### Constructor
**<font color="green">`CS(name, duration=0, s_group=None, s_age=None, s_type=None, b_group=None, b_pos=None, b_type=None, l_group=None, l_day=None, l_type=None, id=None, sr_stage=True, ad_stage=False, load_in=False, nl=5, addstp=None)`</font>**

Creates a construction stage with specified parameters for structure, boundary, and load group management.

#### Parameters
* `name`: Name of the construction stage
* `duration (default=0)`: Duration of construction stage in days
* `s_group (default=None)`: Structure group name or list of group names
* `s_age (default=None)`: Age of structure group in days (or redistribution % for deactivation)
* `s_type (default=None)`: Structure activation type - "A" to activate, "D" to deactivate
* `b_group (default=None)`: Boundary group name or list of group names
* `b_pos (default=None)`: Boundary position type - "ORIGINAL" or "DEFORMED"
* `b_type (default=None)`: Boundary activation type - "A" to activate, "D" to deactivate
* `l_group (default=None)`: Load group name or list of group names
* `l_day (default=None)`: Load activation day - "FIRST" or "LAST"
* `l_type (default=None)`: Load activation type - "A" to activate, "D" to deactivate
* `id (default=None)`: Manual construction stage ID assignment (auto-assigned if None)
* `sr_stage (default=True)`: Save results of this stage
* `ad_stage (default=False)`: Add additional step results
* `load_in (default=False)`: Load incremental steps for material nonlinear analysis
* `nl (default=5)`: Number of load incremental steps
* `addstp (default=None)`: List of additional steps

#### Class Attributes
*CS.CSA* -> List of all construction stages.

### Examples

#### Single Group Activation
```py
# Create groups first
Group.Structure("Main Girder", nlist=[1, 2, 3], elist=[1, 2])
Group.Boundary("Support Group")
Group.Load("Dead Load Group")
Group.create()

# Create construction stage with single group activation
cs1 = CS("Stage 1", 
         duration=7, 
         s_group="Main Girder", 
         s_age=7, 
         s_type="A",
         b_group="Support Group", 
         b_pos="DEFORMED", 
         b_type="A",
         l_group="Dead Load Group", 
         l_day="FIRST", 
         l_type="A")

print(f'Stage: {cs1.NAME} | Duration: {cs1.DURATION} days')
```

#### Multiple Group Activation
```py
# Create multiple groups
Group.Structure("Girder 1", elist=[1, 2])
Group.Structure("Girder 2", elist=[3, 4])
Group.Boundary("Support 1")
Group.Boundary("Support 2")
Group.Load("Dead Load")
Group.Load("Live Load")
Group.create()

# Create construction stage with multiple groups
cs2 = CS("Stage 2", 
         duration=14,
         s_group=["Girder 1", "Girder 2"], 
         s_age=[7, 10], 
         s_type=["A", "A"],
         b_group=["Support 1", "Support 2"], 
         b_pos=["DEFORMED", "DEFORMED"], 
         b_type=["A", "A"],
         l_group=["Dead Load", "Live Load"], 
         l_day=["FIRST", "FIRST"], 
         l_type=["A", "A"])
```

#### Mixed Activation and Deactivation
```py
# Activate some groups and deactivate others
cs3 = CS("Stage 3", 
         duration=7,
         s_group=["New Girder", "Old Girder"], 
         s_age=[7, 80], 
         s_type=["A", "D"],  # Activate New Girder, Deactivate Old Girder
         b_group=["New Support", "Old Support"], 
         b_pos=["DEFORMED", "DEFORMED"], 
         b_type=["A", "D"],  # Activate New Support, Deactivate Old Support
         l_group="Construction Load", 
         l_day="FIRST", 
         l_type="A")
```

#### Advanced Options
```py
# Construction stage with advanced analysis options
cs4 = CS("Complex Stage", 
         duration=21,
         s_group="Complex Structure", 
         s_age=14, 
         s_type="A",
         sr_stage=True,      # Save stage results
         ad_stage=True,      # Save additional step results
         load_in=True,       # Use load incremental steps
         nl=10,              # 10 incremental steps
         addstp=[1, 2, 3])   # Additional steps to save
```

### Methods

#### json
Returns a JSON representation of all Construction Stages defined in python.

```py
cs1 = CS("Stage 1", 7, "Main Girder", 7, "A")
cs2 = CS("Stage 2", 14, "Side Girder", 14, "A")

print(CS.json())

# Output will show detailed JSON structure for all stages
```

#### create
Sends the current construction stage list to Civil NX using a PUT request.

```py
cs1 = CS("Stage 1", 7, "Main Girder", 7, "A")
cs2 = CS("Stage 2", 14, "Side Girder", 14, "A")

CS.create()
```

#### get
Fetches construction stages from Civil NX and returns the JSON representation.

```py
print(CS.get())
# Output will show all construction stages from Civil NX database
```

#### sync
Retrieves Construction Stage data from Civil NX and rebuilds the internal stage list.

```py
CS.sync()
for cs in CS.CSA:
    print(f'Stage: {cs.NAME} | Duration: {cs.DURATION} days')
    print(f'  Active Structure Groups: {len(cs.act_structure_groups)}')
    print(f'  Active Boundary Groups: {len(cs.act_boundary_groups)}')
    print(f'  Active Load Groups: {len(cs.act_load_groups)}')
```

#### delete
Deletes all construction stage data from both Python and Civil NX.

```py
CS.delete()
```

---

## Construction Stage Workflow Examples

### Bridge Construction Sequence
```py
# Create structural groups for different bridge parts
Group.Structure("Pier 1", nlist=[1, 2, 3], elist=[1, 2])
Group.Structure("Pier 2", nlist=[4, 5, 6], elist=[3, 4])
Group.Structure("Deck Span 1", nlist=[7, 8, 9], elist=[5, 6])
Group.Structure("Deck Span 2", nlist=[10, 11, 12], elist=[7, 8])

# Create boundary groups
Group.Boundary("Foundation Support")
Group.Boundary("Expansion Joint")

# Create load groups
Group.Load("Self Weight")
Group.Load("Construction Load")
Group.Load("Traffic Load")

Group.create()

# Stage 1: Construct Piers
cs1 = CS("Pier Construction", 
         duration=30,
         s_group=["Pier 1", "Pier 2"], 
         s_age=[28, 28], 
         s_type=["A", "A"],
         b_group="Foundation Support", 
         b_pos="DEFORMED", 
         b_type="A",
         l_group="Self Weight", 
         l_day="FIRST", 
         l_type="A")

# Stage 2: Construct First Deck Span
cs2 = CS("Deck Span 1", 
         duration=21,
         s_group="Deck Span 1", 
         s_age=21, 
         s_type="A",
         l_group="Construction Load", 
         l_day="FIRST", 
         l_type="A")

# Stage 3: Construct Second Deck Span and Apply Traffic Load
cs3 = CS("Deck Span 2 & Traffic", 
         duration=21,
         s_group="Deck Span 2", 
         s_age=21, 
         s_type="A",
         b_group="Expansion Joint", 
         b_pos="DEFORMED", 
         b_type="A",
         l_group=["Construction Load", "Traffic Load"], 
         l_day=["LAST", "LAST"], 
         l_type=["D", "A"])  # Remove construction load, add traffic load

CS.create()
```

### Demolition and Reconstruction
```py
# Groups for existing and new structures
Group.Structure("Old Structure", elist=[1, 2, 3])
Group.Structure("New Structure", elist=[4, 5, 6])
Group.Boundary("Temporary Support")
Group.Load("Existing Load")
Group.Load("New Load")
Group.create()

# Initial stage: Existing structure with loads
cs1 = CS("Existing Condition", 
         duration=0,
         s_group="Old Structure", 
         s_age=3650,  # 10 years old
         s_type="A",
         l_group="Existing Load", 
         l_day="FIRST", 
         l_type="A")

# Stage 2: Add temporary support before demolition
cs2 = CS("Temporary Support", 
         duration=1,
         b_group="Temporary Support", 
         b_pos="DEFORMED", 
         b_type="A")

# Stage 3: Demolish old structure (with 75% load redistribution)
cs3 = CS("Demolition", 
         duration=1,
         s_group="Old Structure", 
         s_age=75,  # 75% redistribution
         s_type="D",
         l_group="Existing Load", 
         l_day="FIRST", 
         l_type="D")

# Stage 4: Construct new structure
cs4 = CS("New Construction", 
         duration=28,
         s_group="New Structure", 
         s_age=28, 
         s_type="A",
         b_group="Temporary Support", 
         b_pos="DEFORMED", 
         b_type="D",  # Remove temporary support
         l_group="New Load", 
         l_day="LAST", 
         l_type="A")

CS.create()
```

### Nonlinear Analysis with Load Steps
```py
# Heavy load application with incremental steps
Group.Structure("Main Structure", elist=[1, 2, 3, 4])
Group.Load("Heavy Load")
Group.create()

# Construction stage with incremental loading
cs1 = CS("Heavy Loading", 
         duration=7,
         s_group="Main Structure", 
         s_age=28, 
         s_type="A",
         l_group="Heavy Load", 
         l_day="FIRST", 
         l_type="A",
         sr_stage=True,      # Save results
         ad_stage=True,      # Save additional step results
         load_in=True,       # Enable incremental loading
         nl=15,              # 15 load steps
         addstp=[3, 7, 10])  # Save results at steps 3, 7, and 10

CS.create()
```

---

## Complete Example

```py
from midasapi import *

MAPI_KEY("eyJ1ciI6IklOMjQwN0ZZVDIiLCJwZyI6ImNpdmlsIiwi")  # Paste your Mapi Key

# Create nodes and elements for bridge model
for i in range(12):
    Node(i*5, 0, 0)
Node.create()

for i in range(11):
    Element.Beam(i+1, i+2)
Element.create()

# Create structural groups for bridge components
Group.Structure("Foundation", nlist=[1, 2], elist=[1])
Group.Structure("Pier 1", nlist=[3, 4, 5], elist=[2, 3])
Group.Structure("Pier 2", nlist=[7, 8, 9], elist=[6, 7])
Group.Structure("Girder Span 1", nlist=[5, 6, 7], elist=[4, 5])
Group.Structure("Girder Span 2", nlist=[9, 10, 11], elist=[8, 9])
Group.Structure("Deck", nlist=[2, 5, 7, 9, 12], elist=[10, 11])

# Create boundary and load groups
Group.Boundary("Fixed Support")
Group.Boundary("Expansion Joint")
Group.Load("Dead Load")
Group.Load("Construction Load")
Group.Load("Live Load")

Group.create()

# Construction Stage 1: Foundation and Fixed Support
cs1 = CS("Foundation Stage", 
         duration=28,
         s_group="Foundation", 
         s_age=28, 
         s_type="A",
         b_group="Fixed Support", 
         b_pos="ORIGINAL", 
         b_type="A",
         l_group="Dead Load", 
         l_day="FIRST", 
         l_type="A")

# Construction Stage 2: Piers Construction
cs2 = CS("Pier Construction", 
         duration=21,
         s_group=["Pier 1", "Pier 2"], 
         s_age=[21, 21], 
         s_type=["A", "A"],
         l_group="Construction Load", 
         l_day="FIRST", 
         l_type="A")

# Construction Stage 3: First Girder Span
cs3 = CS("Girder Span 1", 
         duration=14,
         s_group="Girder Span 1", 
         s_age=14, 
         s_type="A",
         sr_stage=True,
         ad_stage=True)

# Construction Stage 4: Second Girder Span with Expansion Joint
cs4 = CS("Girder Span 2", 
         duration=14,
         s_group="Girder Span 2", 
         s_age=14, 
         s_type="A",
         b_group="Expansion Joint", 
         b_pos="DEFORMED", 
         b_type="A")

# Construction Stage 5: Deck Construction and Load Transfer
cs5 = CS("Deck & Load Transfer", 
         duration=7,
         s_group="Deck", 
         s_age=7, 
         s_type="A",
         l_group=["Construction Load", "Live Load"], 
         l_day=["FIRST", "LAST"], 
         l_type=["D", "A"],  # Remove construction load, add live load
         load_in=True,       # Use incremental steps for live load
         nl=8)               # 8 incremental steps

# Create all construction stages in Civil NX
CS.create()

# Display construction stage information
print("Construction Stages Created:")
for cs in CS.CSA:
    print(f"\nStage {cs.ID}: {cs.NAME}")
    print(f"  Duration: {cs.DURATION} days")
    print(f"  Active Structure Groups: {len(cs.act_structure_groups)}")
    print(f"  Active Boundary Groups: {len(cs.act_boundary_groups)}")
    print(f"  Active Load Groups: {len(cs.act_load_groups)}")
    print(f"  Deactive Structure Groups: {len(cs.deact_structure_groups)}")
    print(f"  Save Results: {cs.SR_stage}")
    if cs.Load_IN:
        print(f"  Load Steps: {cs.NL}")
```