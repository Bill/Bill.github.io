---
author: Bill Burcham
comments: true
date: 2007-04-10 18:07:52+00:00
layout: post
slug: fight-id-proliferation-and-update-any-element-you-want
title: Fight id Proliferation and Update Any Element You Want
wordpress_id: 66
categories:
- AJAX
- Ruby on Rails
- script.aculo.us
---

**update**: July 5, 2007: see [ID Proliferation Eradication Technique #1…](/2007/07/05/id-proliferation-eradication-technique-1-leverage-pageselect-with-pageinsert/) for an update.



![](http://www.prototypejs.org/images/logo-home.gif)

The [Prototype](http://www.prototypejs.org/) Updater constructor takes an Element (object) or element id (String) as the first parameter. You can see this in prototype.js in Abstract.Insertion.initialize and Ajax.Updater.updateContent . In both situations the first parameter sent to the constructor has $() applied before use. And as I'm sure you're aware, the effect of that is that if the parameter is an Element, then the same element is returned. OTOH, if the element is a String, then that string is assumed to be an element id and the Element is found in the DOM and returned.

The big deal here is that the [documentation](http://www.prototypejs.org/api) lies or at a minimum fails to make this point clearly. Have a look at prototype-api.pdf. And this misunderstanding is carried forward in the [Rails wrapper API](http://caboo.se/doc/classes/ActionView/Helpers/PrototypeHelper/JavaScriptGenerator/GeneratorMethods.html) functionality ActionView::Helpers::PrototypeHelper::JavaScriptGenerator::GeneratorMethods#[insert_html](http://caboo.se/doc/classes/ActionView/Helpers/PrototypeHelper/JavaScriptGenerator/GeneratorMethods.html#M005078), [replace_html](http://caboo.se/doc/classes/ActionView/Helpers/PrototypeHelper/JavaScriptGenerator/GeneratorMethods.html#M005079), remove, show, hide, and visual_effect. The result of this misunderstanding is that many JavaScript programmers think they need id's all over the place -- and Rails programmers actually _do_ need id's all over the place (unless and until the API is repaired...)


What is wanted in the Ruby wrapper, in order to expose the full capability of the underlying JavaScript libraries, is the ability to pass not only a String (containing an Element id) but optionally to pass an instance of JavaScriptGenerator to insert_html and replace_html and to have that generator rendered. Then we could do (borrowing from the Rails doc and extending...):



    
    <span class="linenum">    1</span> <span class="source source_ruby source_ruby_rails">update_page <span class="keyword keyword_control keyword_control_ruby keyword_control_ruby_start-block">do </span>|page|
    <span class="linenum">    2</span>   page.insert_html <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:bottom</span>, page.select(<span class="string string_quoted string_quoted_single string_quoted_single_ruby">'p.welcome b'</span>).first, <span class="string string_quoted string_quoted_double string_quoted_double_ruby">"<li><span class="source source_ruby source_ruby_embedded source_ruby_embedded_source">#{<span class="variable variable_other variable_other_readwrite variable_other_readwrite_instance variable_other_readwrite_instance_ruby">@item</span>.name}</span></li>"</span>
    <span class="linenum">    3</span>   page.visual_effect <span class="constant constant_other constant_other_symbol constant_other_symbol_ruby">:highlight</span>, <span class="string string_quoted string_quoted_single string_quoted_single_ruby">'list'</span>
    <span class="linenum">    4</span>   page.hide <span class="string string_quoted string_quoted_single string_quoted_single_ruby">'status-indicator'</span>, <span class="string string_quoted string_quoted_single string_quoted_single_ruby">'cancel-link'</span>
    <span class="linenum">    5</span> <span class="keyword keyword_control keyword_control_ruby">end</span></span>


Note that instead of the simple string literal 'list' (from the Rails doc) we've got a full-fledged expression there. And generate something like this:

    
    <span class="linenum">    1</span> <span class="source source_ruby source_ruby_rails"><span class="keyword keyword_other keyword_other_special-method keyword_other_special-method_ruby">new</span> <span class="variable variable_other variable_other_constant variable_other_constant_ruby">Insertion</span>.<span class="variable variable_other variable_other_constant variable_other_constant_ruby">Bottom</span>(<span class="variable variable_other variable_other_readwrite variable_other_readwrite_global variable_other_readwrite_global_pre-defined variable_other_readwrite_global_pre-defined_ruby">$$</span>(<span class="string string_quoted string_quoted_single string_quoted_single_ruby">'p.welcome b'</span>).first, <span class="string string_quoted string_quoted_double string_quoted_double_ruby">"<li>Some item</li>"</span>);
    <span class="linenum">    2</span> <span class="keyword keyword_other keyword_other_special-method keyword_other_special-method_ruby">new</span> <span class="variable variable_other variable_other_constant variable_other_constant_ruby">Effect</span>.<span class="variable variable_other variable_other_constant variable_other_constant_ruby">Highlight</span>(<span class="variable variable_other variable_other_readwrite variable_other_readwrite_global variable_other_readwrite_global_pre-defined variable_other_readwrite_global_pre-defined_ruby">'list'</span>);
    <span class="linenum">    3</span> [<span class="string string_quoted string_quoted_double string_quoted_double_ruby">"status-indicator"</span>, <span class="string string_quoted string_quoted_double string_quoted_double_ruby">"cancel-link"</span>].each(<span class="variable variable_other variable_other_constant variable_other_constant_ruby">Element</span>.hide);</span>


But really, the proposed change to the Ruby wrapper isn't limited to CSS selectors of course. Once insert_html, replace_html and friends support a full-fledged generator parameter, you could put arbitrary JavaScript in there. The most obvious examples would be calling custom functions and DOM traversal functions.
