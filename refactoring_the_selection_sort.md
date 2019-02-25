The selection sort we wrote looks like this:

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

The code above is imperative, not declarative. The changes made to the array are explicit and not abstracted. In other words, it does not adhere to the priciples of funtional programming. Let's refactor the code using a funtional programming approach.

Our goal is to break our code into several functions that each have a unique operation.

We'll start by working on the innermost block of code (the code that is nested deepest), and work our way outwards.

The innermost block of code serves to reassign the value of `minIndex` if a new minimum is found:

```
    const nextIsSmaller = arr[minIndex] > arr[i+1];
    if (nextIsSmaller) {
      minIndex = i+1;
    }
```
Since these three lines of code serve a single purpose, we should extract them and create a new function to put them in:

```
const getMinIndex = (arr, minIndex, nextIndex) = {
  return arr[minIndex] > arr[nextIndex] ? nextIndex : minIndex;
}
```

```
arr.reduce((n, minIndex) => {

});
