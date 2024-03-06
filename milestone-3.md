# Thought process

    I designed my symbol table to not include references to avoid any Rc<RefCell> shenanigans. I also find this implementation pretty easy to follow.

    The Symbol table assumes the correct scope id's are generated by the user. My SymbolTable generator visitor will use a scope counter and increment it every time it enters a block. The visitor will use a stack to keep record of its current scope id and parents.

    I will need to modify my AST to keep track of which scope it belongs to so that future visitors can easily use the symbol table without having to calculate scope values again. I'm assuming during desugaring I will need to use the symbol table and if the tree has changed then how will I calculate scope values again?

# Symbol Table

    scopes: Vec<Scope>  // Scope index represents their unique id

    /// Fetch a symbol by their scope id and symbol id, traversing upwards until it is found.
    fn symbol_by_id(&self, from_scope: usize, id: String) -> Option<&Symbol>

    /// Try to insert a symbol. Fails if symbol id already declared in scope or shadows class name.
    fn try_insert(&mut self, from_scope: usize, symbol: Symbol) -> Result<(), ()>

## Scope

    scope_id: usize                 // Scope id, ex: 2
    parent_id: Option<usize>        // Parent scope id, ex: Some(1)
    HashMap<usize, Symbol> symbols

## Symbol

    identifier: String
    type: Type
    scope_id: usize
    is_static: bool
    is_private: bool
    // I don't need the run time value, but do I need the L value?
    // When assigning to a symbol x = 0, I'm guessing the actual L value will be determined during desurgaring?

## Enum Type

    Void,
    Null,
    Char,
    Int,
    Bool,
    StrPtr,
    ArrPtr(Box<Type>),
    ObjPtr {
        members: Vec<Type>
    },
    Method {
        return_type: Box<Type>,
        params: Vec<Type>,
    },
    DataMember {
        type_: Box<Type>,
    },
    Param(Box<Type>),   // Do I need both a Method and Param type?
    Unknown,            // Maybe helpful in error handling