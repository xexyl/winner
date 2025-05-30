#!/usr/bin/env make
#
# IOCCC 1993 winning entry - jonth

################################################################################
#
# IOCCC winning entry code may not work on your system.  What was liked/allowed
# and worked in the past may no longer be liked/allowed or compile/run today.
#
# Bug fixes, corrections and typo fixes are VERY WELCOME.  If you see a problem,
# first check this URL for a list of known bugs and (mis)features of IOCCC entries:
#
#	https://www.ioccc.org/bugs.html
#
# GitHub pull requests are welcome!  Please see the above URL for details.
#
################################################################################
#
# This file is Copyright (c) 2023 by Landon Curt Noll.  All Rights Reserved.
# You are free to share and adapt this file under the terms of this license:
#
#	Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)
#
# For more information, see: https://creativecommons.org/licenses/by-sa/4.0/


#############################
# shell used by this Makefile
#############################
#
SHELL= bash

#######################
# common tool locations
#######################
#
include ../../var.mk

# Common C compiler warnings to silence
#
CSILENCE= -Wno-error -Wno-implicit-function-declaration \
	  -Wno-incompatible-pointer-types -Wno-int-conversion \
	  -Wno-c99-extensions -Wno-deprecated-declarations \
          -Wno-deprecated-non-prototype -Wno-format -Wno-pedantic \
	  -Wno-return-type -Wno-strict-prototypes -Wno-unused-variable \
	  -Wno-implicit-int -Wno-builtin-declaration-mismatch \
	  -Wno-misleading-indentation -Wno-missing-parameter-type \
	  -Wno-unused-value

# Attempt to silence unknown warning option
#
CUNKNOWN= -Wno-unknown-warning-option

# Common C compiler warning flags
#
CWARN= -Wall -Wextra ${CSILENCE} ${CUNKNOWN}

# Compiler standard
#
CSTD= -std=gnu90

# Compiler bit architecture
#
ARCH=

# Defines that are needed to compile
#
CDEFINE= -DXX1='H 19;}M(J,4)}N H 20;}M(T,4)}q(m,w,' \
	-DXX='XKeycodeToKeysym(D,o[13],0);K 12:Y();}R;}d XX1'

# Include files that are needed to compile
#
CINCLUDE= -I ${X11_INCDIR} -I ${X11_INCDIR}/X11

# Optimization
#
OPT= -O3

# Default flags for ANSI C compilation
#
CFLAGS= ${CSTD} ${CWARN} ${ARCH} ${CDEFINE} ${CINCLUDE} ${OPT}

# Libraries needed to build
#
LDFLAGS= -L ${X11_LIBDIR} -lX11

# C compiler to use
#
CC= cc

# Compiler add-ons or replacements for clang only
#
ifeq "$(findstring $(CLANG),${CC})" "$(CLANG)"
#
CSILENCE+= -Wno-cast-align -Wno-comma -Wno-missing-prototypes \
	   -Wno-missing-variable-declarations -Wno-poison-system-directories \
	   -Wno-sign-conversion -Wno-strict-prototypes \
	   -Wno-implicit-int-conversion -Wno-shadow -Wno-shorten-64-to-32 \
	   -Wno-extra-semi-stmt -Wno-unsafe-buffer-usage
#
CWARN+= -Weverything
#
endif

# Specific add-ons or replacements for gcc only
#
ifeq "$(findstring $(GCC),${CC})" "$(GCC)"
#
#CSILENCE+=
#
#CWARN+=
#
endif


##############################
# Special Makefile variables for this entry
##############################
#
ENTRY= jonth
PROG= ${ENTRY}
#
OBJ= ${PROG}.o
DATA=
TARGET= ${PROG}
#
ALT_OBJ=
ALT_TARGET=


#################
# build the entry
#################
#
all: data ${TARGET}
	@${TRUE}

.PHONY: all alt data everything clean clobber

${PROG}: ${PROG}.tmp.c
	${CC} ${CFLAGS} $< -o $@ ${LDFLAGS}

${PROG}.tmp.c: ${PROG}.c
	${SED} -e 's/\^\ /#define/' -e 's/@/Window(D,/g' $< > $@

# alternative executable
#
alt: data ${ALT_TARGET}
	@${TRUE}

# data files
#
data: ${DATA}
	@${TRUE}

# both all and alt
#
everything: all alt
	@${TRUE}


###############
# utility rules
###############
#
clean:
	${RM} -f ${OBJ} ${ALT_OBJ}

clobber: clean
	${RM} -f ${TARGET} ${ALT_TARGET}
	${RM} -rf *.dSYM
	${RM} -f jonth.tmp.c


######################################
# optional include of 1337 hacker rulz
######################################

-include 1337.mk ../1337.mk ../../1337.mk
