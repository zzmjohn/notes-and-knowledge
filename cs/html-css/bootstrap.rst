overview
========

- A component library (not a framework)

- features

  * responsive
   
  * mobile-first

- JS dependencies (in loading order)

  * jquery

  * popper.js

- browser compatibility:
  
  * latest, stable releases of all major browsers.

package content
===============

- ``*.map`` is map file used for debugging.

css
---

- ``bootstrap[.min].css`` include all modules: layout, content, components, utilities.
  Without ``.min``, is just source files compiled together. With ``.min``, also minified.

- ``bootstrap-grid[.min].css`` includes: grid layout system and flex utilities.

- ``bootstrap-reboot[.min].css`` includes: reboot content module.

js
--

- ``bootstrap[.min].js`` include bootstrap js library.

- ``bootstrap.bundle[.min].js`` include also popper.js library.

- In source distro, there is individual js files for each components under ``js/dist/``.

setup
=====

- viewport tag::

    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

- bootstrap css tag.

- jquery, popper.js, bootstrap script tags.

global styles
=============

- change all elements' box-sizing to ``border-box``.

data api
========

- Nearly all Bootstrap plugins can be enabled and configured through HTML alone
  with data attributes.

- To disable the data attribute API, unbind all events on the document
  namespaced with ``data-api``. to target a specific plugin, just include the
  plugin’s name as a namespace along with the ``data-api`` namespace.

evnets
======

- Custom events for most plugins’ unique actions.

- Custom events come in an infinitive and past participle form - where the
  infinitive (ex. show) is triggered at the start of an event, and its past
  participle form (ex. shown) is triggered on the completion of an action.

- All infinitive events provide preventDefault() functionality. This provides
  the ability to stop the execution of an action before it starts.

APIs
====

- All public APIs are single, chainable methods, and return the collection
  acted upon.

- All methods should accept an optional options object, a string which targets
  a particular method, or nothing (which initiates a plugin with default
  behavior).

- Each plugin also exposes its raw constructor on a ``Constructor`` property:
  ``$.fn.popover.Constructor``. If you’d like to get a particular plugin
  instance, retrieve it directly from an element:
  ``$('[rel="popover"]').data('popover')``.

- You can change the default settings for a plugin by modifying the plugin’s
  ``Constructor.Default`` object.

- call ``.noConflict`` on the plugin you wish to revert the value of to its
  previously assigned value.

- The version of each of Bootstrap’s jQuery plugins can be accessed via the
  ``VERSION`` property of the plugin’s constructor.

layout
======

breakpoints
-----------
breakpoints are defined in px, because viewport is computed in pixel.

- xs.
  
  * extra small.
    
  * 0 - 576px

- sm.
  
  * small.
    
  * 576px - 768px

- md.
  
  * medium.
    
  * 768px - 992px

- lg.
  
  * large.
    
  * 992px - 1200px

- xl.
  
  * extra large.

  * 1200px - inf.

grid variables in Sass
----------------------
::

  $grid-columns:      12;
  $grid-gutter-width: 30px;

  $grid-breakpoints: (
    // Extra small screen / phone
    xs: 0,
    // Small screen / phone
    sm: 576px,
    // Medium screen / tablet
    md: 768px,
    // Large screen / desktop
    lg: 992px,
    // Extra large screen / wide desktop
    xl: 1200px
  );

  $container-max-widths: (
    sm: 540px,
    md: 720px,
    lg: 960px,
    xl: 1140px
  );

grid mixins in Sass
-------------------
::

  make-row
  make-container
  make-col-ready
  make-col
  make-col-offset

media queries
-------------

sass mixins:

- ``media-breakpoint-up($width)``

- ``media-breakpoint-down($width)``

- ``media-breakpoint-only($width)``

- ``media-breakpoint-between($low, $high)``

stacking context
----------------

- sass z-index variables::

    $zindex-dropdown:          1000 !default;
    $zindex-sticky:            1020 !default;
    $zindex-fixed:             1030 !default;
    $zindex-modal-backdrop:    1040 !default;
    $zindex-modal:             1050 !default;
    $zindex-popover:           1060 !default;
    $zindex-tooltip:           1070 !default;

- To handle overlapping borders within components, we use low single digit
  z-index values of 1, 2, and 3 for default, hover, and active states.

- On hover/focus/active, we bring a particular element to the forefront with a
  higher z-index value to show their border over the sibling elements.

flexbox grid system
-------------------
- bootstrap's grid system is built with css flexbox layout.

containers
^^^^^^^^^^
- Containers has the following purposes.
  
  * provide a means to horizontally center site's content.

  * provide a responsive global width constraints.

- Container should be a global wrapper, it should never be nested.

- Container has a 15px left/right padding, so that the content doesn’t touch
  the edge of the browser.

types of containers
"""""""""""""""""""

- fixed-width container:

  * class: ``.container``
 
  * container's ``max-width`` changes at each breakpoint (by media query). 也就
    是说, 在 viewport 为某个宽度阈值与下一个阈值之间时, 限制 container 的宽度不
    能超过某个值.

- full-width container: 

  * class: ``.container-fluid``

  * spanning the entire width of the viewport. (``width: 100%`` without
    ``max-width`` constraints.)

rows
^^^^
- Rows are wrappers for columns.

  * class: ``.row``

- a row is a flexbox container.

- a row has -15px left/right margin, this pushes row's border box (由于默认没有
  border and padding, 也就意味着 content box) 与 container border box 接壤.  这
  个操作的意义是, 避免 container, row, column 三层嵌套导致 column content
  indented too much.

- never use a row outside of container, it doesn't work.

- The direct children of a row must be a set of columns.

alignment of columns
""""""""""""""""""""
- vertical and horizontal alignments of columns in a row are achieved by
  flexbox alignments.

- classes to control vertical alignment of columns in a row.

  * ``.align-items-start``. set ``align-items: flex-start``

  * ``.align-items-center``. similar.

  * ``.align-items-end``

- classes to control horizontal alignment of columns in a row.

  * ``.justify-content-start``

  * ``.justify-content-center``

  * ``.justify-content-end``

  * ``.justify-content-around``

  * ``.justify-content-between``

no gutters
""""""""""
- the -15px margins on rows and paddings on their contained columns can be
  removed by ``.no-gutters`` class::

    <div class="row no-gutter">
        ...
    </div>

columns
^^^^^^^
- contents must be placed inside columns.

- Each column has 15px left/right padding (called a gutter) for controlling the
  space between columns.
  
  这个 15px 让 column 在作为 row 的第一个子元素时, 又获得了离 viewport 边界
  15px 的 padding. 并让 columns 之间有 30px 的 padding.

- 在 column 中, 还可以创建 nested grids. 这时, 需要在 column 中创建 child rows.
  在 rows 中再创建 columns.

  注意到此时外层的 column 与顶层的 container 处于相同的地位. 而 row 的 -15px
  margin 与 column 的 15px padding 抵消, 让内层 columns 不至于过度地 indent.
  这充分体现了 row 的 -15px margin 的意义.

- columns without a specified width (e.g. by column width classes) will
  automatically layout as equal width columns. This is achieved by ``flex``
  property.

- columns width classes 体现一个列的宽度占到总宽度的比例, 总宽度设置为 12.

- Column widths are set in percentages, so they’re always fluid and sized
  relative to their parent element.

column classes
""""""""""""""
- auto-layout columns::

    .col[-{breakpoint}]

  * 当 viewport 宽度大于 breakpoint 时, auto layout 才生效, 否则每个 column
    的宽度是 100%, 即对于 narrow viewport 呈现 stacking 效果.

  * 对于 xs, 不指定 breakpoint, 因为是从 0 开始. 此时, 对任何宽度的 viewport 都
    有效.

  * 所有使用该 class 的 column 自动 expand to an equal width for the rest of
    the row. 这是由 ``flex-grow: 1`` 实现的.

  * auto layout columns 可以与 responsive column classes 一起使用, 按照 flexbox
    layout, auto layout columns 会均分 responsive column classes 剩下的区域.

- variable width columns::

    .col[-{breakpoint}]-auto

  * 宽度由内容宽度决定. 这是 ``flex-basis: auto`` 的效果.

  * ``breakpoint`` 限制了只有宽度大于该 breakpoint 时这才生效. 否则宽度为 100%.

- proportional columns::

    .col[-{breakpoint}]-{width}

  * breakpoint 对应于 `breakpoints`_ 定义的 5 类宽度范围.
    
  * 该 class 的意义为当 viewport 宽度大于相应 breakpoint 的宽度值时, ``{width}``
    所指定的 flexbox proportional 效果才得到应用. 否则, column 占到 viewport 的全
    部宽度.
  
  * 对于 xs, 不指定 breakpoint, 因为是从 0 开始. 此时, ``{width}`` proportional
    效果总是成立, 而无论 viewport 宽度是多少.
  
  * ``{width}`` 最大是 12.

column wrapping
""""""""""""""""
- 如果某个 row 中的 columns 总宽度大于 12, 超出 12 的那些 columns 会自动 wrap
  至下一个 flex line. 由于每个 column 的宽度比例本质上是 flex-basis, 所以是
  仍然以 row 的宽度为单位来缩放.

column break
""""""""""""
- add a div with ``.w-100`` class to force break columns into multiple lines::

    <div class="w-100"></div>

- This is can combined with responsive display utilities to make the column
  break responsive.::

    <div class="w-100 d-none d-md-block"></div>

mix column classes
""""""""""""""""""
- 可以给一组 column 设置多个 column classes, 分别应对不同宽度下的不同 layout 行
  为. 本质即在不同宽度下, 不同的 media query 生效.

  例如, 以下保证在 viewport 达不到 ``col-sm`` 时, 按照 ``col-*`` 的布局来分配宽
  度.::

    <div class="row">
      <div class="col-12 col-md-8">.col-12 .col-md-8</div>
      <div class="col-6 col-md-4">.col-6 .col-md-4</div>
    </div>

column self alignment
"""""""""""""""""""""
- a column can overrides its vertical and horizontal alignments stipulated by
  the containing row (a flex container).

- vertical alignment

  * ``.align-self-start``

  * ``.align-self-center``

  * ``.align-self-end``

column ordering
"""""""""""""""
- Use ``.order-`` classes for controlling the visual order of your content.::

    .order[-{breakpoint}]-{N}

  本质上使用了 ``order`` flexbox property.

- order 可以是 1-12. 注意若没有设置, 该 flex item 的默认值是 0, 导致总是第一个.

- special classes to enforce first and last order::

    .order[-{breakpoint}]-first
    .order[-{breakpoint}]-last

  they use order -1 and order 13 under the hood.

column offset
"""""""""""""
- responsive column offsets::

    .offset[-{breakpoint}]-{N}
    .offset[-{breakpoint}]-0

- 这些 offset 实际是创建了 left margin.
  
- 对于 ``N=0``, 是在 viewport 大于这个 breakpoint 时, reset offset 至 0. 例如::

    <div class="col-sm-6 col-md-5 col-lg-6"></div>
    <div class="col-sm-6 col-md-5 offset-md-2 col-lg-6 offset-lg-0"></div>

- column offset and `margin utilities`_ 可用于创建 responsive heterogenous
  layout.

nesting
^^^^^^^
- nesting rows inside columns is possible.

- 外层 column 中可以有其他内容, 例如 inline content.

media object
============
- media object is an image (or other media content) to the left, with
  descriptive content to the right. E.g., a tweet.

basic layout
------------
- To create a media object, the following structure: ``.media`` wrapper, a
  media element as the first child, ``.media-body`` as the second child.::

    <div class="media">
      <img class="mr-3" src="..." alt="...">
      <div class="media-body">
      ...
      </div>
    </div>

- ``.media`` is a flex container. media element and ``.media-body`` are flex
  items.  ``.media-body`` is growable but media element is not.

nesting
-------
- Media objects can be infinitely nested. Place nested ``.media`` within the
  ``.media-body`` of a parent media object.

alignment
---------
- The two flex items by default has horizontal and vertical alignment to
  flex-start.

- use normal column alignment classes to change default behavior.

media list
----------

- Use ``<ul>`` to create media list.

  * remove list's default style by ``.list-unstyled``

  * add ``.media`` to ``<li>``.

components
==========

forms
-----
- bootstrap form controls are based on Reboot's customization on tags.  It
  basically provides a set of classes to style input components.

- Use an appropriate ``type`` of input, to take advantage of different device's
  customizations.

form layout
^^^^^^^^^^^

stacked
"""""""
- Most of bootstrap form controls by default have ``display: block``, therefore
  they stack vertically by default (在没有任何 wrapper 或其他自定义的情况下).

- form group.
  
  A form control and its label, its help text, form validation messages, etc.
  can be grouped into a ``.form-group``. ``.form-group`` 只提供了一个 bottom
  margin.

  * ``.form-group`` can be a ``<div>`` ``<fieldset>`` etc.

  * 这也可以看到, layout 更多的取决于 form control, label, etc. 上面的设定.

  * 注意, ``.form-group`` wrapper 不是必须的存在, 如果只有 form control, 而没有
    其他相关的元素例如 label, 则可以没有 ``.form-group`` wrapper.

  * 若 form 的某一行使用了 ``.form-group``, 所有其他行也应该以某种方式使用
    ``.form-group``, 以保证 margin-bottom 带来的 layout 一致性.

grid
""""
- use row/columns grid layout in form, to build complex form layout.

  * just add row divs inside form as direct children.

  * 只要 ``<form>`` 本身没有margin, padding etc., form 里面的 row 就会
    与 form 外面的比如 column 或 container 的 padding 抵消, 不会造成多余的
    indent. 这与 `flexbox grid system`_ 中的讨论是一致的.

- use ``.form-row`` instead of ``.row`` to get more tight layout.

  * ``.form-row`` 给出的 margin 只有 -5px. 它里面的 ``.col*`` 的 padding 也
    只有 5px. 这样 gutter 是 10px.

- row/form-row 不是 ``.form-group`` 的替代品. 需要 form-group 时就还是需要.
  form-group 在 row 里面时, 需要配合恰当的 column classes 以保证 flexbox
  layout.

horizontal form
"""""""""""""""
- Make labels and form controls as flex items, to build a horizontal form.  To
  do this, use ``.row`` and ``.form-group`` classes together on wrapper
  element.

- Use ``.col*`` classes on label and form controls for sizing. Add
  ``.col-form-label`` to your ``<label>`` as well so they’re vertically
  centered with their associated form controls.

inline form
"""""""""""
- Use ``.form-inline`` class on ``<form>`` to make its contents inline.  此时
  ``<form>`` 成为了 flex container. 里面是 flex items or inline elements.

- 感觉 inline form 不是很必要, 因为我们有 grid form, horizontal form 等都是
  基于 flexbox 的, 可以做成 inline 的样子.

- form controls 之间的 spacing 通过 spacing and flexbox utilities 来设置.

- Controls and input groups receive ``width: auto`` to override the Bootstrap
  default ``width: 100%``. 这样根据内容来确定宽度.

- Controls only appear inline in viewports that are at least 576px wide to
  account for narrow viewports on mobile devices.

- In inline form, ``.form-group`` becomes an inner flex container.

help text
^^^^^^^^^
- Create help text with ``<small>``, ``<span>`` etc.

- block-level help text, add ``.form-text`` class, which makes it block-level.
  Add block-level help text below form control.

- For inline help text, 不需要额外的 layout classes.

- Add utilities classes to style the help text, like ``.text-muted``.

textual form controls
^^^^^^^^^^^^^^^^^^^^^
- Textual form controls, like ``<input>``, ``<select>``, and ``<textarea>`` are
  styled with the ``.form-control`` class.

file inputs
^^^^^^^^^^^
- Basic file input, use ``.form-control-file``.

range inputs
^^^^^^^^^^^^
- For ``input[type="range"]``, use::

    .form-control-range

  效果是将 range input 变成 block-level element, 并占据 parent element 的
  全部宽度.

checkboxes and radios
^^^^^^^^^^^^^^^^^^^^^
- wrapper's class for stacked checkbox and radio input::

    .form-check

  * 这作为 block-level element 保证一行一个 checkbox/radio.

  * 注意 ``.form-check`` wrapper 不能替代 ``.form-group``.

- additional wrapper class for inline checkbox and radio input::

    .form-check-inline

  这将 display 改成 inline-flex.

- class for checkboxes and radios::

    .form-check-input

- Add following class to checkbox and radio that don’t have any label text::

    .position-static

- class for their labels::

    .form-check-label

custom form controls
^^^^^^^^^^^^^^^^^^^^
checkboxes and radios
"""""""""""""""""""""
- 用 ``.custom-control`` 替换 ``.form-check``, 添加 ``.custom-check`` or
  ``.custom-radio`` for checkbox or radio respectively.

  For inline layout, add another ``.custom-control-inline`` class.

- Inside ``.custom-control``, place a checkbox or radio input as would
  normally do.

- For label, 用 ``.custom-control-label`` 替换 ``.form-check-label``.

selects
""""""""
- Add ``.custom-select`` class to ``<select>``.

- Control sizing by adding ``.custom-select-{sm|lg}``

ranges
""""""
- Add ``.custom-range`` class to ``<input type="range">``.

file inputs
"""""""""""
- Use the following::

    <div class="custom-file">
      <input type="file" class="custom-file-input">
      <label class="custom-file-label">Choose file</label>
    </div>

customization
^^^^^^^^^^^^^

sizing
""""""
- Add the following additional class to customize sizing of form controls::

    .form-control-{sm|lg}

  注意只负责 sizing 部分, form control 的 styling 要靠各自的 main class 来实现.

- Add the following additional class to customize sizing of labels to match
  that of form controls in horizontal forms::

    .col-form-label-{sm|lg}

form input as plain text
""""""""""""""""""""""""
- useful if you want to have ``<input readonly>`` elements in your form styled
  as plain text::

    .form-control-plaintext

  不该再添加 ``.form-control`` class, 因为 style overriding.

form validation
^^^^^^^^^^^^^^^
- 两种 form validation, 即 browser default validation feedback or bootstrap's
  custom validation feedback. 只能二选一, 不然会很奇怪的.

- 避免使用 browser default validation feedback, 因为内容、样式等难以自定义, 与
  整体风格不协调, 并且它的校验逻辑比较基础, 并不一定能满足业务需求.

validation and styling logic
""""""""""""""""""""""""""""
- 根据当前输入值是否 valid, form 中的各个 form control 总是处于 ``:valid`` or
  ``:invalid`` 两个 pseudo class 状态中的一个. 只要值发生变化, 这个校验就会重新
  进行.

- ``<form>`` 设置 ``novalidate``, 禁止在提交 form 时显示 browser default
  feedback tooltips, 禁止浏览器因为数据不合法而阻止提交. 但是这并不会禁用浏览器
  自动进行的 validation.

- Bootstrap scopes the ``:invalid`` and ``:valid`` styles to parent
  ``.was-validated`` class. 这样, 根据是否在 form 上有这个 class, 决定是否显示
  validation messages.
  
  这个 class 一般通过 submit event handler 设置. 这样避免了在用户尝试提交之前,
  尤其是用户还没开始填写表单时, 就显示 validation message.

- 使用 constraint validation API 进行自定义校验.

validation messages
"""""""""""""""""""
- Use ``.valid-feedback`` and ``.invalid-feedback`` divs for valid and invalid
  input messages.

- 这些 divs 需要作为 ``.form-control`` 或者 ``.form-check-input`` 等 form control
  element 的 next siblings.

- bootstrap css 根据相应的 form control 的 pseudo class 状态选择 display valid
  or invalid feedback div.

validation tooltips
"""""""""""""""""""
- Use ``.valid-tooltip`` and ``.invalid-tooltip`` for validation message as
  a tooltip.

- By default, tooltip will be at the bottom of the form control.

- Be sure to have a parent with ``position: relative`` on it for tooltip
  positioning, e.g., column classes.

server side validation
""""""""""""""""""""""
- Add ``is-valid`` or ``.is-invalid`` to corresponding form controls during
  server side validation.

- 这些 classes 不需要 form 上设置 ``.was-validated``. 可以独立生效.

- form control 设置了这些 classes 时, 它们的 next siblings ``.valid-feedback``
  and ``.invalid-feedback`` divs 会根据恰当的 css 规则进行显示.

input groups
^^^^^^^^^^^^
- input group 的作用是对 form control 进行扩展, 例如添加 text, button, etc., 或
  者进行分段输入, 等等.

- 注意 input group wraps form control and its addon elements, 它替代原来 form
  control element 所在的位置.

- For selects and file inputs, only bootstrap's custom versions are supported.

structure
""""""""""
- One ``.input-group`` wrapper.
  
  * This wrapper is a flex container.

  * 在 input group 中, form control 作为 flex item 会自动 flex-grow 至 input
    group 内剩余宽度.

- Inside of it, add a ``.input-group-prepend`` and/or ``.input-group-append``
  div for prepending and appending content.
  
  * These are flex containers.

- Inside of ``.input-group-{prepend|append}``, put in what the fuck you need.

text
""""
- 在 addon 区域中, 添加 ``.input-group-text`` element.

  * 一般就是 span element.

  * It's a flex container.

  * text is vertically centered by default.

- 是 ``.input-group-text`` 而不是 ``.input-group-{prepend|append}`` 给予了
  addon 区域灰色调.

checkbox and radio
""""""""""""""""""
- Place any checkbox or radio option within ``.input-group-text`` in place of
  text.

buttons and dropdowns
""""""""""""""""""""""
- just put your buttons and dropdowns inside of ``.input-group-{prepend|append}``.

multiple inputs
""""""""""""""""
- just add multiple inputs inside a input group.

  * border radius will be taken care of.

- Only supported visually, validation styles are only available for input
  groups with a single input.

multiple addons
"""""""""""""""
- just add multiple shit inside of ``.input-group-{prepend|append}``, since
  it's a flex container.

sizing
""""""

- 在 ``.input-group`` 上添加 ``.input-group-{sm|lg}`` 即可, 里面的 addon 以及
  form control 会自动改变大小, 无需再次设置.

tooltips
--------
- rely on popper.js

- Triggering tooltips on hidden elements will not work.

- When triggered from hyperlinks that span multiple lines, tooltips will be
  centered. Use ``white-space: nowrap;`` on your ``<a>`` to avoid this
  behavior.

- Tooltips must be hidden before their corresponding elements have been removed
  from the DOM.

setup
^^^^^
- add ``data-toggle="tooltip"`` to any element to make a tooltip over it.

- ``title`` attribute specifies its content.  Tooltips with zero-length titles
  are never displayed.

- tooltip options can all be specified as ``data-*`` attributes and their
  values as string.

- Every tooltip must be initialized separately. To initialize a tooltip::

    $(selector).tooltip({/* options */});

initialization options
""""""""""""""""""""""
- animation.

- container. Appends the tooltip to a specific element.
  Specify ``container: 'body'`` option to avoid rendering problems in more
  complex components.

- delay. Delay showing and hiding the tooltip.

- html. Allow HTML in the tooltip.

- placement. position the tooltip at top/bottom/left/right/auto.

- selector. delegate tooltip to the specified element.

- template. tooltip's html structure template.

- title. default title if ``title`` attribute is not present.
  
  If a function is given, it will be called with its this reference set to the
  element that the tooltip is attached to. 这可用于设置动态的 title text.

- trigger. how tooltip is triggered, a space separated combination of
  click/hover/focus/manual. manual means to trigger why js API.

- offset. offset of the tooltip relative to its target.

- fallbackPlacement.

- boundary.

disabled elements
^^^^^^^^^^^^^^^^^
- Tooltips for ``.disabled`` or ``disabled`` elements must be triggered on a
  wrapper element.

JS APIs
^^^^^^^
- All API methods are asynchronous and start a transition. They return to the
  caller as soon as the transition is started. A method call on a transitioning
  component will be ignored.

- format ``.tooltip(<method>)``

show
""""
- Reveals an element’s tooltip. Returns to the caller before the tooltip has
  actually been shown, i.e. before the ``shown.bs.tooltip`` event occurs.

hide
""""
- Hides an element’s tooltip. rest dito.

toggle
""""""
- toggle show/hide.

dispose
""""""""
- Hides and destroys an element’s tooltip.

enable
""""""
- Gives an element’s tooltip the ability to be shown. Tooltips are enabled by
  default.

disable
""""""""
- Removes the ability for an element’s tooltip to be shown.

toggleEnable
""""""""""""
- Toggles the ability for an element’s tooltip to be shown or hidden.

update
""""""
- Updates the position of an element’s tooltip.

events
^^^^^^
- ``{show|shown}.bs.tooltip``

- ``{hide|hidden}.bs.tooltip``

- ``inserted.bs.tooltip`` fired after the show.bs.tooltip event when the
  tooltip template has been added to the DOM.

modals
------
- modals are positioned over everything else.

- Only one modal window at a time, nested modal is not supported.

- modals position themself using ``position: fixed``. 这是为了直接相对于
  viewport 进行 positioning. 为了避免一些 parent element 影响 fixed
  positioning, 最好把 modals 都放在 top-level, 作为 body's direct child.

- When a modal is triggered, it adds ``.modal-open`` to the ``<body>`` to
  override default scrolling behavior, setting it to ``hidden`` (这样 scroll 时
  滚动的就是 modal window), and generates a ``.modal-backdrop`` to provide a
  click area for dismissing shown modals when clicking outside the modal.

- modal with long content is scrollable.

setup
^^^^^
- a modal window's structure::

    <div class="modal" tabindex="-1" role="dialog">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">...</h5>
                    <button type="button" class="close"
                            data-dismiss="modal"
                            aria-label="Close">
                      <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">
                ...
                </div>
                <div class="modal-footer">
                ...
                </div>
            </div>
        </div>
    </div>

  * ``.modal`` div 覆盖整个 viewport. 它是 fixed position 的.

  * ``.modal-dialog`` 是 modal window 的可见部分. 它具有 auto left/right margin
    所以左右居中. 它是 modal window 的真正 wrapper. 修改 modal window 的宽度应该
    修改这个元素的宽度 (它相对于 ``.modal``).[UnderstandModal]_

  * ``.modal-content`` tells ``bootstrap.js`` where to look for the contents of
    the modal.

  * ``.modal-header``, ``.modal-body``, ``.modal-footer`` 是 modal 的三个部分.

  * header 中, title 应用 ``.modal-title``.

  * modal 中应该设置至少一个 ``<button>``/``<a>`` etc., 具有 ``data-dismiss="modal"``.
    用于关闭 modal window.

- trigger a modal via one of the following methods.
  
  * A controller element to trigger modal window.::

      <... data-toggle="modal" data-target="modal-selector"...>

    - ``data-target`` is the selector to modal window.

  * Via javascript::

    $(<modal-selector>).modal({/* options */});

- modal options can be passed as ``data-*`` attributes or js object.

initialization options
""""""""""""""""""""""
- backdrop. true/false/'static'. For ``static``, the backdrop doesn't close the
  modal on click. default true.

- keyboard. close modal when esc is pressed. default true.

- focus. focus modal when initialized. default true.

- show. show modal when initialized. default true.

grid inside modal
^^^^^^^^^^^^^^^^^
- To use bootstrap grid system inside modal, nesting ``.container-fluid`` div
  within the ``.modal-body``.

centering
^^^^^^^^^
- Add ``.modal-dialog-centered`` to ``.modal-dialog`` to vertically center the
  modal.

fade in modal
^^^^^^^^^^^^^
- Add ``.fade`` to ``.modal`` div.

modal as a template
^^^^^^^^^^^^^^^^^^^
- 如果需要根据从不同 ``data-toggle`` element 触发的 modal 来填入不同的内容, 可以
  使用 ``show.bs.modal`` event 的 ``relatedTarget`` 来自定义. 将需要填入的内容
  放在 ``data-*`` attributes 上, 在 ``show.bs.modal`` event handler 中填入内容.

dynamic height
^^^^^^^^^^^^^^
- When changing a modal's height while it's open, call ``$().modal('handleUpdate')``
  to adjust its position.

sizing
^^^^^^
- Add the following class to ``.modal-dialog`` for large and small responsive modal::

    .modal-{sm|lg}

API methods
^^^^^^^^^^^
::

  $().modal(<method>)

- methods are async.

toggle
""""""
- toggle modal.

show
""""
- show modal.

hide
""""
- hide modal.

handleUpdate
""""""""""""
- adjust the modal’s position.

dispose
""""""""
- destroy modal.

events
^^^^^^
All modal events are fired at the modal itself (div with ``.modal``).

For all following events, the triggering element (if there's one) is available
as the ``relatedTarget`` property of the event.

- show.bs.modal. 

- shown.bs.modal.

- hide.bs.modal.

- hidden.bs.modal.

utilities
=========

margin utilities
----------------

references
==========

.. [WhyBS3GridWorks] `The Subtle Magic Behind Why the Bootstrap 3 Grid Works <http://www.helloerik.com/the-subtle-magic-behind-why-the-bootstrap-3-grid-works>`_
.. [UnderstandModal] `Understanding Bootstrap Modals <https://www.sitepoint.com/understanding-bootstrap-modals/>`_