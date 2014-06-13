Domácí úloha
============

Zadání: cílem je vytvořit jednoduchý nástroj fungující v příkazové řádce, který převede vstupní XML na zápis ve specifickém jazyce, říkejme mu třeba Ladoc.

Program je možné odevzdat libovolným Vámi preferovaným způsobem (mail, github, ...). Kromě kompletních zdrojových kódů by měl obsahovat stručnou dokumentaci popisující přeložení programu a použití.

Vstup
-----

###### Formát vstupu

Vstupem jsou XML data specifiková následujícím DTD:

```dtd
<!ELEMENT codal (memory* | interface* | connect*)>
<!ELEMENT memory (id, address, word, lau, endianness, size)>
<!ELEMENT interface (id, type, flags?)>
<!ELEMENT connect (from, to)>
<!ELEMENT flags (flag*)>
<!ELEMENT id (#PCDATA)>
<!ELEMENT address (#PCDATA)>
<!ELEMENT word (#PCDATA)>
<!ELEMENT lau (#PCDATA)>
<!ELEMENT size (#PCDATA)>
<!ELEMENT type (#PCDATA)>
<!ELEMENT flag (#PCDATA)>
```

###### Ukázka vstupu

Vstup může vypada např. takto:

```xml
<ladoc>
  <memory>
    <id>mem1</id>
    <address>32</address>
    <word>32</word>
    <lau>8</lau>
    <endianness>big</endianness>
    <size>1024</size>
  </memory>
  <memory>
    <id>mem2</id>
    <address>8</address>
    <word>8</word>
    <lau>1</lau>
    <endianness>little</endianness>
    <size>0x8000</size>   
    <interface>
      <id>i1</id>
      <type>master</type>
      <flags>
        <flag>r</flag>
        <flag>w</flag>
      </flags>
    </interface>   
  </memory>
  <memory>
    <id>mem3</id>
    <address>8</address>
    <word>8</word>
    <lau>1</lau>
    <endianness>little</endianness>
    <size>0x10000</size>
  </memory>
  <interface>
    <id>i1</id>
    <type>master</type>
  </interface>
  <interface>
    <id>i2</id>
    <type>slave</type>
    <flags>
      <flag>r</flag>
    </flags>
  </interface>
  <connect>
    <from>mem2.i1</from>
    <to>i1</to>
  </connect>
  <connect>
    <from>mem2.i1</from>
    <to>i2</to>
  </connect>
<ladoc>
```

Výstup
------

###### Formát výstupu

Ladoc je jednoduchý jazyk specifikovaný následující gramatikou:

```
syntaxe:
--------
codal : (memory* | interface* | connect*)
memory : 'memory' id '{' mem_attr* '}' ';'
mem_attr : 'bits' '=' { address ',' word ',' lau } ';'
mem_attr : 'endianness' '=' '"' endianness '"' ';' 
mem_attr : 'size' '=' size ';'
mem_attr : interface
interface : 'interface' id '{' interface_attr* '}' ';'
interface_attr : 'type' '=' '"' type '"' ';'
interface_attr : 'flags' '=' '"' flag+ '"' ';' 
interface_attr : 'size' '=' size ';'
connect : from "=>" to

lexemy:
-------
id : [a-zA-Z_][a-zA-Z0-9_]*
address : [0-9]+
word : [0-9]+
lau : [0-9]+
size : 0x[0-9]+
type : master | slave
flag : r | w | rw
```

###### Ukázka výstupu

Pro ukázkový vstup by měl výstup vypadat takto:

```
memory mem1 {
  bits = { 32, 32, 8 };
  endianness = "big";
  size = 0x400;
};

memory mem2 {
  bits = { 8, 8, 1 };
  endianness = "little";
  size = 0x8000;

  interface i1 {
    type = "master";
    flags = "rw";
  }
};

memory mem3 {
  bits = { 8, 8, 1 };
  endianness = "little";
  size = 0x10000;
};

interface i1 {
  type = "slave";
};

interface i2 {
  type = "slave";
  flags = "r";
};

connect mem2.i1 => i1;
connect mem2.i1 => i2;
```

Poznámky
--------
- Program musí být implementován v jazyce Java.
- Je povoleno použít libovolné knihovny, jejich použití je ale třeba vysvětlit a obhájit.
- Zádání záměrně není příliš striktní. V případě nejasností je možné se rozhodnout dle vlastního úsudku nebo napsat.
- Implementace by neměla zabrat více než 3 až 4 hodiny.
