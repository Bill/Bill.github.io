---
author: bburcham
comments: true
date: 2006-12-02 20:34:06+00:00
layout: post
slug: put-nulls-last-on-mysql
title: Put NULLS Last on MySQL
wordpress_id: 49
categories:
- Ruby on Rails
- SQL
---

NULLs are a wonderful but strange feature of SQL.  They are the source of some deep coolness but also some lost hair.  One place where NULLs can bite you is when you are sorting. The way you sort in SQL is by appending the `ORDER BY` clause to the end of a `SELECT` statement.  The problem is that the SQL standards say that for the purpose of sorting, NULL = NULL but they don't specify how NULL values should be ordered relative to non-NULL ones.  Doh!

In this post I describe the solution to this problem in the context of MySQL and Ruby on Rails.
<!-- more -->

Now imagine you've got a Ruby on Rails app that manages _shipments_ and those shipments have an attribute called `expected_at`:


    
    <span class="linenum">    1</span> <span class="source source_ruby">      t.column <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:expected_at</span>, <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:datetime</span></span>



And then let's say you'd like to present a list of shipments ordered by their expected time.  You'd like shipments expected soonest to appear before shipments expected later.  You might have a core snippet of code like this:


    
    <span class="linenum">    1</span> <span class="source source_ruby source_ruby_rails"><span class="variable variable_other variable_other_readwrite variable_other_readwrite_instance variable_other_readwrite_instance_ruby">@sorted_shipments</span> = association.find(
    <span class="linenum">    2</span>   <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:all</span>,
    <span class="linenum">    3</span>   <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:order</span> => <span class="string string_quoted string_quoted_single string_quoted_single_ruby">'expected_at ASC'</span>,
    <span class="linenum">    4</span>   <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:limit</span> => <span class="variable variable_other variable_other_readwrite variable_other_readwrite_instance variable_other_readwrite_instance_ruby">@shipment_pages</span>.items_per_page,
    <span class="linenum">    5</span>   <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:offset</span> => <span class="variable variable_other variable_other_readwrite variable_other_readwrite_instance variable_other_readwrite_instance_ruby">@shipment_pages</span>.current.offset)
    </span>



Now what happens if you don't know the expected time for a shipment?  Well the natural thing to do in SQL is to leave the value NULL.  Now the problem arises.  If for instance you're running on MySQL, shipments with NULL `expected_at` times will appear _before_ those with non-NULL ones.  So what can you do?

Well my first thought was to have two separate associations -- one for shipments that had NULL `expected_at` times and a second one for those that didn't.  The problem with that approach (if you're using the Ruby on Rails Paginator framework) is that it leaves you to do a bunch of bookkeeping in application code.  You really don't want to hand code pagination over multiple associations.  Or I should say -- _I_ certainly don't want to.

My second thought was that I was missing some key knowledge of Ruby on Rails.  When in doubt -- assume that Rails has thought of it.  But a fair amount of spelunking turned up nothing.  Rails is mute on this issue.

So where to turn next?  The database of course!  An initial search for "sql sort null"  led me to an Oracle [reference](http://www.xquery.com/white_papers/generating_sql/sorting-data.html) that mentioned Oracle's `NULLS LAST` clause.  When I tried `NULLS LAST` on MySQL, naturally it failed. MySQL doesn't support it.  A subsequent search for "mysql nulls last" yielded [gold](http://troels.arvin.dk/db/rdbms/).  Turns out there is an obscure syntax available in MySQL which you can use to control the relative ordering of NULL and non-NULL values:


<blockquote>NULLs are considered lower than any non-NULL value, except if a - (minus) character is added before the column name and ASC is changed to DESC, or DESC to ASC; this minus-before-column-name feature seems undocumented.</blockquote>


Clear enough.  Er.  Not very.  If you're like me you need an example.  To make the previous example do what we want, here's what the code looks like now:


    
    <span class="linenum">    1</span> <span class="source source_ruby source_ruby_rails"><span class="variable variable_other variable_other_readwrite variable_other_readwrite_instance variable_other_readwrite_instance_ruby">@sorted_shipments</span> = association.find(
    <span class="linenum">    2</span>   <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:all</span>,
    <span class="linenum">    3</span>   <span class="comment comment_line comment_line_number-sign comment_line_number-sign_ruby"># this is the same as doing 'expected_at ASC NULLS LAST' on Oracle</span>
    <span class="linenum">    4</span>   <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:order</span> => <span class="string string_quoted string_quoted_single string_quoted_single_ruby">'-expected_at DESC'</span>,
    <span class="linenum">    5</span>   <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:limit</span> => <span class="variable variable_other variable_other_readwrite variable_other_readwrite_instance variable_other_readwrite_instance_ruby">@shipment_pages</span>.items_per_page,
    <span class="linenum">    6</span>   <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:offset</span> => <span class="variable variable_other variable_other_readwrite variable_other_readwrite_instance variable_other_readwrite_instance_ruby">@shipment_pages</span>.current.offset)</span>



So that's... stick a minus in front of the column name and invert the apparent sense of the ordering.  Now you'll get earlier arrivals before later ones and you'll get NULL's (indeterminate arrivals) at the very end.  Whew.
