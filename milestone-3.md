# Symbol Table 1

    scopes: HashMap<(String, Scope)>
    scope_stack: Vec<String>    // keeps track of current scope when enter/leaving
    scope_counter: u32 = 0

    fn get_id(&self, from_scope: String, id: String) -> Option<&Symbol> {
        match self.scopes.get(from_scope) {
            Some(scope) => {
                match scope.get(id) {
                    Some(symbol) => symbol,
                    None => self.scopes.get(scope.parent)       // fix (scope.parent is an Option)
                }
            }
            None => None
        }
    }

    fn add(&mut self, from_scope: String, symbol: Symbol) -> Result<(), ()> {
        if let Some(_) = self.get_id(symbol.id) {
            return Err(());
        }
        // todo
    }


    fn enter_scope(&mut self)   // increments scope_counter, pushes new scope id
    fn leave_scope(&mut self)   // pops

## Scope

    id: String                              // Scope id, ex: $2
    parent: Option<String>                  // Parent scope id, ex: $1
    HashMap<(String, Symbol)> symbols

## Symbol

    id: String
    type: Type
    scope: String

# Symbol Table 2

    scopes: HashMap<Scope>
    symbols: HashMap<Symbol>
    scope_counter: i32

## Scope

    id: String
    parent: Option<String>

## Symbol

    id: String
    scope: String
    type: Type
