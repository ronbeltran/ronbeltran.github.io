---
layout: post
title:  "Usability vs Code Maintainability"
---

Fresh out of college I joined a bank as a programmer, I was supporting 
the loans department. Then one day the loans processor people complained 
to us that the Loans Application System they were using is way too 
complicated.

They said they were processing 50-100 applications per day and they are 
unproductive in using a menu that has so many tabs (around ten) to click 
and never even entering a single value in it. People are already in queue, 
obviously waiting and when these people lost their patience they may 
take their business elsewhere. By the way that banking system we used were 
designed and built by a third party.

So my supervisor asked me to rewrite and redesign it, that became my first real world 
taste of building software. According to my users - that what we usually 
call them. They want to have all those important fields in a single page, 
they don't care if they need to scroll down.

Good thing is that this banking system exposes an API to handle CRUD 
actions to their system. I then proceed to implement the front-end using HTML, 
Javascript and CSS. Then I implemented the back end in a proprietary
programming language and JSP. At that time I know nothing about CSS. Due to my 
inexperience I made this code.

{% highlight html %}
 <p style="position:absolute;top:140px;left:10px;color:black"> FUNCTION</p>
 <select style="position:absolute;top:140px;left:100px;color:black" name="cust.selFunction" id="custfunction" value="<%=sselfunction%>" onblur="init_func()">
 <option value="."></option> 
 <option value="A">A - Add</option>
 <option value="V">V - Verify</option>
 <option value="C">C - Cancel</option>
 </select>

 <p style="position:absolute;top:140px;left:220px;color:black" > Cust. Id</p>
 <INPUT style="position:absolute;top:140px;left:270px;color:black" TYPE="TEXT" CLASS="CTEXT" NAME="cust.txtcustid" id="custid" value="<%=scustid%>"  onblur="fillOnBlur(document.forms[0].custid)" maxlength="25" size="25" />         
 <a style=position:absolute;top:140px;left:460px;color:black" id="sLnk1" href="javascript:popcustid();">
 <img class="img" height="20" src="../images/search2.gif"></img></a>
{% endhighlight %}

 I know this code is horrible, it's a crap. But it solved their problem. 
 If you are a web designer then you may noticed that I used CSS absolute 
 positioning. The page has around 100+ fields and I manually and painfully
 computed their values with respect to left and top positions. 
 After my first production release, I added new features from time to time
 until I left the company.

 After 3 years of solely maintaining it, I took the time to make it less painful 
 to enhance the code base for future developers and since I'm already planning 
 to resign. I started with CSS and I fixed the absolute positioning issue. 
 Then I told my supervisor of the changes I made. I was shocked with the reply.
 I can't edit the project looks anymore. I mean the fields positioning,
 even it really looked better now. Why? Because my users are already get used to how it looks.
 I passed my resignation the next week.
