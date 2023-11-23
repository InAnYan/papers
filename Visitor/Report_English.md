# Slide 1
Good afternoon! My name is Ruslan Popov and I would like to present our paper on "...".
Our research deals with the theory of programming languages and compiler development, and proposes a new method of implementing the Visitor pattern in C++, which is fast to write and provides many interesting features.

# Slide 2
To begin with, compilers and interpreters have a common stage of source code processing - parsing. It is necessary to transform the program text into an intermediate representation called an abstract syntax tree. This is a recursive data type that extracts the program syntax from a sequence of characters and is very convenient for further processing of the source code.

# Slide 3
Having such a tree, we can do many things with it. For example: for debugging, it is useful to print the tree to the console, which will result in a string; an interpreter can evaluate an expression, which will result in a value; or an optimizer can transform the tree into another. When developing compilers, the task is to encode the AST in such a way that functions for processing this tree can be quickly added. This task is called "expression problem" in foreign literature.

# Slide 4
Since C++ is an object-oriented programming language, it is natural to represent the nodes of the tree as classes, and the functions over this tree as class methods. That is, you need to create a `Node` interface that contains virtual methods, and each AST node implements them in its own way. This method works well and coincides with the OOP paradigm, but it has a drawback. Different functions of working on the tree are mixed in one class. And this observation prompts the question: is it possible to write the specific node processing functions in one source file?

# Slide 5
The classic way to solve the "expression problem" in object-oriented programming languages is to use the Visitor pattern. In this pattern, we divide all the code into two classes: the first are classes for the nodes of the tree, and the second are classes that process the tree. A `Visitor` interface is created that must be implemented by all classes that need access to the tree. Each node must implement the `accept` method, which accepts an object of type visitor and calls the corresponding `visit` method on it.

# Slide 6
This approach completely solves the "expression problem", but it also has its drawbacks. It is not easy to understand, you need to clearly understand the architecture of the program and how the program will be executed. This pattern is quite difficult to write. If there is a need to create a new node in the AST, you need to create a virtual `visit` method in the visitor, and the new node needs to implement the `accept` method. We asked ourselves: can this pattern be reproduced more easily?

# Slide 7
Having dived into the C++ standard, we found such a container as `std::variant` and the `std::visit` function. `std::variant` is a modern type-safe implementation of union in C++. It is a standard container and uses the capabilities of template metaprogramming. Previously, access to union members was done through the use of methods to check the stored type and direct access to the union element, but `std::variant` also offers a more interesting way.

# Slide 8
There is such a function as `std::visit`. It takes two arguments: the first is a `Callable` object called a visitor, and the second is a variant. The visitor must have callable functions defined with all the data types that are stored in the variant. `std::visit` will look at the element that is stored and call the necessary method in the visitor to process the type. This slide shows a code sample. We have created a class that defines methods for working with an integer and a boolean value. Inside the main function, we created a variant that stores an integer and a boolean value. We also created an instance of visitor and thus can call the `std::visit` method, which will return a string according to the type it stores.

# Slide 9
Our method of solving the "expression problem" is to use this container. The abstract nodes of the AST can be represented as a variant. And certain functionality on the tree remains a separate class, in which the methods for processing the tree should have the same name, but have a different type of parameter. The advantage of the proposed method is that it eliminates the need to create a `Visitor` interface and create `accept` methods in tree nodes. This reduces the time to create code and is easier to understand. But the biggest advantage is that visitors can return any type of data. The thing is that in the classical way of implementation, the `Visitor` is an abstract class, and the `visit` methods are virtual, which means they cannot be wrapped in a template and must remain `void`. The optimizer can't return a new tree, the interpreter can't return the result of calculations, so they have to store all the results in the field of their class. But by using the `std::visit` method, this problem is solved.

# Slide 10
This method has several drawbacks that you should pay attention to before using it. First, `std::variant` is available only from C++17. Secondly, `std::variant` is implemented using template metaprogramming, so the compiler may generate strange and complicated error messages in case of any errors in the code. Thirdly, you should be careful when creating new AST nodes, you can't directly use `std::variant` inside a node, because you will face the classic problem of using a structure inside itself.

# Slide 11
Thus, we have shown how to use `std::variant` to encode an AST and recreate the Visitor pattern. Our method is faster and more versatile than the classic Visitor implementation. This will speed up the creation of new interpreters and compilers. Also, we want to note that our method is universal and fits all problems involving the sum type.

# Slide 12
That's all, thank you for your attention!
