# Day 8: Treetop Tree House

The expedition comes across a peculiar patch of tall trees all planted carefully in a grid. The Elves explain that a previous expedition planted these trees as a reforestation effort. Now, they're curious if this would be a good location for a [tree house](https://en.wikipedia.org/wiki/Tree_house).

First, determine whether there is enough tree cover here to keep a tree house *hidden*. To do this, you need to count the number of trees that are *visible from outside the grid* when looking directly along a row or column.

The Elves have already launched a [quadcopter](https://en.wikipedia.org/wiki/Quadcopter) to generate a map with the height of each tree (your puzzle input). For example:

```
3 0 3 7 3
2 5 5 1 2
6 5 3 3 2
3 3 5 4 9
3 5 3 9 0
```

Each tree is represented as a single digit whose value is its height, where `0` is the shortest and `9` is the tallest.

A tree is *visible* if all of the other trees between it and an edge of the grid are *shorter* than it. Only consider trees in the same row or column; that is, only look up, down, left, or right from any given tree.

All of the trees around the edge of the grid are *visible* - since they are already on the edge, there are no trees to block the view. In this example, that only leaves the *interior nine trees* to consider:

- The top-left `5` is *visible* from the left and top. (It isn't visible from the right or bottom since other trees of height `5` are in the way.)
- The top-middle `5` is *visible* from the top and right.
- The top-right `1` is not visible from any direction; for it to be visible, there would need to only be trees of height 0 between it and an edge.
- The left-middle `5` is *visible*, but only from the right.
- The center `3` is not visible from any direction; for it to be visible, there would need to be only trees of at most height `2` between it and an edge.
- The right-middle `3` is *visible* from the right.
- In the bottom row, the middle `5` is *visible*, but the `3` and `4` are not.

With 16 trees visible on the edge and another 5 visible in the interior, a total of `21` trees are visible in this arrangement.

Consider your map; *how many trees are visible from outside the grid?*

## JavaScript Solution

```js
const input = `
30373
25512
65332
33549
35390
`;

function countVisible(grid) {
  const gridArr = grid
    .trim()
    .split('\n')
    .map(item => item.split('').map(str => Number(str)));

  const checkVisible = (rowIdx, columnIdx) => {
    // edge
    if (
      rowIdx === 0 ||
      rowIdx === gridArr.length - 1 ||
      (rowIdx !== 0 && columnIdx === 0) ||
      (rowIdx !== 0 && columnIdx === gridArr[rowIdx].length - 1)
    )
      return true;

    // interior
    const currentHeight = gridArr[rowIdx][columnIdx];

    let isLeftVisible = true;
    for (let leftIdx = 0; leftIdx < columnIdx; leftIdx++) {
      if (gridArr[rowIdx][leftIdx] >= currentHeight) {
        isLeftVisible = false;
        break;
      }
    }

    let isRightVisible = true;
    for (let rightIdx = gridArr[rowIdx].length - 1; rightIdx > columnIdx; rightIdx--) {
      if (gridArr[rowIdx][rightIdx] >= currentHeight) {
        isRightVisible = false;
        break;
      }
    }

    let isTopVisible = true;
    for (let topIdx = 0; topIdx < rowIdx; topIdx++) {
      if (gridArr[topIdx][columnIdx] >= currentHeight) {
        isTopVisible = false;
        break;
      }
    }

    let isBottomVisible = true;
    for (let bottomIdx = gridArr.length - 1; bottomIdx > rowIdx; bottomIdx--) {
      if (gridArr[bottomIdx][columnIdx] >= currentHeight) {
        isBottomVisible = false;
        break;
      }
    }

    return isLeftVisible || isRightVisible || isTopVisible || isBottomVisible;
  };

  let count = 0;

  for (let rowIdx = 0; rowIdx < gridArr.length; rowIdx++)
    for (let columnIdx = 0; columnIdx < gridArr[rowIdx].length; columnIdx++)
      count += Number(checkVisible(rowIdx, columnIdx));

  return count;
}

console.log(countVisible(input));
```