---
layout: post
title: XSS Vulnerabilities of JSP 2.0 Expressions
date: 2006-06-25 15:46:48 -04:00
comments: false
tags: security
---
JSP 2.0 introduced a handy new capability allowing you to use JSP Expressions directly within the template text (i.e. outside of tag libraries or tag files) of a web page. This allows developers to easily expose data and objects stored in application, session, request, or page scope using a simple Ant-style syntax. It allows you to replace this

``` jsp
<table>
  <c:forEach var="book" items="${books}">
    <tr>
      <td><c:out value="${book.title}"/></td>
      <td><c:out value="${book.author}"/></td>
      <td><c:out value="${book.isbn}"/></td>
    </tr>
  </c:forEach>
</table>
```

with this

``` jsp
<table>
  <c:forEach var="book" items="${books}">
    <tr>
      <td>${book.title}</td>
      <td>${book.author}</td>
      <td>${book.isbn}</td>
    </tr>
  </c:forEach>
</table>
```

As you can see, the syntax in the second example is much cleaner. But, "with great power comes great responsibility." The chink in the armor that every tutorial I've read (including [Sun's Java EE 5 Tutorial](http://java.sun.com/javaee/5/docs/tutorial/doc/index.html) fails to mention is that the expression syntax does not escape HTML characters. Therefore, *any web application using JSP Expressions to output unsanitized, user-entered data will be vulnerable to Cross Site Scripting (XSS) attacks.*

Unfortunately, the cure for this XSS vulnerability leads us right back to our first example. As section 2.2.2 of the [JSP 2.0 Specification](http://jcp.org/aboutJava/communityprocess/final/jsr152) reads, "In cases where escaping is desired (for example, to help prevent cross-site scripting attacks), the JSTL core tag c:out can be used." 

My advice is to use JSP Expressions only as tag library attribute values and stick to using JSTL's c:out tag for writing text to a page. Deciding which instances of template text expression usage are safe and which are not is simply too error prone and the consequences of a mistake too dangerous. 
