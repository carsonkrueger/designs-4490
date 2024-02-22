# AST Structure

### enum ScalarType:

    Void
    Int
    Char
    Bool
    Str
    Identifier(String)

### enum Type

    Base(ScalarType)
    Array(Box<Type>)

### struct CompilationUnit

    classes: Vec<Class>
    main: Vec<Stmt>

### struct Class

    id: String
    members: Vec<ClassMember>

### enum ClassMember

    DataMember {
        is_static: bool
        is_private: bool
        stmt: Stmt
    }

    MethodMember {
        is_static: bool
        is_private: bool
        return_type: Type
        id: String
        params: Vec<Parameter>
        body: Vec<Stmt>
    }

    Constructor {
        id: String
        params: Vec<Parameter>
        body: Vec<Stmt>
    }

### struct Parameter

    type: Type
    id: String

### enum Stmt

    If {
        condition: Expr
        then: Box<Stmt>
    }

    IfElse {
        condition: Expr
        then: Box<Stmt>
        else: Box<Stmt>
    }

    While {
        condition: Expr
        stmt: Box<Stmt>
    }

    For {
        init: Option<Expr>
        condition: Expr
        post: Option<Expr>
        stmt: Box<Stmt>
    }

    Return(Option<Expr>)

    Cout(Expr)

    Cin(Expr)

    Switch {
        match_: Expr
        cases: Vec<Stmt>
        default: Vec<Stmt>
    }

    Break

    Expr(Expr)

    Case {
        case: Expr
        stmts: Vec<Stmt>
    }

    Block(Vec<Stmt>)

    VarDeclaration {
        type_: Type
        id: String
        init: Option<Expr>
    }

### enum Expr

    Null

    This

    Bool(bool)

    Int(i64)

    Char(char)

    Str(String)

    Variable(String)

    Unary {
        op: Operator
        rhs: Box<Expr>
    }

    Binary {
        lhs: Box<Expr>
        op: Operator
        rhs: Box<Expr>
    }

    Index {
        id: Box<Expr>
        index: Box<Expr>
    }

    ClassAccessor {
        class: Box<Expr>
        id: String
    }

    Function {
        id: Box<Expr>
        params: Vec<Expr>
    }

    NewObject {
        type_: Type
        params: Vec<Expr>
    }

    NewArray {
        type_: Type
        length: Box<Expr>
    }

# Grammar

    Grammar is split into 4 sections: MISC, Expressions, Statements, and CompilationUnit.

    Each section is split into their corresponding rules, and each row represents an option in that rule.

    Rules that contain rules are surrounded by <>. ex: <Expression>

    Terminals are all other groups of words, except a few exceptions that I'm sure you'll understand.

## MISC

#### ScalarType

    void
    int
    char
    string
    identifier

#### Type #[inline]

    <ScalarType> []*

#### Comma<T> (Takes generic rule T)

    (<T> ,)* <T>?

#### ExpressionList

    <Comma<Expression>>

#### ParameterList

    <Comma<Param>>

## EXPRESSIONS

#### Assignment

    <Or> == <Assignment>
    <Or> += <Assignment>
    <Or> -= <Assignment>
    <Or> \*= <Assignment>
    <Or> /= <Assignment>
    Or

#### Or

    <Or> || <And>

#### And

    <And> && <Equality>

#### Equality

    <Equality> == <Comparison>
    <Equality> != <Comparison>

#### Comparison

    <Comparison> < <Term>
    <Comparison> > <Term>
    <Comparison> <= <Term>
    <Comparison> >= <Term>

#### Term

    <Term> + <Factor>
    <Term> - <Factor>

#### Factor

    <Factor> * <Unary>
    <Factor> / <Unary>

#### Unary

    + <Unary>
    - <Unary>
    ! <Unary>

#### Primary

    --- Precedence 1 --- (highest)

    <Value>

    --- Precedence 2 ---

    <Primary> [ <Expression> ]      // Index

    <Primary> . <Identifier>        // Member accessor

    <Primary> ( <ExpressionList> )  // Function

    --- Precedence 3 --- (lowest)

    new <Type> ( <ExpressionList> )

    new <Type> [ <Expression> ]

#### VALUE

    null
    this
    false
    true
    IntLit
    CharLit
    StrLit
    Identifier
    ( <Expression> )

## Statements

#### Statement

    <OpenStatement>
    <ClosedStatement>

#### OpenStatement

    <OpenIf>
    <OpenWhile>
    <OpenFor>

#### ClosedStatement

    <SimpleStatement>
    <ClosedIf>
    <ClosedWhile>
    <ClosedFor>

#### SimpleStatement

    <ExpressionStatement>
    <VariableDeclaration>       // causes reduce/shift Identifier [ conflict
    <Block>
    <Switch>
    cin >> <Expression> ;
    cout << <Expression> ;
    return <Expression>? ;
    break ;

#### OpenIf

    if ( <Expression> ) <ClosedStatement>
    if ( <Expression> ) <OpenStatement>
    If ( <Expression> ) <ClosedStatement> Else <OpenStatement>

#### ClosedIf

    if ( <Expression> ) <ClosedStatement> Else <ClosedStatement>

#### OpenWhile

    while ( <Expression> ) <OpenStatement>

#### ClosedWhile

    while ( <Expression> ) <ClosedStatement>

#### VariableDeclaration

    <Type> Identifier (= <Expression>)? ;

#### Switch

    switch ( <Expression> ) { <Case*> default : <Statement*> }

#### Case

    case <Expression> : <Statement*>

#### OpenFor

    For ( <Expression>? ; <Expression> ; <Expression?> ) <ClosedStatement>

#### ExpressionStatement

    <Expression> ;

#### Block

    { <Statement> }

## CompilationUnit

#### CompilationUnit

    <ClassDefinition>* void Identifier ( ) <Block>      // Identifier must match "main"

#### Modifier

    private
    public

#### ClassDefinition

    class Identifier { <ClassMemberDefinition>* }

#### ClassMemberDefinition

    DataMember,
    MethodMember,
    Constructor

#### DataMember

    static? <modifier>? <Type> Identifier ( <ParameterList> ) <Block>

#### Constructor

    Identifier ( <ParameterList> ) <Block>

#### Param

    <Type> Identifier