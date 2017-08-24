https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/

# Contents
[TOC]

# Part One

## Chapter 1. Category. The essence of composition

  __Properties of composition:__
  1. Composition is associative.
     ```haskell
     h . (g . f) = (h . g) . f = h . g . f
     ```

  2. For every object there is an arrow which is a unit of composition.
     ```haskell
     f . id = f
     id . f = f
     ```

## Chapter 2. Types and functions
  The simplest intuition for types is that they are sets of values.
