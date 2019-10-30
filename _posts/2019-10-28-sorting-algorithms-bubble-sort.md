---
title: "Implementing bubble sort"
categories:
  - Sorting algorithms
tags:
  - sorting
  - performance
  - loops
toc: true
toc_sticky: true
---
Bubble sort is a simple sorting algorithm. Whilst not particularly efficient, it can be useful for small datasets, or datasets that are already reasonably sorted. The reason for this is that bubble sort takes multiple loops through the entire array to get to a fully sorted state, so the more that we can limit these iterations, the more effective it is.

The algorithm is based on comparison of pairs. It compares pairs of values and swaps them based on their rank.

# Step-by-step

Bubble sort begins by comparing the first two values in the unsorted array. In this case, 3 is greater than 2, so the values are swapped such that they are sorted.

![Comparing and swapping elements 1 and 2](/assets/post/bubblesort/algo_step1.png)

The algorithm then compares elements 2 and 3. In this case, 3 is less than 4, so the elements are already sorted and no action is carried out.

This is repeated for each pair across the array until we reach the end. At this point, the last element in the array is in its completely sorted position. We've moved the largest number to the end of the array.

![Completing the first iteration](/assets/post/bubblesort/algo_step2.png)

Then the algorithm starts again from the left, swapping pairs as applicable. At the end of this iteration, the last two elements are in their completely sorted positions.

![Second iteration](/assets/post/bubblesort/algo_step3.png)

This is repeated until the algorithm can move through the entire array without having to do any pair swapping.

![Third and final iteration](/assets/post/bubblesort/algo_step4.png)

# Implementation
Here was my first attempt at implementing bubble sort.

```matlab
function a = bubblesort(a)
%BUBBLESORT Sort using bubble sort algorithm
%
%   out = bubblesort(in) where in is an vector of numbers

% loop through the entire array multiple times
for ii = 1:numel(a) - 1
    swapped = false;
    
    % loop through each pair in the array
    for jj = 1:numel(a) - 1

    	% swap the pair if required
        if a(jj) > a(jj + 1)
            a(jj:jj + 1) = a(jj + 1:-1:jj);
            swapped = true;
        end % if
    end % for
    
    % if we didn't have to any swapping, then the array is sorted
    if ~swapped
        return
    end % if
end % for

end % bubblesort
```

While this does work, there are a couple improvements we can make. One of them is performance based. In this original implementation, I am ignoring the fact that for each full iteration through the array, we no longer need to check the last element. Hence, for each `ii`, we can check one less `jj`. For an array of 10,000 elements, this cut my sorting time down from around 120 seconds to 60 seconds.

```matlab
% loop through each pair in the array
for jj = 1:numel(a) - ii
```

The final thing to do was to tidy up the original loop. Rather than using a for loop which we break out of, this is the perfect use case for a while loop. We know that we need to continue to iterate through the array until a certain condition is met; in this case that no swaps were carried out.

```matlab
function a = bubblesort(a)
%BUBBLESORT Sort using bubble sort algorithm
%
%   out = bubblesort(in) where in is an vector of numbers

% initialise
swapped = true;
iter = 0;

% loop through the entire array until we haven't done any pair swapping
while swapped
    
    % reset and increment
    swapped = false;
    iter = iter + 1;
    
    % for each pair in the array that we can't guanrantee is in its correct
    % location, swap if out of order
    for ii = 1:numel(a) - iter
        if a(ii) > a(ii + 1)
            a(ii:ii + 1) = a(ii + 1:-1:ii);
            swapped = true;
        end % if
    end % for
end % for

end % bubblesort
```

**BONUS:** Due to how strings work in matlab, this function will also sort arrays of strings alphabetically! Try `bubblesort(["g" "c" "e" "b" "a" "d" "f"])`
{: .notice--info}