A selection sort algorithm follows these steps:

## Define the Algorithm

1. Find the position of the smallest integer.
2. Swap it with the integer located at the smallest integer's correct position.
3. Find the smallest integer that has not been sorted and swap it with the integer in its correct position.
4. Repeat step 3 until the list is sorted.

## Define the Steps

1. Assume that `arr` is equal to the input array and that `j=0`.
2. Assume that the next smallest integer that has not been sorted belongs in the position at `j`, and that `i=j` and `minIndex=j`.
3. Compare `arr[minIndex]` with `arr[i+1]` to see if `arr[minIndex]` > `arr[i+1]`.
4. If `arr[mindIndex]` > `arr[i+1]`, assume `arr[i+1]` is the smallest integer that has not been sorted and `minIndex=i+1`.
5. Increase `i` by 1.
6. Repeat steps 3, 4, and 5 until `i` is equal to the length of `arr` minus 1.
7. Swap `arr[minIndex]` with `arr[j]`.
8. Increase `j` by 1.
9. Repeat steps 6-9 until list is sorted.


## Translate into Code

1. Assume that `arr` is equal to the input array and that `j=0`.
```
const arr = input;
const j = 0;
```
2. Assume that the next smallest integer that has not been sorted belongs in the position at `j`, and that `i=j` and `minIndex=j`.
```
const i = j;
const minIndex = j;
```
3. Compare `arr[minIndex]` with `arr[i+1]` to see if `arr[minIndex]` > `arr[i+1]`.
```
const nextIsSmaller = arr[minIndex] > arr[i+1];
```
4. If `arr[mindIndex]` > `arr[i+1]`, assume `arr[i+1]` is the smallest integer that has not been sorted and `minIndex=i+1`.
```
if(nextIsSmaller) {
  minIndex = i+1;
}
```
5. Increase `i` by 1.
```
i++;
```
6. Repeat steps 3, 4, and 5 until `i` is equal to the length of `arr` minus 1.
```
for(let i=0; i<arr.length; i++) {
  const nextIsSmaller = arr[minIndex] > arr[i+1];
  if (nextIsSmaller) {
    minIndex = i+1;
  }
}
```
7. Swap `arr[minIndex]` with `arr[j]`.
```
temp = arr[j];
arr[j] = arr[minIndex];
arr[minIndex] = temp;
```
8. Increase `j` by 1.
```
j++
```
9. Repeat steps 6-9 until list is sorted.
```
const arr = input;

for(j=0; j<arr.length; j++) {
  minIndex=j;
  for(let i=j; i<arr.length; i++) {
    const nextIsSmaller = arr[minIndex] > arr[i+1];
    if (nextIsSmaller) {
      minIndex = i+1;
    }
  }

  temp = arr[j];
  arr[j] = arr[minIndex];
  arr[minIndex] = temp;
}
```
