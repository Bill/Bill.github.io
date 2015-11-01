---
author: Bill Burcham
comments: true
date: 2007-01-07 18:46:19+00:00
layout: post
slug: updated-validateable-recipe
title: Updated Validateable Recipe
wordpress_id: 58
categories:
- Ruby on Rails
---

Recipe 64[[1](http://www.memerocket.com/wp-admin/post.php#fn_1)] shows how to use Rails' ActiveRecord Validations functionality on objects that don't have corresponding database records.  The canonical example of this situation is a `Password` (model) class.  It helps support the view but there is no actual `passwords` table in the database.

When I went to use the recipe, I ran into an issue that [others](http://made-of-stone.blogspot.com/2006/11/active-record-validations-and.html) have seen.   Turns out that Rails has changed since the recipe was written and now the validation functionality is expecting a validateable class to define a _class method_ called `human_attribute_name`.  Looking back at my old projects I notice that this has been the case since at least Spring 2006.  Whatever.  I tweaked the recipe and now it works w/ my fairly edgy Rails version (I'm on revision 5662 just now).  Here's the code:


    <span class="linenum">  1</span>  <span class="source source_ruby"><span class="declaration declaration_module declaration_module_ruby"><span class="keyword keyword_control keyword_control_module keyword_control_module_ruby">module</span> <span class="entity entity_name entity_name_module entity_name_module_ruby">Validateable</span></span>
    <span class="linenum">  2    </span>[<span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:save</span>, <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:save!</span>, <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:update_attribute</span>].each{|<span class="keyword keyword_other keyword_other_special-method keyword_other_special-method_ruby">attr</span>| define_method(<span class="keyword keyword_other keyword_other_special-method keyword_other_special-method_ruby">attr</span>){}}
    <span class="linenum">  3</span><span class="declaration declaration_function declaration_function_method declaration_function_method_with-arguments declaration_function_method_with-arguments_ruby">    <span class="keyword keyword_control keyword_control_def keyword_control_def_ruby">def</span> <span class="entity entity_name entity_name_function entity_name_function_ruby">method_missing</span>(<span class="variable variable_parameter">symbol, *params</span>)</span>
    <span class="linenum">  4</span>      <span class="keyword keyword_control keyword_control_ruby">if</span>(symbol.to_s =~<span class="string string_regexp string_regexp_classic string_regexp_classic_ruby"> /<span class="string string_regexp string_regexp_group string_regexp_group_ruby">(.*)</span>_before_type_cast$/</span>)
    <span class="linenum">  5</span>        send(<span class="variable variable_other variable_other_readwrite variable_other_readwrite_global variable_other_readwrite_global_pre-defined variable_other_readwrite_global_pre-defined_ruby">$1</span>)
    <span class="linenum">  6</span>      <span class="keyword keyword_control keyword_control_ruby">end</span>
    <span class="linenum">  7</span>     <span class="keyword keyword_control keyword_control_ruby">end</span>
    <span class="linenum">  8    </span><span class="declaration declaration_module declaration_module_ruby"><span class="keyword keyword_control keyword_control_module keyword_control_module_ruby">module</span> <span class="entity entity_name entity_name_module entity_name_module_ruby">ClassMethods</span></span>
    <span class="linenum">  9      </span><span class="declaration declaration_function declaration_function_method declaration_function_method_with-arguments declaration_function_method_with-arguments_ruby"><span class="keyword keyword_control keyword_control_def keyword_control_def_ruby">def</span> <span class="entity entity_name entity_name_function entity_name_function_ruby">human_attribute_name</span>(<span class="variable variable_parameter">attribute_key_name</span>)</span>
    <span class="linenum">  10       </span>attribute_key_name.humanize
    <span class="linenum">  11</span>     <span class="keyword keyword_control keyword_control_ruby">end</span>
    <span class="linenum">  12</span>   <span class="keyword keyword_control keyword_control_ruby">end</span>
    <span class="linenum">  13</span><span class="declaration declaration_function declaration_function_method declaration_function_method_with-arguments declaration_function_method_with-arguments_ruby">   <span class="keyword keyword_control keyword_control_def keyword_control_def_ruby">def</span> <span class="entity entity_name entity_name_function entity_name_function_ruby">self.included</span>(<span class="variable variable_parameter">base</span>)</span>
    <span class="linenum">  14     </span>base.send(<span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:include</span>, <span class="variable variable_other variable_other_constant variable_other_constant_ruby">ActiveRecord</span>::<span class="variable variable_other variable_other_constant variable_other_constant_ruby">Validations</span>)
    <span class="linenum">  15</span>     base.extend(<span class="variable variable_other variable_other_constant variable_other_constant_ruby">ClassMethods</span>)
    <span class="linenum">  16   </span><span class="keyword keyword_control keyword_control_ruby">end</span>
    <span class="linenum">  17</span> <span class="keyword keyword_control keyword_control_ruby">end</span></span>


Just put that code into `validateable.rb`, drop it into your `lib` directory and put an `include Validateable` line at the top (inside) of your class definition.
Notice that instead of implementing the `append_features` class method (as the original recipe did), I'm implementing `included` as recommended in the Module#append_features documentation[[2](http://www.memerocket.com/wp-admin/post.php#fn_2)].  It's just a little cleaner in that it eliminates the need to invoke `super`.

One last interesting bit is that I ran into difficulties convincing Ruby to add a _class method_ to the target class.  At first I tried simply using this definition:


    <span class="linenum"> 1</span> <span class="source source_ruby"><span class="declaration declaration_function declaration_function_method declaration_function_method_with-arguments declaration_function_method_with-arguments_ruby"><span class="keyword keyword_control keyword_control_def keyword_control_def_ruby">def</span> <span class="entity entity_name entity_name_function entity_name_function_ruby">self.human_attribute_name</span>(<span class="variable variable_parameter">attribute_key_name</span>)</span>
    </span>


In place of the sub-module `ClassMethods` and the `base.extend...` call.  I still don't fully get why that approach wouldn't work, but after reading Jay Fields' post on [Ruby: instance and class methods from a module](http://jayfields.blogspot.com/2006/12/ruby-instance-and-class-methods-from.html) I thought I'd give this approach a try — and voilÃ .
[1] Recipe 64 _Validating Non-Active Record Objects_ in Chad Fowler's _[Rails Recipes](http://www.pragmaticprogrammer.com/titles/fr_rr/http://www.pragmaticprogrammer.com/titles/fr_rr/)_.

[2]  p. 554 of _Dave Thomas'_ _[Programming Ruby](http://www.pragmaticprogrammer.com/titles/ruby/index.html)_ a.k.a. _The Pickaxe Book_.
