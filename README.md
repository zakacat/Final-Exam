# Final Exam (Chapters 6-11)

## Table of Contents 
- [Chapter 6 Stacks and Queues](#chapter-6)
- [Chapter 7 Hash Tables](#chapter-7)
- [Chapter 8 Trees](#chapter-8)
- [Chapter 9 Balanced Trees](#chapter-9)
- [Chapter 10 Heaps and Treaps](#chapter-10)
- [Chapter 11 Sets](#chapter-11)

## Chapter 6

### Code
<details>
<summary> Stacks and Queues </summary>

Stack
```java
class Stack {
   private LinkedList linkedList;
    
   Stack() {
      linkedList = new LinkedList();
   }
   
   public void push(int newData) {
      // Create a new node and prepend
      Node newNode = new Node(newData);
      linkedList.prepend(newNode);
   }
   
   public int pop() {
      // Copy list head's data
      int poppedItem = linkedList.getHeadData();
      
      // Remove list head
      linkedList.removeAfter(null);
      
      // Return popped item
      return poppedItem;
   }
   
   public void print() {
      linkedList.printList();
   }
}
```

Queue
```java
class Queue {
   private LinkedList linkedList;
    
   Queue() {
      linkedList = new LinkedList();
   }
   
   public void enqueue(int newData) {
      // Create a new node
      Node newNode = new Node(newData);
       
      // Insert as list tail (end of queue)
      linkedList.append(newNode);
   }
   
   public int dequeue() {
      // Copy list head's data
      int dequeuedItem = linkedList.getHeadData();
      
      // Remove list head
      linkedList.removeAfter(null);
      
      // Return dequeued item
      return dequeuedItem;
   }
   
   public void print() {
      linkedList.printList();
   }
}
```
Singly Linked List 
```java
class Node {
   public int data;
   public Node next;

   public Node(int initialData) {
      data = initialData;
      next = null;
   }
}

class LinkedList {
   private Node head;
   private Node tail;
    
   public LinkedList() {
      head = null;
      tail = null;
   }
    
   public void append(Node newNode) {
      if (head == null) {
         head = newNode;
         tail = newNode;
      }
      else {
         tail.next = newNode;
         tail = newNode;
      }
   }
   
   public void prepend(Node newNode) {
      if (head == null) {
         head = newNode;
         tail = newNode;
      }
      else {
         newNode.next = head;
         head = newNode;
      }
   }
   
   public void printList() {
      Node node = head;
      while (node != null) {
         System.out.print(node.data + " ");
         node = node.next;
      }
      System.out.println();
   }
   
   public void insertAfter(Node currentNode, Node newNode) {
      if (head == null) {
         head = newNode;
         tail = newNode;
      }
      else if (currentNode == tail) {
         tail.next = newNode;
         tail = newNode;
      }
      else {
         newNode.next = currentNode.next;
         currentNode.next = newNode;
      }
   }
   
   public void removeAfter(Node currentNode) {
      if (currentNode == null && head != null) {
         // Special case: remove head
         Node succeedingNode = head.next;
         head = succeedingNode;
         if (succeedingNode == null) {
             // Last item was removed
             tail = null;
         }
      }
      else if (currentNode.next != null) {
         Node succeedingNode = currentNode.next.next;
         currentNode.next = succeedingNode;
         if (succeedingNode == null) {
            // Remove tail
            tail = currentNode;
         }
      }
   }
   
   public void insertionSortSinglyLinked() {
      Node beforeCurrent = head;
      Node currentNode = head.next;
      while (currentNode != null) {
         Node nextNode = currentNode.next;
         Node position = findInsertionPosition(currentNode.data);
         if (position == beforeCurrent)
            beforeCurrent = currentNode;
         else {
            removeAfter(beforeCurrent);
            if (position == null)
               prepend(currentNode);
            else
               insertAfter(position, currentNode);
         }
         currentNode = nextNode;
      }
   }

   public Node findInsertionPosition(int dataValue) {
      Node positionA = null;
      Node positionB = head;
      while (positionB != null && dataValue > positionB.data) {
         positionA = positionB;
         positionB = positionB.next;
      }
      return positionA;
   }
   
   // Added for Stack/Queue section
   public int getHeadData() {
      return head.data;
   }
}
```
</details>



### Notes
Stack 
- last in, first out (think stack of pancakes, add the new one to the top and eat it first)
- push/pop happen at the same end
- Operations
   - **Push:** inserts item at top
   - **Pop:** returns and removes from top
   - **Peek:** returns without removing item at top
   - **IsEmpty:** returns true if it's empty
   - **GetLength:** returns number of items in stack
- all operations O(1)

Queue 
- first in, first out (think line up, first to get there gets help first)
- enqueue/dequeue happen at different ends 
- Operations
   - **Enqueue:** inserts item at end
   - **Dequeue:** returns and removes from front
   - **Peek:** returns without removing item at front 
   - **IsEmpty:** returns true if it's empty
   - **GetLength:** returns number of items in queue 
- all operations O(1)

## Chapter 7

### Code
<details>
<summary> Hash Tables </summary>

Hash Table
```java
// Abstract class for a hash table that supports the insert, remove, and search 
// operations.
public abstract class HashTable {
   // Returns a non-negative hash code for the specified key.
   protected int hash(Object key) {
      long keyHash = key.hashCode();
      
      // Java's hashCode() method may return a negative number
      if (keyHash < 0) {
         keyHash += 2147483648L;
      }
      
      return (int)keyHash;
   }
   
   // Inserts the specified key/value pair. If the key already exists, the 
   // corresponding value is updated. If inserted or updated, true is returned. 
   // If not inserted, then false is returned.
   public abstract boolean insert(Object key, Object value);
   
   // Searches for the specified key. If found, the key/value pair is removed 
   // from the hash table and true is returned. If not found, false is returned.
   public abstract boolean remove(Object key);
   
   // Searches for the key, returning the corresponding value if found, null if 
   // not found.
   public abstract Object search(Object key);
}

```
Chaining Hash Table Item
```java
class ChainingHashTableItem {
   public Object key;
   public Object value;
   public ChainingHashTableItem next;

   ChainingHashTableItem(Object itemKey, Object itemValue) {
      key = itemKey;
      value = itemValue;
      next = null;
   }
}

```
Chaining Hash Table
```java
public class ChainingHashTable extends HashTable {
   private ChainingHashTableItem[] table;

   public ChainingHashTable(int initialCapacity) {
      table = new ChainingHashTableItem[initialCapacity];
   }
   
   public ChainingHashTable() {
      // Initialize with an initial capacity of 11
      this(11);
   }

   // Inserts the specified key/value pair. If the key already exists, the 
   // corresponding value is updated. If inserted or updated, true is returned. 
   // If not inserted, then false is returned.
   @Override
   public boolean insert(Object key, Object value) {
      // Hash the key to get the bucket index
      int bucketIndex = hash(key) % table.length;
      
      // Traverse the linked list, searching for the key. If the key exists in 
      // an item, the value is replaced. Otherwise a new item is appended.
      ChainingHashTableItem item = table[bucketIndex];
      ChainingHashTableItem previous = null;
      while (item != null) {
         if (key.equals(item.key)) {
            item.value = value;
            return true;
         }
         
         previous = item;
         item = item.next;
      }
      
      // Append to the linked list
      if (table[bucketIndex] == null) {
         table[bucketIndex] = new ChainingHashTableItem(key, value);
      }
      else {
         previous.next = new ChainingHashTableItem(key, value);
      }
      return true;
   }

   // Searches for the specified key. If found, the key/value pair is removed 
   // from the hash table and true is returned. If not found, false is returned.
   @Override   
   public boolean remove(Object key) {
      // Hash the key to get the bucket index
      int bucketIndex = hash(key) % table.length;
      
      // Search the bucket's linked list for the key
      ChainingHashTableItem item = table[bucketIndex];
      ChainingHashTableItem previous = null;
      while (item != null) {
         if (key.equals(item.key)) {
            if (previous == null) {
               // Remove linked list's first item
               table[bucketIndex] = item.next;
            }
            else {
               previous.next = item.next;
            }
            return true;
         }
         
         previous = item;
         item = item.next;
      }
      
      return false; // key not found
   }
   
   // Searches for the key, returning the corresponding value if found, null if 
   // not found.
   @Override
   public Object search(Object key) {
      // Hash the key to get the bucket index
      int bucketIndex = hash(key) % table.length;
      
      // Search the bucket's linked list for the key
      ChainingHashTableItem item = table[bucketIndex];
      while (item != null) {
         if (key.equals(item.key)) {
            return item.value;
         }
         item = item.next;
      }
      
      return null; // key not found
   }
   
   @Override
   public String toString() {
      String result = "";
      for (int i = 0; i < table.length; i++) {
         result += (i + ": ");
         
         if (table[i] == null) {
            result += "(empty)\n";
         }
         else {
            ChainingHashTableItem item = table[i];
            while (item != null) {
               result += String.format("%s, %s --> ", item.key.toString(), 
                  item.value.toString());
               item = item.next;
            }
            result += "\n";
         }
      }
      return result;
   }
}

```

</details>

### Notes
Hash table is a data structure that stores items by mapping ot hashing items to a location in an array. Usually 
O(1) for searching or (inserting/removing)

Modulus Calc: use A(b/c) c must ne the same as divisor in question

Collision Strategies:
 - Chaining: 
   - each bucket has a list of items 
 - Open addressing: 
   - looks for an empty bucket elsewhere in the table
 - Linear Probing:  
   - starts at the key's mapped bucket, and then linearly searches subsequent buckets until an empty bucket is found.
 - Quadratic Probing:
   - starts at the key's mapped bucket, and then quadratically searches subsequent buckets until an empty bucket is found
   - (H + (c1) + (c2 • i^2)) mod(table size)
 - Double Hashing:
   - an open-addressing collision resolution technique that uses 2 different hash functions to compute bucket indices
   - (h1(key) + i • h2(key)) mod(table size)



## Chapter 8
### Code
<details>
<summary> Trees</summary>

Node
```java
class Node {
   public int key;
   public Node left;
   public Node right;

   public Node(int nodeKey) {
      key = nodeKey;
      left = null;
      right = null;
   }
}

```
Binary Search Tree
```java
class BinarySearchTree {
   private Node root;
   
   public BinarySearchTree() {
      root = null;
   }
   
   public Node getRoot() {
      return root;
   }
   
   public Node search(int desiredKey) {
      Node currentNode = root;
      while (currentNode != null) {
         // Return the node if the key matches
         if (currentNode.key == desiredKey) {
            return currentNode;
         }
         
         // Navigate to the left if the search key is 
         // less than the node's key.
         else if (desiredKey < currentNode.key) {
            currentNode = currentNode.left;
         }
         
         // Navigate to the right if the search key is 
         // greater than the node's key.
         else {
            currentNode = currentNode.right;
         }
      }
      
      // The key was not found in the tree.
      return null;
   }
   
   public void insert(Node node) {
      // Check if tree is empty
      if (root == null) {
         root = node;
      }
      else {
         Node currentNode = root;
         while (currentNode != null) {
            if (node.key < currentNode.key) {
               // If no left child exists, add the new node
               // here; otherwise repeat from the left child.
               if (currentNode.left == null) {
                  currentNode.left = node;
                  currentNode = null;
               }
               else {
                  currentNode = currentNode.left;
               }
            }
            else {
               // If no right child exists, add the new node
               // here; otherwise repeat from the right child.
               if (currentNode.right == null) {
                  currentNode.right = node;
                  currentNode = null;
               }
               else {
                  currentNode = currentNode.right;
               }
            }
         }
      }
   }
   
   public boolean remove(int key) {
      Node parent = null;
      Node currentNode = root;
      
      // Search for the node.
      while (currentNode != null) {
         // Check if currentNode has a matching key.
         if (currentNode.key == key) {
            if (currentNode.left == null && currentNode.right == null) {
               if (parent == null) { // Node is root
                  root = null;
               }
               else if (parent.left == currentNode) { 
                  parent.left = null;
               }
               else {
                  parent.right = null;
               }
               return true; // Node found and removed
            }
            else if (currentNode.left != null && currentNode.right == null) {
               if (parent == null) { // Node is root
                  root = currentNode.left;
               }
               else if (parent.left == currentNode) {
                  parent.left = currentNode.left;
               }
               else {
                  parent.right = currentNode.left;
               }
               return true; // Node found and removed
            }
            else if (currentNode.left == null && currentNode.right != null) {
               if (parent == null) { // Node is root
                  root = currentNode.right;
               }
               else if (parent.left == currentNode) {
                  parent.left = currentNode.right;
               }
               else {
                  parent.right = currentNode.right;
               }
               return true; // Node found and removed
            }
            else {
               // Find successor (leftmost child of right subtree)
               Node successor = currentNode.right;
               while (successor.left != null) {
                  successor = successor.left;
               }
               currentNode.key = successor.key; // Copy successor to current node
               parent = currentNode;
               currentNode = currentNode.right; // Remove successor from right subtree
               key = successor.key;             // Loop continues with new key
            }
         }
         else if (currentNode.key < key) { // Search right
            parent = currentNode;
            currentNode = currentNode.right;
         }
         else { // Search left
            parent = currentNode;
            currentNode = currentNode.left;
         }
      }
      return false; // Node not found
   }
}

```
BST Print
```java
class BSTPrint {
   public static String treeToString(Node subtreeRoot) {
      if (subtreeRoot == null) {
         return "(empty tree)";
      }
      
      // First convert the tree to an array of line strings
      String[] lines = treeToLines(subtreeRoot);
      
      // Combine all lines into 1 string
      String treeString = lines[0];
      for (int i = 1; i < lines.length; i++) {
         treeString += ("\n" + lines[i]);
      }
      return treeString;
   }
   
   private static String[] treeToLines(Node subtreeRoot) {
      if (subtreeRoot == null) {
         return new String[0];
      }
      
      // Make a string with the subtreeRoot's key enclosed in brackets
      String rootString = "[" + subtreeRoot.key + "]";
      int rootStrLen = rootString.length();
      
      // Case 1: subtreeRoot is a leaf
      if (subtreeRoot.left == null && subtreeRoot.right == null) {
         String[] oneLine = new String[1];
         oneLine[0] = rootString;
         return oneLine;
      }
      
      // Recursively make line strings for each child
      String[] leftLines = treeToLines(subtreeRoot.left);
      String[] rightLines = treeToLines(subtreeRoot.right);
      
      int lineCount = Math.max(leftLines.length, rightLines.length);
      String[] allLines = new String[lineCount + 2];
      
      // Case 2: subtreeRoot has no left child
      if (subtreeRoot.left == null) {
         // Create the first 2 lines, not yet indented
         allLines[0] = rootString;
         allLines[1] = getSpaces(rootStrLen) + "\\";
         
         // Find where the right child starts
         int rightChildIndent = rightLines[0].indexOf('[');
         
         // Goal: Indent lines appropriately so that the parent's right branch 
         // character ('\') matches up with the right child's '['.
         
         if (rightChildIndent <= rootStrLen) {
            // Indent all lines below
            indentLines(rightLines, rootStrLen - rightChildIndent);
         }
         else {
            // Indent first 2 lines
            String indent = getSpaces(rightChildIndent - rootStrLen);
            allLines[0] = indent + allLines[0];
            allLines[1] = indent + allLines[1];
         }
         
         // Copy rightLines into allLines starting at index 2
         System.arraycopy(rightLines, 0, allLines, 2, rightLines.length);
         
         return allLines;
      }
      
      // Case 3: subtreeRoot has no right child
      if (subtreeRoot.right == null) {
         // Goal: Indent lines appropriately so that the parent's left branch 
         // character ('/') matches up with the left child's ']'.
         
         // Create the first 2 lines
         String indent = getSpaces(leftLines[0].indexOf(']'));
         allLines[0] = indent + " " + rootString;
         allLines[1] = indent + "/";
         
         // Copy leftLines into allLines starting at index 2
         System.arraycopy(leftLines, 0, allLines, 2, leftLines.length);
         
         return allLines;
      }
      
      // Case 4: subtreeRoot has both a left and right child
      
      // The goal is to have the two child nodes as close to the parent as 
      // possible without overlap on any level.
      
      // Compute absolute indentation, in number of spaces, needed for right lines
      int indentNeeded = 0;
      if (rightLines.length > 0) {
         // Indent should at least get the immediate right child to be to the 
         // right of the root
         indentNeeded = Math.max(0, 
            leftLines[0].length() + rootString.length() - rightLines[0].indexOf('['));
      }
      for (int i = 0; i < leftLines.length && i < rightLines.length; i += 2) {
         // Lines with branches are skipped, so the line of interest has only 
         // nodes. The difference between where the left line ends and the 
         // right line begins should be at least 3 spaces for clarity.
         int leftEnd = leftLines[i].lastIndexOf(']');
         int rightBegin = rightLines[i].indexOf('[');
         
         int forThisLine = leftLines[i].length() + 3 - rightBegin;
         indentNeeded = Math.max(indentNeeded, forThisLine);
      }
      
      // Build final lines in allLines starting at index 2
      String absoluteIndent = getSpaces(indentNeeded);
      for (int i = 0; i < leftLines.length || i < rightLines.length; i++) {
         // If no right line, just take the left
         if (i >= rightLines.length) {
            allLines[2 + i] = leftLines[i];
         }
         else {
            String left = "";
            if (i < leftLines.length) {
               left = leftLines[i];
            }
            String right = absoluteIndent + rightLines[i];
            allLines[2 + i] = left + right.substring(left.length());
         }
      }
      
      // The first 2 lines remain. allLines[2] has the proper string for the 
      // 2 child nodes, and thus can be used to create branches in allLines[1].
      int leftIndex = allLines[2].indexOf(']');
      int rightIndex = allLines[2].lastIndexOf('[');
      allLines[1] = getSpaces(leftIndex) + "/" + 
         getSpaces(rightIndex - leftIndex - 1) + "\\";
      
      // The space between leftIndex and rightIndex is the space that 
      // subtreeRoot's string should occupy. If rootString is too short, put 
      // underscores on the sides.
      rootStrLen = rightIndex - leftIndex - 1;
      if (rootString.length() < rootStrLen) {
         int difference = rootStrLen - rootString.length();
         String underscores = getRepeated('_', difference / 2);
         if (difference % 2 == 0) {
            rootString = underscores + rootString + underscores;
         }
         else {
            rootString = underscores + rootString + underscores + "_";
         }
      }
      allLines[0] = getSpaces(leftIndex + 1) + rootString;
      
      return allLines;
   }
   
   private static String getRepeated(char toRepeat, int numberOfTimes) {
      if (numberOfTimes <= 0) {
         return "";
      }

      char[] chars = new char[numberOfTimes];
      for (int i = 0; i < numberOfTimes; i++) {
         chars[i] = toRepeat;
      }
      return new String(chars);
   }
   
   private static String getSpaces(int numberOfSpaces) {
      return getRepeated(' ', numberOfSpaces);
   }
   
   private static void indentLines(String[] lines, int numberOfSpaces) {
      if (numberOfSpaces <= 0) {
         return;
      }
       
      // Prepend indentation to each line
      String indent = getSpaces(numberOfSpaces);
      for (int i = 0; i < lines.length; i++) {
         lines[i] = indent + lines[i];
      }
   }
}

```
</details>

### Notes
BST Trees
 - **Successor:** The node that comes after in the BST ordering
 - **Predecessor:** The node that comes before in the BST ordering
 - Search worst case: [Log2N] + 1
 - Search perfect case: [Log2N]
 - Calc (ln x)/(ln 2)

In a binary tree each node has up to two children (left child and right child) other definitions:
 - **Leaf:** A tree node with no children
 - **Internal Node:** A node with at least one child.
 - **Parent:** A node with a child is said to be that child's parent. 
 - **Ancestors:** include the node's parent, the parent's parent, etc., up to the tree's root.
 - **Root:** The one tree node with no parent (the "top" node)
 - **Edge:** The link from a node to a child
 - **Depth:** The number of edges on the path from the root to the node. The root node thus has depth 0.
 - **Level:** All nodes with the same depth
 - **Height:** The largest depth of any node. A tree with just one node has height 0.

 Special types of Binary Trees
  - **Full:** Every node contains 0 or 2 children.
  - **Complete:** All levels, except possibly the last level, contain all possible nodes and all nodes in the last level are as far left as possible.
  - **Perfect:** All internal nodes have 2 children and all leaf nodes are at the same level

<details>
<summary> Traversal Methods</summary>


 - **preOrder:**
```java
public void preOrder(Node<T> node) {
      if (node == null) {
         return;
      } else {
         System.out.print(node.key + " ");
         preOrder(node.left);
         preOrder(node.right);

      }

   }
 ```
 - **postOrder:**
```java
  public void postOrder(Node<T> node) {
      if (node == null) {
         return;
      } else {
         postOrder(node.left);
         postOrder(node.right);
         System.out.print(node.key + " ");

      }

   }
 ```
 - **inOrder:**
```java
public void inOrder(Node<T> node) {
      if (node == null) {
         return;
      } else {
         inOrder(node.left);
         System.out.print(node.key + " ");
         inOrder(node.right);
      }
   }
```
 - **levelOrder:**
```java
 public void levelOrder(Node<T> node) {
      if (node == null) {
         return;
      }

      Queue<Node<T>> queue = new LinkedList<Node<T>>();
      queue.add(node);

      while (!queue.isEmpty()) {
         Node<T> tempNode = queue.poll();
         System.out.print(tempNode.key + " ");

         if (tempNode.left != null) {
            queue.add(tempNode.left);
         }

         if (tempNode.right != null) {
            queue.add(tempNode.right);
         }

      }

   }
```

</details>

## Chapter 9
### Code
<details>
<summary> Balanced Trees </summary>

Node
```java
class Node {
   public enum Child {
      LEFT, RIGHT
   }

   public int key;
   public Node parent;
   public Node left;
   public Node right;
   public int height;

   // Constructor with a key parameter creates the Node object.
   public Node(int nodeKey) {
      key = nodeKey;
      parent = null;
      left = null;
      right = null;
      height = 0;
   }
   
   // Calculate this nodes' balance factor, defined as 
   // height(left subtree) - height(right subtree)
   public int getBalance() {
      // Get current height of left subtree, or -1 if null
      int leftHeight = -1;
      if (left != null) {
         leftHeight = left.height;
      }
            
      // Get current height of right subtree, or -1 if null
      int rightHeight = -1;
      if (right != null) {
         rightHeight = right.height;
      }
            
      // Calculate the balance factor.
      return leftHeight - rightHeight;
   }
   
   // Recalculate the current height of the subtree rooted at
   // the node, usually called after a subtree has been 
   // modified.
   public void updateHeight() {
      // Get current height of left subtree, or -1 if null
      int leftHeight = -1;
      if (left != null) {
         leftHeight = left.height;
      }
            
      // Get current height of right subtree, or -1 if null
      int rightHeight = -1;
      if (right != null) {
         rightHeight = right.height;
      }

      // Assign height with calculated node height.
      height = Math.max(leftHeight, rightHeight) + 1;
   }
   
   // Assign either the left or right data member with a new
   // child. Returns true if the new child is successfully 
   // assigned to this node, false otherwise.
   public boolean setChild(Child whichChild, Node child) {
      // Assign the left or right data member.
      if (whichChild == Child.LEFT) {
         left = child;
      }
      else {
         right = child;
      }

      // Assign the parent data member of the new child,
      // if the child is not null.
      if (child != null) {
         child.parent = this;
      }

      // Update the node's height, since the subtree's structure
      // may have changed.
      updateHeight();
      return true;
   }
   
   // Replace a current child with a new child. Determines if
   // the current child is on the left or right, and calls
   // setChild() with the new node appropriately.
   // Returns true if the new child is assigned, false otherwise.
   public boolean replaceChild(Node currentChild, Node newChild) {
      if (left == currentChild) {
         return setChild(Child.LEFT, newChild);
      }
      else if (right == currentChild) {
         return setChild(Child.RIGHT, newChild);
      }
      
      // If neither of the above cases applied, then the new child
      // could not be attached to this node.
      return false;
   }
}
```
AVL Tree
```java
class AVLTree {
   private Node root;
   
   public AVLTree() {
      root = null;
   }
   
   public Node getRoot() {
      return root;
   }
   
   // Performs a left rotation at the given node. Returns the
   // subtree's new root.
   public Node rotateLeft(Node node) {
      // Define a convenience pointer to the right child of the 
      // left child.
      Node rightLeftChild = node.right.left;
        
      // Step 1 - the right child moves up to the node's position.
      // node is temporarily detached from the tree, but will be reattached
      // later.
      if (node.parent != null) {
         node.parent.replaceChild(node, node.right);
      }
      else { // node is root
         root = node.right;
         root.parent = null;
      }

      // Step 2 - the node becomes the left child of what used
      // to be node's right child, but is now node's parent. This will
      // detach rightLeftChild from the tree.
      node.right.setChild(Node.Child.LEFT, node);
        
      // Step 3 - reattach rightLeftChild as the right child of node.
      node.setChild(Node.Child.RIGHT, rightLeftChild);
        
      return node.parent;
   }
   
   // Performs a right rotation at the given node. Returns the
   // subtree's new root.
   public Node rotateRight(Node node) {
      // Define a convenience pointer to the left child of the 
      // right child.
      Node leftRightChild = node.left.right;
        
      // Step 1 - the left child moves up to the node's position.
      // node is temporarily detached from the tree, but will be reattached
      // later.
      if (node.parent != null) {
         node.parent.replaceChild(node, node.left);
      }
      else { // node is root
         root = node.left;
         root.parent = null;
      }

      // Step 2 - the node becomes the right child of what used
      // to be node's left child, but is now node's parent. This will
      // detach leftRightChild from the tree.
      node.left.setChild(Node.Child.RIGHT, node);
        
      // Step 3 - reattach leftRightChild as the right child of node.
      node.setChild(Node.Child.LEFT, leftRightChild);
        
      return node.parent;
   }
   
   // Updates the given node's height and rebalances the subtree if
   // the balancing factor is now -2 or +2. Rebalancing is done by
   // performing a rotation. Returns the subtree's new root if
   // a rotation occurred, or the node if no rebalancing was required.
   public Node rebalance(Node node) {
      // First update the height of this node.
      node.updateHeight();      

      // Check for an imbalance.
      if (node.getBalance() == -2) {
         // The subtree is too big to the right.
         if (node.right.getBalance() == 1) {
            // Double rotation case. First do a right rotation
            // on the right child.
            rotateRight(node.right);
         }

         // A left rotation will now make the subtree balanced.
         return rotateLeft(node);
      }
      else if (node.getBalance() == 2) {
         // The subtree is too big to the left
         if (node.left.getBalance() == -1) {
            // Double rotation case. First do a left rotation
            // on the left child.
            rotateLeft(node.left);
         }

         // A right rotation will now make the subtree balanced.
         return rotateRight(node);
      }
            
      // No imbalance, so just return the original node.
      return node;
   }
   
   // Searches for a node with a matching key. Does a regular
   // binary search tree search operation. Returns the node with the
   // matching key, or null if no matching key exists in the tree.
   public Node search(int desiredKey) {
      Node currentNode = root;
      while (currentNode != null) {
         // Return the node if the key matches
         if (currentNode.key == desiredKey) {
            return currentNode;
         }
         
         // Navigate to the left if the search key is 
         // less than the node's key.
         else if (desiredKey < currentNode.key) {
            currentNode = currentNode.left;
         }
         
         // Navigate to the right if the search key is 
         // greater than the node's key.
         else {
            currentNode = currentNode.right;
         }
      }
      
      // The key was not found in the tree.
      return null;
   }
   
   public void insert(Node node) {
      // Check if tree is empty
      if (root == null) {
         root = node;
      }
      else {
         // Step 1 - do a regular binary search tree insert.
         Node currentNode = root;
         while (currentNode != null) {
            // Choose to go left or right
            if (node.key < currentNode.key) {
               // Go left. If left child is null, insert the new
               // node here.
               if (currentNode.left == null) {
                  currentNode.left = node;
                  node.parent = currentNode;
                  currentNode = null;
               }
               else {
                  // Go left and do the loop again.
                  currentNode = currentNode.left;
               }
            }
            else {
               // Go right. If the right child is null, insert the
               // new node here.
               if (currentNode.right == null) {
                  currentNode.right = node;
                  node.parent = currentNode;
                  currentNode = null;
               }
               else {
                  // Go right and do the loop again.
                  currentNode = currentNode.right;
               }
            }
         }

         // Step 2 - Rebalance along a path from the new node's parent up
         // to the root.
         node = node.parent;
         while (node != null) {
            rebalance(node);
            node = node.parent;
         }
      }
   }
   
   // Attempts to remove a node with a matching key. If no node has a matching key
   // then nothing is done and false is returned; otherwise the node is removed and
   // true is returned.
   public boolean removeKey(int key) {
      Node node = search(key);
      if (node == null) {
         return false;
      }
      else {
         return removeNode(node);
      }
   }
   
   private boolean removeNode(Node nodeToRemove) {
      // # Base case: 
      if (nodeToRemove == null) {
         return false;
      }
        
      // Parent needed for rebalancing.
      Node parent = nodeToRemove.parent;
        
      // Case 1: Internal node with 2 children
      if (nodeToRemove.left != null && nodeToRemove.right != null) {
         // Find successor
         Node successorNode = nodeToRemove.right;
         while (successorNode.left != null) {
            successorNode = successorNode.left;
         }
            
         // Copy the value from the node
         nodeToRemove.key = successorNode.key;
            
         // Recursively remove successor
         removeNode(successorNode);
            
         // Nothing left to do since the recursive call will have rebalanced
         return true;
      }
    
      // Case 2: Root node (with 1 or 0 children)
      else if (nodeToRemove == root) {
         if (nodeToRemove.left != null) {
            root = nodeToRemove.left;
         }
         else {
            root = nodeToRemove.right;
         }

         if (root != null) {
            root.parent = null;
         }

         return true;
      }
      
      // Case 3: Internal with left child only
      else if (nodeToRemove.left != null) {
         parent.replaceChild(nodeToRemove, nodeToRemove.left);
      }
        
      // Case 4: Internal with right child only OR leaf
      else {
         parent.replaceChild(nodeToRemove, nodeToRemove.right);
      }
        
      // nodeToRemove is gone. Anything that was below nodeToRemove that has persisted 
      // is already correctly balanced, but ancestors of nodeToRemove may need rebalancing.
      nodeToRemove = parent;
      while (nodeToRemove != null) {
         rebalance(nodeToRemove);         
         nodeToRemove = nodeToRemove.parent;
      }
    
      return true;
   }
}

```
BST Print
```java
class BSTPrint {
   public static String treeToString(Node subtreeRoot) {
      if (subtreeRoot == null) {
         return "(empty tree)";
      }
      
      // First convert the tree to an array of line strings
      String[] lines = treeToLines(subtreeRoot);
      
      // Combine all lines into 1 string
      String treeString = lines[0];
      for (int i = 1; i < lines.length; i++) {
         treeString += ("\n" + lines[i]);
      }
      return treeString;
      //return String.join("\n", lines);
   }
   
   private static String[] treeToLines(Node subtreeRoot) {
      if (subtreeRoot == null) {
         return new String[0];
      }
      
      // Make a string with the subtreeRoot's key enclosed in brackets
      String rootString = "[" + subtreeRoot.key + "]";
      int rootStrLen = rootString.length();
      
      // Case 1: subtreeRoot is a leaf
      if (subtreeRoot.left == null && subtreeRoot.right == null) {
         String[] oneLine = new String[1];
         oneLine[0] = rootString;
         return oneLine;
      }
      
      // Recursively make line strings for each child
      String[] leftLines = treeToLines(subtreeRoot.left);
      String[] rightLines = treeToLines(subtreeRoot.right);
      
      int lineCount = Math.max(leftLines.length, rightLines.length);
      String[] allLines = new String[lineCount + 2];
      
      // Case 2: subtreeRoot has no left child
      if (subtreeRoot.left == null) {
         // Create the first 2 lines, not yet indented
         allLines[0] = rootString;
         allLines[1] = getSpaces(rootStrLen) + "\\";
         
         // Find where the right child starts
         int rightChildIndent = rightLines[0].indexOf('[');
         
         // Goal: Indent lines appropriately so that the parent's right branch 
         // character ('\') matches up with the right child's '['.
         
         if (rightChildIndent <= rootStrLen) {
            // Indent all lines below
            indentLines(rightLines, rootStrLen - rightChildIndent);
         }
         else {
            // Indent first 2 lines
            String indent = getSpaces(rightChildIndent - rootStrLen);
            allLines[0] = indent + allLines[0];
            allLines[1] = indent + allLines[1];
         }
         
         // Copy rightLines into allLines starting at index 2
         System.arraycopy(rightLines, 0, allLines, 2, rightLines.length);
         
         return allLines;
      }
      
      // Case 3: subtreeRoot has no right child
      if (subtreeRoot.right == null) {
         // Goal: Indent lines appropriately so that the parent's left branch 
         // character ('/') matches up with the left child's ']'.
         
         // Create the first 2 lines
         String indent = getSpaces(leftLines[0].indexOf(']'));
         allLines[0] = indent + " " + rootString;
         allLines[1] = indent + "/";
         
         // Copy leftLines into allLines starting at index 2
         System.arraycopy(leftLines, 0, allLines, 2, leftLines.length);
         
         return allLines;
      }
      
      // Case 4: subtreeRoot has both a left and right child
      
      // The goal is to have the two child nodes as close to the parent as 
      // possible without overlap on any level.
      
      // Compute absolute indentation, in number of spaces, needed for right lines
      int indentNeeded = 0;
      if (rightLines.length > 0) {
         // Indent should at least get the immediate right child to be to the 
         // right of the root
         indentNeeded = Math.max(0, 
            leftLines[0].length() + rootString.length() - rightLines[0].indexOf('['));
      }
      for (int i = 0; i < leftLines.length && i < rightLines.length; i += 2) {
         // Lines with branches are skipped, so the line of interest has only 
         // nodes. The difference between where the left line ends and the 
         // right line begins should be at least 3 spaces for clarity.
         int leftEnd = leftLines[i].lastIndexOf(']');
         int rightBegin = rightLines[i].indexOf('[');
         
         int forThisLine = leftLines[i].length() + 3 - rightBegin;
         indentNeeded = Math.max(indentNeeded, forThisLine);
      }
      
      // Build final lines in allLines starting at index 2
      String absoluteIndent = getSpaces(indentNeeded);
      for (int i = 0; i < leftLines.length || i < rightLines.length; i++) {
         // If no right line, just take the left
         if (i >= rightLines.length) {
            allLines[2 + i] = leftLines[i];
         }
         else {
            String left = "";
            if (i < leftLines.length) {
               left = leftLines[i];
            }
            String right = absoluteIndent + rightLines[i];
            allLines[2 + i] = left + right.substring(left.length());
         }
      }
      
      // The first 2 lines remain. allLines[2] has the proper string for the 
      // 2 child nodes, and thus can be used to create branches in allLines[1].
      int leftIndex = allLines[2].indexOf(']');
      int rightIndex = allLines[2].lastIndexOf('[');
      allLines[1] = getSpaces(leftIndex) + "/" + 
         getSpaces(rightIndex - leftIndex - 1) + "\\";
      
      // The space between leftIndex and rightIndex is the space that 
      // subtreeRoot's string should occupy. If rootString is too short, put 
      // underscores on the sides.
      rootStrLen = rightIndex - leftIndex - 1;
      if (rootString.length() < rootStrLen) {
         int difference = rootStrLen - rootString.length();
         String underscores = getRepeated('_', difference / 2);
         if (difference % 2 == 0) {
            rootString = underscores + rootString + underscores;
         }
         else {
            rootString = underscores + rootString + underscores + "_";
         }
      }
      allLines[0] = getSpaces(leftIndex + 1) + rootString;
      
      return allLines;
   }
   
   private static String getRepeated(char toRepeat, int numberOfTimes) {
      if (numberOfTimes <= 0) {
         return "";
      }

      char[] chars = new char[numberOfTimes];
      for (int i = 0; i < numberOfTimes; i++) {
         chars[i] = toRepeat;
      }
      return new String(chars);
   }
   
   private static String getSpaces(int numberOfSpaces) {
      return getRepeated(' ', numberOfSpaces);
   }
   
   private static void indentLines(String[] lines, int numberOfSpaces) {
      if (numberOfSpaces <= 0) {
         return;
      }
       
      // Prepend indentation to each line
      String indent = getSpaces(numberOfSpaces);
      for (int i = 0; i < lines.length; i++) {
         lines[i] = indent + lines[i];
      }
   }
}

```
</details>

### Notes
AVL tree is a BST with a height balance property and specific operations to rebalance the tree when a node is inserted or removed.

**Height Balance**  if for any node, the heights of the node's left and right subtrees differ by only 0 or 1.
**Balance Factor** is the left subtree height minus the right subtree height, which is 1, 0, or -1 in an AVL tree.

Rotation Types:
 - Left 
 - Right
 - Left-Right
 - Right-Left

Worst Case Runtime:
  - Insertion: O(LogN)
  - Removal: O(LogN)

## Chapter 10
### Code
<details>
<summary> Heaps and Treaps </summary>

Max Heap
```java
class MaxHeap {
   private int[] heapArray;
   private int heapSize;

   public MaxHeap() {
      heapArray = new int[2];
      heapSize = 0;
   }
   
   private void resizeArray() {
      int newLength = heapArray.length * 2;
      int[] newArray = new int[newLength];
      if (newArray != null) {
         // Copy from existing array to new array
         for (int i = 0; i < heapArray.length; i++) {
            newArray[i] = heapArray[i];
         }
         
         // Set the reference to the new array
         heapArray = newArray;
      }
   }
   
   private void percolateUp(int nodeIndex) {
      while (nodeIndex > 0) {
         // Compute the parent node's index
         int parentIndex = (nodeIndex - 1) / 2;
            
         // Check for a violation of the max heap property
         if (heapArray[nodeIndex] <= heapArray[parentIndex]) {
            // No violation, so percolate up is done.
            return;
         }
         else {
            // Swap heapArray[nodeIndex] and heapArray[parentIndex]
            System.out.printf("   percolateUp() swap: %d <-> %d\n",
               heapArray[parentIndex], heapArray[nodeIndex]);
            int temp = heapArray[nodeIndex];
            heapArray[nodeIndex] = heapArray[parentIndex];
            heapArray[parentIndex] = temp;
                
            // Continue the loop from the parent node
            nodeIndex = parentIndex;
         }
      }
   }
   
   private void percolateDown(int nodeIndex) {
      int childIndex = 2 * nodeIndex + 1;
      int value = heapArray[nodeIndex];

      while (childIndex < heapSize) {
         // Find the max among the node and all the node's children
         int maxValue = value;
         int maxIndex = -1;
         int i = 0;
         while (i < 2 && i + childIndex < heapSize) {
            if (heapArray[i + childIndex] > maxValue) {
               maxValue = heapArray[i + childIndex];
               maxIndex = i + childIndex;
            }
            i++;
         }

         // Check for a violation of the max heap property
         if (maxValue == value) {
            return;
         }
         else {
            // Swap heapArray[nodeIndex] and heapArray[maxIndex]
            System.out.printf("   percolateDown() swap: %d <-> %d\n",
               heapArray[nodeIndex], heapArray[maxIndex]);
            int temp = heapArray[nodeIndex];
            heapArray[nodeIndex] = heapArray[maxIndex];
            heapArray[maxIndex] = temp;
                
            // Continue loop from the max index node
            nodeIndex = maxIndex;
            childIndex = 2 * nodeIndex + 1;
         }
      }
   }
   
   public void insert(int value) {
      // Resize if needed
      if (heapSize == heapArray.length) {
         resizeArray();
      }
      
      // Add the new value to the end of the array
      System.out.printf("insert(%d):\n", value);
      heapArray[heapSize] = value;
      heapSize++;
        
      // Percolate up from the last index to restore heap property.
      percolateUp(heapSize - 1);
   }
   
   public int remove() {
      // Save the max value from the root of the heap.
      System.out.println("remove():");
      int maxValue = heapArray[0];
        
      // Move the last item in the array into index 0.
      int replaceValue = heapArray[heapSize - 1];
      heapSize--;
      if (heapSize > 0) {
         heapArray[0] = replaceValue;

         // Percolate down to restore max heap property.
         percolateDown(0);
      }
                
      // Return the max value
      return maxValue;
   }
   
   public String getHeapArrayString() {
      if (heapSize == 0) {
         return "[]";
      }
      
      String arrayString = String.format("[%d", heapArray[0]);
      for (int i = 1; i < heapSize; i++) {
         arrayString += (", " + heapArray[i]);
      }
      return arrayString + "]";
   }
   
   public int getHeapSize() {
      return heapSize;
   }
}

```
Heap Sort
```java
import java.util.Arrays;

public class HeapsortDemo {
   // Binary max heap percolate down
   static void maxHeapPercolateDown(int nodeIndex, int[] heapArray, int heapSize) {
      int childIndex = 2 * nodeIndex + 1;
      int value = heapArray[nodeIndex];
      
      while (childIndex < heapSize) {
         // Find the max among the node and all the node's children
         int maxValue = value;
         int maxIndex = -1;
         int i = 0;
         while (i < 2 && i + childIndex < heapSize) {
            if (heapArray[i + childIndex] > maxValue) {
               maxValue = heapArray[i + childIndex];
               maxIndex = i + childIndex;
            }
            i++;
         }
         
         if (maxValue == value) {
            return;
         }
         
         // Swap heapArray[nodeIndex] and heapArray[maxIndex]
         int temp = heapArray[nodeIndex];
         heapArray[nodeIndex] = heapArray[maxIndex];
         heapArray[maxIndex] = temp;
         
         nodeIndex = maxIndex;
         childIndex = 2 * nodeIndex + 1;
      }
   }
   
   // Sorts the array of numbers using the heap sort algorithm
   static void heapSort(int[] numbers) {
      // Heapify numbers array
      int i = numbers.length / 2 - 1;
      while (i >= 0) {
         maxHeapPercolateDown(i, numbers, numbers.length);
         i--;
      }
                
      i = numbers.length - 1;
      while (i > 0) {
         // Swap numbers[0] and numbers[i]
         int temp = numbers[0];
         numbers[0] = numbers[i];
         numbers[i] = temp;

         maxHeapPercolateDown(0, numbers, i);
         i--;
      }
   }
```
</details>

### Notes
Heaps are a tree based structure that can be two types:
 - **Max Heap:** A complete binary tree that maintains the simple property that a node's key is greater than or equal to the node's children's keys (Largest on top)
   - insert
   - remove 
   - percolating: upward movement 
 - **Min Heap:** A node's key is less than or equal to its children's keys. (Smallest on top)
## Chapter 11
### Code
<details>
<summary> Sets </summary>

BST Node
```java
public class BSTNode {
   public int data;
   public BSTNode parent;
   public BSTNode left;
   public BSTNode right;
   
   public BSTNode(int data, BSTNode parent, BSTNode left, BSTNode right) {
      this.data = data;
      this.parent = parent;
      this.left = left;
      this.right = right;
   }
   
   public BSTNode(int data, BSTNode parent) {
      this(data, parent, null, null);
   }
   
   public int count() {
      int leftCount = 0;
      int rightCount = 0;
      if (left != null) {
         leftCount = left.count();
      }
      if (right != null) {
         rightCount = right.count();
      }
      return 1 + leftCount + rightCount;
   }
   
   public BSTNode getSuccessor() {
      // Successor resides in right subtree, if present
      if (right != null) {
         BSTNode successor = right;
         while (successor.left != null) {
            successor = successor.left;
         }
         return successor;
      }
      
      // Otherwise the successor is up the tree
      // Traverse up the tree until a parent is encountered from the left
      BSTNode node = this;
      while (node.parent != null && node == node.parent.right) {
         node = node.parent;
      }
      return node.parent;
   }
   
   public void replaceChild(BSTNode currentChild, BSTNode newChild) {
      if (currentChild == left) {
         left = newChild;
         if (left != null) {
            left.parent = this;
         }
      }
      else if (currentChild == right) {
         right = newChild;
         if (right != null) {
            right.parent = this;
         }
      }
   }
}
```
Set
```java
import java.lang.Iterable;
import java.util.Iterator;
import java.util.function.Function;
import java.util.function.Predicate;

class BSTIterator implements Iterator<Integer> {
   private BSTNode nextNode;
   
   public BSTIterator(BSTNode startNode) {
      nextNode = startNode;
   }
   
   public boolean hasNext() {
      return nextNode != null;
   }
   
   public Integer next() {
      Integer returnMe = null;
      if (nextNode != null) {
         returnMe = nextNode.data;
         nextNode = nextNode.getSuccessor();
      }
      return returnMe;
   }
}

public class Set implements Iterable<Integer> {
   private BSTNode root;
   
   public Set() {
      root = null;
   }
   
   public boolean add(Integer newElement) {
      if (contains(newElement)) {
         return false;
      }
      
      BSTNode newNode = new BSTNode(newElement, null);
      
      // Special case for empty set
      if (root == null) {
         root = newNode;
         return true;
      }
      
      BSTNode node = root;
      while (node != null) {
         if (newElement < node.data) {
            // Go left
            if (node.left != null) {
               node = node.left;
            }
            else {
               node.left = newNode;
               newNode.parent = node;
               node = null;
            }
         }
         else {
            // Go right
            if (node.right != null) {
               node = node.right;
            }
            else {
               node.right = newNode;
               newNode.parent = node;
               node = null;
            }
         }
      }
      return true;
   }
   
   public boolean contains(Integer element) {
      return nodeSearch(element) != null;
   }
   
   public Set difference(Set otherSet) {
      Set result = new Set();
      for (Integer element : this) {
         if (!otherSet.contains(element)) {
            result.add(element);
         }
      }
      return result;
   }
   
   public Set filter(Predicate<Integer> predicate) {
      Set result = new Set();
      for (Integer element : this) {
         if (predicate.test(element)) {
            result.add(element);
         }
      }
      return result;
   }
   
   public Set intersection(Set otherSet) {
      Set result = new Set();
      for (Integer element : this) {
         if (otherSet.contains(element)) {
            result.add(element);
         }
      }
      return result;
   }
   
   public Iterator<Integer> iterator() {
      // Special case for empty set
      if (root == null) {
         return new BSTIterator(null);
      }
      
      // Start the iterator at the minimum node
      BSTNode minNode = root;
      while (minNode.left != null) {
         minNode = minNode.left;
      }
      return new BSTIterator(minNode);
   }
   
   public Set map(Function<Integer, Integer> mapFunction) {
      Set result = new Set();
      for (Integer element : this) {
         Integer newElement = mapFunction.apply(element);
         result.add(newElement);
      }
      return result;
   }
   
   private BSTNode nodeSearch(Integer element) {
      // Search the BST
      BSTNode node = root;
      while (node != null) {
         // Compare node's data against the search element
         if (element.equals(node.data)) {
            return node;
         }
         else if (element > node.data) {
            node = node.right;
         }
         else {
            node = node.left;
         }
      }
      return node;
   }
   
   public void remove(Integer element) {
      removeNode(nodeSearch(element));
   }
   
   private void removeNode(BSTNode nodeToRemove) {
      if (nodeToRemove == null) {
          return;
      }
      
      // Case 1: Internal node with 2 children
      if (nodeToRemove.left != null && nodeToRemove.right != null) {
         BSTNode successor = nodeToRemove.getSuccessor();

         // Copy the data value from the successor
         int dataCopy = successor.data;

         // Remove successor
         removeNode(successor);

         // Replace nodeToRemove's data with successor data
         nodeToRemove.data = dataCopy;
      }

      // Case 2: Root node (with 1 or 0 children)
      else if (nodeToRemove == root) {
         if (nodeToRemove.left != null) {
            root = nodeToRemove.left;
         }
         else {
            root = nodeToRemove.right;
         }

         if (root != null) {
            root.parent = null;
         }
      }

      // Case 3: Internal node with left child only
      else if (nodeToRemove.left != null) {
         nodeToRemove.parent.replaceChild(nodeToRemove, nodeToRemove.left);
      }

      // Case 4: Internal node with right child only, or leaf node
      else {
         nodeToRemove.parent.replaceChild(nodeToRemove, nodeToRemove.right);
      }
   }
   
   public int size() {
      if (root == null) {
         return 0;
      }
      return root.count();
   }
   
   public Set union(Set otherSet) {
      Set result = new Set();
      for (Integer element : this) {
         result.add(element);
      }
      for (Integer element : otherSet) {
         result.add(element);
      }
      return result;
   }
}
```
</details>

### Notes