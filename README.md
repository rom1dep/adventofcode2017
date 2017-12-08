# adventofcode2017
My answers to http://adventofcode.com/2017/

day1
====

Puzzle:
```
requires you to review a sequence of digits (your puzzle input) and find the sum of all digits that match the next digit in the list. The list is circular, so the digit after the last digit is the first digit in the list.

For example:

    1122 produces a sum of 3 (1 + 2) because the first digit (1) matches the second digit and the third digit (2) matches the fourth digit.
    1111 produces 4 because each digit (all 1) matches the next.
    1234 produces 0 because no digit matches the next.
    91212129 produces 9 because the only digit that matches the next one is the last digit, 9.
```

Answer:
```scala
val input = puzzle :+ puzzle.last
val answer = input.foldLeft(0){case (acc, (a,b)) => if (a==b) acc + a else acc}
```
