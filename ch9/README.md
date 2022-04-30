Lua的完整语法
===========
这里是用扩展的BNF表示的Lua的完整语法。`{A}`表示0个或多个`A`。`[A]`表示A是可选的。(For operator precedences, see §3.4.8; for a description of the terminals Name, Numeral, and LiteralString, see §3.1.)


	chunk ::= block

	block ::= {stat} [retstat]

	stat ::=  ‘;’ |
		 varlist ‘=’ explist |
		 functioncall |
		 label |
		 break |
		 goto Name |
		 do block end |
		 while exp do block end |
		 repeat block until exp |
		 if exp then block {elseif exp then block} [else block] end |
		 for Name ‘=’ exp ‘,’ exp [‘,’ exp] do block end |
		 for namelist in explist do block end |
		 function funcname funcbody |
		 local function Name funcbody |
		 local namelist [‘=’ explist]

	retstat ::= return [explist] [‘;’]

	label ::= ‘::’ Name ‘::’

	funcname ::= Name {‘.’ Name} [‘:’ Name]

	varlist ::= var {‘,’ var}

	var ::=  Name | prefixexp ‘[’ exp ‘]’ | prefixexp ‘.’ Name

	namelist ::= Name {‘,’ Name}

	explist ::= exp {‘,’ exp}

	exp ::=  nil | false | true | Numeral | LiteralString | ‘...’ | functiondef |
		 prefixexp | tableconstructor | exp binop exp | unop exp

	prefixexp ::= var | functioncall | ‘(’ exp ‘)’

	functioncall ::=  prefixexp args | prefixexp ‘:’ Name args

	args ::=  ‘(’ [explist] ‘)’ | tableconstructor | LiteralString

	functiondef ::= function funcbody

	funcbody ::= ‘(’ [parlist] ‘)’ block end

	parlist ::= namelist [‘,’ ‘...’] | ‘...’

	tableconstructor ::= ‘{’ [fieldlist] ‘}’

	fieldlist ::= field {fieldsep field} [fieldsep]

	field ::= ‘[’ exp ‘]’ ‘=’ exp | Name ‘=’ exp | exp

	fieldsep ::= ‘,’ | ‘;’

	binop ::=  ‘+’ | ‘-’ | ‘*’ | ‘/’ | ‘//’ | ‘^’ | ‘%’ |
		 ‘&’ | ‘~’ | ‘|’ | ‘>>’ | ‘<<’ | ‘..’ |
		 ‘<’ | ‘<=’ | ‘>’ | ‘>=’ | ‘==’ | ‘~=’ |
		 and | or

	unop ::= ‘-’ | not | ‘#’ | ‘~’
