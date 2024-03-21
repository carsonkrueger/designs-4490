# Symbol Table

- Add offsets to symbol table for local variables and non static class member variables

- Symbols must first be fully qualified before symbol table is flattened. - SymbolTableQualifierVisitor

- Symbol table will need to be flattened so that everything is accessible from all scopes and usable in assembly - SymbolTableFlattenerVisitor

- New data structure - HashMap<(String, Symbol)>? Or I could write a function that manually loops through symbol table to find symbols without a scope id. This is O(n) and creates the possibility of misuse in the incorrect context.

### Qualifying

    local variables: live on stack but must be qualified to find offsets // {scope_id}${id}
    static class variables: live in the data segment.                    // {class_name}${id}
    functions: live in the code segment                                  // {class_name}${id}

### Nonstatic Functions

    Add explicit class variable as parameter in definition.
    Rewrite function calls to pass an instance variable in.

# Expression Desugaring

    Binary expressions will use R1 & R2, lhs & rhs respectively.
    When visiting a binary expression we will use the operand stack to calculate,
    and it will know if it is in a lhs or rhs context, finalizing to R1 or R2 respectively.

    This example helped me figure out exactly how to desugar
    Example: x = y + 1 * a - z; // z is static -- these variables will be fully qualified

    // visit x
    // fetch x from symbol table = non-static int & offset
    // add $i{counter} .INT to data segment
    MOV R1, FP
    ADD R1, x.offset
    LDR R1, R1
    STR R1, $i{counter}
    // now push $i{counter} to operand stack & increment counter

    // visit y
    // fetch y from symbol table = non-static int & offset
    // add $i{counter} .INT to data segment
    MOV R1, FP
    ADD R1, y.offset    // R1 contains pointer
    LDR R1, R1          // R1 contains value
    STR R1, $i{counter} // store R1 in $i{counter}
    // now push $i{counter} to stack

    // visit int literal 1
    // add $i{counter} .INT to data segment
    MOVI R1, #1
    STR  R1, #i{counter}
    // now push $i{counter} to stack

    // visit a
    // fetch a from symbol table = non-static int & offset
    // add $i{counter} .INT to data segment
    MOV R1, FP
    ADD R1, y.offset    // R1 contains pointer
    LDR R1, R1          // R1 contains value
    STR R1, $i{counter} // store R1 in $i{counter}
    // now push $i{counter} to stack

    // visit * binary
    // pop lhs and rhs from operand stack
    MOV R2, pop1        // rhs is on top of stack
    MOV R1, pop2
    MUL R1, R2
    STR R1, pop1        // store result in pop1 for reuse
    // now push pop1 to stack

    // visit + binary
    // pop lhs and rhs from operand stack
    MOV R2, pop1        // rhs is on top of stack
    MOV R1, pop2
    ADD R1, R2
    STR R1, pop1        // store result in pop1 for reuse
    // now push pop1 to stack

    // visit z (static)
    // add $i{counter} .INT to data segment
    MOV R1, z           // z will be a fully qualified static variable
    STR R1, $i{counter}
    // now push $i{counter} to stack

    // visit - binary
    // pop lhs and rhs from operand stack
    MOV R2, pop1        // rhs is on top of stack
    MOV R1, pop2
    SUB R1, R2
    STR R1, pop1        // store result in pop1 for reuse
    // now push pop1 to stack

    // visit = binary
    // pop lhs and rhs from operand stack
    // fetch pop2 (x) from symbol table => non-static int & offset
    MOV R1, FP
    ADD R1, pop2.offset // lhs is bottom of stack (pop2)
    LDR R1, R1
    STR R1, pop1        // lhs = rhs
    STR R1, $i{counter} // store result in counter variable to prevent override of lhs
    // now push $i{counter} to stack

# Statement Desugaring

### If Statement
