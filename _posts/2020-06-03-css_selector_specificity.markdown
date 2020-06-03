---
layout: post
title:      "CSS Selector Specificity"
date:       2020-06-03 17:05:40 +0000
permalink:  css_selector_specificity
---


After a quiz I took recently I found I have confusion about CSS selector specificity. IT seemed to me the more CSS selectors you have in a declaration, the more specific it is, which means it’s style should win over the conflicting style of a less specific selector.
For example html body main .content h5 seems more specific that simply #product-info (which is an ID selector). It doesn’t work like that. Another example, if I have the following HTML :

```
        <div class="container">
                <div id="contact">
                    <h5>Address:</h5>
                    <h5 class="bold">Contact Us</h5>
             /div>
```

and this is my stylesheet: 

```
#contact h5 {
      color: blue;
}

.bold {
    color: red;
    font-weight: bold;
}
```

we can see that the bold font style was applied but not the color. Both text are in blue. Why?
In the case of font-weight there is no conflict, only one rule exists. But on the case of color we have two rules that apply to h5 color. In this case the more specific one wins. ID (identified by #) is more specific than class (indentified by . ) so blue takes over.
 One way is to think of selector specificity as a 3-digit number, or a triplet of digits such as (0,1,0). The first number (the hundreds) is the number of ID selectors in a compound selector. For example #product-info #product-image has two Id selectors and will be (2, 0, 0). The second number shown the number of class-selectors (or as we’ll see later, selectors equivalent to class-level). Classes start with . for example .content .featured would have specificity (0, 2, 0). The third number is the tag selector, so as in my initial example html body main .content h5 is (0,1,4). So my initial example is 100 vs 014. 100 wins and its style will be applied.
As we can see, the ID selector has higher specificity that any number of class or tag selectors, so in order to override it you have to use another ID selector. It is a good practice to avoid using ID selectors unless absolutely necessary as overusing them can lead to a messy stylesheet and unpredictable results.
 In my example simply changing from .bold to #bold (and class to id in html) will still not be enough and the blue will still prevail. #contact h5 would be (1,0,1) and just #bold would be (1,0,0). 
 If I add  h5#bold, then they are both (1,0,1) and the latest one applies.
 
Here are the rules of specificity:
    1. The selector with most IDs wins
    2. If two selectors have the same number of IDs, the one with most classes wins
    3. If the number of classes are also equal then the selector with most tag names wins

Other considerations:
*      pseudo-class selectors are used to select an element when it is in a certain state such as a:hover, :nth-child(n). You know it is a pseudo-class if it begins with colon (:). Pseudoclasses have the same specificity as classes, i.e. (0,1,0)
* 		 attribute selectors select elements with specific HTML attributes, e.g. input[type="email"]. They also have class specificity, i.e. (0, 1, 0)
* 		 the universal selector * {} has no effect on specificity, i.e. it is (0, 0, 0)
* 		 combinatora, such as >, + or ~ are used to join multiple selectors. The most common one is the space between two simple selectors, e.g.  .content li . It is also known as the descendant combinator, as in the example it indicates that the *li* element is a descendant of an element with class "content". > is the child combinator, which targets elements that are direct descendants of another element. The adjacend sibling combinator (+)selects elements that are adjacent to each other. e.g. *h1+p*. All combinators don't have any effect on specificity, i.e. they are (0, 0, 0).
* 		 the quickest way to enforce a style you want is by using *!important* however this is not always best practice! If you start using it all over the place it may lead to a big confusion.



     
