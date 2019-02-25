Let's write an algorithm that performs the bubble sort on an array of integers.

## Describe the Algorithm

The first step to writing a good algorithm is to decide upon the series of steps that need to be performed to produce the desired result. The bubble sort algorithm we will write will sort an array of integers into ascending order. It will do this by comparing the first to values in the collection and swapping them if the first number is greater than the second. Then, the algorithm will compare the second number in the list with the third number, and swap them if the second is greater than the third. The algorithm will continue to move through the array comparing adjacent numbers and swapping them if necessary. When the end of the array is reached, the algorithm will return the beginning of the array and repeat the process. It will continue to cycle over the array until it has iterated over the entire array once without making any swaps, which will indicate that the array is sorted.

Let's see how this works by examining how an array changes after each comparison.

Original Array: `[ 7, 5, 9, 1, 4 ]`

1. `[ 7, 5, 9, 1, 4 ]`
      ^  ^
2. `[ 5, 7, 9, 1, 4 ]`
         ^  ^
3. `[ 5, 7, 9, 1, 4 ]`
            ^  ^
4. `[ 5, 7, 1, 9, 4 ]`
               ^  ^
5. `[ 5, 7, 1, 4, 9 ]`
      ^  ^
6. `[ 5, 7, 1, 4, 9 ]`
         ^  ^
7. `[ 5, 1, 7, 4, 9 ]`
            ^  ^
8. `[ 5, 1, 4, 7, 9 ]`
               ^  ^
9. `[ 5, 1, 4, 7, 9 ]`
      ^  ^
10. `[ 1, 5, 4, 7, 9 ]`
          ^  ^
11. `[ 1, 4, 5, 7, 9 ]`
             ^  ^
12. `[ 1, 4, 5, 7, 9 ]`
                ^  ^
13. `[ 1, 4, 5, 7, 9 ]`
       ^  ^
14. `[ 1, 4, 5, 7, 9 ]`
          ^  ^
15. `[ 1, 4, 5, 7, 9 ]`
             ^  ^
16. `[ 1, 4, 5, 7, 9 ]`
                ^  ^

## Define the Algorithm as Steps

Now that we understand how our algorithm is supposed to work, let's break down what it does into steps:

1. Compare the first number and the second number in the array.
2. Swap the first number with the second number if the first number is greater than the second number.
3. Repeat steps 1 and 2 on each successive pair of integers in the array.
4. Repeat step 1, 2, and 3 until no swaps are necessary.

These steps do an alright job describing what our algorithm is supposed to do, but we can make them clearer by using some common notation found in computer science and mathematics. Let's rewrite these steps using variables to refer to the integers in the array:

1. Assume that the array to be sorted is called `arr` and that `i=0`.
2. Compare `arr[i]` with `arr[i+1]` to see if `arr[i] > arr[i+1]`.
3. If `arr[i]` > `arr[i+1]`, swap the value of `arr[n]` with the value of `arr[n+1]`.
4. Increase `i` by 1.
5. Repeat steps 2, 3, and 4 until `i` is equal to the length of `arr` minus 1.
6. Repeat steps 2, 3, 4, and 5 until no swaps are made.

## Translate Each Step into Code

These steps are not only clearer but they also translate easier into code. Let's write each step as code now.

_1. Assume that the array to be sorted is called `arr` and that `i=0`._

The word `assume` suggests that we need to assign values. Let's declare two variables to satisfy the first step. The variable `inputArr` will represent the array entered by the user. Instead of altering the `inputArr` directly, let's make a copy of `inputArr` so that our function has no side effects.

```
const arr = [ ...inputArr ];
const i = 0;
```

_2. Compare `arr[i]` with `arr[i+1]` to see if `arr[i] > arr[i+1]`._

Let's create a variable to store the value (`true`/`false`) of this conditional.

```
const shouldBeSwapped = arr[i] > arr[i+1];
```

_3. If `arr[i]` > `arr[i+1]`, swap the value of `arr[n]` with the value of `arr[n+1]`._

We'll place the code for swapping values inside of an `if` block and use the conditional we declared in the previous step.

```
if(shouldBeSwapped) {
  const temp = arr[i];
  arr[i] = arr[i+1];
  arr[i+1] = temp;
}
```
_4. Increase `i` by 1._

```
i++;
```

_5. Repeat steps 2, 3, and 4 until `i` is equal to the length of `arr` minus 1._

Create a `for` loop to iterate over each element in the array. Increasing `i` by 1 (step 4) will be accomplished by the declaration for the `for` loop (`i++`) . The `for` loop also initializes `i`, so we no longer need this line of code:

```
const i = 0;
```

Here's what the code should look like now:

```
for(let i=0; i<arr.length-1; i++){
  const shouldBeSwapped = arr[i] > arr[i+1];
  if(shouldBeSwapped) {
    const temp = arr[i];
    arr[i] = arr[i+1];
    arr[i+1] = temp;
  }
}
```

_6. Repeat steps 2, 3, 4, and 5 until no swaps are made._

We'll declare a variable (`noSwaps`) to keep track of whether a swap has been made during a single pass (iteration) over the array. We set it equal to `true` before iterating over the array, and switch it to `false` if a swap is made. We use a `do while` loop to repeat the process as long as each time the loop iterates, `noSwaps` is switched to `false`. Using a boolean value in this way is a common technique for ending a `do while` or `while` loop.

The entire code block should look like this:

```
let noSwaps;
do{
  noSwaps = true;
  for(let i=0; i<arr.length-1; i++){
    const shouldBeSwapped = arr[i] > arr[i+1];
    if(shouldBeSwapped) {
      noSwaps = false;
      const temp = arr[i];
      arr[i] = arr[i+1];
      arr[i+1] = temp;
    }
  }
} while(noSwaps===false);
```


Let's put the code together and wrap it in a function:

```
const bubbleSort = (input) => {
  const arr = [ ...input ];

  let noSwaps;
  do{
    noSwaps = true;
    for(let i=0; i<arr.length-1; i++){
      let shouldBeSwapped = arr[i] > arr[i+1];
      if(shouldBeSwapped) {
        noSwaps = false;
        const temp = arr[i];
        arr[i] = arr[i+1];
        arr[i+1] = temp;
      }
    }
  } while(noSwaps === false);
  return arr;
}
```

Notice that we no longer have the line `const i = 0;` from step 1 because `i` declared by the `for` loop.


Plug this code into JSFiddle or the console to test it out. Add a log to see how the array changes after each comparison:

```
  ...
    noSwaps=true;
    for(let i = 0; i < arr.length-1; i++){
      console.log(`Array: ${arr}; i: ${i}`);
      const shouldBeSwapped = arr[i] > arr[i+1];
      if(shouldBeSwapped) {
        ...
      }
    }
  ...
```

Spend some time altering values and moving lines of code to make sure you have a full understanding of how this algorithm works. What happens when you move the line `noSwaps=true` inside of the `for` loop? What happens if you set `noSwaps` to `true` outside of the `do` loop? What happens if we use `arr.length` instead of `arr.length-1` as the condition for the `for` loop?
