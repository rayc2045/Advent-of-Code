# Day 9: Rope Bridge

This rope bridge creaks as you walk along it. You aren't sure how old it is, or whether it can even support your weight.

It seems to support the Elves just fine, though. The bridge spans a gorge which was carved out by the massive river far below you.

You step carefully; as you do, the ropes stretch and twist. You decide to distract yourself by modeling rope physics; maybe you can even figure out where *not* to step.

Consider a rope with a knot at each end; these knots mark the *head* and the *tail* of the rope. If the head moves far enough away from the tail, the tail is pulled toward the head.

Due to nebulous reasoning involving [Planck lengths](https://en.wikipedia.org/wiki/Planck_units#Planck_length), you should be able to model the positions of the knots on a two-dimensional grid. Then, by following a hypothetical *series of motions* (your puzzle input) for the head, you can determine how the tail will move.

Due to the aforementioned Planck lengths, the rope must be quite short; in fact, the head (`H`) and tail (`T`) must *always be touching* (diagonally adjacent and even overlapping both count as touching):

```
. . . .
. T H .
. . . .

. . . .
. H . .
. . T .
. . . .

. . .
. H .  (H covers T)
. . .
```

If the head is ever two steps directly up, down, left, or right from the tail, the tail must also move one step in that direction so it remains close enough:

```
. . . . .      . . . . .      . . . . .
. T H . .  ->  . T . H .  ->  . . T H .
. . . . .      . . . . .      . . . . .

.  .  .        .  .  .        .  .  .
.  T  .        .  T  .        .  .  .
.  H  .   ->   .  .  .   ->   .  T  .
.  .  .        .  H  .        .  H  .
.  .  .        .  .  .        .  .  .
```

Otherwise, if the head and tail aren't touching and aren't in the same row or column, the tail always moves one step diagonally to keep up:

```
. . . . .      . . . . .      . . . . .
. . . . .      . . H . .      . . H . .
. . H . .  ->  . . . . .  ->  . . T . .
. T . . .      . T . . .      . . . . .
. . . . .      . . . . .      . . . . .

. . . . .      . . . . .      . . . . .
. . . . .      . . . . .      . . . . .
. . H . .  ->  . . . H .  ->  . . T H .
. T . . .      . T . . .      . . . . .
. . . . .      . . . . .      . . . . .
```

You just need to work out where the tail goes as the head follows a series of motions. Assume the head and the tail both start at the same position, overlapping.

For example:

```
R 4
U 4
L 3
D 1
R 4
D 1
L 5
R 2
```
This series of motions moves the head *right* four steps, then *up* four steps, then *left* three steps, then *down* one step, and so on. After each step, you'll need to update the position of the tail if the step means the head is no longer adjacent to the tail. Visually, these motions occur as follows (`s` marks the starting position as a reference point):

```
==  Initial State  ==

. . . . . .
. . . . . .
. . . . . .
. . . . . .
H . . . . .  (H covers T, s)

==  R 4  ==

. . . . . .
. . . . . .
. . . . . .
. . . . . .
T H . . . .  (T covers s)

. . . . . .
. . . . . .
. . . . . .
. . . . . .
s T H . . .

. . . . . .
. . . . . .
. . . . . .
. . . . . .
s . T H . .

. . . . . .
. . . . . .
. . . . . .
. . . . . .
s . . T H .

==  U 4  ==

. . . . . .
. . . . . .
. . . . . .
. . . . H .
s . . T . .

. . . . . .
. . . . . .
. . . . H .
. . . . T .
s . . . . .

. . . . . .
. . . . H .
. . . . T .
. . . . . .
s . . . . .

. . . . H .
. . . . T .
. . . . . .
. . . . . .
s . . . . .

==  L 3  ==

. . . H . .
. . . . T .
. . . . . .
. . . . . .
s . . . . .

. . H T . .
. . . . . .
. . . . . .
. . . . . .
s . . . . .

. H T . . .
. . . . . .
. . . . . .
. . . . . .
s . . . . .

==  D 1  ==

. . T . . .
. H . . . .
. . . . . .
. . . . . .
s . . . . .

==  R 4  ==

. . T . . .
. . H . . .
. . . . . .
. . . . . .
s . . . . .

. . T . . .
. . . H . .
. . . . . .
. . . . . .
s . . . . .

. . . . . .
. . . T H .
. . . . . .
. . . . . .
s . . . . .

. . . . . .
. . . . T H
. . . . . .
. . . . . .
s . . . . .

==  D 1  ==

. . . . . .
. . . . T .
. . . . . H
. . . . . .
s . . . . .

==  L 5  ==

. . . . . .
. . . . T .
. . . . H .
. . . . . .
s . . . . .

. . . . . .
. . . . T .
. . . H . .
. . . . . .
s . . . . .

. . . . . .
. . . . . .
. . H T . .
. . . . . .
s . . . . .

. . . . . .
. . . . . .
. H T . . .
. . . . . .
s . . . . .

. . . . . .
. . . . . .
H T . . . .
. . . . . .
s . . . . .

==  R 2  ==

. . . . . .
. . . . . .
. H . . . .  (H covers T)
. . . . . .
s . . . . .

. . . . . .
. . . . . .
. T H . . .
. . . . . .
s . . . . .
```

After simulating the rope, you can count up all of the positions the *tail visited at least once*. In this diagram, `s` again marks the starting position (which the tail also visited) and `#` marks other positions the tail visited:

```
. . # # . .
. . . # # .
. # # # # .
. . . . # .
s # # # . .
```

So, there are `13` positions the tail visited at least once.

Simulate your complete hypothetical series of motions. *How many positions does the tail of the rope visit at least once?*

## JavaScript Solution

```js
const input = `
R 4
U 4
L 3
D 1
R 4
D 1
L 5
R 2
`;

function createGridArr(element, columns, rows) {
  const arr = `${String(element).repeat(columns)}\n`.repeat(rows).split('\n');
  arr.length--;
  if (typeof element === 'number')
    return arr.map(row => row.split('').map(item => Number(item)));
  return arr.map(row => row.split(''));
}

function getTailPath(moves, startingPosition = [0, 0], marks = ['.', '#', 's']) {
  const [sx, sy] = [startingPosition[1], startingPosition[0]];
  let [hx, hy, tx, ty] = [sx, sy, sx, sy];
  const tailPath = createGridArr(marks[0], 6, 5);
  const countDiff = () => Math.abs(hx - tx) + Math.abs(hy - ty);
  const markTailPosition = () => (tailPath[ty][tx] = marks[1]);

  moves.trim().split('\n').forEach(move => {
    const [direction, step] = move.split(' ');

    for (let i = 0; i < +step; i++) {
      if (direction === 'R') {
        hx++;
        if (ty === hy && countDiff() >= 2) tx++;
        else if (ty !== hy && countDiff() >= 3) {
          ty = hy;
          tx++;
        }
      } else if (direction === 'L') {
        hx--;
        if (ty === hy && countDiff() >= 2) tx--;
        else if (ty !== hy && countDiff() >= 3) {
          ty = hy;
          tx--;
        }
      } else if (direction === 'U') {
        hy--;
        if (tx === hx && countDiff() >= 2) ty--;
        else if (tx !== hx && countDiff() >= 3) {
          tx = hx;
          ty--;
        }
      } else if (direction === 'D') {
        hy++;
        if (tx === hx && countDiff() >= 2) ty++;
        else if (tx !== hx && countDiff() >= 3) {
          tx = hx;
          ty++;
        }
      }

      markTailPosition();
    }
  });

  tailPath[sy][sx] = marks[2];
  return tailPath;
}

console.log(
  getTailPath(input, [4, 0], [0, 1, 1])
    .reduce((acc, cur) => acc + cur.reduce((a, b) => a + b), 0) // 13
);
```