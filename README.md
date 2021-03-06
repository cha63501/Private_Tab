﻿Works only in Gecko 20.0 and higher because used API doesn't exist in older versions!

#### Known issues:
* We just inherit private state from selected tab and tries preserve private state of dropped link-like things, this is simple to implement, but may confuse a bit…
* If you use "New Private Tab" + "New Tab" buttons after tabs toolbar, you need to manually remove "New Private Tab" button before disabling or uninstalling Private Tab. Or you can remove "New Tab" button, press OK in Customize Toolbar dialog and then place "New Tab" directly after tabs.
* Can't open new private tab, if installed <a href="https://addons.mozilla.org/addon/scriptify/">Scriptify</a>-based extension: please use <a href="https://addons.mozilla.org/addon/greasemonkey/">Greasemonkey</a> or <a href="https://addons.mozilla.org/addon/scriptish/">Scriptish</a> instead (<a href="https://github.com/Infocatcher/Private_Tab/issues/110">#110</a>)

#### Styles:
You can use `.tabbrowser-tab[privateTab-isPrivate]` (private tab), `#main-window[privateTab-selectedTabIsPrivate]` (selected tab is private) and `#main-window[privateTab-isPrivate]` (built-in private window) selectors in styles for <a href="http://kb.mozillazine.org/UserChrome.css">userChrome.css</a>/<a href="https://addons.mozilla.org/addon/stylish/">Stylish</a>.
<br>Example styles:
* <a href="https://github.com/Infocatcher/UserStyles/blob/master/Private_Tab_underline">change underline of private tabs</a>
* <a href="https://github.com/Infocatcher/UserStyles/blob/master/Private_Tab_icon">change icon</a>
* <a href="https://github.com/Infocatcher/UserStyles/blob/master/Private_Tab_background">change background color</a>
* <a href="https://github.com/Infocatcher/UserStyles/blob/master/Private_Tab_overlay_icon">add overlay icon</a>
* <a href="https://github.com/Infocatcher/UserStyles/blob/master/Private_Tab_menu_icons">add icons to menu items</a>
* <a href="https://github.com/Infocatcher/UserStyles/blob/master/Private_Tab_hide_items">hide some menu items</a>

#### Keyboard shortcuts:
You can modify keyboard shortcuts through <a href="http://kb.mozillazine.org/About:config">about:config</a> page, see notes about <em>extensions.privateTab.key.</em>\* preferences in <a href="https://github.com/Infocatcher/Private_Tab/blob/master/defaults/preferences/prefs.js">defaults/preferences/prefs.js</a>.

#### API for other extensions:
##### Events:
You can listen for following events:
<table>
<thead>
<tr>
	<th>event.type</th>
	<th>event.target</th>
	<th>event.detail</th>
	<th>Description</th>
</tr>
</thead>
<tbody>
	<tr>
		<td>PrivateTab:PrivateChanged</td>
		<td>tab</td>
		<td>1 – private tab<br>0 – not private</td>
		<td>Changed private state of the tab</td>
	</tr>
	<tr>
		<td>PrivateTab:OpenInNewTab</td>
		<td>tab</td>
		<td>1 – may be opened as child tab</td>
		<td>Link was opened in new private tab</td>
	</tr>
	<tr>
		<td>PrivateTab:OpenNewTab</td>
		<td>tab</td>
		<td>1 – opened using middle-click<br>(or left-click with any modifier)</td>
		<td>Opened new (empty) private tab</td>
	</tr>
</tbody>
</table>

##### API functions:
boolean [privateTab.isTabPrivate](#privatetabistabprivate)(in DOMNode tab)
<br>boolean [privateTab.toggleTabPrivate](#privatetabtoggletabprivate)(in DOMNode tab[, in boolean isPrivate])
<br>void [privateTab.readyToOpenTab](#privatetabreadytoopentab)(in boolean isPrivate)
<br>void [privateTab.readyToOpenTabs](#privatetabreadytoopentabs)(in boolean isPrivate)
<br>boolean [privateTab.hasClosedTabs](#privatetabhasclosedtabs)
<br>void [privateTab.forgetClosedTabs](#privatetabforgetclosedtabs)()
<br>boolean [privateTab.tabLabelIsEmpty](#privatetabtablabelisempty)(in string tabLabel[, in boolean isEmpty])

###### privateTab.isTabPrivate()
Investigates that the tab are private (`true`) or not (`false`), example:
```js
// Close all (visible) private tabs:
Array.slice(gBrowser.visibleTabs || gBrowser.tabs).forEach(function(tab) {
	if(privateTab.isTabPrivate(tab))
		gBrowser.removeTab(tab);
});
```
###### privateTab.toggleTabPrivate()
Changes tab private state:
<br>Toggle: `privateTab.toggleTabPrivate(tab)`
<br>Make private: `privateTab.toggleTabPrivate(tab, true)`
<br>Make not private: `privateTab.toggleTabPrivate(tab, false)`
```js
// Make all (visible) tabs private:
Array.forEach(
	gBrowser.visibleTabs || gBrowser.tabs,
	function(tab) {
		if(!privateTab.isTabPrivate(tab))
			privateTab.toggleTabPrivate(tab, true);
	}
);
```
###### privateTab.readyToOpenTab()
Allows to open private or not private tab (independent of any inheritance mechanism), example:
```js
// Open in private tab:
privateTab.readyToOpenTab(true);
gBrowser.addTab("https://mozilla.org/");
```
```js
// Open in not private tab:
privateTab.readyToOpenTab(false);
gBrowser.addTab("https://mozilla.org/");
```
###### privateTab.readyToOpenTabs()
Allows to open many private or not private tabs (independent of any inheritance mechanism), example:
```js
// Open in private tabs:
privateTab.readyToOpenTabs(true);
gBrowser.addTab("https://mozilla.org/");
gBrowser.addTab("https://addons.mozilla.org/");
// ...
privateTab.stopToOpenTabs();
```
###### privateTab.hasClosedTabs
Only for <em>extensions.privateTab.rememberClosedPrivateTabs</em> = true, Private Tab 0.1.7.4+.
<br>Return true, if there is at least one private tab in undo close list, example:
```js
if(privateTab.hasClosedTabs)
	alert("We have at least one closed private tabs");
else
	alert("We don't have closed private tabs");
```
###### privateTab.forgetClosedTabs()
Only for <em>extensions.privateTab.rememberClosedPrivateTabs</em> = true, Private Tab 0.1.7.4+.
<br>Forget about all closed private tabs in window, example:
```js
// Forget about all closed private tabs in window
privateTab.forgetClosedTabs();
```
###### privateTab.tabLabelIsEmpty()
Mark tab label as empty (or non-empty), example:
```js
// Mark tab label/URI as empty:
if("tabLabelIsEmpty" in privateTab) // Private Tab 0.1.7.2+
	privateTab.tabLabelIsEmpty("chrome://speeddial/content/speeddial.xul", true);
```
```js
// Check state:
var isEmpty = privateTab.tabLabelIsEmpty("chrome://speeddial/content/speeddial.xul");
```
```js
// Restore state (e.g. for restartless extensions):
privateTab.tabLabelIsEmpty("chrome://speeddial/content/speeddial.xul", false);
```
Note: used global storage for labels (not per-window)! So, it's enough to call this function only once.

##### Backward compatibility:
Check for Private Tab installed (and enabled):
```js
if("privateTab" in window) {
	// Do something with "privateTab" object
}
```