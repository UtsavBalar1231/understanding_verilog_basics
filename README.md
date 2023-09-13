# Verilog basics

## Models and Modules

### Models (Modeling the design)

1. behavioral modeling

   - Design without showing internal structure, simple and easy to understand.
   - abstract and a good start.

```verilog
module but_not_gate_behavioral(
    input a, b,
    output c,
)

    c = a & ~b;

endmodule
```

2. structural modeling

   - Use other circuilts, blocks, etc for building the design examples can be some adders, flip-flops etc.
   - Better approach for building large project, divide the modules that can be reused later and organize it for better understanding

```verilog
module but_not_gate(
    input a,
    input b,
    output c,
)
    assign c = a & ~b;
endmodule

module test_but_not_gate(
    input x,
    input y,
    output z,
)

    wire out;
    but_not_gate g1 (.a(y), .b(x), .c(out));

    assign z = out;
endmodule
```

## Modules

Modules are somewhat similar like a class, you define your logic there.
You can reuse modules and create as many as modules you want, depending on your
logic and project.

```verilog
module foo
(
    /* input and output ports */
)

/* statements */

endmodule
```

> declaration <- foo
>
> statement <- /* statements */

Modules introduce flexibility and allows us to design a large project easily.

**Module declaration**

- inputs <- wires that will provide inputs to the module
- outputs <- wires that will be assigned some output value to be used later
- inout <- 3 state wire
- net <- basically wire but with some other properties
- variable <- a C like variable, cannot be connected as wire, but can be used in statements
- parameter <- used for procedural logic
- function and task <- No idea currently

**Vector**

[msb:lsb]

```verilog
wire [0:7] hello_first_wire;
```

[lsb:msb]

```verilog
reg [1:0] hello_first_reg;
```

> [!WARNING]
> reg is not a register
> more like a var or bitvar

**Logic system**

1 bit signal can posses 4 state, values.

- 0
- 1
- x (unknown logic value)
- z (high impedance state)

usually used in 3 state wires.

**Basic Operators**

| Operator | Operation |
| :------: | :-------: |
|   `&`    |   `AND`   |
|   `\|`   |   `OR`    |
|   `^`    |   `XOR`   |
|   `~`    |   `NOT`   |
|   `^~`   |  `XNOR`   |

**Verlog signals**

**Nets**, **Variables** and **Constants**

### Nets

`Nets` are basically wires same aspect as in a physical circuit.
Nets allows us to connect logic together. i.e provides connectivity.

Types of Nets

1. wire <- Normal signal connectivity
2. tri <- 3-state wire, 0,1 and high-impedance
3. trior <- wired or, 1 only if a or b is 1, otherwise high-impedance
4. triand <- wired and, 1 only if a and b is 1 , otherwise high-impedance
5. tri0 <- tristate with default value 0
6. tri1 <- tristate with default value 1
7. trireg <- tristate that can hold value, used for bidirectional signals
8. wand <- wired and, 1 only if a and b is 1, otherwise 0
9. wor <- wired or, 1 only if a or b is 1, otherwise 0
10. supply0 <- Used for constant 0, Power supply logic (GND)
11. supply1 <- Used for constant 1, Power supply logic (VCC, VDD)

### Variables

In verilog, `variables` can store value, no physical significance unlike nets (wires).
Only used for procedural logic in the module.

Types of Variables

1. reg <- literally `var` in procedural languages
2. integer <- literally numerical integers.
3. real <- literally floating point numbers.

Some examples

```verilog
reg a = 1'b0;
reg [7:0] b = 8'b0000111;
reg [7:0] c = 8'h8;
```

```text
8 ' h 8
^ ^ ^ ^
| | | |____ one or more digits
| | |____ base type (b(binary), h(hexdecimal), o(Octal), d(decimal))
| |___ seperator
|___ literal bit size
```

Some weird examples

```verilog
reg [16:0] x = 16'b010x00x00z1x??z1;
```

> `x` represent some unknown bit
>
> `z` and `?` represents high-Z bits

reg [1:0] y = 2'b1001;

> `bit size is 2 but 4 bits were assigned to the reg y`
>
> `compiler will remove the extra bits from left side`
>
> `y = 2'b01`

### Constants

Constant or Constant expression can be stored in verilog using `parameter`

Examples:

```verilog
parameter MAX_SIZE = 32;

// Multiple declaration using single parameter syntax
parameter I2C3_ADDR = 8'h20,
          I2C4_ADDR = 8'h21;

// Constant expression using parameter syntax
parameter BASE_ADDRESS = 16'hfe103044,
          OFFSET = 16'h2000,
          USB_PHY_ADDRESS = BASE_ADDRESS + OFFSET;
```

## Vectors and Operators

Verilog allows individual bits to be grouped together to form a vector.

```verilog
reg [31:0] foo;
```

Anything can be a vector, Nets, Variables, Constants, etc.

- Indexing and Part-Selection is possible in vectors.

```verilog
reg [31:0] foo;

foo[0] = 1;
foo[31:7] = 0;
```

- Grouping multiple vectors (Concatenation) to create a larger vector

```verilog
reg [15:0] foo;
reg [15:0] bar;
reg [31:0] baz;

baz = {foo, bar};
```

- Concatenation of vectors using multiple copies of the same vector

```verilog
reg [7:0] foo;
reg [7:0] bar;
reg [31:0] baz;

baz = {2{foo}, 2{bar}};

reg [31:0] qux = {32{1'b0}};

qux[15:0] = {16{1'b1}};
```

> [!NOTE]
> Out-of-bounds indexing gets handle like this in verilog
> suppose you are indexing or part-selecting an out-of-bounds value
> Verilog will return you with `x` which are unknown logic values
>
> ```verilog
> qux[15:0] = {16{1'b1}};
>
> reg t = qux[32]; // unknown `x`
> ```

- Bitwise operations on vectors

```verilog
reg [7:0] a = 8'b10101011;
reg [7:0] b = 8'b00101010;
reg [7:0] c;

assign c = a & b; // result: `c = 8'b00101010`
```

```verilog
// Operation of vectors of different sizes

reg [7:0] a = 8'b10101011;
reg [4:0] b = 4'b1001;
reg [7:0] c;

assign c = a & b; // result: `c = 8'b00001001`
```

- Arithmetical opertors in verilog

Verilog has some built-in arithmetic operators.

| Opertor | Operation                |
| ------- | ------------------------ |
| +       | Addition                 |
| -       | Subtraction              |
| *       | Multiplication           |
| /       | Division                 |
| %       | Modulus                  |
| **      | Exponentiation           |
| <<      | (Logical) Left shift     |
| >>      | (Logical) Right shift    |
| <<<     | (Arithmetic) Left shift  |
| >>>     | (Arithmetic) Right shift |

- Signed integers in verilog

```verilog
reg signed [31:0] foo;
output reg signed [7:0] bar;
```
