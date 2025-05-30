# Composite Section

A nested class within Section used to create composite sections.

## Composite PSC I-Section

#### Constructor
**<font color="green">`Section.Composite.PSCI(Name='', Symm=True, Joint=[0,0,0,0,0,0,0,0,0], Bc=0, tc=0, Hh=0, H1=0, HL1=0, HL2=0, HL21=0, HL22=0, HL3=0, HL4=0, HL41=0, HL42=0, HL5=0, BL1=0, BL2=0, BL21=0, BL22=0, BL4=0, BL41=0, BL42=0, HR1=0, HR2=0, HR21=0, HR22=0, HR3=0, HR4=0, HR41=0, HR42=0, HR5=0, BR1=0, BR2=0, BR21=0, BR22=0, BR4=0, BR41=0, BR42=0, EgdEsb=0, DgdDsb=0, Pgd=0, Psb=0, TgdTsb=0, MultiModulus=False, CreepEratio=0, ShrinkEratio=0, Offset:Offset=Offset.CC(), useShear=True, use7Dof=False, id:int=0)`</font>**

Creates composite PSC I-sections with concrete slab.

#### Parameters
* `Name`: Section name
* `Symm (default=True)`: Symmetric section flag
* `Joint`: List of joint connectivity values
* `Bc, tc, Hh`: Slab parameters (width, thickness, haunch height)
* `H1`: Web height
* `HL1-HL5, BL1-BL4`: Left flange parameters
* `HR1-HR5, BR1-BR4`: Right flange parameters
* `EgdEsb`: Modular ratio (Egirder/Eslab)
* `DgdDsb`: Density ratio (Dgirder/Dslab)
* `Pgd, Psb`: Poisson's ratios for girder and slab
* `TgdTsb`: Thermal coefficient ratio
* `MultiModulus (default=False)`: Enable multi-modulus analysis
* `CreepEratio, ShrinkEratio`: Creep and shrinkage ratios
* `Offset (default=Offset.CC())`: Section offset parameters
* `useShear (default=True)`: Enable shear deformation
* `use7Dof (default=False)`: Enable warping effect
* `id (default=0)`: Section ID

#### Examples
```py
# Composite PSC I-Section Example
for i in range(2):
    Node(i * 10, 0, 0)
    Node.create()

Element.Beam(1, 2)
Element.create()

# Create composite PSC I-section 
Section.Composite.PSCI(
    Name="Composite_PSC_I",
    Symm=True,  # Symmetrical section
    Joint=[0, 0, 0, 0, 0, 0, 0, 0, 0],  # Joint 

    # slab parameters
    Bc=3,
    tc=0.225,
    Hh=0,


    # Girder parameters
    HL1=0.15,
    HL2=0.1,
    HL3=1.43,
    HL4=0.12,
    HL5=0.3,

    BL1=0.14,
    BL2=0.425,
    BL4=0.375,
  

    # Material properties
    EgdEsb=1.06922,     # Elastic modulus ratio (girder/slab)
    DgdDsb=1.0,     # Density ratio
    Pgd=0.2,        # Poisson's ratio (girder)
    Psb=0.2,        # Poisson's ratio (slab)
    TgdTsb=1.0,     # Thermal expansion coefficient ratio

    # Time-dependent properties
    MultiModulus=False,

    # Offset and effects
    Offset=Offset.CT(),
    useShear=True,
    use7Dof=False,
    id=10
)
Section.create()

```

## Composite Steel I-Section

#### Constructor
**<font color="green">`Section.Composite.SteelI_Type1(Name='', Bc=0, tc=0, Hh=0, Hw=0, B1=0, tf1=0, tw=0, B2=0, tf2=0, EsEc=0, DsDc=0, Ps=0, Pc=0, TsTc=0, MultiModulus=False, CreepEratio=0, ShrinkEratio=0, Offset:Offset=Offset.CC(), useShear=True, use7Dof=False, id:int=0)`</font>**

Creates composite steel I-sections with concrete slab.

#### Parameters
* `Name`: Section name
* `Bc, tc, Hh`: Slab parameters (width, thickness, haunch height)
* `Hw`: Web height
* `B1, tf1`: Top flange width and thickness
* `tw`: Web thickness
* `B2, tf2`: Bottom flange width and thickness
* `EsEc`: Modular ratio (Esteel/Econcrete)
* `DsDc`: Density ratio (Dsteel/Dconcrete)
* `Ps, Pc`: Poisson's ratios for steel and concrete
* `TsTc`: Thermal coefficient ratio
* `MultiModulus (default=False)`: Enable multi-modulus analysis
* `CreepEratio, ShrinkEratio`: Creep and shrinkage ratios
* `Offset (default=Offset.CC())`: Section offset parameters
* `useShear (default=True)`: Enable shear deformation
* `use7Dof (default=False)`: Enable warping effect
* `id (default=0)`: Section ID

#### Examples
```py
# Composite Steel I-Section Example
for i in range(2):
    Node(i*10, 0, 0)
    Node.create()

Element.Beam(1, 2)
Element.create()

# Create composite steel I-section
Section.Composite.SteelI_Type1(
    Name="Composite_Steel_I",
    Bc=3, tc=0.25,  # Slab parameters
    Hw=2, B1=2.5, tf1=0.2, # Steel I parameters
    tw=0.2, B2=2.5, tf2=0.2,  
    EsEc=6.39, DsDc=3.0792, Ps=0.3, Pc=0.2, TsTc=1.2,
    
    # Offset and effects
    Offset=Offset.CT(),
    useShear=True,
    use7Dof=False,
    id=13
)
Section.create()
```
