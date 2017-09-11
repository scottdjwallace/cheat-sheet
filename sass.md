# sass

A cheatsheet containing Sass tips, tricks, best practices and code
snippet examples for your day to day workflow.

## Table of Contents

- [Compiling](#compiling)
- [Nesting](#nesting)
- [Variables](#variables)
- [Types](#types)

## Compiling

Browsers can't handle Sass, therefore it must be compiled in CSS before the browser can work with it.

```bash
sass main.scss main.css
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Nesting

One of the things we can do with sass now is nesting. Take a look at the two examples of nesting selectors below adn see how the sass code looks and how the compiled css code looks.

```css
.banner {
  font-family: 'Pacifico', cursive;
  height: 400px;
  background-image: url("lemonade.jpg");
  
  .slogan {
    position: absolute;
    border: 4px solid black;
    top: 200px;
    left: 25%;
    width: 50%;
    height: 200px;
    text-align: center;
    
    span {
      font-size: 24px;
      line-height: 200px;
    }
  }
} 
```

compiles into

```css
.banner {
  font-family: 'Pacifico', cursive;
  height: 400px;
  background-image: url("lemonade.jpg");
}

.banner .slogan {
  position: absolute;
  border: 4px solid black;
  top: 200px;
  left: 25%;
  width: 50%;
  height: 200px;
  text-align: center;
}

.banner .slogan span {
  font-size: 24px;
  line-height: 200px;
}
```

> Selectors aren't the only thing that can be nested, we can also nest properties.

```css
.parent {
  font : {
    family: Roboto, sans-serif;
    size: 12px;
    decoration: none;
  }
}
```

compiles into

```css
.parent {
  font-family: Roboto, sans-serif;
  font-size: 12px;
  font-decoration: none;
}
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Variables

> Not sure this need be explained

```css
// Declare
$translucent-white: rgba(255,255,255,0.3);

// Reference
background-color: $translucent-white;
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Types

> Sass has a few data types you can work with

Numbers

```css
8
12.1
10px // Notice that while 10 has a unit of px associated with it, it is still considered a number
```

Strings

```css
"burger"
'fries'
shake // with or without quotes
```

Booleans

```css
true
false
```

null

```css
It's null, considered an empty value
```

Lists

```css
// separated by either spaces or commas
1.5em Helvetica bold
4px, solid, black

// you can also surround lists with parentheses and make lists within lists
```

Maps

```css
// similar to lists but instead each object is a key-value pair
(key1: value1, key2: value2);
// In a map, the value of a key can be a list or another map.
```

<sup>[(back to table of contents)](#table-of-contents)</sup>
