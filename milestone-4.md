# Symbol Table

- Symbol table will need to be flattened so that everything is accessible - SymbolTableFlattener visitor.

- Symbols must first be fully qualified before symbol table is flattened.

- New data structure - HashMap<(String, Symbol)>? Or I could write a function that manually loops through symbol table to find symbols without a scope id. This is O(n) and creates the possibily misuse in the incorrect context.

### Qualifying

    // ignore variables inside functions, they will be on the stack. How will I desugar local variables to assembly?
    Variables: {scope}${id}

    Functions & Static Class Members: {class_name}${id}

### Nonstatic Functions

    Add explicit class variable as parameter in definition.
    Rewrite function calls to pass an instance variable in.

# Expression Desugaring

    Expr Literals:

# Sequential Desugaring

### If Statement
