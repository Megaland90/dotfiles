#!/bin/sh

num_fileC=$(find -name "*.c" -not -path "./include/*" | wc -l)
num_fileCpp=$(find -name "*.cpp" -not -path "./include/*" | wc -l)
name="a.out"
ldflags="$2"

if  [ $1 ]
then
	name=$1
else
	echo "Usage : $0 [name]"
	exit 0
fi

if [ $num_fileCpp -gt 0 ]
then
	find -name "*.cpp"  -not -path "./include/*" > tmp_file
	sed -i 's/\.\//\t/g' tmp_file
	sed -i 's/$/ \\/g' tmp_file
	src=$(cat tmp_file)

	makefile=".SILENT:

CC	= g++

DEBUG	?= 0

RM	= rm -rf

LDFLAGS	+= $ldflags

CXXFLAGS += -W -Wall -Iinclude

ifeq (\$(DEBUG), 1)
	CXXFLAGS+= -DDEBUG -g3
else
	CXXFLAGS+= -Werror 
endif

SRCS	= $src

OBJS	= \$(SRCS:.cpp=.o)

NAME	= $name

all: \$(NAME) ## Compile

\$(NAME): \$(OBJS)
	\$(CC) -o \$(NAME) \$(OBJS) \$(LDFLAGS)

clean: ## Clean .o
	\$(RM) \$(OBJS)

fclean: clean ## Clean binary and .o
	\$(RM) \$(NAME)
	\$(RM) debug

clean_comment: ## Clean all comment //
	find -type f -name \"*.cpp\" | xargs sed -i 's://.*$$::g'

help: 
	@grep -E '(^[a-zA-Z_-]+:.*?##.*\$\$)|(^##)' Makefile | awk 'BEGIN {FS = \":.*?## \"}; {printf \"\033[32m%-30s\033[0m %s\n\", \$\$1, \$\$2}' | sed -e 's/\[32m##/[33m/'

debug: fclean ## Compile to debug mode
	make -j re DEBUG=1
	mkdir debug
	mv \$(NAME) debug/

re: fclean all ## Recompile

.PHONY: all re clean fclean help debug clean_comment"

elif [ $num_fileC -gt 0 ]
then
	find -name "*.c" > tmp_file
	sed -i 's/\.\///g' tmp_file
	sed -i 's/$/ \\/g' tmp_file

	src=$(cat tmp_file)
	
	makefile=".SILENT:
	
CC	= gcc

DEBUG	?= 0

RM	= rm -rf

LDFLAGS	+= $ldflags

CFLAGS	+= -W -Wall -Iinclude

ifeq (\$(DEBUG), 1)
	CFLAGS+= -DDEBUG -g3
else
	CFLAGS+= -Werror 
endif

SRCS	= $src

OBJS	= \$(SRCS:.c=.o)

NAME	= $name

all: \$(NAME)

\$(NAME): \$(OBJS) ## Compile
	\$(CC) -o \$(NAME) \$(OBJS) \$(LDFLAGS)

clean: ## Clean .o
	\$(RM) \$(OBJS)

fclean: clean ## Clean binary and .o
	\$(RM) \$(NAME)
	\$(RM) debug

clean_comment: ## Clean all comment //
	find -type f -name \"*.c\" | xargs sed -i 's://.*$$::g'

re: fclean all ## Recompile

help: 
	@grep -E '(^[a-zA-Z_-]+:.*?##.*\$\$)|(^##)' Makefile | awk 'BEGIN {FS = \":.*?## \"}; {printf \"\033[32m%-30s\033[0m %s\n\", \$\$1, \$\$2}' | sed -e 's/\[32m##/[33m/'

debug: fclean ## Compile to debug mode
	make -j re DEBUG=1
	mkdir debug
	mv \$(NAME) debug/

.PHONY: all re clean fclean help debug clean_comment"
fi


rm tmp_file

echo  "$makefile" > Makefile
