---
# You can also start simply with 'default'
theme: academic
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
title: YeSQL Report
info: |
  ## YeSQL Report
  Presentation slides for my colleague and only for academic purposes.

  Learn more at https://mocusez.site/zh-CN/posts/78ca1/
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# take snapshot for each slide in the overview
overviewSnapshots: true
coverAuthor: MocusEZ
coverAuthorUrl: https://github.com/mocusez
---

# YeSQL: “You extend SQL” 
## with Rich and Highly Performant User-Defined Functions in Relational Databases
**Presenter**: MocusEZ

<!--
Hello every one, this is MocusEZ from ****. Today what I want to talk about is YeSQL: "You extend SQL" with rich and highly performant User-Defined Functions in Relational Databases
-->

---
transition: fade
layout: two-cols
---

# Authors

- **Yannis Foufoulas**
  - U. of Athens, Athena R.C.
  - johnfouf@di.uoa.gr
- **Alkis Simitsis**
  - Athena Research Center
  - alkis@athenarc.gr
- **Lefteris Stamatogiannakis**
  - University of Athens
  - estama@gmail.com
- **Yannis Ioannidis**
  - U. of Athens, Athena R.C.
  - yannis@di.uoa.gr

::right::


<div style="position: relative; top: 16rem">
    <img src="https://www.athenarc.gr/sites/all/themes/aohna/images/athena-logo-symbol.svg" style="position: absolute; height: auto;">
    <img src="https://www.athenarc.gr/sites/all/themes/aohna/images/athena-logo-type-en.svg" style="position: absolute; height: auto;">
    <img src="https://www.athenarc.gr/sites/all/themes/aohna/images/athena-logo-blurb-en.svg" style="position: absolute; height: auto;">
</div>

<div style="position: relative;top: 7rem;left:4rem">
    <img src="https://en.uoa.gr/fileadmin/user_upload/uoa_logo_eng.svg" style="position: absolute; height: auto;">
</div>

<!--
The author team is mainly from University of Athens and Athena Research Center. where Yannis Ioannidis is the Presdient of ACM and Associated Faculty of Athena Research Center.

(https://translate.google.com/?sl=auto&tl=zh-CN&text=Yannis%20Ioannidis&op=translate)
-->

---
transition: fade

# layout: two-cols
---

# Motivation

<v-clicks> 

- Many programming language tools to assist developers design pipelines
  - **But:** Complicated ecosystem, unscalable processing

- Relational DBMSs offer efficient large data processing
  - **But:** SQL has limited expressive power

- UDFs in SQL merge relational and programming syntax and semantics
  - **But:** Impedance mismatch between declarative (SQL) and procedural (e.g., Python) operation
  
</v-clicks>


<div style="position: relative; left: 11rem">
    <img src="/Snipaste_2024-11-05_10-49-36.png" style="height: 12rem; ">
</div>

<!--
There are three points of motivation about this paper. 

The first thing is,although there are numerous programming language tools available to help developers design complex data pipelines.However, using these tools often introduces challenges. For exmaple, Multiple tools and languages can lead to complexity, creating a fragmented ecosystem that’s difficult to manage. And this will also cause scalability issues: Many of these solutions struggle to efficiently process large volumes of data at scale.

Second thing is  there’s a trade-off for SQL, the language we use at databases, is great for querying data but not as flexible for more complex processing tasks.

The last thing is Impedance mismatch between declarative (SQL) and procedural (e.g., Python) operation. I'll talk about about this thing in the next page.
-->

---
transition: fade
---

# Main issues addressed
## Mismatch between the relational (SQL) evaluation and the procedural (Python) execution

<v-clicks>

- (a) Context switching overhead:
  - one facility needs to invoke the other through various levels of indirection. This is potentially expensive when performed frequently. 

- (b) Data conversion overhead: 
  - data is represented differently in the two environments and need to be wrapped/unwrapped or checked (e.g., for overflow) and encoded/decoded.


</v-clicks>


<style>
.slidev-vclick-target {
  transition: all 500ms ease;
}

.slidev-vclick-hidden {
  transform: scale(0);
}
</style>

<!--
So what factors make up this Mismatch? 

There are two main points about this:

One is for context switching overhead. For exmple, running a loop in Python where each iteration requires a SQL query to retrieve a small amount of data. Each time, Python needs to "switch" to SQL, call the database, wait for it to respond, then switch back. This back-and-forth can cause a significant slowdown, especially in cases where the database call happens hundreds or thousands of times within a single process.

Another thing is data conversion overhead. Suppose a SQL query returns a large dataset with precise floating-point numbers, which Python then has to read and convert to its internal format. This conversion may involve checking for things like overflow or loss of precision. Additionally, any strings or binary data might need to be encoded or decoded differently in each environment, adding to the time and resources needed for these conversions.
-->

---
transition: fade
---

# Our Approach: YeSQL

<v-clicks>

- Usability and expressiveness
  - Stateful, parametric, polymorphic, dynamically typed, scalar/aggregate/table UDFs
- JIT-compiled UDFs and stateful UDFs
  - UDF parallelization and UDF fusion

</v-clicks>

<v-clicks depth="2">

- Performance enhancements
  - Tracing JIT compilation
  - Seamless integration with the DBMS(**Mainly dependent on CFFI**)
  - UDF fusion
  - Parallelism
  - Stateful UDFs.

</v-clicks>

<!--
So what they do to approach YeSQL?

They designed UDFs to be stateful, parametric, and polymorphic, allowing them to handle complex data and operations. The YeSQL support dynamic typing and can return results as scalars, aggregates, or even tables—making UDFs more versatile for various data tasks.

And all these UDFs will be JIT-compiled and stateful.

These issues focus on 5 main points in the code implementation: 

- Tracing JIT compilation
 - Seamless integration with the DBMS(This relies heavily on the C Foreign Function Interface for Python (aka CFFI).)
 - UDF fusion
 - Parallelism
 - (And) Stateful UDFs.
-->

---
transition: fade
layout: two-cols
---

# Tracing JIT compilation & Parallelism


<v-click>

> "Tracing Just-In-Time (JIT) compilation is an approach to dynamic code optimization that focuses on identifying and optimizing the most frequently executed paths, or "hot paths," within a program. Instead of compiling entire methods or functions, tracing JIT compilers record and compile a sequence of instructions as the program runs, specifically targeting these hot paths to produce highly optimized machine code. This optimized code can then be reused in future executions, making frequently executed code much faster."


</v-click>
<br/>
<v-click> 

> Examples of tracing JIT implementations include PyPy (for Python) and LuaJIT (for Lua), both of which achieve significant performance gains by optimizing frequently executed paths in their respective interpreted languages.

</v-click>


::right::
<v-click>

<div style="position: relative; top: 6rem;left:2rem;text-align: center;">
  <span>Using PyPy Instead of CPython</span>
  <img src="https://pypy.org/images/pypy-logo.svg" style="height: auto;margin-top: 3rem">
</div>

</v-click>

<!--
So what is tracing JIT compilation?

Here's a quote from the ChatGPT summary. In a nutshell, this is a means of speeding up the function's operation

LuaJIT and Pypy provide an implementation of this kind of JIT scheme.

So the project can be accelerated by using PyPy(Pie-pie) instead of CPython without adding any extra code!
Meanwhile, although PyPy also has a GIL lock, the GIL lock will lazily released during CFFI conversions, so parallelisation won't be a problem
-->

---
transition: fade
---

# Seamless Integration with DBMS
>"UDFs are wrapped using embedded CFFI"
![](/Snipaste_2024-11-04_15-46-18.png)

<!--
As for the implementation of Seamless Integration with DBMS, YeSQL adopts CFFI as the solution.

We can see from this diagram, both systems support similar functionalities through scalar, aggregate, and table functions. The main difference comes from the way the Server Database and the Embedded Database running, the Server Database has a independent thread, so the UDF is registered to the UDF manager through the Server Database's C API, and then the UDF manager looks up the UDF when it is used. Embed Database has no independent thread, so the project takes SQLite as an example, and calls SQLite's C API to implement UDF through CFFI.
-->

---
transition: fade
---

# UDF Fusion

- Fusable UDFs
  - The second UDF’s input data is the same as the first UDF’s output 
  - The argument data types are available in the query plan

<v-click>

- Example: fuse two scalar UDFs

![](/Snipaste_2024-11-04_19-42-26.png)
</v-click>

<!--
For UDF Fusion, it looks more like an inline function in a programming language.

This picture showing the transformation of two separate Python functions ('len' and 'add') and their SQL usage into a more efficient implementation. The workflow moves from left to right, starting with individual Python function definitions and a SQL query, then transforms into a fused Python function using the '@ffi.def_extern()' decorator that combines both operations, and finally converts into CFFI with corresponding SQL syntax.
-->

---
transition: fade
layout: two-cols
---

# Stateful UDFs

>Stateful UDFs are functions that can maintain state (memory) across multiple invocations during data processing. Unlike regular UDFs which process each row independently, stateful UDFs can remember information from previous rows and use it in processing subsequent rows.

<br/>

<v-click>

>Key characteristics of Stateful UDFs:
>1. State Maintenance
>   - Can store and update variables between function calls
>   - Useful for running calculations, aggregations, or pattern detection
> 2.  Support in Different Systems:
>   - Apache Flink: Extensive support for stateful operations
>   - Apache Spark: Supported through mapGroupsWithState and flatMapGroupsWithState
>   - Many streaming systems support stateful processing

</v-click>

::right::
<v-click>

<div style="position: relative;left:2rem;top:3rem">


e.g., via a global dictionary

```python
globaldict = {}
def var(arg1, arg2 = None):
if arg2 is not None:
  globaldict[arg1] = arg2
  return True
else:
  return globaldict[arg1]
```

```sql
SELECT var(‘a’, ’HELLO WORLD’);
>> 1
SELECT lower(var(‘a’));
>> hello world
```

</div>

</v-click>

<!--
For Stateful UDF, in a nut shell, you can treat this as to store the contextual relationships of functions. It's a programme that trades space for time. 

You can see implementations of this on many Data Systems, such as Apache Spark and Apache Flink.

It's not  difficult to implement for coding, just add a global dictionary variable can achieve this goal.
-->

---
transition: fade
---

# Evaluation Results
- Significant performance improvements, achieving up to **68x speedups** compared to other traditional python methods.

<Figure1 />

<!--
Now, it's time to show you the Evaluation results:
It can achieving up to 68 times faster compared to other traditional python methods.

Some people may say that this comparison is not fair: PostgreSQL is a columnar database and MonetDB is a row-store database, so it is natural that row-store data is processed more efficiently. So I think it's more reasonable to use this as a comparison with PostgreSQL, so a relatively reasonable multiplier is 13.6x.
-->

---
layout: iframe-right
url: https://mocusez.site/zh-CN/posts/78ca1/
class: my-cool-content-on-the-left
---

# More Deatils Information

My Chinese Blog：<br/>
[如何让数据库中的Python跑的更快-VLDB22-YeSQL文章阅读](https://mocusez.site/zh-CN/posts/78ca1/)

Relative Information:<br/>
<Youtube id="NDPqO1o4Ba8" />
[31st Symposium on Advanced Database Systems](https://sebd2023.dei.unipd.it/)

<!--
For more information, you can read my Chinese blog, which contains more details about this paper. 

If I find more interesting information, I will update it as soon as possible. By the way, this also welcome for discuss.

The one of the author, Professor Yannis Ioannidis gave a talk on YeSQL at 31st Symposium on Advanced Database Systems at Italy. You can check this video and link which next to it for more information.
-->

---
layout: center
---

# Thanks for Listening
<PoweredBySlidev />

<!--
That's all for my presentation.
Thanks for listening.
-->
