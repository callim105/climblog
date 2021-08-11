---
title: "Invert binary tree"
date: 2021-08-10T12:23:50-05:00
draft: false
tags: ['trees']
categories: ['Algo']
---
# Invert a Binary Tree
## Overview & Problem Space
Problem: Write a function that takes in a Binary Tree and inverts it. The function should swap every left node in the tree for its corresponding right node.
Each BinaryTree node has an integer value, a left child node and a right child node. Children nodes can either be BinaryTree nodes themselves or None/null.

## Theory
I like this problem because its a classic and the solution is actually really clean. So "inverting a binary tree" really means swap each child node. I think the recursive solution is the easiest to think about, so I'll go over that first, and then talk about the iterative solution.
Recursive: For the recursive solution, I like creating a helper method to handle the swaps. In the helper, first we check the base case if the node is none, and then we do the swaps. I love doing swaps in Python because they're so simple and don't require a temp variable:

After the swap, we call the function on the left child and the right child. Finally return the node! Then call this function in the original invertBinaryTree function and we're done.
Iterative: So this solution follows an approach similar to BFS, where we use a queue to hold the nodes we have to iterate through. We first create a queue with a single element, the root node. Then while the length of the queue is not zero, we pop off the first element of the queue, swap the left and right children, and then append that node's left and right child nodes

## Implementation

### Recursive
```python
# O(n) time | O(log(n)) space
def invertBinaryTree(tree):
    return invertBinaryTreeHelper(tree)

def invertBinaryTreeHelper(node):
		if node is None:
			return
		node.left, node.right = node.right, node.left
		invertBinaryTreeHelper(node.left)
		invertBinaryTreeHelper(node.right)
		return node

```

### Iterative
```python
# O(n) time | O(n) space
def invertBinaryTree(tree):
    queue = [tree]
	while len(queue):
		current = queue.pop(0)
		if current is None:
			continue
		swapLeftAndRight(current)
		queue.append(current.left)
		queue.append(current.right)
	return tree

def swapLeftAndRight(tree):
	tree.left, tree.right = tree.right, tree.left
```
