# Building a Complete C Compiler in JavaScript: From Lexical Analysis to Three-Address Code Generation

You can take a look at the [repository](https://github.com/caje-vi/C_Compiler_Using_Jison)

*Ever wondered how compilers work under the hood? I recently built a fully functional C compiler using JavaScript and Jison, implementing everything from lexical analysis to semantic verification and intermediate code generation. Here's the journey and what I learned.*

## The Challenge: Building a Real Compiler

When I set out to build this compiler, I wanted to create something that wasn't just a toy example, but a real compiler that could handle the essential features of C programming:

- **Complete lexical and syntax analysis**
- **Robust semantic analysis** with type checking
- **Proper scope management** with nested scopes
- **Symbol table implementation** for variable tracking
- **Three-address code generation** for correct assignments
- **Comprehensive error reporting**

The result? A compiler that can take C code like this:

```c
int main() {
    int x, y;
    float z;
    
    x = 10;
    y = x + 5;
    z = 3.14;
    
    {
        int inner = 100;
        y = y + inner;
    }
    
    return 0;
}
```

And produce this three-address intermediate code:

```
@i0 = 10
t0 = @i0 ADD 5
@i1 = t0
@f0 = 3.14
t1 = @i1 ADD @i2
@i1 = t1
```

## Architecture Overview: The Four Phases

### 1. Lexical Analysis (Tokenization)

The first phase breaks the input C code into meaningful tokens. My lexer recognizes:

```javascript
// Keywords and operators
"int"                 %{ return 'INT'; %}
"float"               %{ return 'FLOAT'; %}
"="                   return 'tokATT'
"+"                   %{ yytext = "ADD"; return 'tokOPD'; %}

// Identifiers and literals
[a-zA-Z][a-zA-Z0-9_]* return 'tokVAR'
[0-9]+\.[0-9]+        return 'tokFLOAT'
[0-9]+                return 'tokINT'
```

This phase handles all the basic building blocks: keywords, operators, identifiers, and literals, while ignoring whitespace and comments.

### 2. Syntax Analysis (Parsing)

Using Jison's powerful parsing capabilities, I implemented a context-free grammar that builds a complete syntax tree. The key insight was ensuring the tree is built **before** semantic analysis:

```javascript
atribuicao
    : tokVAR tokATT expr
        {
            // Build syntax tree node
            var node = newNode();
            node.valor = "ATT";
            node.tipo = 10; // tokATT

            node.esquerda = newNode();
            node.esquerda.valor = $1;
            node.esquerda.tipo = 9; // tokVAR

            node.direita = $3; // Expression subtree
            addNode(node);
        }
```

### 3. Semantic Analysis: The Heart of the Compiler

This is where the magic happens. My semantic analyzer implements:

#### Symbol Table Management
Each variable gets a unique internal identifier based on its type:

```javascript
function newSimbolo(tipo, nome) {
    var prefix = tipo === 'int' ? 'i' : tipo === 'float' ? 'f' : 'c';
    return {
        nome: nome,
        id: '@' + prefix + counter++,
        tipo: tipo,
        escopo: escopoAtual,
        declarado: true
    };
}
```

So `int x` becomes `@i0`, `float y` becomes `@f0`, etc.

#### Scope Management with Stack
I implemented proper nested scope handling using a scope stack:

```javascript
function enterEscopo() {
    escopoAtual++;
    escopoPilha.push({
        id: escopoCounter++,
        nivel: escopoAtual,
        simbolos: []
    });
}
```

This allows variables in inner scopes to shadow outer ones, while maintaining proper scope accessibility rules.

#### Type Compatibility System
The compiler implements intelligent type checking:

```javascript
function verificarCompatibilidade(tipo1, tipo2) {
    if (tipo1 === tipo2) {
        return { compativel: true, conversao: 'nenhuma' };
    }
    
    // int ↔ float conversions
    if ((tipo1 === 'int' && tipo2 === 'float') || 
        (tipo1 === 'float' && tipo2 === 'int')) {
        return { compativel: true, conversao: 'numeric_conversion' };
    }
    
    // char ↔ int conversions  
    if ((tipo1 === 'char' && tipo2 === 'int') || 
        (tipo1 === 'int' && tipo2 === 'char')) {
        return { compativel: true, conversao: 'char_int_conversion' };
    }
    
    return { compativel: false, conversao: 'impossivel' };
}
```

### 4. Code Generation: Three-Address Code

Here's the crucial part - the compiler only generates intermediate code for **semantically correct** assignments. This ensures that only valid operations make it to the code generation phase:

```javascript
if (atribuicaoCorreta) {
    // ✅ Generate three-address code
    var instrucao = simboloEsq.id + ' = ' + direita.temp;
    codigoTresEnderecos.push(instrucao);
    console.log('✅ CORRECT ASSIGNMENT: Compatible types and scopes');
    atribuicoesCorretas++;
} else {
    // ❌ Don't generate code for incorrect assignments
    console.log('❌ ASSIGNMENT ERROR: Incompatible types or scopes');
    atribuicoesIncorretas++;
}
```

## Key Implementation Insights

### 1. Tree Construction Before Analysis
One of the most important decisions was building the complete syntax tree before running semantic analysis. This allows for multiple passes and more sophisticated analysis:

```javascript
// First: Build the tree
processarArvore();

// Then: Analyze semantics using the tree
gerarCodigoTresEnderecos(node);
```

### 2. Integrated Symbol Table Lookup
Every variable reference triggers a symbol table lookup that respects scope rules:

```javascript
function lookupSimbolo(nome) {
    // Search from innermost to outermost scope
    for (var i = escopoAtual; i >= 0; i--) {
        var simbolos = escopoPilha[i].simbolos;
        for (var j = 0; j < simbolos.length; j++) {
            if (simbolos[j].nome === nome) {
                return simbolos[j];
            }
        }
    }
    return null; // Variable not found
}
```

### 3. Conditional Code Generation
The compiler takes a strict approach: **no code is generated for incorrect assignments**. This prevents propagation of semantic errors into the intermediate representation.

## Error Handling and Diagnostics

The compiler provides comprehensive error reporting:

```
ERRO: Variável "undeclared_var" não declarada em nenhum escopo acessível
ERRO: Redeclaração da variável "x" no escopo 1  
❌ ERRO ATRIBUIÇÃO: Tipos ou escopos incompatíveis
```

It tracks multiple error categories:
- Undeclared variables
- Variable redeclarations  
- Type incompatibilities
- Scope violations

## Performance and Statistics

The compiler provides detailed compilation statistics:

```javascript
return {
    status: errosEncontrados === 0 ? "SUCESSO" : "ERRO",
    estatisticas: {
        variaveisDeclaradas: tabelaSimbolos.length,
        atribuicoesCorretas: atribuicoesCorretas,
        atribuicoesIncorretas: atribuicoesIncorretas,
        instrucoesGeradas: codigoTresEnderecos.length,
        operacoesCompativeis: operacoesCompativeis
    }
};
```

## What I Learned

### 1. Compiler Design is About Data Structures
Good compilers are built on solid data structures:
- **Symbol Table**: Hash table with scope chaining
- **Syntax Tree**: Recursive tree structure with proper parent-child relationships  
- **Scope Stack**: Stack-based scope management
- **Three-Address Code**: Linear intermediate representation

### 2. Error Recovery is Crucial
A production compiler must handle errors gracefully and continue analysis to find as many errors as possible in a single pass.

### 3. Semantic Analysis is Complex
Type checking, scope resolution, and symbol management require careful coordination between multiple compiler phases.

## Testing the Compiler

Here's a simple test you can run:

```javascript
const parser = require('./compiler.js');

const testCode = `
int main() {
    int x, y;
    float z;
    
    x = 10;        // ✅ Correct: int = int
    y = x + 5;     // ✅ Correct: int = int + int  
    z = 3.14;      // ✅ Correct: float = float
    y = z;         // ✅ Correct: int = float (with conversion)
    
    undeclared = 5; // ❌ Error: undeclared variable
    x = "string";   // ❌ Error: type mismatch
    
    return 0;
}`;

try {
    const result = parser.parse(testCode);
    console.log('Compilation Result:', result);
} catch (error) {
    console.error('Compilation Error:', error);
}
```

## Future Enhancements

While the current compiler handles the core features well, there are several areas for expansion:

1. **Control Flow**: if/else statements, while/for loops
2. **Functions**: Function declarations, calls, and parameter passing
3. **Arrays and Pointers**: Memory management and dereferencing
4. **Optimization**: Basic optimizations like constant folding
5. **Target Code Generation**: Assembly or machine code output

## Conclusion

Building this C compiler was an incredible learning experience that deepened my understanding of:
- How programming languages are processed
- The importance of proper error handling in development tools
- The elegance of compiler design patterns
- The power of formal grammars and parser generators

The complete implementation is available on GitHub with full documentation, examples, and tests. Whether you're a student learning about compilers or a developer interested in language implementation, I hope this project provides useful insights into the fascinating world of compiler construction.

The source code, examples, and detailed documentation are available at: **[GitHub Repository Link]**

---

*Have you ever built a compiler or interpreter? I'd love to hear about your experiences and challenges in the comments below!*

## Technical Deep-Dive: Key Code Snippets

For those interested in the implementation details, here are some key functions:

### Symbol Table Creation
```javascript
function newSimbolo(tipo, nome) {
    var t = tipo === 'float' ? 'f' : tipo === 'char' ? 'c' : 'i';
    var n = tipo === 'float' ? nFloat++ : tipo === 'char' ? nChar++ : nInt++;
    
    return {
        nome: nome,
        id: '@' + t + n,
        tipo: tipo,
        escopo: escopoAtual,
        declarado: true
    };
}
```

### Three-Address Code Generation
```javascript
case 10: // tokATT (assignment)
    var direita = gerarCodigoTresEnderecos(node.direita);
    var simboloEsq = lookupSimbolo(node.esquerda.valor);
    
    if (verificarAtribuicaoCorreta(simboloEsq, direita.tipoResultado)) {
        var instrucao = simboloEsq.id + ' = ' + direita.temp;
        codigoTresEnderecos.push(instrucao);
        atribuicoesCorretas++;
    } else {
        atribuicoesIncorretas++;
    }
    break;
```

### Scope Management
```javascript
function exitEscopo() {
    if (escopoAtual > 0) {
        var simbolosRemovidos = escopoPilha[escopoAtual].simbolos;
        console.log('Variables leaving scope:', simbolosRemovidos.map(s => s.nome));
        escopoPilha.pop();
        escopoAtual--;
    }
}
```

These snippets show the practical implementation of compiler theory concepts in working JavaScript code.
