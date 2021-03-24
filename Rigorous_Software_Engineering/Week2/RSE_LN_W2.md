# Rigorous Software Engineering- Week 2 (Lectures)
- Author: Ruben Schenk
- Date: 23.03.2021
- Contact: ruben.schenk@inf.ethz.ch

# 3. Modularity
One way to master `modularity` is through `decomposition`. The benefits of decomposition are:
- Partition the overall developement effort
- Support independent testing and analysis
- Decouple parts of a system so that changes to one part do not affect other parts
- Enable reuse of components

## 3.1 Coupling
`Coupling` measures the interdependence between different modules.

<img src="./Figures/RSE_Fig2-1.PNG" width="500px"/><br>

- Tightly-coupled modules cannot be developed, tested, cahnged, or reused in isolation
- Low coupling is a key concern when developing correct and maintainable software
  
### 3.1.1 Data Coupling
Consider the following class:

```java
    class Coordinate {
        public double angle, radius;

        public double getX() {
            return Math.cos(angle)*radius;
        }
    }
```

The problem here is that `angle` and `radius` are `public`. This means that every client can directly access and edit the two attributes and therefore the class is coupled to every client:
- Modules that `expose` their `internal` data representation become tightly coupled to their clients
- Data representation is `difficult to change` during maintenance (if for example one client relies on the attributes `radius` and `angle`)

#### Approach 1: Restrict Access to Data
We may change the above class as follows:

```java
    class Coordinate {
        private double radius, angle;
        invariant 0 <= radius;

        public void setRadius(double r) {
            requires 0 <= r;
            {synchronized(this) {radius = r;}}
        }
        ...
    }
```
- `Information hiding`: Hide implementation details behind narrow interface
- `No leaking`: Do not return references to sub-objects
- `No capturing`: Do not store arguments as sub-objects
- `Clone` objects if necessary

#### Design Patterns
`Design patterns` are general, reusable solutions to commonly occuring design problems. They capture the best practices in detailed design.

**Flyweight Pattern** <br>
The `flyweight pattern` maximizes sharing of immutable objects.

<img src="./Figures/RSE_Fig2-2.PNG" width="500px"/><br>

The idea is that the factory takes responsibility for creating and maintaining the flyweights. Clients simply ask the factory for a flyweight `X`, if it exists, the factory returns the key, if not, it creates `X` first and then returns the key.

### 3.1.2 Procedural Coupling
Modules are coupled to other modules whose method they call. This results in the following two problems:
- `Reuse`: Callers cannot be reused without calle modules
- `Adaptation`: Changing signature in callee requires changing caller

Example:

```java
    class Controller {
        Sensor sensor;

        public boolean selfTest() {
            List<LogEntry> log = sensor.log();
            for(LogEntry e : log) {
                if(e.isError()) {return false;}
            }
            return true;
        }
    }

    class Sensor {
        List<LogEntry> log() {return ...;}
    }

    class LogEntry {
        ...
        boolean isError() {...}
    }
```

#### Approach 1: Moving Code

```java
    class Controller {
        Sensor sensor;

        boolean selfTest() {
            return sensor.noError();
        }
    }

    class Sensor {
        List<LogEntry> logData;

        boolean noError() {
            for(LogEntry e : logData) {
                if(e.isError()) {return false;}
            }
            return true;
        }
    }

    class LogEntry {
        ...
        boolean isError() {...}
    }
```
- Moving code may reduce procedural coupling
- It is common to even duplicate functionality to avoid dependencies on other code

#### Design Pattern
**Observer Pattern**

<img src="./Figures/RSE_Fig2-3.PNG" width="650px"/><br>

<img src="./Figures/RSE_Fig2-4.PNG" width="500px"/><br>

#### Approach 2: Event-Based Style
- Components may generate events, and/or register for events of other components using callback
- Generators of events do not know which component will be effected by their events
- Commonly used in user interfaces

Event-based style is usually used within a `model-view-controller architecture`:
- Components
  - `Model` contains the core functionality and data
  - One or more `views` display information to the user
  - One or more `controllers` handle user input
- Communication
  - Change-propagation mechanism via `events` ensures consistency between user interface and model

<img src="./Figures/RSE_Fig2-5.PNG" width="600px"/><br>

### 3.1.3 Class Coupling
The main type of class coupling is `inheritance`. Inheritance couples the subclass to the superclass. Changes in the superclass may break the subclass (also called the `fragile baseclass problem`).

#### Approach 1: Inheritance by Subtyping and Delegation
Multiple inheritance can be repalce by `subtyping and delegation`:

<img src="./Figures/RSE_Fig2-6.PNG" width="650px"/><br>

#### Approach 2: Using Interfaces
- Repalce occurences of class names by `supertypes` (interfaces)
- Use the most general supertype that offers all required operations

For example, instead of the following code

```java
    class SymbolTable {
        TreeMap<Ident, Type> types;

        TreeMap<Ident, Type> getTypes() {
            return types.clone();
        }
    }
```

use the following, more "general" code:

```java
    class SymbolTable {
        Map<Ident, Type> types;

        Map<Ident, Type> getTypes() {
            return types.clone();
        }
    }
```

We still have the problem of `instantiation`, since allocations couple clients to the instantiated class. This problem can be shifted to the client by letting the client allocate:

```java
    class SymbolTable {
        Map<Ident, Type> types;

        SymbolTable(Map<Ident, Type> t) {
            /*Instad of writing "types = new TreeMap<Ident, Type>();" */
            types = t;
        }
    }
```

#### Design Pattern
**Abstract Factory Pattern**

<img src="./Figures/RSE_Fig2-7.PNG" width="650px"/><br>

We can show the above pattern with the following code example:

```java
    interface MapFactory<K, V> {
        Map<K, V> make();
    }

    class TreeMapFactory implements MapFactory<K, V> {
        Map<K, V> make() {
            return new TreeMap<K, V>();
        }
    }

    class SymbolTable {
        MapFactory<Ident, Type> factory;
        Map<Ident, type> types;

        SymbolTable(MapFactory<Ident, Type> f) {
            factory = f;
            types = factory.make();
        }
    }
```

## 3.2 Adaptation
Since software is easy to change, software systems often deviate from their initial design. Typical changes are:
- New features
- New interfaces
- Bug fixing, performance tuning

Through `parametrization`, modules can be prepared for change by allowing clients to influence their behavior. One might make modules parametric in:
- the values they manipualte
- The data structures they operate on
- The types they operate on
- The algorithms they apply

We show as an example a class which is not parametrized:

```java
    class Merger {
        
        // Source of data and numbers of sources is fixed;
        StringStream f1, f2;
        boolean toggle;

        // Type of data is fixed
        String getNext() {
            String res = null;
            do {
                res = (toggle ? f1.getNext() : f2.getNext());
            
            // Filter criterion is fixed
            } while (res == null);

            // Alternation between sources is fixed
            toggle = !toggle;
            return res;
        }
    }
```

We can parametrize this class by using interfaces and factories instead of concrete classes:

```java
    class Merger {
        Filter[] filters;
        int next;

        String getNext() {
            String res = null;
            do {
                res = filters[next].getNext();
            } while(res == null);
        }
        next = (next + 1) % filters.length;
        return res;
    }
```

#### Design Pattern
**Strategy Pattern**

<img src="./Figures/RSE_Fig2-8.PNG" width="650px"/><br>

**Visitor Pattern**

<img src="./Figures/RSE_Fig2-9.PNG" width="650px"/><br>