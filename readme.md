# Makefiles
*Always set up the buildsystem at the beginning of the project*

Alternative to Makefile:
- Maven
- Rake

## Auomatic variables for samaller ans easier to maintain makefile

Exemple:
```
CC=gcc
CFLAGS=-g -Wall

all: app

list.o: list.h list.c
	$(CC) $(CFLAGS) -C list.c

tree.o tree.h tree.c
	$(CC) $(CFLAGS) -C tree.c

app: list.o tree.o main.c
	$(CC) $(CFLAGS) -o app main.c list.o tree.o

clean:
	rm *.o app
```

Valiable-name:
- $@: Target name
- $^: Dependencies (from a target) 
- %.index_of_file: Pattern rules
- $<: The name of the first prerequisite. If the target got its recipe from an implicit rule, this will be the first prerequisite added by the implicit rule

Refactored exemple:
```
CC=gcc
CFLAGS=-g -Wall
 
all: app

%.o: %.c %.h
	$(CC) $(CFLAGS) -c $^
 
app: list.o tree.o main.c
   $(CC) $(CFLAGS) -o $@ $^

clean:
	rm *.o app
```

## Makefiles: subdirectories and structure
> Setting up a programming project

```
CC=gcc
CFLAGS=-g -Wall
OBJS=a.o b.o
BIN = main
SUBMITNAME=proj.zip

all: $(BIN)

main: $(OBJS)
	$(CC) $(CFLAGS) $(OBJS) -o main

%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	$(RM) -r main *.o

submit:
	$(RM) $(SUBMITNAME)
	zip $(SUBMITNAME) $(BIN)
```

Refactoring this to make it work with subdirectories.

creat folders
1. src
	1. all .c files here
2. obj
3. bin

refactored makefile: 
```
CC=gcc
CFLAGS=-g -Wall
OBJS=obj/a.o obj/b.o
BIN = bin/main
SUBMITNAME=proj.zip

all: $(BIN)

bin/main: $(OBJS)
	$(CC) $(CFLAGS) $(OBJS) -o $@

obj/%.o: src/%.c
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	$(RM) -r bin/* obj/*

submit:
	$(RM) $(SUBMITNAME)
	zip $(SUBMITNAME) $(BIN)
```

refactoring to get rid of hardcoded paths:
```
CC=gcc
CFLAGS=-g -Wall
SRC=src
SRCS=$(wildcard $(SRC)/*.c)
OBJ=obj
OBJS=$(patsubst $(SRC)/%.c, $(OBJ)/%.o, $(SRCS))
BINDER=bin
BIN = $(BINDER)/main
SUBMITNAME=proj.zip

all: $(BIN)

$(BIN): $(OBJS)
	$(CC) $(CFLAGS) $(OBJS) -o $@

$(OBJ)/%.o: $(SRC)/%.c
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	$(RM) -r $(BINDER)/* $(OBJ)/*

submit:
	$(RM) $(SUBMITNAME)
	zip $(SUBMITNAME) $(BIN)
```

> $(wildcard $()) --> look for anything in the src-dir that ends with .c
> OBJS=$(patsubst $(SRC)/%.c, $(OBJ)/%.o, $(SRCS)) --> for pattern substitution. if anyting ands with .c replace it with .o. And do the replacement on the SRCS dir

