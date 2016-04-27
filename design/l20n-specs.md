# L20n For Games

## Version 1.2

This is briefly annotated version of the grammar used for the L20n-for-games language. This is a fork of the proof-of-concept grammar as specified by Mozilla which can be found on [their L20n Spec Repository](https://github.com/l20n/spec).

## Grammar

The fileformat to be used for l20n is completely described by the following Localizable Objects List (lol) production. The file consists of entries and whitespaces.

+ lol

        (WS | entry)* EOF

An entry is either an entity, a macro, a comment or an import statement.

+ entry

        entity | macro | comment | import_statement

A entity consists of an identifier, a value and an optional set of attributes. The optional index specifies which value to actually return when askng for the value of this entity, for example, this can specify which plural form to use.

+ entity

        '<' identifier index? WS+ value attributes? WS? '>'

+ identifier

        [_a-zA-Z]\w*

+ simple_identifier

        [a-zA-Z]\w*

+ index

        '[' WS? expression WS? ( ',' WS? expression WS? )*']'

+ attributes

        ( WS keyValuePair)+

Statements are similar to C-style statements.

+ import_statement

        'import(' WS? expression WS? ')'

Expressions are closely modeled after C expressions, merely binary operators and some C-specifics have been removed.

+ expression:

        conditional_expression

+ conditional_expression

        logical_expression WS? ( '?' WS? expression ':' WS? expression )?

+ logical_expression

        binary_expression (WS? ('||' | '&&') WS? logical_expression)?

+ binary_expression

        unary_expression (WS? ('==' | '!=' | '<' | '>' | '<=' | '>=' | '+' | '-' | '*' | '/' | '%') WS? binary_expression)?

+ unary_expression

        ( ('+' | '-' | '!') WS? unary_expression ) | member_expression

+ member_expression

        call_expression | property_expression | attribute_expression | parenthesis_expression

+ call_expression

        member_expression '(' WS? expression WS? ( ', ' WS? expression WS? )* ')'

+ property_expression

        identifier ( '.' simple_identifier | '[' WS? expression WS? ']' )+

+ attribute_expression

        identifier '::' ( simple_identifier | '[' WS? expression WS? ']' )
        ( '.' simple_identifier | '[' WS? expression WS? ']' )*

+ parenthesis_expression

        '(' WS? expression WS? ')' | primary_expression

+ primary_expression

        literal | value | identifier_expression

+ identifier_expression

        identifier | variable | global

+ variable

        '$' simple_identifier

+ global

        '@' simple_identifier

+ literal

        ('-' | '+')? [0-9]+

One of the fundamental elements of l20n are values, which can be plain strings, strings with parameters or hashes. Hashes have values as values, that is, they can be hierarchical.

+ value

        string | hash

+ macro

        '<' identifier '(' WS? ( variable WS? ( ',' WS? variable WS? )* )? ')' WS+ '{' WS? expression WS? '}' WS? '>'

The expander production used inside strings is used to reference expressions to be expanded within a string.

+ string

        ('\'' ([^'] | escape | expander )* '\'') |
        ('"' ([^"] | escape | expander )* '"')

+ escape

        '\' .

+ expander

        '{{' WS? expression WS? '}}'

+ hash

        '{' WS? hashItem WS? ( ',' WS? hashItem WS? )* ','? '}'

+ hashItem

        '*'? simple_identifier WS? ':' WS? value
        
+ keyValuePair

        simple_identifier index? WS? ':' WS? value

Comments are following doxygen style.

+ comment

        '/*' .*? '*/'

+ WS

        \s+