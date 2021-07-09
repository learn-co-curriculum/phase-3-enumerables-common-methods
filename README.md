# Enumerables: Common Methods

## Learning Goals

- Use common enumerable methods like `#each`, `#map`, `#find`, and `#filter`
  with arrays and hashes

## Introduction

Let's review some common enumerable methods that you've already encountered in
JavaScript, and see how to write them in Ruby. Follow along with the examples
in IRB to get some practice with each of these methods.

### `#each`

**Use Case**: When you want to access each element of the array, but don't care
about returning a new array.

In JavaScript, the `forEach` method can be used to access every element of an
array. It's a general-purpose tool: you can do a lot with it, but it often takes
more work than other array methods to achieve similar results. It's useful in
situations where you just want to access every element from an array and don't
care about the return value:

```js
const newArray = ["this", "is", "JavaScript"].forEach((str) => {
  console.log(str.toUpperCase());
});
// => "THIS"
// => "IS"
// => "JAVASCRIPT"

newArray;
// => ["this", "is", "JavaScript"]
```

Ruby's `#each` method gives similar functionality:

```rb
new_array = ["this", "is", "Ruby"].each do |str|
  puts str.upcase
end
# "THIS"
# "IS"
# "RUBY"

new_array
# => ["this", "is", "Ruby"]
```

### `#map` / `#collect`

**Use Case**: When you want to access every element of an array, calculate a new
value, and return a new array of those new values with the same length as the
original array.

You've seen some examples with `#map` in the previous lesson. Here are a few
other ways you might use this method with arrays:

```rb
[1, 2, 3].map { |num| num * 2 }
# => [2, 4, 6]

users = [{ name: "Duane", phone: "555-555-5555"}, { name: "Liza", phone: "555-555-5556" }]
users.map do |user|
  "Name: #{user[:name]} | Phone: #{user[:phone]}"
end
# => ["Name: Duane | Phone: 555-555-5555", "Name: Liza | Phone: 555-555-5556"]
```

Many of Ruby's enumerable methods have **aliases** to make them easier to
remember for programmers coming from other language. The alias for `#map` is
`#collect`, which works exactly the same as `#map` under the hood:

```rb
[1, 2, 3].collect { |num| num * 2 }
# => [2, 4, 6]
```

This can, unfortunately, lead to confusion when encountering other Ruby code
that uses different method names than you may be expecting to perform the same
tasks. Our advice: stick to using the same method names you're familiar with
from JavaScript!

### `#filter` / `#select` / `#find_all`

**Use Case**: When you want to access every element of an array, check if it
matches some criteria, and return a new array of all the values that match.

Ruby's `#filter` method (also aliased as `#select` and `#find_all`) gives you
the same functionality as `#filter` in JavaScript. For example, here's how you
could filter an array of numbers in JavaScript and return only the even values:

```js
[1, 2, 3, 4, 5].filter((num) => num % 2 === 0);
// => [2, 4]
```

Here's the equivalent syntax in Ruby:

```rb
[1, 2, 3, 4, 5].filter { |num| num.even? }
# => [2, 4]
```

Just like with JavaScript, Ruby's `#filter` method won't let you perform any
transformation of elements; it will only return the original elements based on
whether a truthy or falsy value is returned from the block. For example, let's
say you're working on a method to filter out all the `nil` values from an array
of strings, then capitalize each word. You may be tempted to try something like
this:

```rb
words = ["give", "it", nil, "your", nil, "best", "shot"]
words.filter do |word|
  if word != nil
    word.capitalize
  end
end
# => ["give", "it", "your", "best", "shot"]
```

The reason this doesn't produce an array of capitalized words is that `#filter`
will only return elements from the original array; it won't produce any new
values. To achieve the desired effect, you'd need to `#filter`, then `#map`:

```rb
words = ["give", "it", nil, "your", nil, "best", "shot"]

filtered_words = words.filter do |word|
  word != nil
end

capitalized_words = filtered_words.map do |word|
  word.capitalize
end
# => ["Give", "It", "Your", "Best", "Shot"]
```

Or, more concisely, thanks to method chaining:

```rb
words.filter { |word| word != nil }.map(&:capitalize)
```

Or if you really want to impress your friends:

```rb
words.compact.map(&:capitalize)
```

### `#find` / `#detect`

**Use Case**: When you want to access every element of an array, check if it
matches some criteria, and return **the first element** that matches.

The `#find` method (and its alias method `#detect`) work much like the `#filter`
method. The key difference is that while `#filter` will return an array of all
the elements that match some criteria, `#find` will only return the first one:

```rb
[1, 2, 3, 4, 5].find { |num| num.even? }
# => 2

[1, 2, 3, 4, 5].filter { |num| num.even? }
# => [2, 4]

users = [{ name: "Duane", phone: "555-555-5555"}, { name: "Liza", phone: "555-555-5556" }]
users.find do |user|
  user[:name] == "Liza"
end
# => { name: "Liza", phone: "555-555-5556" }
```

It's very handy for picking one element out of an array.

### `#sort`

**Use case**: return a new array where all the elements have been sorted based
on some criteria.

Ruby's `#sort` method can be called a couple different ways. If you have a simple
array of strings or numbers, for example, you can call `#sort` without a block,
and Ruby will do its best to sort the elements:

```rb
nums = [1, 5, 3]
sorted_nums = nums.sort
sorted_nums
# => [1, 3, 5]
```

Note that unlike JavaScript, `#sort` will not alter the original array:

```rb
nums
# => [1, 5, 3]
sorted_nums
# => [1, 3, 5]
```

When sorting more complex data, you can also provide a block and write a sorting
algorithm of your own. Like JavaScript, the block must return either -1, 0, or 1
to determine the sort order. This can result in some not-so-pleasant code:

```rb
users = [
  { name: "Liza", phone: "555-555-5556" },
  { name: "Duane", phone: "555-555-5555"},
  { name: "Cara", phone: "555-555-5556"},
]

sorted_users = users.sort do |user1, user2|
  if user1[:name] == user2[:name]
    0
  elsif user1[:name] < user2[:name]
    -1
  elsif user1[:name] > user2[:name]
    1
  end
end

sorted_users
# => [{:name=>"Cara", :phone=>"555-555-5556"}, {:name=>"Duane", :phone=>"555-555-5555"}, {:name=>...
```

We can simplify the code above a bit by using the "spaceship operator" (`<=>`).
The spaceship operator, also called the **combined comparison operator**, returns:

- `0` if the first operand equals the second,
- `-1` if the first operand is less than the second, and
- `1` if the first operand is greater than the second.

So, instead of utilizing `if` and `elsif` logic like we did above, we can simply
call `#sort` with the following code:

```rb
users.sort do |user1, user2|
  user1[:name] <=> user2[:name]
end
# => [{:name=>"Cara", :phone=>"555-555-5556"}, {:name=>"Duane", :phone=>"555-555-5555"}, {:name=>...
```

Ruby also provides a `#sort_by` method, which, instead of passing two elements
to the block and requiring you to write the comparison logic, just passes one
element to the block:

```rb
users.sort_by do |user1|
  user1[:name]
end
# => [{:name=>"Cara", :phone=>"555-555-5556"}, {:name=>"Duane", :phone=>"555-555-5555"}, {:name=>...
```

## Conclusion

There are more enumerable methods to explore, so make sure to check out the
[Ruby documentation][ruby docs enumerable] for other examples! These common
methods will take you a long way, but one of the joys of being a Ruby developer
is taking advantage of some of the more abstract enumerable methods that help
with common tasks. In the next lesson, you'll get practice writing some methods
that use enumerables, so refer back to this lesson and the Ruby docs for
reference.

## Resources

- [Enumerable documentation][ruby docs enumerable]

[ruby docs enumerable]: https://ruby-doc.org/core-2.7.3/Enumerable.html
