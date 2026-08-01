# Java Revision — Intern DSA Prep

---

## Phase A — Language Basics

---

### 1. Data Types

Java has two categories:

**Primitive Types** — stored directly on the stack
```
byte    (1 byte)
short   (2 bytes)
int     (4 bytes)
long    (8 bytes)
float   (4 bytes)
double  (8 bytes)
char    (2 bytes)
boolean (1 bit)
```

**Reference Types** — variable stores the memory address of the object, not the object itself
```
String, Arrays, Objects, Collections (ArrayList, HashMap etc.)
```

---

### 2. Primitive vs Reference — The Most Important Distinction

```java
// Primitive — COPY is passed/assigned
int a = 5;
int b = a;   // b gets a copy of 5
b = 10;
System.out.println(a); // still 5 — a is untouched

// Reference — ADDRESS is passed/assigned, both point to SAME object
int[] arr1 = {1, 2, 3};
int[] arr2 = arr1;     // arr2 points to same memory location
arr2[0] = 99;
System.out.println(arr1[0]); // 99 — arr1 is affected!
```

**Mental model:**
- Primitive = giving someone a photocopy of a document
- Reference = giving someone the key to the cabinet where the document lives

---

### 3. Type Casting

```java
// Widening — automatic, no data loss
int x = 100;
long y = x;       // fine, int fits inside long
double d = x;     // fine

// Narrowing — manual, may lose data
double pi = 3.99;
int truncated = (int) pi;  // 3 — decimal CHOPPED, not rounded

// Gotcha — overflow
byte b = (byte) 130;  // byte max is 127, wraps around → -126
```

---

### 4. Pass by Value — Java is ALWAYS pass by value

```java
// Case 1 — primitive: method gets a copy
void change(int x) { x = 100; }
int a = 5;
change(a);
System.out.println(a); // still 5

// Case 2 — object: address is copied, both point to same object
void change(int[] arr) { arr[0] = 100; }
int[] nums = {1, 2, 3};
change(nums);
System.out.println(nums[0]); // 100 — object was modified through address

// Case 3 — reassigning inside method: only local copy changes
void change(int[] arr) { arr = new int[]{99, 99}; }
int[] nums = {1, 2, 3};
change(nums);
System.out.println(nums[0]); // still 1 — original reference unchanged
```

---

### 5. Labeled Break — useful in nested loops (DSA grids/matrices)

```java
outer:
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (j == 1) break outer;  // breaks BOTH loops
        System.out.println(i + " " + j);
    }
}
// Output: 0 0
```

---

## Phase B — OOP

---

### 1. Classes & Objects

```java
class Node {
    int data;
    Node next;  // reference to another Node (used in Linked Lists, Trees)

    Node(int data) {
        this.data = data;   // 'this' refers to current object
        this.next = null;
    }
}

Node n1 = new Node(5);
Node n2 = new Node(10);
n1.next = n2;

System.out.println(n1.data);       // 5
System.out.println(n1.next.data);  // 10
```

---

### 2. Inheritance

```java
class Animal {
    String name;
    Animal(String name) { this.name = name; }
    void speak() { System.out.println("..."); }
}

class Dog extends Animal {
    Dog(String name) {
        super(name);  // must be FIRST line — calls parent constructor
    }

    @Override          // optional but always write it — catches typos
    void speak() { System.out.println(name + " says Woof"); }
}

Dog d = new Dog("Bruno");
d.speak();  // Bruno says Woof
```

---

### 3. Polymorphism — runtime method resolution

```java
Animal a = new Dog("Bruno");  // Animal reference, Dog object
a.speak();  // "Bruno says Woof" — Dog's version runs, not Animal's
```

- **Reference type** (`Animal`) — determines what methods you can *call*
- **Object type** (`Dog`) — determines which *version* runs at runtime

This is why this works in DSA:
```java
List<Integer> list = new ArrayList<>();  // List reference, ArrayList object
```

---

### 4. Abstract Class vs Interface

| | Abstract Class | Interface |
|---|---|---|
| Constructor | Yes | No |
| Concrete methods | Yes | Yes (default, Java 8+) |
| Instance variables | Yes | No (only public static final) |
| A class can extend/implement | Only **one** | **Multiple** |
| Use when | Shared base with common code | Defining a contract/capability |

```java
// Abstract class — partial implementation
abstract class Shape {
    String color;
    abstract double area();       // subclass MUST implement
    void describe() {             // concrete — shared
        System.out.println("Color: " + color);
    }
}

// Interface — pure contract
interface Drawable {
    void draw();
}

class Circle extends Shape implements Drawable {
    double radius;
    Circle(double r) { this.radius = r; }

    @Override
    public double area() { return Math.PI * radius * radius; }

    @Override
    public void draw() { System.out.println("Drawing circle"); }
}
```

**Simple rule:**
- Abstract class = "is-a" with shared behavior
- Interface = defines what something *can do*, regardless of what it *is*

---

### 5. Encapsulation

Private fields + public getters/setters to control access and add validation.

```java
class BankAccount {
    private double balance;

    public double getBalance() { return balance; }

    public void deposit(double amount) {
        if (amount > 0) balance += amount;  // validation here
    }
}
```

---

### OOP Four Pillars (for theory interviews)
1. **Encapsulation** — hiding data, controlling access
2. **Inheritance** — child class reuses parent code
3. **Polymorphism** — same method name, different behavior
4. **Abstraction** — hiding implementation, showing only what's needed

---

## Phase C — Core Java (DSA Critical)

---

### 1. String

**Strings are immutable** — every operation creates a new String object.

```java
// String Pool
String s1 = "hello";
String s2 = "hello";
String s3 = new String("hello");

System.out.println(s1 == s2);      // true  — same object in pool
System.out.println(s1 == s3);      // false — new object on heap
System.out.println(s1.equals(s3)); // true  — content comparison

// RULE: Always use .equals() to compare strings, never ==
```

**Immutability in action:**
```java
String s = "hello";
s.toUpperCase();             // result ignored — s unchanged
s = s.toUpperCase();         // must capture the result
System.out.println(s);       // "HELLO"
```

**Most used String methods:**
```java
String s = "Hello World";

s.length()                        // 11
s.charAt(0)                       // 'H'
s.indexOf('o')                    // 4 (first occurrence)
s.lastIndexOf('o')                // 7
s.substring(6)                    // "World"
s.substring(0, 5)                 // "Hello" (end index exclusive)
s.toLowerCase()                   // "hello world"
s.toUpperCase()                   // "HELLO WORLD"
s.trim()                          // removes leading/trailing spaces
s.replace('l', 'r')               // "Herro Worrd"
s.contains("World")               // true
s.startsWith("Hello")             // true
s.endsWith("World")               // true
s.equals("Hello World")           // true
s.equalsIgnoreCase("hello world") // true
s.split(" ")                      // ["Hello", "World"]
s.toCharArray()                   // char array

String.valueOf(123)               // "123" — int to String
Integer.parseInt("123")           // 123  — String to int
```

---

### 2. StringBuilder

Mutable — modifies in place, no new objects. Use for string building in loops.

```java
// BAD — O(n²): each + creates a new String
String result = "";
for (int i = 0; i < n; i++) result += "a";

// GOOD — O(n): StringBuilder modifies in place
StringBuilder sb = new StringBuilder();
for (int i = 0; i < n; i++) sb.append("a");
String result = sb.toString();
```

**StringBuilder does NOT refer to the String Pool.** It maintains its own internal char array on the heap. When you pass a String to it, contents are copied — the two have zero connection after that.

**StringBuilder methods:**
```java
StringBuilder sb = new StringBuilder("hello");

sb.append(" world")       // "hello world"
sb.insert(5, "!")         // "hello! world"
sb.delete(5, 6)           // "hello world"
sb.deleteCharAt(0)        // "ello world"
sb.reverse()              // "dlrow olle"
sb.replace(0, 4, "hi")    // replace index 0–3
sb.charAt(0)              // 'h'
sb.setCharAt(0, 'H')      // modify in place
sb.length()               // length
sb.toString()             // convert back to String
```

**Common pattern — modify string via char array:**
```java
String s = "abcde";
char[] chars = s.toCharArray();
chars[0] = 'z';
String modified = new String(chars);  // "zbcde"
```

---

### 3. Arrays Utility Class

```java
import java.util.Arrays;

int[] arr = {5, 3, 1, 4, 2};

Arrays.sort(arr);                        // [1,2,3,4,5] — in place
Arrays.sort(arr, 1, 4);                  // sort only index 1 to 3
int idx = Arrays.binarySearch(arr, 3);   // array must be sorted first
Arrays.fill(arr, 0);                     // [0,0,0,0,0]
Arrays.fill(arr, 1, 4, 9);              // fill index 1–3 with 9
int[] copy = Arrays.copyOf(arr, 3);      // first 3 elements
int[] copy2 = Arrays.copyOfRange(arr, 1, 4); // index 1 to 3
Arrays.equals(arr, copy);               // element by element comparison
System.out.println(Arrays.toString(arr));       // [1, 2, 3, 4, 5]
System.out.println(Arrays.deepToString(matrix)); // [[1,2],[3,4]] for 2D

// Sort with Comparator — only works on object arrays (Integer[], not int[])
Integer[] arr2 = {5, 3, 1, 4, 2};
Arrays.sort(arr2, (a, b) -> b - a);  // descending

// Convert to List
List<Integer> list = Arrays.asList(arr2);  // fixed size
```

---

### 4. ArrayList

Dynamic array — grows automatically.

```java
ArrayList<Integer> list = new ArrayList<>();

list.add(10);                     // add at end
list.add(0, 99);                  // add at index 0, shifts others right
list.get(0);                      // 99
list.size();                      // size
list.set(0, 55);                  // replace index 0
list.remove(0);                   // remove by INDEX
list.remove(Integer.valueOf(10)); // remove by VALUE — important difference
list.contains(10);                // true/false
list.indexOf(10);                 // first occurrence, -1 if not found

Collections.sort(list);                    // ascending
Collections.sort(list, (a, b) -> b - a);  // descending
Collections.reverse(list);
Collections.max(list);
Collections.min(list);
Collections.frequency(list, 10);           // count of 10

Integer[] arr = list.toArray(new Integer[0]);  // to array
```

**ArrayList vs int[]:**
| | `int[]` | `ArrayList<Integer>` |
|---|---|---|
| Size | Fixed | Dynamic |
| Speed | Faster | Slightly slower |
| Methods | None | Many |
| DSA use | When size is known | When size varies |

---

### 5. HashMap

Key-value pairs. O(1) average for get/put. Most used DSA structure after arrays.

```java
HashMap<String, Integer> map = new HashMap<>();

map.put("apple", 3);
map.get("apple");               // 3
map.get("mango");               // null
map.getOrDefault("mango", 0);  // 0 — safe access with default
map.containsKey("apple");       // true
map.containsValue(3);           // true
map.size();
map.remove("apple");

// Frequency counter pattern — used in 50+ problems
HashMap<String, Integer> freq = new HashMap<>();
for (String word : words) {
    freq.put(word, freq.getOrDefault(word, 0) + 1);
}

// Iterating
for (String key : map.keySet()) { }
for (int val : map.values()) { }
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}
```

---

### 6. HashSet

Unique elements only. O(1) lookup. Used to track seen elements.

```java
HashSet<Integer> set = new HashSet<>();
set.add(1);
set.add(1);      // duplicate — silently ignored
set.size();      // 1
set.contains(1); // true — O(1)
set.remove(1);

// Detect duplicates
int[] arr = {1, 2, 3, 2, 4};
HashSet<Integer> seen = new HashSet<>();
for (int num : arr) {
    if (!seen.add(num)) {  // add() returns false if already exists
        System.out.println("Duplicate: " + num);
    }
}
```

---

### 7. Stack & Queue

```java
// Stack — LIFO. Use ArrayDeque in DSA, not Stack class
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1); stack.push(2); stack.push(3);
stack.peek();  // 3 — top, no removal
stack.pop();   // 3 — removes top

// Queue — FIFO. Use ArrayDeque in DSA, not LinkedList
Queue<Integer> queue = new ArrayDeque<>();
queue.offer(1); queue.offer(2);    // add to rear
queue.peek();   // 1 — front, no removal
queue.poll();   // 1 — removes front

// offer() over add() — offer returns false if full, add throws exception
```

---

### 8. ArrayDeque — use this instead of Stack and LinkedList

Faster than both Stack and LinkedList. Works as stack, queue, or deque.

```java
Deque<Integer> deque = new ArrayDeque<>();

// As Stack
deque.push(1);        // addFirst
deque.peek();         // peekFirst
deque.pop();          // pollFirst

// As Queue
deque.offerLast(1);   // add to rear
deque.peekFirst();    // look at front
deque.pollFirst();    // remove from front

// As Deque (both ends)
deque.offerFirst(0);  // add to front
deque.offerLast(3);   // add to rear
deque.pollFirst();    // remove from front
deque.pollLast();     // remove from rear
```

---

### 9. PriorityQueue (Heap)

Min element at top by default. O(log n) for offer/poll.

```java
// Min heap
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
minHeap.offer(5); minHeap.offer(1); minHeap.offer(3);
minHeap.peek();   // 1 — always minimum
minHeap.poll();   // 1 — removes minimum

// Max heap — reverse comparator
PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a);
maxHeap.offer(5); maxHeap.offer(1); maxHeap.offer(3);
maxHeap.peek();   // 5 — always maximum
```

---

### 10. TreeMap & TreeSet — sorted versions

O(log n) operations. Sorted by natural order or custom comparator.

```java
// TreeMap — sorted by key
TreeMap<Integer, String> tmap = new TreeMap<>();
tmap.put(3, "c"); tmap.put(1, "a"); tmap.put(2, "b");
// Internally: {1=a, 2=b, 3=c}

tmap.firstKey();        // 1
tmap.lastKey();         // 3
tmap.floorKey(2);       // 2 — largest key <= 2
tmap.ceilingKey(2);     // 2 — smallest key >= 2
tmap.lowerKey(2);       // 1 — largest key strictly < 2
tmap.higherKey(2);      // 3 — smallest key strictly > 2

// TreeSet — sorted unique elements
TreeSet<Integer> tset = new TreeSet<>();
tset.add(5); tset.add(1); tset.add(3);
// Internally: [1, 3, 5]

tset.first(); tset.last();
tset.floor(3); tset.ceiling(4);
tset.lower(3); tset.higher(3);
```

---

### Which Collection to Use — Quick Reference

| Situation | Use |
|---|---|
| Fast lookup by key | `HashMap` |
| Sorted keys | `TreeMap` |
| Insertion order preserved | `LinkedHashMap` |
| Unique elements, fast lookup | `HashSet` |
| Sorted unique elements | `TreeSet` |
| Dynamic array | `ArrayList` |
| Stack or Queue | `ArrayDeque` |
| Min/Max element fast | `PriorityQueue` |

---

### 11. Comparator & Comparable

**The problem:** Java can't sort custom objects without being told how.

---

#### Comparable — class defines its own natural ordering

```java
class Student implements Comparable<Student> {
    String name;
    int age;
    double gpa;

    Student(String name, int age, double gpa) {
        this.name = name; this.age = age; this.gpa = gpa;
    }

    @Override
    public int compareTo(Student other) {
        return this.age - other.age;  // sort by age ascending
    }
}

Collections.sort(students);  // uses compareTo automatically
```

**Return value rules:**
```
negative  →  this comes BEFORE other
zero      →  equal
positive  →  this comes AFTER other
```

---

#### Comparator — defined outside the class, more flexible

```java
// Lambda — what you'll use 95% of the time in DSA
Collections.sort(students, (a, b) -> a.age - b.age);             // age ascending
Collections.sort(students, (a, b) -> b.age - a.age);             // age descending
Collections.sort(students, (a, b) -> a.name.compareTo(b.name));  // name alphabetical
Collections.sort(students, (a, b) -> Double.compare(b.gpa, a.gpa)); // gpa descending
```

**Never subtract doubles — use Double.compare:**
```java
// BAD — floating point precision issues
return this.gpa - other.gpa;  // 3.9 - 3.8 might give 0.09999999964

// GOOD
return Double.compare(this.gpa, other.gpa);  // safe
```

---

#### Multi-field sorting

```java
// Sort by gpa descending, then name ascending if gpa is equal
Collections.sort(students, (a, b) -> {
    if (Double.compare(b.gpa, a.gpa) != 0)
        return Double.compare(b.gpa, a.gpa);
    return a.name.compareTo(b.name);
});
```

---

#### Arrays.sort with Comparator

```java
// Integer[] (object array) — works with Comparator
Integer[] arr = {5, 3, 1};
Arrays.sort(arr, (a, b) -> b - a);  // descending

// 2D array sorting — very common (intervals, meeting rooms)
int[][] intervals = {{3,6}, {1,4}, {2,5}};
Arrays.sort(intervals, (a, b) -> a[0] - b[0]);  // sort by first element
Arrays.sort(intervals, (a, b) -> a[1] - b[1]);  // sort by second element
```

---

#### PriorityQueue with Comparator

```java
PriorityQueue<Student> pq = new PriorityQueue<>((a, b) -> a.age - b.age);   // min age at top
PriorityQueue<Student> pq2 = new PriorityQueue<>((a, b) -> Double.compare(b.gpa, a.gpa)); // max gpa at top
```

---

#### Comparable vs Comparator

| | Comparable | Comparator |
|---|---|---|
| Defined | Inside the class | Outside the class |
| Method | `compareTo(T other)` | `compare(T a, T b)` |
| Sorting orders | One (natural) | Multiple possible |
| DSA use | Rarely | Almost always — lambda |

---

### 12. Autoboxing & Wrapper Classes

Collections can't store primitives — only objects. Wrapper classes bridge the gap.

```
int → Integer,  long → Long,  double → Double,
float → Float,  char → Character,  boolean → Boolean
```

```java
// Autoboxing — primitive to wrapper (automatic)
ArrayList<Integer> list = new ArrayList<>();
list.add(5);          // 5 autoboxed to Integer(5)

// Unboxing — wrapper to primitive (automatic)
int val = list.get(0); // Integer(5) unboxed to 5
```

**Interview trap — Integer caching:**
```java
Integer a = 127;
Integer b = 127;
System.out.println(a == b);   // true  — cached range: -128 to 127

Integer c = 128;
Integer d = 128;
System.out.println(c == d);   // false — new objects created beyond 127

// Fix: always use .equals() for wrapper comparison
System.out.println(c.equals(d));  // true — always correct
```

---

**Useful wrapper methods:**
```java
Integer.parseInt("123")         // String → int
Integer.valueOf("123")          // String → Integer
Integer.toString(123)           // int → String
Integer.MAX_VALUE               // 2147483647
Integer.MIN_VALUE               // -2147483648
Integer.toBinaryString(10)      // "1010"
Integer.bitCount(7)             // 3 — count of 1 bits

Double.parseDouble("3.14")      // String → double

Character.isLetter('a')         // true
Character.isDigit('5')          // true
Character.isUpperCase('A')      // true
Character.isLowerCase('a')      // true
Character.toUpperCase('a')      // 'A'
Character.toLowerCase('A')      // 'a'
Character.isWhitespace(' ')     // true
```

---

**Char arithmetic — used constantly in string problems:**
```java
char c = 'a';
int pos = c - 'a';   // 0 — position in alphabet (b=1, c=2 ... z=25)

char d = '7';
int num = d - '0';   // 7 — convert digit char to int

// Character frequency array — classic pattern
int[] freq = new int[26];
String s = "hello";
for (char ch : s.toCharArray()) {
    freq[ch - 'a']++;  // maps 'a'→0, 'b'→1 ... 'z'→25
}
```

---

## Phase D — Time & Space Complexity

---

### Time Complexity — reading it instantly

```java
// O(1) — no loops, direct access
int x = arr[0];
map.get(key);
set.contains(val);

// O(log n) — input halves each step
while (n > 0) { n = n / 2; }  // binary search, TreeMap ops

// O(n) — single pass
for (int i = 0; i < n; i++) { }

// O(n log n) — sorting
Arrays.sort(arr);

// O(n²) — nested loops
for (int i = 0; i < n; i++)
    for (int j = 0; j < n; j++) { }

// O(2ⁿ) — recursion with two branches (backtracking, subsets)
```

---

### Space Complexity

```java
int sum = 0;                        // O(1) — constant space
int[] dp = new int[n];              // O(n) — linear space
int[][] matrix = new int[n][n];     // O(n²) — quadratic space
```

---

### Key Operation Complexities

| Operation | Time |
|---|---|
| `HashMap` get/put | O(1) average |
| `TreeMap` get/put | O(log n) |
| `ArrayList` get | O(1) |
| `ArrayList` add at end | O(1) amortized |
| `ArrayList` add at middle | O(n) |
| `PriorityQueue` poll/offer | O(log n) |
| `Arrays.sort` | O(n log n) |
| String `+` in loop | O(n²) — avoid! |
| `StringBuilder` append | O(1) |

---

### The Three Rules for Complexity Analysis

1. **Identify dominant operation** — what runs most often?
2. **Drop constants** — O(2n) = O(n), O(n + 100) = O(n)
3. **Drop smaller terms** — O(n² + n) = O(n²)

```java
// Example: what's the complexity?
for (int i = 0; i < n; i++) {
    map.put(arr[i], map.getOrDefault(arr[i], 0) + 1);  // O(1)
}
// Answer: O(n) — loop is n, each operation inside is O(1)
```

---

*Notes compiled during DSA intern prep — covers Phase A through D of Java revision.*
