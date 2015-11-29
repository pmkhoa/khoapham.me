---
title: Tower of Hanoi
date: 2014-12-02
published: false
tags: Ruby, Recursion, Fun 
---

One of my favorite childhood game was the [Tower of Hanoi](http://en.wikipedia.org/wiki/Tower_of_Hanoi) which I loved playing it
with my neighbor friends all the time. Also, one of my very first project in college
was implementing the tower of Hanoi in C, C++, or Java. I guess this game has sticked with
me for a very long time, so that today I decided to implement it again in my new
favorite language - Ruby. In fact, it's simple, elegant, and easy to implement in ruby.

    class TowerOfHanoi
      SRC = 0
      DST = 2
      TEMP = 1

      def initialize(disks: 4)
        @disks = disks
      end

      def run
        move(@disks, SRC, DST, TEMP)
      end

      def move(disks, src, dst, temp)
        return moveFromTo(src, dst) if disks == 1
        move(disks-1, src, temp, dst)
        moveFromTo(src, dst)
        move(disks-1, temp, dst, src)
      end

      def moveFromTo(src, dst)
        puts "Move from #{src} to #{dst}"
      end
    end

To run the program, open your terminal and try it in your irb:

    $ irb
    require_relative 'TowerOfHanoi'
    game = TowerOfHanoi.new # or game = TowerOfHanoi.new(disks: 4)
    game.run

