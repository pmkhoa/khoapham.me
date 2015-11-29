---
title: ES6 Review Part 1
date: 2015-03-03
tags: Javascript, ES6
---

ECMAScript 6 (ES6) is the upcoming version of ECMACScript standard which is
supposed to be ready soon. There are number of significant changes in
this new standard version of Javascript. So far I can tell, this is indeed the
future of Javascript, and I love it.

In this short blog, I use ES6 to demonstrate some new features of ES6 by writing
the linked list data structure.

<div class="center"><img src="/assets/img/LinkedLists-deletingnode.png" alt="Linked List" /></div>

### Classes ###
ES6 finally allows you to declare your object using class keyword. This is one
big step forward of Javascript.

    class Node {
      constructor(data, nextNode) {
        this.data = data;
        this.next = nextNode;
      }
    }

The Class comes with the `constructor` method. This method will execute right
after the object is initiated.

### Better variable scope with let ###
What is it wrong with `var` keyword? Here is a short example showing what wrong
with var keyword:

    function foo(flag) {
      if (flag) {
        var foobar = 2;
      }
      return foobar;
    };
    console.log(foo(true)); // Return 2

As we can see in the example above, the `foobar` variable is declared inside the
if block, not the function scope. But it's still working when we `return foobar`
at the end of function.

However, this is not working in ES6 with the `let` keyword. Clearly, `let` gives us a better way to scope the variable correctly in
Javascript.

    function foo(flag) {
      if (flag) {
        let foobar = 2;
      }
      return foobar;
    }
    console.log(foo(true)); // Error. foobar is not defined.


Here is the full source code of how I implemented linked list in ES6.

    class Node {
      constructor(data, nextNode) {
        this.data = data;
        this.next = nextNode;
      }
    }
    class LinkedList {
      constructor() {
        this.length = 0;
        this.head = null;
      }
      insert(data) {
        let node = new Node(data, null);
        let currentNode;
        if ( ! this.head  ) {
          this.head = node;
        }
        else {
          currentNode = this.head;
          while ( currentNode.next ) {
            currentNode = currentNode.next;
          }
          currentNode.next = node;
        }
        this.length++;
      }
      remove(index) {
        if ( index > -1 && index < this.length ) {
          let currentNode = this.head,
              previousNode,
              i = 0;
          if ( index === 0 ) {
            this.head = currentNode.next;
          } else {
            while ( i++ < index ) {
              previousNode = currentNode;
              currentNode = currentNode.next;
            }
            previousNode.next = currentNode.next;
          }
          this.length--;
          return currentNode.data;
        } else {
          return null;
        }
      }

      getAt(index) {
        if ( index > -1 && index < this.length ) {
          let currentNode = this.head,
              i = 0;
          while (i++ < index) {
            currentNode = currentNode.next;
          }
          return currentNode.data;
        } else {
          return null;
        }
      }
    }


