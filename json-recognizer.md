# JSON Recognizer Design - Carson Krueger

## Grammar Rules:

This grammar specifies the JSON language based upon its tokens. Spaces in this grammar do not represent any token.

_Non-Terminals_ = **BOLDED** words

_Terminals_ = lowercased words and punctuation

## Grammar

**VALUE** =
**OBJECT** |
**ARRAY** |
string |
number |
true |
false |
null

**OBJECT** =
{ **MEMS** }

**MEMS** =
string : **VALUE** **NEXT_MEMS** | epsilon

**NEXT_MEMS** =
, string : **VALUE** **NEXT_MEMS** | epsilon

**ARRAY** =
[ **ELS** ]

**ELS** =
**VALUE** **NEXT_ELS** | epsilon

**NEXT_ELS** =
, **VALUE** **NEXT_ELS** | epsilon

## FIRST SET:

First(**VALUE**) = { [ string number true false null

First(**OBJECT**) = {

First(**MEMS**) = string epsilon

First(**NEXT_MEMS**) = , epsilon

First(**ARRAY**) = [

First(**ELS**) = { [ string number true false null epsilon

First(**NEXT_ELS**) = , epsilon

## FOLLOW SET:

Follow(**VALUE**) = $ , } ]

Follow(**OBJECT**) = $ , } ]

Follow(**MEMS**) = }

Follow(**NEXT_MEMS**) = } ,

Follow(**ARRAY**) = $ , } ]

Follow(**ELS**) = ]

Follow(**NEXT_ELS**) = ] ,

Follow(**NEXT_ELS**) = ] ,

## PARSE TABLE

|           | {               | }   | [              | ]   | ,                   | :   | str                         | num                 | true                | false               | null                | $   |
| --------- | --------------- | --- | -------------- | --- | ------------------- | --- | --------------------------- | ------------------- | ------------------- | ------------------- | ------------------- | --- |
| VAL       | VAL -> OBJ      |     | VAL -> ARR     |     |                     |     | E                           | E                   | E                   | E                   | E                   | E   |
| OBJ       | OBJ -> { MEMS } |     |                |     |                     |     |                             |                     |                     |                     |                     |     |
| MEMS      |                 |     |                |     |                     |     | MEMS -> str : VAL NEXT_MEMS |                     |                     |                     |                     | E   |
| NEXT_MEMS |                 |     |                |     | NEXT_MEMS -> , MEMS |     |                             |                     |                     |                     |                     | E   |
| ARR       |                 |     | ARR -> [ ELS ] |     |                     |     |                             |                     |                     |                     |                     |     |
| ELS       |                 |     |                |     |                     |     | ELS -> VAL NEXT_ELS         | ELS -> VAL NEXT_ELS | ELS -> VAL NEXT_ELS | ELS -> VAL NEXT_ELS | ELS -> VAL NEXT_ELS | E   |
| NEXT_ELS  |                 |     |                |     | NEXT_ELS -> , ELS   |     |                             |                     |                     |                     |                     | E   |
