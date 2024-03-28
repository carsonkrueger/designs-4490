# Symbol Table

- Add offsets to symbol table for local variables and non static class member variables

- Symbols must first be fully qualified before symbol table is flattened. - SymbolTableQualifierVisitor

- Symbol table might need to be flattened so that everything is accessible from all scopes and usable in assembly - SymbolTableFlattenerVisitor

### Qualifying

    local variables: live on stack but must be qualified to find offsets // {scope_id}${id}
    static class variables: live in the data segment.                    // {class_name}${id}
    functions: live in the code segment                                  // {class_name}${id}

### Nonstatic Functions

    Add explicit class variable as parameter in definition.
    Rewrite function calls to pass an instance variable in.

# Operand Enum

    Literal {
        val: String
        dir_type: DirectiveType
    }
    Static {
        label: String
        dir_type: DirectiveType
    }
    Local(String)

# Expression Desugaring

    Binary expressions will use R1 & R2 - lhs & rhs respectively.
    When visiting a binary expression we will use the operand stack to calculate.

    The result of a binary expression will create temporary variable in the data segment, unless the expression returns an l value.

# Statement Desugaring

### If Statement

    I'll add an extra 2 locations that my visitor will visit that will make generating assembly much easier. One after the condition and another after the body, but before the else clause.

### While Statement

    Convert to an If statement and add a JMP instruction at the end of the body that jumps back to the top of the If statement.

### For Statement

    Convert to a While statement. Move the initializer above, as a Expression statement, the condition will stay as the while condition, and
    finally the post expression will be an Expression statement just before the JMP back to the top condition.

### Switch Statement

    Convert to If statements, default being just a block.
