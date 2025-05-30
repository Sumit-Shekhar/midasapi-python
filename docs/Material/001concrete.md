# CONCRETE

A nested class within Material used to create concrete materials.

## Standard
**<font color="green">`Material.CONC(name='', standard='', db='', id=0)`</font>**

Creates a concrete material from database with specified standard and database code.

### Parameters
* `name (default='')`: Material name
* `standard (default='')`: Standard code (e.g., "EN(RC)", "ACI(RC)", "IS(RC)")
* `db (default='')`: Database material code
* `id (default=0)`: Manual ID assignment

### Examples
```py
# Create concrete material from database
Material.CONC("M30 Grade","IS(RC)","M30",1)
Material.CONC("Concrete ","EN04(RC)","C30/37",2)

Material.create()
```

## User-Defined
**<font color="green">`Material.CONC.User(name='', E=0, pois=0, den=0, mass=0, therm=0, id=0)`</font>**

Creates a user-defined concrete material with custom properties.

### Parameters
* `name (default='')`: Material name
* `E (default=0)`: Elastic modulus
* `pois (default=0)`: Poisson's ratio
* `den (default=0)`: Density
* `mass (default=0)`: Mass density
* `therm (default=0)`: Thermal expansion coefficient
* `id (default=0)`: Manual ID assignment

### Examples
```py
# Create user-defined concrete material
Material.CONC.User("Custom Concrete", E=300000, pois=0.2, den=25, mass=2.5, therm=1e-5, id=3)

Material.create()
```

---