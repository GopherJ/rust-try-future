# `try_future`

This crate aims to provide a convenient short-hand for returning early
from `futures`-based functions.

The general pattern it supports is where before a function performs
an asynchonous task, it does some work that might result in an early
termination, for example:

- certain parsing or validation logic might fail and where the function
  should return immediately with an error
- some local cache lookup or other optimisation that might render the
  asynchronous task unnecessary, and where the function would want immediately
  return a value instead

# Examples

## Using `impl Future<_>` (nightly)

```rust
#[macro_use] extern crate try_future;

fn make_request<C: Connect>(target: &str, client: &Client<C>) ->
    impl Future<Item=Response, Error=Error>
{
    let uri = try_future!(target.parse::<Uri>());

    client.get(uri).into()
}
```

## Using `Box<Future<_>>`

```rust
#[macro_use] extern crate try_future;

fn make_request<C: Connect>(target: &str, client: &Client<C>) ->
    Box<Future<Item=Response, Error=Error>>
{
    let uri = try_future_box!(target.parse::<Uri>());

    Box::new(client.get(uri))
}
```
