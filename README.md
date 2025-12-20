# RcSubstring
A reference-counted substring

For returning part of a string held in an `Rc` that needs to live longer than the source of the string itself.
For more complete alternatives see [arcstr](https://crates.io/crates/arcstr) or [slice-rc](https://crates.io/crates/slice-rc).
This is intended as a simple lightweight alternative where you just want a reference counted substring in single-threaded situations.

It implements both `Deref` and `AsRef` so can be used just as a `str` in most contexts.

[CHANGELOG.md](CHANGELOG.md)

# Example
```rust
# use rcsubstring::RcSubstring;
# use std::rc::Rc;
let shared_text: Rc<String> = Rc::new(String::from("Some text"));
let shared_substring = RcSubstring::new(Rc::clone(&shared_text), 5..9);
drop(shared_text);
assert_eq!(shared_substring, "text");
```

# Use Case
For an intended use case, consider a function that generates text and then returns an iterator over that text.
How do we get the lifetimes to work? Even if we pass the ownership of the generated text to the iterator the
iterator will not be allowed to pass back refs to the text it holds as it is a requirement that the values
returned by `next()` can outlive the iterator. This is simple crate that offeres a simple solution.

```rust
# use rcsubstring::RcSubstring;
# use std::rc::Rc;
struct WordIterator {
    rcstring: Rc<String>,
    start_pos: usize,
}
impl Iterator for WordIterator {
    type Item = RcSubstring;
    fn next(&mut self) -> Option<Self::Item> {
        let pos = self.start_pos + self.rcstring[self.start_pos..].find(" ")?;
        let value = RcSubstring::new(Rc::clone(&self.rcstring), self.start_pos..pos);
        self.start_pos = pos + 1;
        return Some(value);
    }
}

fn generate_text(values: Vec<usize>) -> String {
    let words = vec!["zero", "one", "two", "three", "four", "five"];
    let mut result = String::new();
    for i in values {
        result.push_str(words[i]);
        result.push_str(" ");
    }
    result
}

fn give_me_an_iterator() -> WordIterator {
    let text = generate_text(vec![2, 3, 1, 0, 5]);
    WordIterator {
        rcstring: Rc::new(text),
        start_pos: 0,
    }
}

let mut it = give_me_an_iterator();
assert_eq!(it.next().unwrap(), "two");
assert_eq!(it.next().unwrap(), "three");
assert_eq!(it.next().unwrap(), "one");
assert_eq!(it.next().unwrap(), "zero");
let value = it.next().unwrap();
drop(it);
assert_eq!(value, "five");
```
