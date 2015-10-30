---
author: bburcham
comments: true
date: 2007-07-07 01:17:03+00:00
layout: post
slug: has_many-associations-with-complex-joins-kind-of
title: :has_many Associations with Complex Joins (Kind of)
wordpress_id: 78
categories:
- Ruby on Rails
- SQL
---

![duck-vader](/assets/{{page.path | remove_first: '_posts/' | remove: '.markdown'}}/duck-vader.jpg)In this episode, your fearless Jedi delves within the dark tree of associations, with_scope, inner joins, duck typing and meta-programming all in an effort to keep DRY...

In [Joyomi](http://joyomi.com) I create an Omi when I lend you a book. I can optionally share that Omi with you. If I do, then the Omi shows up in the "Omis Shared With Me" tab in your dashboard. Sharing. Mkay. Social networking 101.

Now the query for a Joyomi user to find his own Omi's (one's he's created) is trivial. The user model simply has_many omis. No brainer. On the other hand, going in the opposite direction and finding all the Omi's that my friends have shared with me is decidedly non-trivial. In fact it's downright tricky. It involves a big long series of joins. This is further complicated by the fact that in order to address potential SPAMming we don't show you Omi's that were created by folks who are not in your contact list. Got that? Good.

<!-- more -->

So I set out to implement the "Omis Shared With Me" tab hoping to reuse a lot of stuff from the "Outstanding Omis" and "Archived Omis" tabs. In particular I've got a nice clean little pagination routine that takes an association as a parameter and then proceeds to paginate the Omis in that association. Here's the routine:

    
    <span class="linenum">    1</span> <span class="source source_ruby"><span class="declaration declaration_function declaration_function_method declaration_function_method_with-arguments declaration_function_method_with-arguments_ruby">  <span class="keyword keyword_control keyword_control_def keyword_control_def_ruby">def</span> <span class="entity entity_name entity_name_function entity_name_function_ruby">paginate_omis</span>(<span class="variable variable_parameter"> association, order</span>)</span>
    <span class="linenum">    2</span>     <span class="variable variable_other variable_other_readwrite variable_other_readwrite_instance variable_other_readwrite_instance_ruby">@omis_pages</span> = <span class="variable variable_other variable_other_constant variable_other_constant_ruby">Paginator</span>.<span class="keyword keyword_other keyword_other_special-method keyword_other_special-method_ruby">new</span> <span class="constant constant_language constant_language_pseudo-variable constant_language_pseudo-variable_ruby">self</span>, association.count, <span class="constant constant_numeric constant_numeric_ruby">10</span>, params[<span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:page</span>]
    <span class="linenum">    3</span>     <span class="variable variable_other variable_other_readwrite variable_other_readwrite_instance variable_other_readwrite_instance_ruby">@omis</span> = association.find(
    <span class="linenum">    4</span>       <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:all</span>,
    <span class="linenum">    5</span>       <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:order</span> => order,
    <span class="linenum">    6</span>       <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:limit</span> => <span class="variable variable_other variable_other_readwrite variable_other_readwrite_instance variable_other_readwrite_instance_ruby">@omis_pages</span>.items_per_page,
    <span class="linenum">    7</span>       <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:offset</span> => <span class="variable variable_other variable_other_readwrite variable_other_readwrite_instance variable_other_readwrite_instance_ruby">@omis_pages</span>.current.offset)
    <span class="linenum">    8</span>   <span class="keyword keyword_control keyword_control_ruby">end</span>
    </span>


(the order parameter lets you send in nice little SQL ordering clauses like ['-expected_at DESC'](/2006/12/02/put-nulls-last-on-mysql/) ) You can see this routine relies on the association parameter responding to count and find. Since "outstanding" and "archived" Omis are associations:

    
    <span class="linenum">    1</span> <span class="source source_ruby">  has_many <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:outstanding_omis</span>, <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:class_name</span> => <span class="string string_quoted string_quoted_single string_quoted_single_ruby">'Omi'</span>, <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:conditions</span> => <span class="string string_quoted string_quoted_double string_quoted_double_ruby">"`archived_at` is null"</span>
    <span class="linenum">    2</span>   has_many <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:archived_omis</span>, <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:class_name</span> => <span class="string string_quoted string_quoted_single string_quoted_single_ruby">'Omi'</span>, <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:conditions</span> => <span class="string string_quoted string_quoted_double string_quoted_double_ruby">"`archived_at` is not null"</span>
    </span>


This works fine. So when it came time to implement "Omis Shared With Me" I thought great, I'll just add another association for that. Granted this is a more complex association, but from reading about the :joins option  to [ActiveRecord::Base#find](http://caboo.se/doc/classes/ActiveRecord/Base.html#M006434) it seemed doable. I'd just use that :joins option on my new association and everything would Just Workâ„¢. Boy was I wrong.

You may wonder why I would think that just because :joins was documented under find, that it would also work for has_many. Well it's because those two methods mostly implement the same options.


### Options Implemented by Both find and has_many





	
  * :conditions

	
  * :order

	
  * :group

	
  * :limit

	
  * :offset

	
  * :include

	
  * :select


But as it turns out there are a handful of discrepancies. Most of the discrepancies make sense. For instance, it wouldn't make sense for find to support a :foreign_key option. But there are a few discrepancies that do not really make sense.


### Options _Implemented by find_ but not by has_many





	
  * _:joins_

	
  * :readonly

	
  * :lock


Hum, it seems to me that all three of these options would be useful on has_many. I groveled around the Rails source and I don't see any particular reason why these aren't supported by has_many. In fact it turns out that the find and has_many sources have many similarities. They just happen to have very little reuse going on.

It occured to me while reading the Rails source and seeing all the duplication between find and associations that there was a missing class, perhaps it should be called a "relation". I'd like to instantiate a "relation" as a first class object. If such an object existed then both find and has_many could certainly use one. But that is the subject of a different post. I digress.

So finding myself in need of an association with :joins I did what any self-respecting Ruby programmer would do -- I made one myself. Now before you freak out, I didn't implement all 23-odd methods of has_many associations. I implemented only the two I needed in my pagination method (find and count). Remember, in Ruby, if it quacks like a duck that's good enough. And I knew exactly which duck sounds this "association" would be called upon to emit.

So here is what the has_many would look like if has_many actually supported :joins:

    
    <span class="linenum">    1</span> <span class="source source_ruby source_ruby_rails"><span class="support support_function support_function_activerecord support_function_activerecord_rails">has_many</span> <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:omis_shared_with_me</span>, <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:class_name</span> => <span class="string string_quoted string_quoted_single string_quoted_single_ruby">'Omi'</span>, <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:joins</span> => <span class="string string_quoted string_quoted_single string_quoted_single_ruby">'inner join identities as i2 on i2.id = omis.identity_id inner join email_addresses as e1 on e1.address = i2.name inner join contacts as c1 on c1.id = e1.contact_id inner join contacts as c2 on omis.counterparty_id = c2.id inner join email_addresses as e2 on c2.id = e2.contact_id'</span>, <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:conditions</span> => <span class="string string_quoted string_quoted_single string_quoted_single_ruby">'identities.id = c1.owner_id and e2.address = identities.name'</span>
    </span>


My workaround was to define a method on the user class (model). That method would return an instance of my new "association" class. Here is the method:

    
    <span class="linenum">    1</span> <span class="source source_ruby"><span class="declaration declaration_function declaration_function_method declaration_function_method_with-arguments declaration_function_method_with-arguments_ruby">  <span class="keyword keyword_control keyword_control_def keyword_control_def_ruby">def</span> <span class="entity entity_name entity_name_function entity_name_function_ruby">omis_shared_with_me</span>(<span class="variable variable_parameter"> *args</span>)</span>
    <span class="linenum">    2</span>     proxy = <span class="variable variable_other variable_other_constant variable_other_constant_ruby">Object</span>.<span class="keyword keyword_other keyword_other_special-method keyword_other_special-method_ruby">new</span>
    <span class="linenum">    3</span>     instance_eval <<<span class="keyword keyword_control keyword_control_ruby">END</span>
    <span class="linenum">    4</span> <span class="declaration declaration_class declaration_class_ruby">    <span class="keyword keyword_control keyword_control_class keyword_control_class_ruby">class</span> <span class="entity entity_name entity_name_class entity_name_class_ruby"><span class="variable variable_other variable_other_object variable_other_object_ruby"><<proxy</span></span></span>
    <span class="linenum">    5</span> <span class="declaration declaration_function declaration_function_method declaration_function_method_with-arguments declaration_function_method_with-arguments_ruby">      <span class="keyword keyword_control keyword_control_def keyword_control_def_ruby">def</span> <span class="entity entity_name entity_name_function entity_name_function_ruby">find</span>(<span class="variable variable_parameter"> *args</span>)</span>
    <span class="linenum">    6</span>         with_scope <span class="keyword keyword_control keyword_control_ruby keyword_control_ruby_start-block">do
    </span><span class="linenum">    7</span>           <span class="variable variable_other variable_other_constant variable_other_constant_ruby">Omi</span>.find( <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:all</span>, *args)
    <span class="linenum">    8</span>         <span class="keyword keyword_control keyword_control_ruby">end</span>
    <span class="linenum">    9</span>       <span class="keyword keyword_control keyword_control_ruby">end</span>
    <span class="linenum">   10</span> <span class="declaration declaration_function declaration_function_method declaration_function_method_with-arguments declaration_function_method_with-arguments_ruby">      <span class="keyword keyword_control keyword_control_def keyword_control_def_ruby">def</span> <span class="entity entity_name entity_name_function entity_name_function_ruby">count</span>(<span class="variable variable_parameter"> *args</span>)</span>
    <span class="linenum">   11</span>         with_scope <span class="keyword keyword_control keyword_control_ruby keyword_control_ruby_start-block">do
    </span><span class="linenum">   12</span>           <span class="variable variable_other variable_other_constant variable_other_constant_ruby">Omi</span>.count( *args)
    <span class="linenum">   13</span>         <span class="keyword keyword_control keyword_control_ruby">end</span>
    <span class="linenum">   14</span>       <span class="keyword keyword_control keyword_control_ruby">end</span>
    <span class="linenum">   15</span>       <span class="keyword keyword_other keyword_other_special-method keyword_other_special-method_ruby">private</span>
    <span class="linenum">   16</span> <span class="declaration declaration_function declaration_function_method declaration_function_method_without-arguments declaration_function_method_without-arguments_ruby">      <span class="keyword keyword_control keyword_control_def keyword_control_def_ruby">def</span> <span class="entity entity_name entity_name_function entity_name_function_ruby">with_scope</span></span>
    <span class="linenum">   17</span>         <span class="comment comment_line comment_line_number-sign comment_line_number-sign_ruby"># without :select omis.* you get superfluous columns an the Omi objects aren't right (description is often NULL)</span>
    <span class="linenum">   18</span>         <span class="variable variable_other variable_other_constant variable_other_constant_ruby">Omi</span>.with_scope(<span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:find</span> => {<span class="meta meta_syntax meta_syntax_ruby meta_syntax_ruby_start-block"> </span><span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:select</span> => <span class="string string_quoted string_quoted_single string_quoted_single_ruby">'omis.*'</span>, <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:joins</span> => <span class="string string_quoted string_quoted_single string_quoted_single_ruby">'inner join identities as i2 on i2.id = omis.identity_id inner join email_addresses as e1 on e1.address = i2.name inner join contacts as c1 on c1.id = e1.contact_id inner join identities as i1 on i1.id = c1.owner_id inner join contacts as c2 on omis.counterparty_id = c2.id inner join email_addresses as e2 on c2.id = e2.contact_id'</span>, <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:conditions</span> => [<span class="string string_quoted string_quoted_single string_quoted_single_ruby">'e2.address = i1.name and :identity_id = i1.id'</span>, {<span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:identity_id</span> => <span class="comment comment_line comment_line_number-sign comment_line_number-sign_ruby">#{id}}]}) do</span>
    <span class="linenum">   19</span>           <span class="keyword keyword_control keyword_control_pseudo-method keyword_control_pseudo-method_ruby">yield</span>
    <span class="linenum">   20</span>         <span class="keyword keyword_control keyword_control_ruby">end</span>
    <span class="linenum">   21</span>       <span class="keyword keyword_control keyword_control_ruby">end</span> <span class="comment comment_line comment_line_number-sign comment_line_number-sign_ruby"># def</span>
    <span class="linenum">   22</span>     <span class="keyword keyword_control keyword_control_ruby">end</span> <span class="comment comment_line comment_line_number-sign comment_line_number-sign_ruby"># <<proxy</span>
    <span class="linenum">   23</span> <span class="keyword keyword_control keyword_control_ruby">END</span>
    <span class="linenum">   24</span>     proxy
    <span class="linenum">   25</span>   <span class="keyword keyword_control keyword_control_ruby">end</span> <span class="comment comment_line comment_line_number-sign comment_line_number-sign_ruby"># def</span></span>


This code bears some explanation. (darn tootin!) The most obvious way to define the association class would be to simply define a new Ruby class at the top level or maybe within the scope of the user class. If we did that however, we wouldn't have access, in instances of that class, to the id attribute of the user. And we need that id when the association is called upon to do its thing.

Now we could add an instance variable to our association class to hold the id and we could initialize that thing on construction but that wouldn't be very educational now would it? Nope, no instance variables for us -- we're going to use a little meta-programming instead.

So instead of defining the new class at the top level or in the context of the user _class_, we define an object-specific class (specific to the proxy object) and we evaluate that definition in the context of the user _object_. In this way, the reference to id in the private with_scope method will reference the id attribute of the appropriate user at runtime.

So that's it. We are leveraging find to create an association class that supports the :joins option in 25 lines of code. This approach could easily be extended to add some of the other methods of has_many depending on your project needs. Also, I'm sure some Ruby Rock Star could DRY it up more.

![The Thing With Two Heads](http://www.meme-rocket.com/wp-content/uploads/2007/07/two-heads.jpg)On the other hand, doing a whole lot more to turn this [hematoma](http://www2.parc.com/csl/groups/sda/projects/oi/workshop-94/foil/main.html) into a [second head](http://www.imdb.com/title/tt0069372/) is not a very wise use of resources. It'd be interesting to go in and steal (factor out) the best bits of find and has_many and build that general purpose 'relation' class I spoke of earlier, DRY-ing up find and associations, normalizing the options between them and giving us first-class 'relations' to boot. Anyone of you ActiveRecord warriors out there interested in some deep hacking?
