## Why Data Structures?

In computer science, one of the most fundamental topics is how to organize data. In fact, computer science majors take an entire class on this topic where they learn the costs and benefits of different organizational strategies. To explore this concept, let's use an example.

Imagine I'm working at a library and the shelves are alphabetized from A to Z like so:

<img src="C:\Users\samgo\data-science-notes\assets\images\library_example.png" alt="library_example" style="zoom:25%;" />

Now, what happens when I add a book to the As? I have to move a book down a shelf, then move a different book down another shelf, then move another book across to the next set of shelves, and on and on. If I'm shelving 100 books, that turns into a huge amount of work!

What if, instead, we organize the books into more shelves, with fewer books per shelf?

<img src="C:\Users\samgo\data-science-notes\assets\images\library_example_2.png" alt="library_example2" style="zoom:25%;" />

This takes up more space, but when we add books at the start of the alphabet, we only have to move a few books out of the way, and we don't have to transfer books from shelf to shelf. Now, we can also split the work between multiple shelvers. Eventually, if too many books end up on one shelf, we may have to shift some books around to rebalance the shelves, but that won't happen very often.

How did we avoid doing so much work? We used a different data structure. Instead of putting all of the books in a list together, we organized them into alphabetic categories. Now, local changes to one category don't affect the other categories.