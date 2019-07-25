# d3-selection

Selections allow powerful data-driven transformation of the document object model (DOM): set [attributes](#selection_attr), [styles](#selection_style), [properties](#selection_property), [HTML](#selection_html) or [text](#selection_text) content, and more. Using the [data join](#joining-data)’s [enter](#selection_enter) and [exit](#selection_enter) selections, you can also [add](#selection_append) or [remove](#selection_remove) elements to correspond to data.

Selections允许对文档对象模型(DOM)进行强大的数据驱动转换:设置[attributes](#selection_attr)、[styles](#selection_style)、[properties](#selection_property)、[HTML](#selection_html)或[text](#selection_text)内容，等等。使用[data join](#joining-data)的[enter](#selection_enter)和[exit](#selection_enter)选项，您还可以[add](#selection_append)或[remove](#selection_remove)元素来对应数据。

Selection methods typically return the current selection, or a new selection, allowing the concise application of multiple operations on a given selection via method chaining. For example, to set the class and color style of all paragraph elements in the current document:

选择方法通常返回当前选择或新选择，允许通过方法链对给定的选择进行多个操作的简洁应用。例如，要设置当前文档中所有段落元素的类和颜色样式:

```js
d3.selectAll("p")
    .attr("class", "graf")
    .style("color", "red");
```

This is equivalent to:

等价于：

```js
const p = d3.selectAll("p");
p.attr("class", "graf");
p.style("color", "red");
```

By convention, selection methods that return the current selection use *four* spaces of indent, while methods that return a new selection use only *two*. This helps reveal changes of context by making them stick out of the chain:

按照惯例，返回当前选择的选择方法使用*四个*缩进空格，而返回新选择的方法只使用*两个*缩进空格。这有助于揭示上下文的变化，使它们突出在链外:

```js
d3.select("body")
  .append("svg")
    .attr("width", 960)
    .attr("height", 500)
  .append("g")
    .attr("transform", "translate(20,20)")
  .append("rect")
    .attr("width", 920)
    .attr("height", 460);
```

Selections are immutable. All selection methods that affect which elements are selected (or their order) return a new selection rather than modifying the current selection. However, note that elements are necessarily mutable, as selections drive transformations of the document!

选择是不可变的。所有影响所选元素(或它们的顺序)的选择方法都会返回一个新的选择，而不是修改当前的选择。但是，请注意元素必须是可变的，因为选择驱动文档的转换!

## 安装

If you use NPM, `npm install d3-selection`. Otherwise, download the [latest release](https://github.com/d3/d3-selection/releases/latest). You can also load directly from [d3js.org](https://d3js.org), either as a [standalone library](https://d3js.org/d3-selection.v1.min.js) or as part of [D3 4.0](https://github.com/d3/d3). AMD, CommonJS, and vanilla environments are supported. In vanilla, a `d3` global is exported:

如果您使用NPM， `npm install d3-selection`。否则，请下载[最新版本](https://github.com/d3/d3-selection/releases/latest)。您还可以直接从[d3js.org](https://d3js.org)或作为[独立库](https://d3js.org/d3-selection.v1.min.js)或[D3 4.0](https://github.com/d3/d3)一部分加载。支持AMD、CommonJS和vanilla环境。在vanilla，一个`d3`全局出口:

```html
<script src="https://d3js.org/d3-selection.v1.min.js"></script>
<script>

const div = d3.selectAll("div");

</script>
```

[Try d3-selection in your browser.](https://tonicdev.com/npm/d3-selection)

[尝试在浏览器中使用d3-selection](https://tonicdev.com/npm/d3-selection)

## API参考

* [Selecting Elements](#selecting-elements)
* [Modifying Elements](#modifying-elements)
* [Joining Data](#joining-data)
* [Handling Events](#handling-events)
* [Control Flow](#control-flow)
* [Local Variables](#local-variables)
* [Namespaces](#namespaces)

### Selecting Elements

Selection methods accept [W3C selector strings](http://www.w3.org/TR/selectors-api/) such as `.fancy` to select elements with the class *fancy*, or `div` to select DIV elements. Selection methods come in two forms: select and selectAll: the former selects only the first matching element, while the latter selects all matching elements in document order. The top-level selection methods, [d3.select](#select) and [d3.selectAll](#selectAll), query the entire document; the subselection methods, [*selection*.select](#selection_select) and [*selection*.selectAll](#selection_selectAll), restrict selection to descendants of the selected elements.

选择方法接受[W3C选择器字符串](http://www.w3.org/TR/selectors-api/)，比如`.`以选择类`.fancy`的元素，或`div`以选择div元素。选择方法有两种形式:select和selectAll:前者只选择第一个匹配的元素，而后者按文档顺序选择所有匹配的元素。顶层选择方法[d3.select](#select)和[d3.selectAll](#selectAll)查询整个文档;子选择方法[*selection*.select](#selection_select)和[*selection*. selectall](#selection_selectAll)将选择限制为所选元素的后代。

<a name="selection" href="#selection">#</a> d3.<b>selection</b>() [<>](https://github.com/d3/d3-selection/blob/master/src/selection/index.js#L38 "Source")

[Selects](#select) the root element, `document.documentElement`. This function can also be used to test for selections (`instanceof d3.selection`) or to extend the selection prototype. For example, to add a method to check checkboxes:

[Selects](#select)根元素, `document.documentElement`。该函数还可以用于测试选择(`instanceof d3.selection`)或扩展选择原型。例如，要添加一个方法来选中复选框:

```js
d3.selection.prototype.checked = function(value) {
  return arguments.length < 1
      ? this.property("checked")
      : this.property("checked", !!value);
};
```

And then to use:

然后使用:

```js
d3.selectAll("input[type=checkbox]").checked(true);
```

<a name="select" href="#select">#</a> d3.<b>select</b>(<i>selector</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/select.js#L3 "Source")

Selects the first element that matches the specified *selector* string. If no elements match the *selector*, returns an empty selection. If multiple elements match the *selector*, only the first matching element (in document order) will be selected. For example, to select the first anchor element:

选择与指定的*选择器*字符串匹配的第一个元素。如果没有匹配的元素*选择器*，返回一个空的选择。如果多个元素匹配*selector*，则只选择第一个匹配的元素(按文档顺序)。例如，要选择第一个锚元素:

```js
const anchor = d3.select("a");
```

If the *selector* is not a string, instead selects the specified node; this is useful if you already have a reference to a node, such as `this` within an event listener or a global such as `document.body`. For example, to make a clicked paragraph red:

如果*selector*不是字符串，则选择指定的节点;如果您已经在事件侦听器中拥有对节点的引用(如`this`)或全局引用(如`document.body`)，这将非常有用。例如，让一个点击过的段落变成红色:

```js
d3.selectAll("p").on("click", function() {
  d3.select(this).style("color", "red");
});
```

<a name="selectAll" href="#selectAll">#</a> d3.<b>selectAll</b>(<i>selector</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/selectAll.js#L3 "Source")

Selects all elements that match the specified *selector* string. The elements will be selected in document order (top-to-bottom). If no elements in the document match the *selector*, or if the *selector* is null or undefined, returns an empty selection. For example, to select all paragraphs:

选择所有与指定的*选择器*字符串匹配的元素。元素将按文档顺序(从上到下)选择。如果文档中没有匹配*selector*的元素，或者如果*selector*为null或未定义，则返回一个空选择。例如，选择所有段落:

```js
const paragraph = d3.selectAll("p");
```

If the *selector* is not a string, instead selects the specified array of nodes; this is useful if you already have a reference to nodes, such as `this.childNodes` within an event listener or a global such as `document.links`. The nodes may instead be a pseudo-array such as a `NodeList` or `arguments`. For example, to color all links red:

如果*selector*不是字符串，则选择指定的节点数组;如果您已经有了对节点的引用，例如`this.childNodes`，那么这将非常有用。事件监听器或全局(如 `document.links`)中的子节点。节点可以是伪数组，如`NodeList`或`arguments`。例如，将所有链接都涂成红色:

```js
d3.selectAll(document.links).style("color", "red");
```

<a name="selection_select" href="#selection_select">#</a> <i>selection</i>.<b>select</b>(<i>selector</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/select.js "Source")

For each selected element, selects the first descendant element that matches the specified *selector* string. If no element matches the specified selector for the current element, the element at the current index will be null in the returned selection. (If the *selector* is null, every element in the returned selection will be null, resulting in an empty selection.) If the current element has associated data, this data is propagated to the corresponding selected element. If multiple elements match the selector, only the first matching element in document order is selected. For example, to select the first bold element in every paragraph:

对于每个选中的元素，选择与指定的*selector*字符串匹配的第一个子代元素。如果没有元素匹配当前元素的指定选择器，则返回的选择中当前索引处的元素将为null。(如果*selector*为空，返回的选择中的每个元素都将为空，从而产生一个空选择。)如果当前元素具有关联的数据，则将该数据传播到相应的选定元素。如果多个元素匹配选择器，则只选择文档顺序中第一个匹配的元素。例如，在每段中选择第一个粗体元素:

```js
const b = d3.selectAll("p").select("b");
```

If the *selector* is a function, it is evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). It must return an element, or null if there is no matching element. For example, to select the previous sibling of each paragraph:

如果*selector*是一个函数，那么将对每个选中的元素求值，依次传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。它必须返回一个元素，如果没有匹配的元素，则返回null。例如，要选择每个段落的前一个同级:

```js
const previous = d3.selectAll("p").select(function() {
  return this.previousElementSibling;
});
```

Unlike [*selection*.selectAll](#selection_selectAll), *selection*.select does not affect grouping: it preserves the existing group structure and indexes, and propagates data (if any) to selected children. Grouping plays an important role in the [data join](#joining-data). See [Nested Selections](http://bost.ocks.org/mike/nest/) and [How Selections Work](http://bost.ocks.org/mike/selection/) for more on this topic.

与[*selection*.selectAll](#selection_selectAll),*selection*.select不影响分组:它保留现有的组结构和索引，并将数据(如果有的话)传播给所选的子元素。分组在[data join](#joining-data)中扮演着重要的角色。有关此主题的更多信息，请参见[嵌套选择](http://bost.ocks.org/mike/nest/)和[选择如何工作](http://bost.ocks.org/mike/selection/)。

<a name="selection_selectAll" href="#selection_selectAll">#</a> <i>selection</i>.<b>selectAll</b>(<i>selector</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/selectAll.js "Source")

For each selected element, selects the descendant elements that match the specified *selector* string. The elements in the returned selection are grouped by their corresponding parent node in this selection. If no element matches the specified selector for the current element, or if the *selector* is null, the group at the current index will be empty. The selected elements do not inherit data from this selection; use [*selection*.data](#selection_data) to propagate data to children. For example, to select the bold elements in every paragraph:

对于每个选中的元素，选择匹配指定的*selector*字符串的后代元素。返回选择中的元素按其在此选择中的对应父节点进行分组。如果没有元素匹配当前元素的指定选择器，或者如果*选择器*为空，则当前索引处的组将为空。所选元素不从该选择中继承数据;使用[*selection*.data](#selection_data)将数据传播给子元素。例如，在每一段中选择粗体元素:

```js
const b = d3.selectAll("p").selectAll("b");
```

If the *selector* is a function, it is evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). It must return an array of elements (or a pseudo-array, such as a NodeList), or the empty array if there are no matching elements. For example, to select the previous and next siblings of each paragraph:

如果*selector*是一个函数，那么将对每个选中的元素求值，依次传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。它必须返回一个元素数组(或伪数组，如节点列表NodeList)，如果没有匹配的元素，则返回一个空数组。例如，要选择每个段落的前一个和下一个同级:

```js
const sibling = d3.selectAll("p").selectAll(function() {
  return [
    this.previousElementSibling,
    this.nextElementSibling
  ];
});
```

Unlike [*selection*.select](#selection_select), *selection*.selectAll does affect grouping: each selected descendant is grouped by the parent element in the originating selection. Grouping plays an important role in the [data join](#joining-data). See [Nested Selections](http://bost.ocks.org/mike/nest/) and [How Selections Work](http://bost.ocks.org/mike/selection/) for more on this topic.

与[*selection*.select](#selection_select)不同， *selection*.selectAll确实影响分组:每个选定的后代都是根据原始选择中的父元素分组的。分组在[data join](#joining-data)中扮演着重要的角色。有关此主题的更多信息，请参见[Nested Selections](http://bost.ocks.org/mike/nest/)和[How Selections Work](http://bost.ocks.org/mike/selection/)。

<a name="selection_filter" href="#selection_filter">#</a> <i>selection</i>.<b>filter</b>(<i>filter</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/filter.js "Source")

Filters the selection, returning a new selection that contains only the elements for which the specified *filter* is true. The *filter* may be specified either as a selector string or a function. If the *filter* is a function, it is evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]).

筛选选择，返回一个只包含指定的*filter*为true的元素的新选择。过滤器*可以指定为选择器字符串或函数。如果*filter*是一个函数，那么将对每个选中的元素求值，依次传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。

For example, to filter a selection of table rows to contain only even rows:

例如，过滤选择的表行以只包含偶数行:

```js
const even = d3.selectAll("tr").filter(":nth-child(even)");
```

This is approximately equivalent to using [d3.selectAll](#selectAll) directly, although the indexes may be different:
这大致相当于直接使用[d3.selectAll](#selectAll)，尽管索引可能不同:

```js
const even = d3.selectAll("tr:nth-child(even)");
```

Similarly, using a function:

类似地，使用函数:

```js
const even = d3.selectAll("tr").filter((d, i) => i & 1);
```

Or using [*selection*.select](#selection_select) (and avoiding an arrow function, since *this* is needed to refer to the current element):

或者使用[*selection*.select](#selection_select)(并避免使用箭头函数，因为*this*需要引用当前元素):

```js
const even = d3.selectAll("tr").select(function(d, i) { return i & 1 ? this : null; });
```

Note that the `:nth-child` pseudo-class is a one-based index rather than a zero-based index. Also, the above filter functions do not have precisely the same meaning as `:nth-child`; they rely on the selection index rather than the number of preceding sibling elements in the DOM.

注意，`:nth-child`伪类是基于1的索引，而不是基于0的索引。此外，上述过滤函数与`:nth-child`含义并不完全相同;它们依赖于选择索引，而不是DOM中前面兄弟元素的数量。

The returned filtered selection preserves the parents of this selection, but like [*array*.filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter), it does not preserve indexes as some elements may be removed; use [*selection*.select](#selection_select) to preserve the index, if needed.

返回的筛选选择保留了该选择的父元素，但是与[*array*.filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)，它不保留索引，因为某些元素可能被删除;如果需要，使用[*selection*.select](#selection_select)保存索引。

<a name="selection_merge" href="#selection_merge">#</a> <i>selection</i>.<b>merge</b>(<i>other</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/merge.js "Source")

Returns a new selection merging this selection with the specified *other* selection. The returned selection has the same number of groups and the same parents as this selection. Any missing (null) elements in this selection are filled with the corresponding element, if present (not null), from the specified *selection*. (If the *other* selection has additional groups or parents, they are ignored.)

返回一个新选择，该选择与指定的*other*选择合并。返回的选择与此选择具有相同数量的组和相同的父级。如果指定的*selection*中存在(非null)对应的元素，则该选择中任何缺少的(null)元素都将被填充。(如果*other*选择有其他组或父组，则忽略它们。)

This method is used internally by [*selection*.join](#selection_join) to merge the [enter](#selection_enter) and [update](#selection_data) selections after [binding data](#joining-data). You can also merge explicitly, although note that since merging is based on element index, you should use operations that preserve index, such as [*selection*.select](#selection_select) instead of [*selection*.filter](#selection_filter). For example:

这个方法由[*selection*.join](#selection_join)内部使用，在[binding data](#joining-data)之后合并[enter](#selection_enter)和[update](#selection_data)选项。您还可以显式地合并，不过请注意，由于合并是基于元素索引的，所以应该使用保存索引的操作，比如[*selection*.select](#selection_select)而不是[*selection*.filter](#selection_filter)。例如:

```js
const odd = selection.select(function(d, i) { return i & 1 ? this : null; ));
const even = selection.select(function(d, i) { return i & 1 ? null : this; ));
const merged = odd.merge(even);
```

See [*selection*.data](#selection_data) for more.

更多信息参见[*selection*.data](#selection_data).

This method is not intended for concatenating arbitrary selections, however: if both this selection and the specified *other* selection have (non-null) elements at the same index, this selection’s element is returned in the merge and the *other* selection’s element is ignored.

但是，此方法不用于连接任意选择:如果此选择和指定的*other*选择在同一索引中都有(非null)元素，则在merge中返回该选择的元素，而忽略*other*选择的元素。

<a name="matcher" href="#matcher">#</a> d3.<b>matcher</b>(<i>selector</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/matcher.js "Source")

Given the specified *selector*, returns a function which returns true if `this` element [matches](https://developer.mozilla.org/en-US/docs/Web/API/Element/matches) the specified selector. This method is used internally by [*selection*.filter](#selection_filter). For example, this:

给定指定的*selector*，返回一个函数，如果`this` 元素[matches](https://developer.mozilla.org/en-US/docs/Web/API/Element/matches)指定的选择器，该函数将返回true。此方法由[*selection*.filter](#selection_filter)在内部使用。例如,这个:

```js
const div = selection.filter("div");
```

Is equivalent to:

等价于:

```js
const div = selection.filter(d3.matcher("div"));
```

(Although D3 is not a compatibility layer, this implementation does support vendor-prefixed implementations due to the recent standardization of *element*.matches.)

(尽管D3不是一个兼容层，但是由于最近标准化了*element*.matches，所以这个实现支持供应商前缀的实现。)

<a name="selector" href="#selector">#</a> d3.<b>selector</b>(<i>selector</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/selector.js "Source")

Given the specified *selector*, returns a function which returns the first descendant of `this` element that matches the specified selector. This method is used internally by [*selection*.select](#selection_select). For example, this:

给定指定的*selector*，返回一个函数，该函数返回与指定选择器匹配的`this`元素的第一个后代。此方法由[*selection*.select](#selection_select)在内部使用。例如,这个:

```js
const div = selection.select("div");
```

Is equivalent to:

等价于:

```js
const div = selection.select(d3.selector("div"));
```

<a name="selectorAll" href="#selectorAll">#</a> d3.<b>selectorAll</b>(<i>selector</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/selectAll.js "Source")

Given the specified *selector*, returns a function which returns all descendants of `this` element that match the specified selector. This method is used internally by [*selection*.selectAll](#selection_selectAll). For example, this:

给定指定的*selector*，返回一个函数，该函数返回与指定选择器匹配的“this”元素的所有后代。该方法由[*selection*.selectAll](#selection_selectAll)内部使用。例如,这个:

```js
const div = selection.selectAll("div");
```

Is equivalent to:

等价于:

```js
const div = selection.selectAll(d3.selectorAll("div"));
```

<a name="window" href="#window">#</a> d3.<b>window</b>(<i>node</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/window.js "Source")

Returns the owner window for the specified *node*. If *node* is a node, returns the owner document’s default view; if *node* is a document, returns its default view; otherwise returns the *node*.

返回指定*节点*的所有者窗口。如果*node*是一个节点，返回所有者文档的默认视图;如果*node*是一个文档，返回它的默认视图;否则返回*node*。

<a name="style" href="#style">#</a> d3.<b>style</b>(<i>node</i>, <i>name</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/style.js#L32 "Source")

Returns the value of the style property with the specified *name* for the specified *node*. If the *node* has an inline style with the specified *name*, its value is returned; otherwise, the [computed property value](https://developer.mozilla.org/en-US/docs/Web/CSS/computed_value) is returned. See also [*selection*.style](#selection_style).

返回具有指定*节点*的指定*name*的style属性的值。如果*节点*具有指定的*name*内联样式，则返回其值;否则，将返回[computed property value](https://developer.mozilla.org/en-US/docs/Web/CSS/computed_value)。参见[*selection*.style](#selection_style)。

### Modifying Elements

After selecting elements, use the selection’s transformation methods to affect document content. For example, to set the name attribute and color style of an anchor element:

选择元素之后，使用选择的转换方法来影响文档内容。例如，要设置锚元素的name属性和颜色样式:

```js
d3.select("a")
    .attr("name", "fred")
    .style("color", "red");
```

To experiment with selections, visit [d3js.org](https://d3js.org) and open your browser’s developer console! (In Chrome, open the console with ⌥⌘J.) Select elements and then inspect the returned selection to see which elements are selected and how they are grouped. Call selection methods and see how the page content changes.

要尝试selections，请访问[d3js.org](https://d3js.org)并打开浏览器的开发人员控制台!(在Chrome浏览器,打开控制台与⌥⌘j .)选择元素，然后检查返回的选择，以查看选择了哪些元素以及它们是如何分组的。调用选择方法，查看页面内容如何更改。

<a name="selection_attr" href="#selection_attr">#</a> <i>selection</i>.<b>attr</b>(<i>name</i>[, <i>value</i>]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/attr.js "Source")

If a *value* is specified, sets the attribute with the specified *name* to the specified value on the selected elements and returns this selection. If the *value* is a constant, all elements are given the same attribute value; otherwise, if the *value* is a function, it is evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). The function’s return value is then used to set each element’s attribute. A null value will remove the specified attribute.

如果指定了*value*，则将具有指定*name*的属性设置为所选元素上的指定值，并返回该选择。如果*value*是常量，则所有元素都具有相同的属性值;否则，如果*value*是一个函数，则对每个选中的元素求值，顺序是传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。然后，函数的返回值用于设置每个元素的属性。空值将删除指定的属性。

If a *value* is not specified, returns the current value of the specified attribute for the first (non-null) element in the selection. This is generally useful only if you know that the selection contains exactly one element.

如果没有指定*value*，则返回选择中的第一个(非null)元素的指定属性的当前值。这通常只有在您知道选择只包含一个元素时才有用。

The specified *name* may have a namespace prefix, such as `xlink:href` to specify the `href` attribute in the XLink namespace. See [namespaces](#namespaces) for the map of supported namespaces; additional namespaces can be registered by adding to the map.

指定的*name*可能有一个名称空间前缀，例如 `xlink:href`来指定xlink名称空间中的`href`属性。支持的名称空间映射见[namespaces](#namespaces);可以通过向映射添加名称空间来注册其他名称空间。

<a name="selection_classed" href="#selection_classed">#</a> <i>selection</i>.<b>classed</b>(<i>names</i>[, <i>value</i>]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/classed.js "Source")

If a *value* is specified, assigns or unassigns the specified CSS class *names* on the selected elements by setting the `class` attribute or modifying the `classList` property and returns this selection. The specified *names* is a string of space-separated class names. For example, to assign the classes `foo` and `bar` to the selected elements:

如果指定了*value*，则通过设置 `class`属性或修改`classList`属性，在选定的元素上分配或取消分配指定的CSS类*names*，并返回该选择。指定的*names*是一串以空格分隔的类名。例如，要将类 `foo` 和 `bar`分配给所选的元素:

```js
selection.classed("foo bar", true);
```

If the *value* is truthy, then all elements are assigned the specified classes; otherwise, the classes are unassigned. If the *value* is a function, it is evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). The function’s return value is then used to assign or unassign classes on each element. For example, to randomly associate the class *foo* with on average half the selected elements:

如果*value*是true，那么所有元素都被分配到指定的类;否则，将不分配类。如果*value*是一个函数，则对每个选中的元素求值，顺序是:传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。然后，函数的返回值用于在每个元素上分配或取消分配类。例如，随机地将类*foo*与平均一半选择的元素相关联:

```js
selection.classed("foo", () => Math.random() > 0.5);
```

If a *value* is not specified, returns true if and only if the first (non-null) selected element has the specified *classes*. This is generally useful only if you know the selection contains exactly one element.

如果没有指定*value*，当且仅当第一个(非null)选择的元素具有指定的*classes*时，返回true。这通常只有在您知道选择只包含一个元素时才有用。

<a name="selection_style" href="#selection_style">#</a> <i>selection</i>.<b>style</b>(<i>name</i>[, <i>value</i>[, <i>priority</i>]]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/style.js "Source")

If a *value* is specified, sets the style property with the specified *name* to the specified value on the selected elements and returns this selection. If the *value* is a constant, then all elements are given the same style property value; otherwise, if the *value* is a function, it is evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). The function’s return value is then used to set each element’s style property. A null value will remove the style property. An optional *priority* may also be specified, either as null or the string `important` (without the exclamation point).

如果指定了*value*，则将具有指定*name*的style属性设置为所选元素上的指定值，并返回该选择。如果*value*是常量，那么所有元素都具有相同的样式属性值;否则，如果*value*是一个函数，则对每个选中的元素求值，顺序是传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。然后，函数的返回值用于设置每个元素的样式属性。空值将删除style属性。还可以指定一个可选的*priority*，可以是null，也可以是字符串`important` (没有感叹号)。

If a *value* is not specified, returns the current value of the specified style property for the first (non-null) element in the selection. The current value is defined as the element’s inline value, if present, and otherwise its [computed value](https://developer.mozilla.org/en-US/docs/Web/CSS/computed_value). Accessing the current style value is generally useful only if you know the selection contains exactly one element.

如果没有指定*value*，则返回选择中的第一个(非null)元素的指定样式属性的当前值。当前值定义为元素的内联值(如果存在)，否则为[computed value](https://developer.mozilla.org/en-US/docs/Web/CSS/computed_value)。只有当您知道所选内容恰好包含一个元素时，访问当前样式值才有用。

Caution: unlike many SVG attributes, CSS styles typically have associated units. For example, `3px` is a valid stroke-width property value, while `3` is not. Some browsers implicitly assign the `px` (pixel) unit to numeric values, but not all browsers do: IE, for example, throws an “invalid arguments” error!

注意:与许多SVG属性不同，CSS样式通常具有关联的单元。例如，`3px`是一个有效的笔画宽度属性值，而`3`不是。有些浏览器隐式地将`px`(像素)单元赋给数值，但并非所有浏览器都这样做:例如，IE会抛出一个“无效参数”错误!

<a name="selection_property" href="#selection_property">#</a> <i>selection</i>.<b>property</b>(<i>name</i>[, <i>value</i>]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/property.js "Source")

Some HTML elements have special properties that are not addressable using attributes or styles, such as a form field’s text `value` and a checkbox’s `checked` boolean. Use this method to get or set these properties.

一些HTML元素具有特殊的属性，这些属性不能使用属性或样式寻址，比如表单字段的文本`value`和复选框的`checked`布尔值。使用此方法获取或设置这些属性。

If a *value* is specified, sets the property with the specified *name* to the specified value on selected elements. If the *value* is a constant, then all elements are given the same property value; otherwise, if the *value* is a function, it is evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). The function’s return value is then used to set each element’s property. A null value will delete the specified property.

如果指定了*value*，则将具有指定*name*的属性设置为所选元素上的指定值。如果*value*是常量，则所有元素都具有相同的属性值;否则，如果*value*是一个函数，则对每个选中的元素求值，顺序是传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。然后，函数的返回值用于设置每个元素的属性。空值将删除指定的属性。

If a *value* is not specified, returns the value of the specified property for the first (non-null) element in the selection. This is generally useful only if you know the selection contains exactly one element.

如果没有指定*value*，则返回选择中的第一个(非null)元素的指定属性的值。这通常只有在您知道选择只包含一个元素时才有用。

<a name="selection_text" href="#selection_text">#</a> <i>selection</i>.<b>text</b>([<i>value</i>]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/text.js "Source")

If a *value* is specified, sets the [text content](http://www.w3.org/TR/DOM-Level-3-Core/core.html#Node3-textContent) to the specified value on all selected elements, replacing any existing child elements. If the *value* is a constant, then all elements are given the same text content; otherwise, if the *value* is a function, it is evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). The function’s return value is then used to set each element’s text content. A null value will clear the content.

如果指定了*value*，将[text content](http://www.w3.org/TR/DOM-Level-3-Core/core.html#Node3-textContent)设置为所有选定元素上的指定值，替换任何现有的子元素。如果*value*是常量，则所有元素都具有相同的文本内容;否则，如果*value*是一个函数，则对每个选中的元素求值，顺序是传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。然后，函数的返回值用于设置每个元素的文本内容。空值将清除内容。

If a *value* is not specified, returns the text content for the first (non-null) element in the selection. This is generally useful only if you know the selection contains exactly one element.

如果没有指定*value*，则返回选择中的第一个(非null)元素的文本内容。这通常只有在您知道选择只包含一个元素时才有用。

<a name="selection_html" href="#selection_html">#</a> <i>selection</i>.<b>html</b>([<i>value</i>]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/html.js "Source")

If a *value* is specified, sets the [inner HTML](http://dev.w3.org/html5/spec-LC/apis-in-html-documents.html#innerhtml) to the specified value on all selected elements, replacing any existing child elements. If the *value* is a constant, then all elements are given the same inner HTML; otherwise, if the *value* is a function, it is evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). The function’s return value is then used to set each element’s inner HTML. A null value will clear the content.

如果指定了*value*，将[inner HTML](http://dev.w3.org/html5/spec-LC/apis-in-html-documents.html#innerhtml)设置为所有选定元素上的指定值，替换任何现有的子元素。如果*value*是常量，那么所有元素都被赋予相同的内部HTML;否则，如果*value*是一个函数，则对每个选中的元素求值，顺序是传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。然后，函数的返回值用于设置每个元素的内部HTML。空值将清除内容。

If a *value* is not specified, returns the inner HTML for the first (non-null) element in the selection. This is generally useful only if you know the selection contains exactly one element.

如果没有指定*value*，则返回选择中的第一个(非null)元素的内部HTML。这通常只有在您知道选择只包含一个元素时才有用。

Use [*selection*.append](#selection_append) or [*selection*.insert](#selection_insert) instead to create data-driven content; this method is intended for when you want a little bit of HTML, say for rich formatting. Also, *selection*.html is only supported on HTML elements. SVG elements and other non-HTML elements do not support the innerHTML property, and thus are incompatible with *selection*.html. Consider using [XMLSerializer](https://developer.mozilla.org/en-US/docs/XMLSerializer) to convert a DOM subtree to text. See also the [innersvg polyfill](https://code.google.com/p/innersvg/), which provides a shim to support the innerHTML property on SVG elements.

使用[*selection*.append](#selection_append)或[*selection*.insert](#selection_insert)创建数据驱动的内容;此方法适用于需要少量HTML的情况，比如用于富格式。此外，*selection*. HTML只支持HTML元素。SVG元素和其他非html元素不支持innerHTML属性，因此与*selection*.html不兼容。考虑使用[XMLSerializer](https://developer.mozilla.org/en-US/docs/XMLSerializer)将DOM子树转换为文本。还请参见[innersvg polyfill](https://code.google.com/p/innersvg/)，它提供了一个shim来支持SVG元素上的innerHTML属性。

<a name="selection_append" href="#selection_append">#</a> <i>selection</i>.<b>append</b>(<i>type</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/append.js "Source")

If the specified *type* is a string, appends a new element of this type (tag name) as the last child of each selected element, or before the next following sibling in the update selection if this is an [enter selection](#selection_enter). The latter behavior for enter selections allows you to insert elements into the DOM in an order consistent with the new bound data; however, note that [*selection*.order](#selection_order) may still be required if updating elements change order (*i.e.*, if the order of new data is inconsistent with old data).

如果指定的*type*是字符串，则将此类型的新元素(标记名)追加为每个选定元素的最后一个子元素，如果这是[enter selection](#selection_enter)，则追加到更新选择中的下一个同级元素之前。后一种用于enter选择的行为允许您按照与新绑定数据一致的顺序将元素插入DOM;但是，请注意，如果更新元素更改了顺序，[*selection*.order](#selection_order)仍然需要(如果新数据的顺序与旧数据不一致)。

If the specified *type* is a function, it is evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). This function should return an element to be appended. (The function typically creates a new element, but it may instead return an existing element.) For example, to append a DIV element to each paragraph:

如果指定的*type*是一个函数，则对每个选定的元素求值，顺序是传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。这个函数应该返回要追加的元素。(该函数通常创建一个新元素，但它可能返回一个现有元素。)例如，在每段后面加上DIV元素:

```js
d3.selectAll("p").append("div");
```

This is equivalent to:

等价于:

```js
d3.selectAll("p").append(() => document.createElement("div"));
```

Which is equivalent to:

等价于:

```js
d3.selectAll("p").select(function() {
  return this.appendChild(document.createElement("div"));
});
```

In both cases, this method returns a new selection containing the appended elements. Each new element inherits the data of the current elements, if any, in the same manner as [*selection*.select](#selection_select).

在这两种情况下，该方法都返回一个包含附加元素的新选择。每个新元素都继承当前元素的数据(如果有的话)，方法与[*selection*.select](#selection_select)相同。

The specified *name* may have a namespace prefix, such as `svg:text` to specify a `text` attribute in the SVG namespace. See [namespaces](#namespaces) for the map of supported namespaces; additional namespaces can be registered by adding to the map. If no namespace is specified, the namespace will be inherited from the parent element; or, if the name is one of the known prefixes, the corresponding namespace will be used (for example, `svg` implies `svg:svg`).

指定的*name*可能具有名称空间前缀，例如`svg:text` ，以便在svg名称空间中指定`text`属性。支持的名称空间映射见[namespaces](#namespaces);可以通过向映射添加名称空间来注册其他名称空间。如果没有指定名称空间，则该名称空间将从父元素继承;或者，如果名称是已知的前缀之一，则使用相应的名称空间(例如，`svg`表示`svg:svg`)。

<a name="selection_insert" href="#selection_insert">#</a> <i>selection</i>.<b>insert</b>(<i>type</i>[, <i>before</i>]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/insert.js "Source")

If the specified *type* is a string, inserts a new element of this type (tag name) before the first element matching the specified *before* selector for each selected element. For example, a *before* selector `:first-child` will prepend nodes before the first child. If *before* is not specified, it defaults to null. (To append elements in an order consistent with [bound data](#joining-data), use [*selection*.append](#selection_append).)

如果指定的*type*是字符串，则在与每个选定元素的指定*before*选择器匹配的第一个元素之前插入此类型的新元素(标记名)。例如，一个*before* selector ':first-child '将把节点放在第一个子节点之前。如果没有指定*before*，则默认为null。(要按照与[bound data](#joining-data)一致的顺序追加元素，请使用[*selection*.append](#selection_append)。)

Both *type* and *before* may instead be specified as functions which are evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). The *type* function should return an element to be inserted; the *before* function should return the child element before which the element should be inserted. For example, to append a DIV element to each paragraph:

可以将*type*和*before*都指定为函数，这些函数将为每个选定的元素求值，依次传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。函数应该返回要插入的元素;函数应该返回插入元素之前的子元素。例如，在每段后面加上DIV元素:

```js
d3.selectAll("p").insert("div");
```

This is equivalent to:

等价于:

```js
d3.selectAll("p").insert(() => document.createElement("div"));
```

Which is equivalent to:

等价于:

```js
d3.selectAll("p").select(function() {
  return this.insertBefore(document.createElement("div"), null);
});
```

In both cases, this method returns a new selection containing the appended elements. Each new element inherits the data of the current elements, if any, in the same manner as [*selection*.select](#selection_select).

在这两种情况下，该方法都返回一个包含附加元素的新选择。每个新元素都继承当前元素的数据(如果有的话)，方法与[*selection*.select](#selection_select)相同。

The specified *name* may have a namespace prefix, such as `svg:text` to specify a `text` attribute in the SVG namespace. See [namespaces](#namespaces) for the map of supported namespaces; additional namespaces can be registered by adding to the map. If no namespace is specified, the namespace will be inherited from the parent element; or, if the name is one of the known prefixes, the corresponding namespace will be used (for example, `svg` implies `svg:svg`).

指定的*name*可能具有名称空间前缀，例如`svg:text`，以便在svg名称空间中指定`text`属性。支持的名称空间映射见[namespaces](#namespaces);可以通过向映射添加名称空间来注册其他名称空间。如果没有指定名称空间，则该名称空间将从父元素继承;或者，如果名称是已知的前缀之一，则使用相应的名称空间(例如，`svg`表示`svg:svg`)。

<a name="selection_remove" href="#selection_remove">#</a> <i>selection</i>.<b>remove</b>() [<>](https://github.com/d3/d3-selection/blob/master/src/selection/remove.js "Source")

Removes the selected elements from the document. Returns this selection (the removed elements) which are now detached from the DOM. There is not currently a dedicated API to add removed elements back to the document; however, you can pass a function to [*selection*.append](#selection_append) or [*selection*.insert](#selection_insert) to re-add elements.

从文档中删除选定的元素。返回这个选择(已删除的元素)，它现在与DOM分离。目前没有专门的API将删除的元素添加回文档;但是，您可以将一个函数传递给[*selection*.append](#selection_append)或[*selection*.insert](#selection_insert)来重新添加元素。

<a name="selection_clone" href="#selection_clone">#</a> <i>selection</i>.<b>clone</b>([<i>deep</i>]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/clone.js "Source")

Inserts clones of the selected elements immediately following the selected elements and returns a selection of the newly added clones. If *deep* is truthy, the descendant nodes of the selected elements will be cloned as well. Otherwise, only the elements themselves will be cloned. Equivalent to:

在选定的元素之后立即插入选定元素的克隆，并返回新添加的克隆的选定项。如果*deep*为真，则也将克隆所选元素的后代节点。否则，只克隆元素本身。等价于:

```js
selection.select(function() {
  return this.parentNode.insertBefore(this.cloneNode(deep), this.nextSibling);
});
```

<a name="selection_sort" href="#selection_sort">#</a> <i>selection</i>.<b>sort</b>(<i>compare</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/sort.js "Source")

Returns a new selection that contains a copy of each group in this selection sorted according to the *compare* function. After sorting, re-inserts elements to match the resulting order (per [*selection*.order](#selection_order)).

返回一个新的选择，该选择中包含根据*compare*函数排序的每个组的副本。排序之后，重新插入元素以匹配结果的顺序(per [*selection*.order](#selection_order))。

The compare function, which defaults to [ascending](https://github.com/d3/d3-array#ascending), is passed two elements’ data *a* and *b* to compare. It should return either a negative, positive, or zero value. If negative, then *a* should be before *b*; if positive, then *a* should be after *b*; otherwise, *a* and *b* are considered equal and the order is arbitrary.

compare函数默认值为[ascending](https://github.com/d3/d3-array#ascending)，传递两个元素的数据*a*和*b*进行比较。它应该返回一个负值、正值或零值。如果是负数，则*a*应在*b*之前;如果是正数，那么*a*应该在*b*之后;否则，*a*和*b*被认为是相等的，顺序是任意的。

Note that sorting is not guaranteed to be stable; however, it is guaranteed to have the same behavior as your browser’s built-in [sort](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Array/sort) method on arrays.

注意，排序不能保证稳定;但是，它保证在数组上具有与浏览器内置的[sort](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Array/sort)方法相同的行为。

<a name="selection_order" href="#selection_order">#</a> <i>selection</i>.<b>order</b>() [<>](https://github.com/d3/d3-selection/blob/master/src/selection/order.js "Source")

Re-inserts elements into the document such that the document order of each group matches the selection order. This is equivalent to calling [*selection*.sort](#selection_sort) if the data is already sorted, but much faster.

将元素重新插入文档，以便每个组的文档顺序与选择顺序匹配。这相当于在数据已经排序的情况下调用[*selection*.sort](#selection_sort)，但是要快得多。

<a name="selection_raise" href="#selection_raise">#</a> <i>selection</i>.<b>raise</b>() [<>](https://github.com/d3/d3-selection/blob/master/src/selection/raise.js "Source")

Re-inserts each selected element, in order, as the last child of its parent. Equivalent to:

按顺序将每个选定的元素作为其父元素的最后一个子元素重新插入。等价于:

```js
selection.each(function() {
  this.parentNode.appendChild(this);
});
```

<a name="selection_lower" href="#selection_lower">#</a> <i>selection</i>.<b>lower</b>() [<>](https://github.com/d3/d3-selection/blob/master/src/selection/lower.js "Source")

Re-inserts each selected element, in order, as the first child of its parent. Equivalent to:

按顺序将每个选定的元素作为其父元素的第一个子元素重新插入。等价于:

```js
selection.each(function() {
  this.parentNode.insertBefore(this, this.parentNode.firstChild);
});
```

<a name="create" href="#create">#</a> d3.<b>create</b>(<i>name</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/create.js "Source")

Given the specified element *name*, returns a single-element selection containing a detached element of the given name in the current document.

给定指定元素*name*，返回一个单元素选择，其中包含当前文档中给定名称的分离元素。

<a name="creator" href="#creator">#</a> d3.<b>creator</b>(<i>name</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/creator.js "Source")

Given the specified element *name*, returns a function which creates an element of the given name, assuming that `this` is the parent element. This method is used internally by [*selection*.append](#selection_append) and [*selection*.insert](#selection_insert) to create new elements. For example, this:

给定指定的元素*name*，返回一个函数，该函数创建给定名称的元素，假设“this”是父元素。[*selection*.append](#selection_append)和[*selection*.insert](#selection_insert)在内部使用此方法创建新元素。例如,这个:

```js
selection.append("div");
```

Is equivalent to:

等价于：

```js
selection.append(d3.creator("div"));
```

See [namespace](#namespace) for details on supported namespace prefixes, such as for SVG elements.

### Joining Data

For an introduction to D3’s data joins, see the [*selection*.join notebook](https://observablehq.com/@d3/selection-join). Also see [Thinking With Joins](http://bost.ocks.org/mike/join/).

有关D3数据连接的介绍，请参见[*selection*.join notebook](https://observablehq.com/@d3/selection-join)。还请参见[Thinking With Joins](http://bost.ocks.org/mike/join/)。

<a name="selection_data" href="#selection_data">#</a> <i>selection</i>.<b>data</b>([<i>data</i>[, <i>key</i>]]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/data.js "Source")

Binds the specified array of *data* with the selected elements, returning a new selection that represents the *update* selection: the elements successfully bound to data. Also defines the [enter](#selection_enter) and [exit](#selection_exit) selections on the returned selection, which can be used to add or remove elements to correspond to the new data. The specified *data* is an array of arbitrary values (*e.g.*, numbers or objects), or a function that returns an array of values for each group. When data is assigned to an element, it is stored in the property `__data__`, thus making the data “sticky” and available on re-selection.

将指定的*data*数组与所选元素绑定，返回一个表示*update*选择的新选择:成功绑定到数据的元素。还在返回的选择中定义了[enter](#selection_enter)和[exit](#selection_exit)选项，可用于添加或删除与新数据相对应的元素。指定的*data*是一个任意值的数组(*例如。或返回每个组的值数组的函数。当数据被分配给一个元素时，它被存储在属性`__data__`中，从而使数据具有“粘性”，并且在重新选择时可用。

The *data* is specified **for each group** in the selection. If the selection has multiple groups (such as [d3.selectAll](#selectAll) followed by [*selection*.selectAll](#selection_selectAll)), then *data* should typically be specified as a function. This function will be evaluated for each group in order, being passed the group’s parent datum (*d*, which may be undefined), the group index (*i*), and the selection’s parent nodes (*nodes*), with *this* as the group’s parent element.

为选择中的**每个组**指定*data*。如果选择有多个组(比如[d3.selectAll](#selectAll)和[*selection*.selectAll](#selection_selectAll))，那么通常应该将*data*指定为一个函数。这个函数将按顺序为每个组计算，传递组的父数据(*d*，可能没有定义)、组索引(*i*)和选择的父节点(*nodes*)，其中* This *作为组的父元素。

In conjunction with [*selection*.join](#selection_join) (or more explicitly with [*selection*.enter](#selection_enter), [*selection*.exit](#selection_exit), [*selection*.append](#selection_append) and [*selection*.remove](#selection_remove)), *selection*.data can be used to enter, update and exit elements to match data. For example, to create an HTML table from a matrix of numbers:

与[*selection*.join](#selection_join)(或者更显式地与[*selection*.enter](#selection_enter)、[*selection*.exit](#selection_exit)、[*selection*.append](#selection_append)和[*selection*.remove](#selection_remove)一起使用，*selection*。数据可用于输入、更新和退出元素以匹配数据。例如，从数字矩阵创建HTML表:

```js
const matrix = [
  [11975,  5871, 8916, 2868],
  [ 1951, 10048, 2060, 6171],
  [ 8010, 16145, 8090, 8045],
  [ 1013,   990,  940, 6907]
];

d3.select("body")
  .append("table")
  .selectAll("tr")
  .data(matrix)
  .join("tr")
  .selectAll("td")
  .data(d => d)
  .join("td")
    .text(d => d);
```

In this example the *data* function is the identity function: for each table row, it returns the corresponding row from the data matrix.

在本例中，*data*函数是恒等函数:对于每个表行，它从数据矩阵返回相应的行。

If a *key* function is not specified, then the first datum in *data* is assigned to the first selected element, the second datum to the second selected element, and so on. A *key* function may be specified to control which datum is assigned to which element, replacing the default join-by-index, by computing a string identifier for each datum and element. This key function is evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]); the returned string is the element’s key. The key function is then also evaluated for each new datum in *data*, being passed the current datum (*d*), the current index (*i*), and the group’s new *data*, with *this* as the group’s parent DOM element; the returned string is the datum’s key. The datum for a given key is assigned to the element with the matching key. If multiple elements have the same key, the duplicate elements are put into the exit selection; if multiple data have the same key, the duplicate data are put into the enter selection.

如果没有指定*key*函数，则将*data*中的第一个数据分配给第一个选中的元素，将第二个数据分配给第二个选中的元素，依此类推。可以指定一个*key*函数，通过计算每个数据和元素的字符串标识符来控制将哪个数据分配给哪个元素，从而取代默认的按索引连接。对每个选中的元素求此键函数的值，依次传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中* This *为当前DOM元素(*nodes*[*i*]);返回的字符串是元素的键。然后对*data*中的每个新数据求key函数的值，传递当前数据(*d*)、当前索引(*i*)和组的新*data*，其中*this*作为组的父DOM元素;返回的字符串是数据的键。给定键的数据被分配给具有匹配键的元素。如果多个元素具有相同的键，则将重复的元素放入退出选择;如果多个数据具有相同的键，则将重复的数据放入enter选择项。

For example, given this document:

例如，给定这个文档:

```html
<div id="Ford"></div>
<div id="Jarrah"></div>
<div id="Kwon"></div>
<div id="Locke"></div>
<div id="Reyes"></div>
<div id="Shephard"></div>
```

You could join data by key as follows:

您可以按以下键连接数据:


```js
const data = [
  {name: "Locke", number: 4},
  {name: "Reyes", number: 8},
  {name: "Ford", number: 15},
  {name: "Jarrah", number: 16},
  {name: "Shephard", number: 23},
  {name: "Kwon", number: 42}
];

d3.selectAll("div")
  .data(data, function(d) { return d ? d.name : this.id; })
    .text(d => d.number);
```

This example key function uses the datum *d* if present, and otherwise falls back to the element’s id property. Since these elements were not previously bound to data, the datum *d* is null when the key function is evaluated on selected elements, and non-null when the key function is evaluated on the new data.

这个示例键函数使用datum *d* if，否则返回到元素的id属性。由于这些元素以前没有绑定到数据，所以当关键函数在选定的元素上求值时，datum *d*为null，而当关键函数在新数据上求值时，datum *d*为非null。

The *update* and *enter* selections are returned in data order, while the *exit* selection preserves the selection order prior to the join. If a key function is specified, the order of elements in the selection may not match their order in the document; use [*selection*.order](#order) or [*selection*.sort](#sort) as needed. For more on how the key function affects the join, see [A Bar Chart, Part 2](http://bost.ocks.org/mike/bar/2/) and [Object Constancy](http://bost.ocks.org/mike/constancy/).

*update*和*enter*选择按数据顺序返回，而*exit*选择保留连接之前的选择顺序。如果指定了键函数，则所选元素的顺序可能与其在文档中的顺序不匹配;根据需要使用[*selection*.order](#order)或[*selection*.sort](#sort)。有关键函数如何影响连接的详细信息，请参见[A Bar Chart, Part 2](http://bost.ocks.org/mike/bar/2/)和[Object Constancy](http://bost.ocks.org/mike/constancy/)。

If *data* is not specified, this method returns the array of data for the selected elements.

如果没有指定*data*，则此方法返回所选元素的数据数组。

This method cannot be used to clear bound data; use [*selection*.datum](#selection_datum) instead.

此方法不能用于清除绑定数据;使用[*selection*.datum](#selection_datum)。

<a name="selection_join" href="#selection_join">#</a> <i>selection</i>.<b>join</b>(<i>enter</i>[, <i>update</i>][, <i>exit</i>]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/join.js "Source")

Appends, removes and reorders elements as necessary to match the data that was previously bound by [*selection*.data](#selection_data), returning the [merged](#selection_merge) enter and update selection. This method is a convenient alternative to the explicit [general update pattern](https://bl.ocks.org/mbostock/3808218), replacing [*selection*.enter](#selection_enter), [*selection*.exit](#selection_exit), [*selection*.append](#selection_append), [*selection*.remove](#selection_remove), and [*selection*.order](#selection_order). For example:

根据需要添加、删除和重新排序元素，以匹配以前由[*selection*.data](#selection_data)绑定的数据，返回[merge](#selection_merge) enter和update选项。该方法是一种方便的选择明确的[general update pattern](https://bl.ocks.org/mbostock/3808218),取代[*selection*.enter](#selection_enter),[*selection*.exit](#selection_exit),[*selection*.append](#selection_append),[*selection*.remove](#selection_remove)和[*selection*.order](#selection_order)。例如:

```js
svg.selectAll("circle")
  .data(data)
  .join("circle")
    .attr("fill", "none")
    .attr("stroke", "black");
```

To control what happens on enter, update and exit, pass separate functions instead of a string:

要控制回车、更新和退出时发生的事情，传递单独的函数而不是字符串:

```js
svg.selectAll("circle")
  .data(data)
  .join(
    enter => enter.append("circle").attr("fill", "green"),
    update => update.attr("fill", "blue")
  )
    .attr("stroke", "black");
```

You can pass a third function for exit, too. The returned enter and update selections are again merged and returned by *selection*.join. By separating enter and update, and by specifying a key function to [*selection*.data](#selection_data), you can minimize changes to the DOM to optimize performance.

您还可以传递第三个函数作为exit。返回的enter和update选项再次合并，并由*selection*.join返回。通过分离enter和update，并通过为[*selection*.data](#selection_data)指定一个关键函数，可以最小化对DOM的更改，从而优化性能。

You also animate enter, update and exit by creating transitions inside the *enter*, *update* and *exit* functions. To avoid breaking the method chain, use *selection*.call to create transitions, or return an undefined enter or update selection to prevent merging: the return value of the *enter* and *update* functions specifies the two selections to merge and return by *selection*.join.

您还可以通过在*enter*、*update*和*exit*函数中创建转换来动画enter、update和exit。为了避免打破方法链，使用*selection*。调用创建转换，或者返回一个未定义的enter或update选项以防止合并:*enter*和*update*函数的返回值指定要合并的两个选项，并通过*selection*.join返回。

For more, see the [*selection*.join notebook](https://observablehq.com/@d3/selection-join).

更重要的是，参见[*selection*.join notebook](https://observablehq.com/@d3/selection-join)。

<a name="selection_enter" href="#selection_enter">#</a> <i>selection</i>.<b>enter</b>() [<>](https://github.com/d3/d3-selection/blob/master/src/selection/enter.js "Source")

Returns the enter selection: placeholder nodes for each datum that had no corresponding DOM element in the selection. (The enter selection is empty for selections not returned by [*selection*.data](#selection_data).)

返回回车选择:对于选择中没有对应DOM元素的每个数据的占位符节点。(对于[*selection*.data](#selection_data)没有返回的选项，enter选项为空。)

The enter selection is typically used to create “missing” elements corresponding to new data. For example, to create DIV elements from an array of numbers:

enter选项通常用于创建与新数据对应的“缺失”元素。例如，从一个数字数组创建DIV元素:

```js
const div = d3.select("body")
  .selectAll("div")
  .data([4, 8, 15, 16, 23, 42])
  .enter().append("div")
    .text(d => d);
```

If the body is initially empty, the above code will create six new DIV elements, append them to the body in-order, and assign their text content as the associated (string-coerced) number:

如果正文最初是空的，上面的代码将创建6个新的DIV元素，按顺序将它们附加到正文中，并将它们的文本内容指定为关联的(字符串强制的)编号:

```html
<div>4</div>
<div>8</div>
<div>15</div>
<div>16</div>
<div>23</div>
<div>42</div>
```

Conceptually, the enter selection’s placeholders are pointers to the parent element (in this example, the document body). The enter selection is typically only used transiently to append elements, and is often [merged](#selection_merge) with the update selection after appending, such that modifications can be applied to both entering and updating elements.

从概念上讲，enter选择的占位符是指向父元素的指针(在本例中是文档主体)。enter选择通常只被短暂地用于附加元素，并且通常在附加之后[merge](#selection_merge)与update选择一起使用，这样修改就可以同时应用于输入和更新元素。

<a name="selection_exit" href="#selection_exit">#</a> <i>selection</i>.<b>exit</b>() [<>](https://github.com/d3/d3-selection/blob/master/src/selection/exit.js "Source")

Returns the exit selection: existing DOM elements in the selection for which no new datum was found. (The exit selection is empty for selections not returned by [*selection*.data](#selection_data).)

返回退出选择:选择中没有找到新数据的现有DOM元素。(对于[*selection*.data](#selection_data)没有返回的选择，退出选择为空。)

The exit selection is typically used to remove “superfluous” elements corresponding to old data. For example, to update the DIV elements created previously with a new array of numbers:

出口选择通常用于删除与旧数据对应的“多余”元素。例如，要用一个新的数字数组更新以前创建的DIV元素:

```js
div = div.data([1, 2, 4, 8, 16, 32], d => d);
```

Since a key function was specified (as the identity function), and the new data contains the numbers [4, 8, 16] which match existing elements in the document, the update selection contains three DIV elements. Leaving those elements as-is, we can append new elements for [1, 2, 32] using the enter selection:

由于指定了一个键函数(作为标识函数)，并且新数据包含与文档中现有元素匹配的数字[4,8,16]，因此update选择包含三个DIV元素。让这些元素保持原样，我们可以使用回车选择为[1,2,32]添加新元素:

```js
div.enter().append("div").text(d => d);
```

Likewise, to remove the exiting elements [15, 23, 42]:

同样，删除现有元素[15,23,42]:

```js
div.exit().remove();
```

Now the document body looks like this:

现在文档主体是这样的:

```html
<div>1</div>
<div>2</div>
<div>4</div>
<div>8</div>
<div>16</div>
<div>32</div>
```

The order of the DOM elements matches the order of the data because the old data’s order and the new data’s order were consistent. If the new data’s order is different, use [*selection*.order](#selection_order) to reorder the elements in the DOM. See the [General Update Pattern](http://bl.ocks.org/mbostock/3808218) example thread for more on data joins.

DOM元素的顺序与数据的顺序匹配，因为旧数据的顺序与新数据的顺序是一致的。如果新数据的顺序不同，使用[*selection*.order](#selection_order)对DOM中的元素重新排序。有关数据连接的更多信息，请参见[General Update Pattern](http://bl.ocks.org/mbostock/3808218)示例线程。

<a name="selection_datum" href="#selection_datum">#</a> <i>selection</i>.<b>datum</b>([<i>value</i>]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/datum.js "Source")

Gets or sets the bound data for each selected element. Unlike [*selection*.data](#selection_data), this method does not compute a join and does not affect indexes or the enter and exit selections.

获取或设置每个选定元素的绑定数据。与[*selection*.data](#selection_data)不同，此方法不计算连接，也不影响索引或enter和exit选择。

If a *value* is specified, sets the element’s bound data to the specified value on all selected elements. If the *value* is a constant, all elements are given the same datum; otherwise, if the *value* is a function, it is evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). The function is then used to set each element’s new data. A null value will delete the bound data.

如果指定了*value*，则将元素的绑定数据设置为所有选定元素上的指定值。如果*value*为常量，则所有元素都给定相同的基准面;否则，如果*value*是一个函数，则对每个选中的元素求值，顺序是传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。然后，函数用于设置每个元素的新数据。空值将删除绑定的数据。

If a *value* is not specified, returns the bound datum for the first (non-null) element in the selection. This is generally useful only if you know the selection contains exactly one element.

如果没有指定*value*，则返回选择中的第一个(非null)元素的绑定数据。这通常只有在您知道选择只包含一个元素时才有用。

This method is useful for accessing HTML5 [custom data attributes](http://www.w3.org/TR/html5/dom.html#custom-data-attribute). For example, given the following elements:

这个方法对于访问HTML5 [custom data attributes](http://www.w3.org/TR/html5/dom.html#custom-data-attribute)很有用。例如，给定以下元素:

```html
<ul id="list">
  <li data-username="shawnbot">Shawn Allen</li>
  <li data-username="mbostock">Mike Bostock</li>
</ul>
```

You can expose the custom data attributes by setting each element’s data as the built-in [dataset](http://www.w3.org/TR/html5/dom.html#dom-dataset) property:

您可以通过将每个元素的数据设置为内置的[dataset](http://www.w3.org/TR/html5/dom.html#dom-dataset)属性来公开自定义数据属性:

```js
selection.datum(function() { return this.dataset; })
```

### Handling Events

For interaction, selections allow listening for and dispatching of events.

对于交互，selections允许监听和分派事件。

<a name="selection_on" href="#selection_on">#</a> <i>selection</i>.<b>on</b>(<i>typenames</i>[, <i>listener</i>[, <i>options</i>]]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/on.js "Source")

Adds or removes a *listener* to each selected element for the specified event *typenames*. The *typenames* is a string event type, such as `click`, `mouseover`, or `submit`; any [DOM event type](https://developer.mozilla.org/en-US/docs/Web/Events#Standard_events) supported by your browser may be used. The type may be optionally followed by a period (`.`) and a name; the optional name allows multiple callbacks to be registered to receive events of the same type, such as `click.foo` and `click.bar`. To specify multiple typenames, separate typenames with spaces, such as `input change` or `click.foo click.bar`.

为指定的事件*typenames*向每个选定的元素添加或删除一个*listener*。*typenames*是一个字符串事件类型，例如 `click`, `mouseover`, 或 `submit`;可以使用浏览器支持的任何[DOM event type](https://developer.mozilla.org/en-US/docs/Web/Events#Standard_events)。类型后面可以选择跟着句号(`.`)和名称;可选名称允许注册多个回调函数来接收相同类型的事件，比如' click '。foo”和“click.bar”。若要指定多个类型名，请使用空格分隔类型名，如`input change` or `click.foo click.bar`。

When a specified event is dispatched on a selected element, the specified *listener* will be evaluated for the element, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). Listeners always see the latest datum for their element, but the index is a property of the selection and is fixed when the listener is assigned; to update the index, re-assign the listener. To access the current event within a listener, use [d3.event](#event).

当在选定的元素上分派指定的事件时，将为该元素计算指定的*listener*，并传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。侦听器总是看到其元素的最新数据，但是索引是选择的属性，并且在分配侦听器时是固定的;若要更新索引，请重新分配侦听器。要访问侦听器中的当前事件，请使用[d3.event](#event)。

If an event listener was previously registered for the same *typename* on a selected element, the old listener is removed before the new listener is added. To remove a listener, pass null as the *listener*. To remove all listeners for a given name, pass null as the *listener* and `.foo` as the *typename*, where `foo` is the name; to remove all listeners with no name, specify `.` as the *typename*.

如果在选定的元素上先前为相同的*typename*注册了事件侦听器，则在添加新侦听器之前删除旧侦听器。若要删除侦听器，请将null作为*listener*传递。若要删除给定名称的所有侦听器，请将null作为*listener*和'传递。foo '作为*typename*，其中' foo '是名称;若要删除所有没有名称的侦听器，请指定'。“就像这个名字一样。

An optional *options* object may specify characteristics about the event listener, such as whether it is capturing or passive; see [*element*.addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener).

一个可选的*options*对象可以指定事件监听器的特征，例如它是捕获的还是被动的;[*element*.addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)。

If a *listener* is not specified, returns the currently-assigned listener for the specified event *typename* on the first (non-null) selected element, if any. If multiple typenames are specified, the first matching listener is returned.

如果没有指定*listener*，则在第一个(非null)选定元素上(如果有)返回指定事件的当前分配的侦听器*typename*。如果指定了多个类型名，则返回第一个匹配的侦听器。

<a name="selection_dispatch" href="#selection_dispatch">#</a> <i>selection</i>.<b>dispatch</b>(<i>type</i>[, <i>parameters</i>]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/dispatch.js "Source")

Dispatches a [custom event](http://www.w3.org/TR/dom/#interface-customevent) of the specified *type* to each selected element, in order. An optional *parameters* map may be specified to set additional properties of the event. It may contain the following fields:

按顺序将指定*类型*的[custom event](http://www.w3.org/tr/dom/# interface-customevent)分派给每个选中的元素。可以指定一个可选的*parameters*映射来设置事件的其他属性。它可能包含以下字段:

* [`bubbles`](https://www.w3.org/TR/dom/#dom-event-bubbles) - if true, the event is dispatched to ancestors in reverse tree order.
* [`cancelable`](https://www.w3.org/TR/dom/#dom-event-cancelable) - if true, *event*.preventDefault is allowed.
* [`detail`](https://www.w3.org/TR/dom/#dom-customevent-detail) - any custom data associated with the event.

If *parameters* is a function, it is evaluated for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). It must return the parameters map for the current element.

如果*parameters*是一个函数，则对每个选定的元素求值，顺序是:传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。它必须返回当前元素的参数映射。

<a name="event" href="#event">#</a> d3.<b>event</b>

The current [event](https://developer.mozilla.org/en-US/docs/DOM/event), if any. This is set during the invocation of an event listener, and is reset after the listener terminates. Use this to access standard event fields such as [*event*.timeStamp](https://www.w3.org/TR/dom/#dom-event-timestamp) and methods such as [*event*.preventDefault](https://www.w3.org/TR/dom/#dom-event-preventdefault). While you can use the native [*event*.pageX](https://developer.mozilla.org/en/DOM/event.pageX) and [*event*.pageY](https://developer.mozilla.org/en/DOM/event.pageY), it is often more convenient to transform the event position to the local coordinate system of the container that received the event using [d3.mouse](#mouse), [d3.touch](#touch) or [d3.touches](#touches).

当前[event](https://developer.mozilla.org/en-US/docs/DOM/event)，如果有的话。这是在调用事件侦听器时设置的，并在侦听器终止后重置。使用它访问标准事件字段，如[*event*.timeStamp](https://www.w3.org/TR/dom/#dom-event-timestamp)和方法，如 [*event*.preventDefault](https://www.w3.org/TR/dom/#dom-event-preventdefault)。虽然您可以使用本机[*event*.pageX](https://developer.mozilla.org/en/DOM/event.pageX)和[*event*.pageY](https://developer.mozilla.org/en/DOM/event.pageY),这是更方便的局部坐标系变换事件位置接收事件使用的容器[d3.mouse](#mouse), [d3.touch](#touch) or [d3.touches](#touches)。

If you use Babel, Webpack, or another ES6-to-ES5 bundler, be aware that the value of d3.event changes during an event! An import of d3.event must be a [live binding](http://www.2ality.com/2015/07/es6-module-exports.html), so you may need to configure the bundler to import from D3’s ES6 modules rather than from the generated UMD bundle; not all bundlers observe [jsnext:main](https://github.com/rollup/rollup/wiki/jsnext:main). Also beware of conflicts with the [*window*.event](https://developer.mozilla.org/en-US/docs/Web/API/Window/event) global.

如果您使用Babel、Webpack或其他ES6-to-ES5 bundlerbundler，请注意d3.event的值在事件期间更改!d3.event事件必须是[live binding](http://www.2ality.com/2015/07/es6-module-exports.html)，因此您可能需要配置bundler从D3的ES6模块导入，而不是从生成的UMD包导入;并不是所有的bundler都遵守[jsnext:main](https://github.com/rollup/rollup/wiki/jsnext:main)。还要注意与[*window*.event](https://developer.mozilla.org/en-US/docs/Web/API/Window/event)全局的冲突。

<a name="customEvent" href="#customEvent">#</a> d3.<b>customEvent</b>(<i>event</i>, <i>listener</i>[, <i>that</i>[, <i>arguments</i>]]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/on.js#L98 "Source")

Invokes the specified *listener*, using the specified *that* `this` context and passing the specified *arguments*, if any. During the invocation, [d3.event](#event) is set to the specified *event*; after the listener returns (or throws an error), d3.event is restored to its previous value. In addition, sets *event*.sourceEvent to the prior value of d3.event, allowing custom events to retain a reference to the originating native event. Returns the value returned by the *listener*.

调用指定的*listener*，使用指定的*that*  `this` 上下文并传递指定的*参数*(如果有的话)。在调用过程中，将[d3.event](#event)设置为指定的*event*;在侦听器返回(或抛出错误)d3之后。事件恢复到其以前的值。此外，设置*event*。sourceEvent到d3的先验值。事件，允许自定义事件保留对原始本机事件的引用。返回由*listener*返回的值。

<a name="mouse" href="#mouse">#</a> d3.<b>mouse</b>(<i>container</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/mouse.js "Source")

Returns the *x* and *y* coordinates of the [current event](#event) relative to the specified *container*. The container may be an HTML or SVG container element, such as a [G element](http://www.w3.org/TR/SVG/struct.html#Groups) or an [SVG element](http://www.w3.org/TR/SVG/struct.html#SVGElement). The coordinates are returned as a two-element array of numbers [*x*, *y*].

关于特定容器的当前事件的协调。这个容器可以是HTML或SVG容器元素，像[G element](http://www.w3.org/TR/SVG/struct.html#Groups)元素一样或[SVG element](http://www.w3.org/TR/SVG/struct.html#SVGElement)。协调器返回为数字的二元阵列(*x*， *y*)。

<a name="touch" href="#touch">#</a> d3.<b>touch</b>(<i>container</i>[, <i>touches</i>], <i>identifier</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/touch.js "Source")

Returns the *x* and *y* coordinates of the touch with the specified *identifier* associated with the [current event](#event) relative to the specified *container*. The container may be an HTML or SVG container element, such as a [G element](http://www.w3.org/TR/SVG/struct.html#Groups) or an [SVG element](http://www.w3.org/TR/SVG/struct.html#SVGElement). The coordinates are returned as a two-element array of numbers [*x*, *y*]. If there is no touch with the specified identifier in *touches*, returns null; this can be useful for ignoring touchmove events where the only some touches have moved. If *touches* is not specified, it defaults to the current event’s [changedTouches](http://developer.apple.com/library/safari/documentation/UserExperience/Reference/TouchEventClassReference/TouchEvent/TouchEvent.html#//apple_ref/javascript/instp/TouchEvent/changedTouches) property.

返回与指定的*容器*相关的[当前事件](#事件)关联的指定*标识符*的触摸的*x*和*y*坐标。容器可以是HTML或SVG容器元素，比如[G元素](http://www.w3.org/TR/SVG/struct.html#Groups)或[SVG元素](http://www.w3.org/TR/SVG/struct.html#SVGElement)。返回的坐标是两个元素的数字数组[*x*， *y*]。如果在* touchs*中没有与指定标识符的触摸，则返回null;这对于忽略只移动了一些触摸的touchmove事件非常有用。如果没有指定*触摸*，它默认为当前事件的[changedtouch] (http://developer.apple.com/library/safari/documentation/UserExperience/Reference/TouchEventClassReference/TouchEvent/TouchEvent.html#//apple_ref/javascript/instp/TouchEvent/ changedtouch)属性。

<a name="touches" href="#touches">#</a> d3.<b>touches</b>(<i>container</i>[, <i>touches</i>]) [<>](https://github.com/d3/d3-selection/blob/master/src/touches.js "Source")

Returns the *x* and *y* coordinates of the touches associated with the [current event](#event) relative to the specified *container*. The container may be an HTML or SVG container element, such as a [G element](http://www.w3.org/TR/SVG/struct.html#Groups) or an [SVG element](http://www.w3.org/TR/SVG/struct.html#SVGElement). The coordinates are returned as an array of two-element arrays of numbers \[\[*x1*, *y1*], [*x2*, *y2*], …\]. If *touches* is not specified, it defaults to the current event’s [touches](http://developer.apple.com/library/safari/documentation/UserExperience/Reference/TouchEventClassReference/TouchEvent/TouchEvent.html#//apple_ref/javascript/instp/TouchEvent/touches) property.

返回与[当前事件](#event)关联的触摸相对于指定的*容器*的*x*和*y*坐标。容器可以是HTML或SVG容器元素，比如[G元素](http://www.w3.org/TR/SVG/struct.html#Groups)或[SVG元素](http://www.w3.org/TR/SVG/struct.html#SVGElement)。返回的坐标是一个由数字\[\[*x1*， *y1*]， [*x2*， *y2*]，…\]组成的双元素数组。如果没有指定* TouchEvent *，它默认为当前事件的[touch] (http://developer.apple.com/library/safari/documentation/UserExperience/Reference/TouchEventClassReference/TouchEvent/TouchEvent.html#//apple_ref/javascript/instp/TouchEvent/ TouchEvent)属性。

<a name="clientPoint" href="#clientPoint">#</a> d3.<b>clientPoint</b>(<i>container</i>, <i>event</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/point.js "Source")

Returns the *x* and *y* coordinates of the specified *event* relative to the specified *container*. (The *event* may also be a [touch](https://www.w3.org/TR/touch-events/#touch-interface).) The container may be an HTML or SVG container element, such as a [G element](http://www.w3.org/TR/SVG/struct.html#Groups) or an [SVG element](http://www.w3.org/TR/SVG/struct.html#SVGElement). The coordinates are returned as a two-element array of numbers [*x*, *y*].

与特定容器相关的特定事件的协调(活动*也可能是一个[touch](https://www.w3.org/tr/touchevents /# touchinterface)。)这个容器可以是HTML或SVG容器元素，像G元素一样(http://www.w3.org/TR/SVG/struct.html#Groups)或SVG元素(http://www.w3.org/TR/SVG/struct.html#SVGElement)。协调器返回为数字的二元阵列(*x*， *y*)。

### Control Flow

For advanced usage, selections provide methods for custom control flow.

对于高级使用，选择为自定义控制流提供方法。

<a name="selection_each" href="#selection_each">#</a> <i>selection</i>.<b>each</b>(<i>function</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/each.js "Source")

Invokes the specified *function* for each selected element, in order, being passed the current datum (*d*), the current index (*i*), and the current group (*nodes*), with *this* as the current DOM element (*nodes*[*i*]). This method can be used to invoke arbitrary code for each selected element, and is useful for creating a context to access parent and child data simultaneously, such as:

为每个选中的元素调用指定的*函数*，依次传递当前数据(*d*)、当前索引(*i*)和当前组(*nodes*)，其中*this*作为当前DOM元素(*nodes*[*i*])。此方法可用于为每个选定的元素调用任意代码，并可用于创建同时访问父数据和子数据的上下文，例如:

```js
parent.each(function(p, j) {
  d3.select(this)
    .selectAll(".child")
      .text(d => `child ${d.name} of ${p.name}`);
});
```

See [Sized Donut Multiples](http://bl.ocks.org/mbostock/4c5fad723c87d2fd8273) for an example.

参见示例[Sized Donut Multiples](http://bl.ocks.org/mbostock/4c5fad723c87d2fd8273)。

<a name="selection_call" href="#selection_call">#</a> <i>selection</i>.<b>call</b>(<i>function</i>[, <i>arguments…</i>]) [<>](https://github.com/d3/d3-selection/blob/master/src/selection/call.js "Source")

Invokes the specified *function* exactly once, passing in this selection along with any optional *arguments*. Returns this selection. This is equivalent to invoking the function by hand but facilitates method chaining. For example, to set several styles in a reusable function:

只调用指定的*函数*一次，并传入此选择项和任何可选的*参数*。返回这个选择。这相当于手工调用函数，但便于方法链接。例如，要在一个可重用的函数中设置几个样式:

```js
function name(selection, first, last) {
  selection
      .attr("first-name", first)
      .attr("last-name", last);
}
```

Now say:

现在说:

```js
d3.selectAll("div").call(name, "John", "Snow");
```

This is roughly equivalent to:

这大致相当于:

```js
name(d3.selectAll("div"), "John", "Snow");
```

The only difference is that *selection*.call always returns the *selection* and not the return value of the called *function*, `name`.

唯一的区别是“选择”。call总是返回*selection*，而不是被调用的*function*的返回值' name '。

<a name="selection_empty" href="#selection_empty">#</a> <i>selection</i>.<b>empty</b>() [<>](https://github.com/d3/d3-selection/blob/master/src/selection/empty.js "Source")

Returns true if this selection contains no (non-null) elements.

如果该选择不包含(非null)元素，则返回true。

<a name="selection_nodes" href="#selection_nodes">#</a> <i>selection</i>.<b>nodes</b>() [<>](https://github.com/d3/d3-selection/blob/master/src/selection/nodes.js "Source")

Returns an array of all (non-null) elements in this selection.

返回此选择项中所有(非空)元素的数组。

<a name="selection_node" href="#selection_node">#</a> <i>selection</i>.<b>node</b>() [<>](https://github.com/d3/d3-selection/blob/master/src/selection/node.js "Source")

Returns the first (non-null) element in this selection. If the selection is empty, returns null.

返回此选择中的第一个(非空)元素。如果选择为空，则返回null。

<a name="selection_size" href="#selection_size">#</a> <i>selection</i>.<b>size</b>() [<>](https://github.com/d3/d3-selection/blob/master/src/selection/size.js "Source")

Returns the total number of elements in this selection.

返回此选择项中的元素总数。

### Local Variables

D3 locals allow you to define local state independent of data. For instance, when rendering [small multiples](http://bl.ocks.org/mbostock/e1192fe405703d8321a5187350910e08) of time-series data, you might want the same *x*-scale for all charts but distinct *y*-scales to compare the relative performance of each metric. D3 locals are scoped by DOM elements: on set, the value is stored on the given element; on get, the value is retrieved from given element or the nearest ancestor that defines it.

D3局部变量允许您定义独立于数据的本地状态。例如，当呈现时间序列数据的[小倍数](http://bl.ocks.org/mbostock/e1192fe405703d8321a5187350910e08)时，您可能希望对所有图表使用相同的*x*-scale，但是不同的*y*-scale比较每个指标的相对性能。D3局部变量由DOM元素限定范围:在set上，值存储在给定的元素上;在get上，从给定元素或定义该元素的最近祖先检索该值。

<a name="local" href="#local">#</a> d3.<b>local</b>() [<>](https://github.com/d3/d3-selection/blob/master/src/local.js "Source")

Declares a new local variable. For example:

声明一个新的局部变量。例如:

```js
const foo = d3.local();
```

Like `var`, each local is a distinct symbolic reference; unlike `var`, the value of each local is also scoped by the DOM.

像“var”一样，每个局部变量都是一个不同的符号引用;与“var”不同，每个局部的值也由DOM限定范围。

<a name="local_set" href="#local_set">#</a> <i>local</i>.<b>set</b>(<i>node</i>, <i>value</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/local.js#L18 "Source")

Sets the value of this local on the specified *node* to the *value*, and returns the specified *value*. This is often performed using [*selection*.each](#selection_each):

将指定*节点*上的此局部值设置为*value*，并返回指定的*value*。这通常使用[*selection*.each](#selection_each)来执行:

```js
selection.each(function(d) { foo.set(this, d.value); });
```

If you are just setting a single variable, consider using [*selection*.property](#selection_property):

如果只设置一个变量，可以考虑使用[*selection*.property](#selection_property):

```js
selection.property(foo, d => d.value);
```

<a name="local_get" href="#local_get">#</a> <i>local</i>.<b>get</b>(<i>node</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/local.js#L13 "Source")

Returns the value of this local on the specified *node*. If the *node* does not define this local, returns the value from the nearest ancestor that defines it. Returns undefined if no ancestor defines this local.

在指定的*节点*上返回此局部值。如果*node*没有定义这个局部值，则从定义它的最近的祖先返回值。如果没有祖先定义此局部，则返回未定义的。

<a name="local_remove" href="#local_remove">#</a> <i>local</i>.<b>remove</b>(<i>node</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/local.js#L21 "Source")

Deletes this local’s value from the specified *node*. Returns true if the *node* defined this local prior to removal, and false otherwise. If ancestors also define this local, those definitions are unaffected, and thus [*local*.get](#local_get) will still return the inherited value.

从指定的*节点*中删除此局部值。如果*节点*在删除之前定义了此本地，则返回true，否则返回false。如果祖先也定义了这个local，那么这些定义不受影响，因此[*local*.get](#local_get)仍然会返回继承的值。

<a name="local_toString" href="#local_toString">#</a> <i>local</i>.<b>toString</b>() [<>](https://github.com/d3/d3-selection/blob/master/src/local.js#L24 "Source")

Returns the automatically-generated identifier for this local. This is the name of the property that is used to store the local’s value on elements, and thus you can also set or get the local’s value using *element*[*local*] or by using [*selection*.property](#selection_property).

返回此本地的自动生成标识符。这是用于在元素上存储本地值的属性的名称，因此您还可以使用*element*[*local*]或使用[*selection*.property](#selection_property)设置或获取本地值。

### Namespaces

XML namespaces are fun! Right? Fortunately you can mostly ignore them.

XML名称空间很有趣!对吧?幸运的是，你基本上可以忽略它们。

<a name="namespace" href="#namespace">#</a> d3.<b>namespace</b>(<i>name</i>) [<>](https://github.com/d3/d3-selection/blob/master/src/namespace.js "Source")

Qualifies the specified *name*, which may or may not have a namespace prefix. If the name contains a colon (`:`), the substring before the colon is interpreted as the namespace prefix, which must be registered in [d3.namespaces](#namespaces). Returns an object `space` and `local` attributes describing the full namespace URL and the local name. For example:

限定指定的*name*，它可能有名称空间前缀，也可能没有。如果名称包含冒号(':')，冒号前的子字符串被解释为名称空间前缀，必须在[d3.namespaces](#namespaces)中注册。返回对象“space”和“local”属性，这些属性描述完整的名称空间URL和本地名称。例如:

```js
d3.namespace("svg:text"); // {space: "http://www.w3.org/2000/svg", local: "text"}
```

If the name does not contain a colon, this function merely returns the input name.

如果名称不包含冒号，则此函数只返回输入名称。

<a name="namespaces" href="#namespaces">#</a> d3.<b>namespaces</b> [<>](https://github.com/d3/d3-selection/blob/master/src/namespaces.js "Source")

The map of registered namespace prefixes. The initial value is:

注册名称空间前缀的映射。初始值为:

```js
{
  svg: "http://www.w3.org/2000/svg",
  xhtml: "http://www.w3.org/1999/xhtml",
  xlink: "http://www.w3.org/1999/xlink",
  xml: "http://www.w3.org/XML/1998/namespace",
  xmlns: "http://www.w3.org/2000/xmlns/"
}
```

Additional prefixes may be assigned as needed to create elements or attributes in other namespaces.

如果需要在其他名称空间中创建元素或属性，可以分配额外的前缀。
