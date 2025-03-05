[Back to notes](index)
[Back to index](/index)

# Makefile Notes

# Preliminaries

A stem is the text between the prefix and suffix. For example, when the pattern "%.o" matches the file test.o, the stem is test.

# Wildcards

Both * and % are called wildcards in Make. 
1. The * searches your filesystem for matching filenames. It is suggested to always wrap it in a wildcard function. The * may be used in the target, prerequisites or in the wildcard function. The * **should not be used** directly in a varible definition.
2. The % can be used in the following situations
  * When used in "matching" mode, it matches one or more characters in a string. This match is called a stem.
  * When used in "replacing" mode, it takes the stem that was matched and replaces that in a string.
  * % is most often used in rule definitions and in some specific situations.

# Automatic variables 

* $@ refers to the target name
* $? refers to all the targets newer than the target
* $^ refers to all prerequisites
* $< referes to the first prerequisite

# Rules

## Implicit Rules

1. Compiling a C program: n.o is made automatically from n.c with a command of the form $(CC) -c $(CPPFLAGS) $(CFLAGS) $^ -o $@
2. Compiling a C++ program: $(CXX) -c $(CPPFLAGS) $(CXXFLAGS) $^ -o $@
3. Linking a single object file: n is made automatically from n.o by running the command $(CC) $(LDFLAGS) $^ $(LOADLIBES) $(LDLIBS) -o $@

The important variables used by implicit rules are:
* CC: Program for compiling C programs; default cc
* CXX: Progrma for compiling C++ programs; default g++
* CFLAGS: Extra flags to give to the C compiler
* CXXFLAGS: Extra flags to give to the C++ compiler
* CPPFLAGS: Extra flags to give to the C preprocessor
* LDFLAGS: Extra flags to give to compilers when they are supposed to invoke the linker

## Static pattern rules

```
targets...: target-pattern: prereq-patterns ...
  commands
```

The essence of this rule is that the target is matched based on the target-pattern (via a % wildcard). The stem is then substituted into the prereq-patterns, to generate the target's
prereqs.

Example without static pattern rules

```
objects = foo.o bar.o all.o
all: $(objects)

# These files compile via implicit rules
foo.o: foo.c
bar.o: bar.c
all.o: all.c

all.c: 
  echo "int main() { return 0; }" > all.c
  
%.c: 
  touch $@
```

Example with static pattern rules

```
objects: foo.o bar.o all.o
all: $(objects)

# In the case of the first target, foo.o, the target pattern matches foo.o and sets the "stem" to be "foo"
# It then replaces the '%' in prereq-patterns with that stem
$(objects): %.o: %.c


all.c: 
  echo "int main() { return 0; }" > all.c
  
%.c: 
  touch $@

```

The filter function can be used in static pattern rules to match the correct files

```
obj_files = foo.result bar.o lose.o
src_files = foo.raw bar.c lose.c

all: $(obj_files)

# This .PHONY is important here. Without it, implicit rules will try to build the executable "all", since the prereqs are ".o" files.
.PHONY: all

# Example 1: .o files depend on .c files. Though we don't actually make the .o file
$(filter %.o, $(obj_files)): %.o: %.c
  commands..
  
# Example 2: .result files depend on .raw files. Though we don't actually make the .result file
$(filter %.result, $(obj_files)): %.result: %.raw
  commands...
```

## Pattern rules

The pattern rules can be looked at as: A way to define our own implicit rules; A simpler form of static pattern rules.

```
# Define a pattern rule that compiles every .c file into a .o file
%.o: %.c
  $(CC) -c $(CFLAGS) $(CPPFLAGS) $< -o $@
```

Pattern rules contains a '%' in the target. This '%' matches any nonempty string, and the other characters match themselves. 

```
# Define a pattern rule that has no pattern in the prerequisites
# This just creates empty .c files when needed.
%.c: 
  touch $@
```

##ouble colon rules

Double colon rules are rarely used, but allow multiple rules to be defined for the same target. 

```
all: blah

blah::
  echo "hello"

blah::
  echo "Hello again"
```

# Commands and Execution

## Command echoing/silencing

Add a @ before a command to stop it from being printed. We can also run make with a -s to add a @ before each executing line.

## Command execution

Each command is run in a "new shell" 

```
all: 
  cd
  
  # The cd above does not affect this line, because each command is effectively run in a new shell.
  echo `pwd`
```

## Default shell
