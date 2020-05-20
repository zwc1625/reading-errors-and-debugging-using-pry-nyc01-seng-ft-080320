# Debugging with Pry

## Overview

We'll cover Pry, a type of REPL, and discuss how to install and use it to debug
a program.

## Objectives

1. Explain how Pry is a more flexible REPL than IRB.
2. Install Pry on your computer. (already installed for IDE users)
3. Debug a program using `binding.pry` within the body of your file.

## What Is a REPL?

You've already been introduced to REPLs through using IRB (Interactive Ruby).
REPL stands for *Read, Evaluate, Print, Loop*. It is an interactive programming
environment that takes a user's input, evaluates it and returns the result to
the user.

Ruby installs with its own REPL, which is IRB, that you've already been using.
Every time you type `irb` into your terminal, you're entering into a REPL.

## What Is Pry?

Pry is another Ruby REPL with some added functionality. When you enter IRB, you
are entering a brand new interactive environment. Any code you want to play with
in IRB, you have to write in IRB or copy and paste into IRB. Pry, on the other
hand, is like a REPL that you can inject into your program.

Pry is far more flexible than IRB. Once you install the Pry library (via the Pry
gem — we'll walk through installation in a bit), you can use the following line
`binding.pry` anywhere in your code.

## Wait... What's 'binding'?

Binding is a built-in ruby class whose objects can encapsulate the context of
your current scope (variables, methods etc.), and retain them for use outside of
that context.

Calling `binding.pry` is essentially 'prying' into the current binding or
context of the code, from outside your file.

So when you place the line `binding.pry` in your code, that line will get
interpreted at runtime (as your program is executed). When the interpreter hits
that line, your program will actually *freeze* and your terminal will turn into
a REPL that exists right in the middle of your program, wherever you added the
`binding.pry` line.

Let's take a look. In this repository, you'll see a file called
`pry_is_awesome.rb`.

## Instructions Part I

1. Ensure that you're in the project "root" directory. If you're using the
   in-browserr IDE, you should be in the project's root directory when you open
   the IDE.

2. Verify that 'pry' is installed by running `gem list pry` in the terminal. You
   should see something like `pry (0.11.3)`. If you _do not_ see a version of
   Pry when running this command, you can install it by running
   `gem install pry`. The Learn IDE already has Pry installed.
  
> **Aside**: If you are receiving an error that mentions `pry-rescue` when
> running the tests, installing the latest version with `gem install pry-rescue`
> should resolve this error. Similarly, if you receive an error related to
> `diff-lcs` when testing, running `gem cleanup diff-lcs` should resolve this
> error.

3. Look at the code in `lib/pry_is_awesome.rb`

   You should see the following code:

    ```ruby
    require 'pry'

    def prying_into_the_method
        inside_the_method = "We're inside the method"
        puts inside_the_method
        puts "We're about to stop because of pry!"
        binding.pry
        this_variable_hasnt_been_interpreted_yet = "The program froze before it could read me!"
        puts this_variable_hasnt_been_interpreted_yet
    end

    prying_into_the_method
    ```

   Here we are requiring `pry`, *which you must do to use pry*, defining a
   method, and then calling that method.

4. In the directory of this repo, in your terminal, run the file by typing
   `ruby lib/pry_is_awesome.rb`. Now, look at your terminal. You should see
   something like this:

    ```ruby
      3: def prying_into_the_method
        4:     inside_the_method = "We're inside the method"
        5:     puts inside_the_method
        6:     puts "We're about to stop because of pry!"
    =>  7:     binding.pry
        8:     this_variable_hasnt_been_interpreted_yet = "The program froze before it could read me!"
        9:     puts this_variable_hasnt_been_interpreted_yet
        10: end
    [1] pry(main)>
    ```

   You have frozen your program *as it executes* and are now inside a REPL
   *inside your program*. You basically just stopped time! How cool is that?

5. In the terminal, in your pry console, type the variable name
   `inside_the_method` and hit enter. You should see a return value of `"We're
   inside the method"`

   You are actually able to explore and manipulate the data *inside* the method
   in which you've placed your binding.

   Now, in the terminal, in your pry console, type the variable name
   `this_variable_hasnt_been_interpreted_yet`. You should see a return value of
   `nil`. That's because the binding you placed on line 7 actually froze the
   program on line 7 but the variable you just called is declared  on line 8. It
   hasn't been interpreted yet. Consequently, our REPL doesn't know about it.
   Now, in the terminal, type `exit`, and you'll leave your pry console and the
   program will continue to execute.

## Instructions Part II: Using Pry to Debug

You can imagine how helpful it will be to use Pry to freeze programs and to
"pry" methods open in order to solve tests and debug your code. Let's walk
through an example together. In this repository that you've forked and cloned
down onto your computer, you'll see a `spec` folder containing a file
`pry_debugging_spec.rb`. This is a test for the file `lib/pry_debugging.rb`.

In `pry_debugging.rb`, we have a broken method. Run `learn` to see the failing
test. The failed test should look like this:

```text
Failures:

  1) #plus_two takes in a number as an argument and returns the sum of that number and 2
     Failure/Error: expect(plus_two(3)).to eq(5)

       expected: 5
            got: 3

       (compared using ==)
     # ./spec/pry_debugging_spec.rb:6:in `block (2 levels) in <top (required)>'
```

Oh no! A broken program! Luckily, we have Pry required at the top of our
`spec/pry_debugging_spec.rb` file, and we know how to use it. Let's place a
`binding.pry` right before the `end` keyword like this.

```ruby
def plus_two(num)
    num + 2
    num
    binding.pry
end
```

Now, run the test suite again and drop into your Pry console. Your terminal
should look like this:

```ruby
From: /Users/sophiedebenedetto/Desktop/Dev/Ruby-Methods_and_Variables/pry-readme/lib/pry_debugging.rb @ line 4 Object#plus_two:

    1: def plus_two(num)
    2:  num + 2
    3:  num
 => 4:  binding.pry
    5: end

[1] pry(#<RSpec::ExampleGroups::PlusTwo>)>
```

The test is calling our `plus_two` method with the argument, `num`. While in
Pry, we can verify the value of `num` set to `3` by typing in the variable name.

```ruby
[1] pry(#<RSpec::ExampleGroups::PlusTwo>)> num
=> 3
[2] pry(#<RSpec::ExampleGroups::PlusTwo>)>
```

We know from running `learn` a moment ago that the test is expecting to receive
`5`, but instead got `3`. Hmm...

Remember that the return value of a method in Ruby is generally the value of the
last line of the method. By checking the value of `num` on the last line of our
method inside our Pry console, we can see that `num` is set to `3` and therefore
the method is returning `3`.

How can we fix this method so that it behaves in the expected way? This method
is called `plus_two` and the test is expecting a return value of `5`, given a
`num` of `3`. Looks like our method should return the *sum* of the original
number (`3`) plus 2. However, our method, as it currently stands, is just
returning the original number. Play around with it inside your Pry console and
get the test to pass. Remember to type `exit` in your terminal. When you think
you've got the solution, **remove your `binding.pry` from the method** and run
`learn` to retest the code.

Once you have your test passing, run `learn submit` to register completion of
this lab.

## Resources

* [4-Minute Guide to Debugging with Pry by Dick Ward](https://medium.com/@TheDickWard/an-intro-to-ruby-debugging-featuring-pry-c931fde69069)
* [Pry documentation](http://pryrepl.org/)