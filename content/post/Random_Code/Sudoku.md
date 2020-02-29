---
title: Sudoku Solver in Python 
author: admin
date: '2020-02-28'
slug:  
categories: [Code]
tags:
  - Python
  - RandomCode
subtitle: ''
summary: 'How to solve Sudoku puzzles with Python'
authors: [M. L. DeBusk-Lane]
lastmod: '2020-02-28T07:57:09-05:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
output: 
  html_document:
    theme: journal
    highlight: zenburn
---

Interestingly I'm writing this blog post through JupyterLab, as opposed to strictly through R. Reason being, RStudio and the reticulate package have a bit of development left to go before knitr, rmarkdown, and RStudio work well together (you can reference this issue that I had a problem with [here](https://github.com/yihui/knitr/issues/1505). 
 
Nevertheless, I'm still hosting this markdown document from JupyterLab to markdown and then through my own Academic Hugo webpage through RStudio... its a party really. 

 
So, let's start. The point of this was to learn a bit more about how Python iterates and how something comparable can be handled in R. In doing so, there are some particulars that I'll touch on, but I would certainly say there is benefit of diving in yourself and writing it out. There is bound to be something you can learn by doing so. 
 
![sudoku](https://raw.githubusercontent.com/debusklaneml/mldebusklane.com/master/content/post/Random_Code/sudoku.png "sudoku")


The above puzzle can easily be objectified by addinging it into Python as a series of lists. 


```python
board = [[5,3,0,0,7,0,0,0,0],
        [6,0,0,1,9,5,0,0,0],
        [0,9,8,0,0,0,0,6,0],
        [8,0,0,0,6,0,0,0,3],
        [4,0,0,8,0,3,0,0,1],
        [7,0,0,0,2,0,0,0,6],
        [0,6,0,0,0,0,2,8,0],
        [0,0,0,4,1,9,0,0,5],
        [0,0,0,0,8,0,0,7,9]]
        
```

Although, it looks pretty worthless when printed. 


```python
print(board)
```

    [[5, 3, 0, 0, 7, 0, 0, 0, 0], [6, 0, 0, 1, 9, 5, 0, 0, 0], [0, 9, 8, 0, 0, 0, 0, 6, 0], [8, 0, 0, 0, 6, 0, 0, 0, 3], [4, 0, 0, 8, 0, 3, 0, 0, 1], [7, 0, 0, 0, 2, 0, 0, 0, 6], [0, 6, 0, 0, 0, 0, 2, 8, 0], [0, 0, 0, 4, 1, 9, 0, 0, 5], [0, 0, 0, 0, 8, 0, 0, 7, 9]]


Thankfully, `numpy` can translate it back into a neat matrix for our viewing pleasure. 


```python
import numpy as np
print(np.matrix(board))
```

    [[5 3 0 0 7 0 0 0 0]
     [6 0 0 1 9 5 0 0 0]
     [0 9 8 0 0 0 0 6 0]
     [8 0 0 0 6 0 0 0 3]
     [4 0 0 8 0 3 0 0 1]
     [7 0 0 0 2 0 0 0 6]
     [0 6 0 0 0 0 2 8 0]
     [0 0 0 4 1 9 0 0 5]
     [0 0 0 0 8 0 0 7 9]]


One thing to note, as compared to other languages, Python counts the first row and column as `0` not `1`. This can be confusing, but you'll see my use herein of 0-9 or 1-10 to tell Python to iterate through these series. I've also tried to mention this throughout the code comments within the chunks. 


```python
def possible(y, x, n) : 
    global board # Identify 'board' as a global variable
    # Check if the `n` number is at the y,i coordinate
    for i in range(0,9) :
        if board[y][i] == n :
            return False
    for i in range(0,9) : 
        if board[i][x] == n :
            return False
    x0 = (x//3)*3 #floor divisor - returns the whole number divisor
    y0 = (y//3)*3
    # For each cell in the given square, is x0 or y0 = n?
    for i in range(0,3) :
        for j in range(0,3) :
            if board[y0+i][x0+j] == n :
                return False
    return True
```

0 through 9 is used. row (y) and then column (x)

As an example, the top left position, x = 0 and y = 0, is 5.


```python
board[0][0]
```




    5



Next, you'll notice the `possible` function serves to identify values that are possible within each row, column, and 3 x 3 square. 

For instance, row 3 (the forth from the top) and column 1 (second from the left) is identified by the middle left most 3 x 3 square. 


```python
board[3][1]
```




    0




```python
possible(3,1,1)
```




    True



Here, a 1 is plausible. 

Next, we draft a `solve` solution to iterate through the `possible` options. 


```python
def solve() : 
    global board
    # Find a blank cell in the matrix
    for y in range(9) :
        for x in range(9) :
            if board[y][x] == 0 :
                for n in range(1,10) :
                    if possible(y,x,n) : 
                        board[y][x] = n
                        solve() # Recursion
                        # If it doesnt work, we make it zero again.
                        board[y][x] = 0
                return
    # No more zeroes, so we print the final matrix out. 
    print(np.matrix(board))
    # Sometimes there are alternative solutions...
    input("Do you want more solutions??")
```

Sometimes, as it may be, with some solutions, there are more than one possible answer to the question. So running it again may produce a slightly different answer. 


```python
solve()
```

    [[5 3 4 6 7 8 9 1 2]
     [6 7 2 1 9 5 3 4 8]
     [1 9 8 3 4 2 5 6 7]
     [8 5 9 7 6 1 4 2 3]
     [4 2 6 8 5 3 7 9 1]
     [7 1 3 9 2 4 8 5 6]
     [9 6 1 5 3 7 2 8 4]
     [2 8 7 4 1 9 6 3 5]
     [3 4 5 2 8 6 1 7 9]]


    Do you want more solutions?? 


So, ultimately, this post has shown how to use recursion (cycling a function over and over again by calling itself) to work through a multiple outcome problem. Although interesting, use of this procedure may be limited to instances where there are multiple options, but only one or two final solutions. Interesting none-the-less. 

As stated earlier, a similar solution is drafted in R [here](xxxx) (which is coming soon!!. 

Cheers!
