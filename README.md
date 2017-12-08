# adventofcode2017
My answers to http://adventofcode.com/2017/

day1
====

Puzzle 1/2
```
requires you to review a sequence of digits (your puzzle input) and find the sum of all digits that match the next digit in the list. The list is circular, so the digit after the last digit is the first digit in the list.

For example:

    1122 produces a sum of 3 (1 + 2) because the first digit (1) matches the second digit and the third digit (2) matches the fourth digit.
    1111 produces 4 because each digit (all 1) matches the next.
    1234 produces 0 because no digit matches the next.
    91212129 produces 9 because the only digit that matches the next one is the last digit, 9.
```
Answer 1/2
```scala
val input = puzzle :+ puzzle.last
val answer = (input zip input.drop(1)).foldLeft(0){case (acc, (a,b)) => if (a==b) acc + a else acc}
```

Puzzle 2/2
```
Now, instead of considering the next digit, it wants you to consider the digit halfway around the circular list. That is, if your list contains 10 items, only include a digit in your sum if the digit 10/2 = 5 steps forward matches it. Fortunately, your list has an even number of elements.

For example:

    1212 produces 6: the list contains 4 items, and all four digits match the digit 2 items ahead.
    1221 produces 0, because every comparison is between a 1 and a 2.
    123425 produces 4, because both 2s match each other, but no other digit has a match.
    123123 produces 12.
    12131415 produces 4.
```
Answer 2/2
```scala
val input = puzzle.toStream #::: puzzle.toStream
val answer = (puzzle zip input.drop(puzzle.size/2)).foldLeft(0){case (acc, (a,b)) => if (a==b) acc + a else acc}
```


day2
====
```scala
//assuming data imported as:
import scala.io.Source
val lines = Source.fromFile(s"$path.txt").getLines.toList.map(_.split('\t').map(_.toInt))
```

Puzzle 1/2
```
The spreadsheet consists of rows of apparently-random numbers. To make sure the recovery process is on the right track, they need you to calculate the spreadsheet's checksum. For each row, determine the difference between the largest value and the smallest value; the checksum is the sum of all of these differences.

For example, given the following spreadsheet:

5 1 9 5
7 5 3
2 4 6 8

    The first row's largest and smallest values are 9 and 1, and their difference is 8.
    The second row's largest and smallest values are 7 and 3, and their difference is 4.
    The third row's difference is 6.

In this example, the spreadsheet's checksum would be 8 + 4 + 6 = 18.

What is the checksum for the spreadsheet in your puzzle input?
```

Answer 1/2
```scala
lines.map(l => l.max - l.min).sum
```

Puzzle 2/2
```
the goal is to find the only two numbers in each row where one evenly divides the other - that is, where the result of the division operation is a whole number. They would like you to find those numbers on each line, divide them, and add up each line's result.

For example, given the following spreadsheet:

5 9 2 8
9 4 7 3
3 8 6 5

    In the first row, the only two numbers that evenly divide are 8 and 2; the result of this division is 4.
    In the second row, the two numbers are 9 and 3; the result is 3.
    In the third row, the result is 2.

In this example, the sum of the results would be 4 + 3 + 2 = 9.

What is the sum of each row's result in your puzzle input?
```

Answer 2/2
```scala
lines.map(_.combinations(2).foldLeft(0){case (res, Array(a,b)) => if (a.max(b) % a.min(b) == 0) a.max(b)/a.min(b) else res}).sum
```


day3
====

Puzzle 1/2
```
How many steps are required to carry the data from the square identified in your puzzle input all the way to the access port?
```

Answer 1/2
The "rings" follow a polynomial sequence of indexes such as: 1, 2-9, 10-25, …, 2(2n^2-2n+1) - 4n^2+4n+1
With this knowledge, it's easy to come-up with an analytical solution to the input's coordinates.
One starting point is to calculate the "ring" it belongs to, then the sector, and finally to count the steps.
(Each sector has a length of 2×ring).
```scala
def coordinates(input: Int) = { //for any input greater than 1
    val ringIndex = (n: Int) => 4 * n * n + 4 * n + 1
    val ring = Stream.from(1).find(n => ringIndex(n) >= input).get
    val offset = input - ringIndex(ring - 1)

    val sectorLength = 2 * ring
    val (sector, steps) = (offset / sectorLength, offset % sectorLength)
    sector match {
      case 0 => (ring, -ring + steps)
      case 1 => (ring - steps, ring)
      case 2 => (-ring, ring - steps)
      case 3 => (-ring + steps, -ring)
      case 4 => (ring, -ring)
    }
  }
  
def distance(a: (Int, Int), b: (Int, Int) = (0, 0)) = (a._1 - b._1).abs + (a._2 - b._2).abs

distance(coordinates(input))
```

