# Static Duck Typing: The worst of both worlds
While consitering type systems for a prototype language, I remembered a type system I had in mind when I was consitering a game engine written in rust and lua for 2d games. 
It is inspired from clojure, lua, and rust, and is intended for an interpreted language.

I'll probably detail this prototype language in the future, but the hint I'll give is it is based on having a really good IDE.

## Objects
Like javascript, clojure, and lua, objects are transparent hashmaps. Privacy is enforced using documentation and naming variables.
To assist in this, identifiers can have any UTF8 contents besides a space. For the rest of this article, I'll use JSON to mark up objects.

```javascript
{
	"object": true
}
```

Unlike javascript and lua however, objects do not use first-class functions to emulate member functions. Instead, something like rust is used:

```rust
fn id(self: Object) {
	return self;
}

{
	"string": "Hello World"
}.id();
```

## Type Enforcers
"If it quacks like a duck, run unit tests at runtime to make sure it quacks under the proper circumstances to ensure it is a duck."

Given
```rust
fn Duck?(self: Object) {
	return self.wings == 2 &&
	self.lefs == 2 &&
	self.sound == "quack"
}
```

The function

```rust
fn quack(self: Duck) {
	println(self.sound);
}
```

Translates to

```rust
fn quack(self) {
	if (!self.Duck?) {
		throw TypeError;
	}
	println(self.sound);
}
```

As seen in the example above, the type of parameters are enforced at runtime when the function is called. 

## Some Syntactic Sugar
```rust
struct Cat {
	purring: Boolean,
	belly_rub_count: i64,
	name: String,
}

fn cat_new(purring: Boolean, name: String) {
	return Cat {
		purring: purring,
		belly_rub_count: 0, // The cat was just born, needs belly rubs
		name: name,
	}
}
```

Desugars to

```rust
fn Cat? (self: Object) {
	return self.purring.Boolean?() &&
	       belly_rub_count.i64?() &&
	       name.String?()
}

fn _Cat_new(purring: Boolean, belly_rub_count: i64, name: String) -> Cat {
	return {
		"purring": purring,
		"belly_rub_count": belly_rub_count,
		"name": name
	}
}

fn cat_new(purring: Boolean, name: String) {
	return _Cat_new(purring, 0, name);
}
```

In `_Cat_new`, it is worth noting that the return type of `Cat` also makes sure that `_Cat_new` returned a `Cat`.
