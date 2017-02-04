---
layout: docpage
title: Plasma (Unofficial) API Docs
permalink: /kde/docs/
---

<style>
/* Assume all ul in the right side are file trees */
section .right ul {
    border-left: 2px solid #bfcdd8;
    margin-left: 16px;
}
section .right li {
    position: relative;
    list-style-type: none;
    padding-left: 8px;
}
section .right li:before {
    position: absolute;
    left: -8px;
    content: "├";
    margin-right: 0;
}
section .right li:last-child:before {
    content: "└";
}
</style>

<!-- ------- -->
{% include docHeader.html label="Setup" %}

{% capture label %}Folder Structure{% endcapture %}
{% capture sectionLeft %}

First create a folder for your new widget. Inside it create another folder called `package`. Everything inside the `package` folder will be what we eventually zip and share online, so we can keep text editor project files in the `helloworld` folder.

Inside the package folder will be `metadata.desktop` file which is basically an Linux INI file. This file will describe the name of the widget, the category it's in, and various other plasma specific keys like the main qml file.

Inside `contents`, we will create the `ui` and `config` folders. `ui` is the folder which should contain your layout files like the `main.qml` and the `configGeneral.qml`. The latter is the layout for the widget's configuration window.

Inside `config` we have the `main.xml` which contains the schema of all our serialized configuration keys+values. The `config.qml` is used to define the tabs in the configuration window, and which QML layout file in the tab will open (like `ui/configGeneral.qml`).

Note that you don't *need* the 3 config files. You can get away with just the `main.qml` and `metadata.desktop` for a barebones widget.

{% endcapture %}{% capture sectionRight %}

* `helloworld/`
    * `package/`
        * `contents/`
            * `config/`
                * `config.qml`
                * `main.xml`
            * `ui/`
                * `configGeneral.qml`
                * `main.qml`
        * `metadata.desktop`

{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}metadata.desktop{% endcapture %}
{% capture sectionLeft %}

Inside the `metadata.desktop` file we need to set the `Name` of the widget. The `Type` should be `Service` since the `.desktop` file is not an app launcher and we don't want this to appear in the app menu.

`Icon` is the icon name associated with the widget. You can search for icon names in the `/usr/share/icon` folder. You can also look for an icon name by right clicking your app launcher widget then editing the icon in it's settings. It uses a searchable interface and lists them by category. Plasma's SDK also has the Cuttlefish app ([screenshot](https://www.kde.org/images/screenshots/cuttlefish.png)) which you can install with `sudo apt install plasma-sdk`.

`X-KDE-PluginInfo-Name` needs to be a unique name, since it's used for the folder name it's installed into. You could use `com.github.zren.helloworld` if you're on github, or use `org.kde.plasma.helloworld` if you're planning on contributing the widget to kde.  

Widget's installed by the user (without root) like when you "Install New Widgets" will be installed to `~/.local/share/plasma/plasmoids/` (which may not yet exist). The default widgets shipped with KDE are installed to `/usr/share/plasma/plasmoids/`.

`X-KDE-PluginInfo-Category` is the category the widget can be filtered with in the widget list. A list of category names can be found [here](https://techbase.kde.org/Projects/Plasma/PIG).

`X-KDE-ServiceTypes`, `X-Plasma-API`, and `X-Plasma-MainScript` are also needed to just define that this package is a plasma widget, and where it's entry point is.

For more, read the [Getting Started](https://techbase.kde.org/Development/Tutorials/Plasma5/QML2/GettingStarted#The_.desktop_file) tutorial on the KDE wiki.


{% endcapture %}{% capture sectionRight %}

{% highlight ini %}
[Desktop Entry]
Name=Hello World
Comment=A widget to take over the world!

Type=Service
Icon=battery
X-KDE-ServiceTypes=Plasma/Applet

X-Plasma-API=declarativeappletscript
X-Plasma-MainScript=ui/main.qml

X-KDE-PluginInfo-Author=My Name
X-KDE-PluginInfo-Email=myemail@gmail.com
X-KDE-PluginInfo-Name=com.github.zren.helloworld
X-KDE-PluginInfo-Version=1
X-KDE-PluginInfo-Website=https://github.com/Zren/plasma5-applet-helloworld
X-KDE-PluginInfo-Category=System Information
{% endhighlight %}

{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}contents/ui/main.qml{% endcapture %}
{% capture sectionLeft %}

This is the entry point. Various properties are available to set. You should know that widgets have several ways of being represented. You can have a widget in the panel, which is just an icon that will show a popup window when clicked. You can also have it on the desktop as a desktop widget which can be resized by the user. As a desktop widget it can switch between the "icon view", which opens a popup, and directly showing the popup on the desktop when there's enough room. You can also have the widget inside another widget (a containment) like the system tray or the panel itself. The widget can also be run like an application in it's own window.

`plasmoid.location` and `plasmoid.formFactor` can tell you how the widget is placed. `plasmoid` is a global variable which is defined when you `import org.kde.plasma.plasmoid 2.0`. Read more below.

`Plasmoid.compactRepresentation` (with a capital) and `Plasmoid.fullRepresentation` are used to define layout of the small "icon" view and the full "popup" view. These are both properties of the main Item. If neither are set, by default the main item is the full representation.

`Layout.preferredWidth` can be used to define the default width of a desktop/panel widget, or the size of the popup window (unless it is in the system tray). The system tray has a fixed hardcoded size for it's popups. It can also define the width of the compact "icon" view in the horizontal panel. Note that the preferredWidth/preferredHeight of the compactRepresentation will automatically scale to the thickness of the panel depending on if it's a vertical or horizontal panel.

`Layout.minimumWidth` can be used to define the minimum size for a desktop widget / popup.

You can set the tooltip contents and various other things in the `main.qml`.

#### Examples

* Various examples in the [Getting Started](https://techbase.kde.org/Development/Tutorials/Plasma5/QML2/GettingStarted#main.qml) tutorial on the KDE wiki.
* [colorpicker/package/contents/ui/main.qml](https://github.com/KDE/kdeplasma-addons/blob/master/applets/colorpicker/package/contents/ui/main.qml)
* [fifteenPuzzle/package/contents/ui/main.qml](https://github.com/KDE/kdeplasma-addons/blob/master/applets/fifteenPuzzle/package/contents/ui/main.qml)

{% endcapture %}{% capture sectionRight %}

{% highlight qml %}
import QtQuick 2.0
import org.kde.plasma.components 2.0 as PlasmaComponents

PlasmaComponents.Label {
    text: "Hello World!"
}
{% endhighlight %}

{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


<!-- ------- -->
{% include docHeader.html label="Testing" %}


{% capture label %}plasmoidviewer{% endcapture %}
{% capture sectionLeft %}
We now have enough to test our widget. If you haven't yet, install the `plasma-sdk` with `sudo apt install plasma-sdk`.
{% endcapture %}{% capture sectionRight %}

{% highlight bash %}
plasmoidviewer --help
{% endhighlight %}
{% highlight log %}
QML debugging is enabled. Only use this in a safe environment.
Usage: plasmoidviewer [options]
Run Plasma widgets in their own window

Options:
  -v, --version                    Displays version information.
  -c, --containment <containment>  The name of the containment plugin
  -a, --applet <applet>            The name of the applet plugin
  -f, --formfactor <formfactor>    The formfactor to use (horizontal, vertical,
                                   mediacenter, planar or application)
  -l, --location <location>        The location constraint to start the
                                   Containment with (floating, desktop,
                                   fullscreen, topedge, bottomedge, leftedge,
                                   rightedge)
  -x, --xPosition <xPosition>      Set the x position of the plasmoidviewer on
                                   the plasma desktop
  -y, --yPosition <yPosition>      Set the y position of the plasmoidviewer on
                                   the plasma desktop
  -s, --size <widthXheight>        Set the window size of the plasmoidview
  -p, --pixmapcache <size>         The size in kB to set the pixmap cache to
  -t, --theme <themeName>          The name of the theme which the shell will
                                   use
  -h, --help                       Displays this help.
{% endhighlight %}

{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}Test as Desktop Widget{% endcapture %}
{% capture sectionLeft %}
Note that `--location=desktop` is used for the desktop wallpaper, not desktop widgets. Desktop widgets use `--location=floating`.
{% endcapture %}{% capture sectionRight %}

{% highlight bash %}
plasmoidviewer -a package --location=floating
plasmoidviewer -a package -l=floating
plasmoidviewer -a package # floating is the default.
{% endhighlight %}

{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}Test as Horizontal Panel Widget{% endcapture %}
{% capture sectionLeft %}
plasmoidviewer isn't really a good testing setup for panel widgets, but you assign `plasmoid.formFactor` and `plasmoid.location` while still having size of a desktop widget.
{% endcapture %}{% capture sectionRight %}

{% highlight bash %}
plasmoidviewer -a package -l bottomedge -f horizontal
{% endhighlight %}

{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}Testing High DPI{% endcapture %}
{% capture sectionLeft %}
By setting the `QT_SCALE_FACTOR=2` we can set the DPI to 192 just for the plasmoidviewer window. This is great for testing if your code will support a HiDPI monitor.

If you're testing a much higher DPI, you'll probably find the default plasmoidviewer window is too small to show the widget, so we'll set the size and position of the window. Note that the window will go maximized if you set a size larger than you desktop has available.
{% endcapture %}{% capture sectionRight %}

{% highlight bash %}
QT_SCALE_FACTOR=2 plasmoidviewer -a package
QT_SCALE_FACTOR=2 plasmoidviewer -a package -l floating -f horizontal -x 0 -y 0 -s 1920x1080
{% endhighlight %}

{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


<!-- ------- -->
{% include docHeader.html label="Qml" %}


{% capture label %}Quick Intro{% endcapture %}
{% capture sectionLeft %}

This is a quick intro to QML. If you're comforatble with it, skip to the next section.

The official QML tutorial can be found in the [QML Documentation](http://doc.qt.io/qt-5/qtqml-syntax-basics.html).

{% endcapture %}{% capture sectionRight %}
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}Rectangle{% endcapture %}
{% capture sectionLeft %}

If we want to draw a colored rectangle, we can easily do so with.

{% endcapture %}{% capture sectionRight %}
{% highlight qml %}
// main.qml
import QtQuick 2.0

Rectangle {
    color: "#0ff" // Teal
}
{% endhighlight %}
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}Items are 0px wide by default{% endcapture %}
{% capture sectionLeft %}

By default, an Item will not expand to fit it's contents. Nor will it expand to fit the width of it's parent (like a `<div>` in HTML). So we need to scale.

In the this example, only the Teal Rectangle will be visible, since the Green Rectangle has the default width of 0px and height of 0px.

{% endcapture %}{% capture sectionRight %}
{% highlight qml %}
// main.qml
import QtQuick 2.0

Rectangle { // Unlike everything else, the widget's main item will have a default size.
    color: "#0ff" // Teal
    
    Rectangle { // For everything else, we need to set the size.
        color: "#00f" // Green
    }
}
{% endhighlight %}
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}anchors.fit{% endcapture %}
{% capture sectionLeft %}

In this second example, we make the Green Rectangle resize to the parent item, the Teal Rectangle. This will completely cover the Teal Rectangle so only the Green Rectangle will be visible.

{% endcapture %}{% capture sectionRight %}
{% highlight qml %}
// main.qml
import QtQuick 2.0

Rectangle { // Unlike everything else, the widget's main item will have a default size.
    color: "#0ff" // Teal
    
    Rectangle { // For everything else, we need to set the size.
        color: "#00f" // Green
        anchors.fit: parent // Make sure we're the same size as the parent.
    }
}
{% endhighlight %}
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}anchors.bottom{% endcapture %}
{% capture sectionLeft %}

In this third example, we anchor the Green Rectangle to the bottom right, and make it half the width & height of the Teal rectangle. So we end up with a rectangle which is 3/4 teal and 1/4 green.

{% endcapture %}{% capture sectionRight %}
{% highlight qml %}
// main.qml
import QtQuick 2.0

Rectangle {
    color: "#0ff" // Teal
    
    Rectangle {
        color: "#00f" // Green
        anchors.right: parent.right
        anchors.bottom: parent.bottom
        width: parent.width / 2
        height: parent.height / 2
    }
}
{% endhighlight %}
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}ColumnLayout{% endcapture %}
{% capture sectionLeft %}

If you want to stack a number of items on top of each other, you should use a [ColumnLayout](http://doc.qt.io/qt-5/qml-qtquick-layouts-columnlayout.html).

Labels (which are just fancy Text items which follow Plasma's colors) have a default font size, which means they have their own default height. So they will be stacked on top of each other.

Note that if the ColumnLayout is taller than it's contents, the children will have padding between them.

{% endcapture %}{% capture sectionRight %}
{% highlight qml %}
// main.qml
import QtQuick 2.0
import QtQuick.Layouts 1.0
import org.kde.plasma.components 2.0 as PlasmaComponents

ColumnLayout {
    PlasmaComponents.Label {
        text: "Item 1"
    }
    PlasmaComponents.Label {
        text: "Item 2"
    }
}
{% endhighlight %}
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}Layout.fitWidth: true{% endcapture %}
{% capture sectionLeft %}

If you want an item to scale to the parent's width, you have the option of setting it to be the same width as the parent (which doesn't work in a Layout). You can also try anchoring to the left and right (which does work).

Within a Layout however, the proper way to do so is to use the special property attached to the contents of a Layout, `Layout.fillWidth`. Setting it to `true` will make the item scale to fill up the empty space.

![](https://i.imgur.com/s2QXkON.png)

{% endcapture %}{% capture sectionRight %}
{% highlight qml %}
// main.qml
import QtQuick 2.0
import QtQuick.Layouts 1.0

ColumnLayout {
    Rectangle {
        color: "#f00" // Red
        height: 40
        Layout.fillWidth: true
    }
    Rectangle {
        color: "#0f0" // Green
        height: 40
        width: parent.width // Does not work
    }
    Rectangle {
        color: "#00f" // Blue
        height: 40
        width: 40 // Does not fill parent
    }
    Rectangle {
        color: "#ff0" // Yellow
        height: 40
        anchors.left: parent.left
        anchors.right: parent.right
    }
}
{% endhighlight %}
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}Layout.fitHeight: true{% endcapture %}
{% capture sectionLeft %}

If you want one item (or several) in a Layout to expand to take up the unused space, you can use `Layout.fitHeight : true`.

![](https://i.imgur.com/xo0LyfQ.png)

{% endcapture %}{% capture sectionRight %}
{% highlight qml %}
// main.qml
import QtQuick 2.0
import QtQuick.Layouts 1.0

ColumnLayout {
    Rectangle {
        color: "#f00" // Red
        height: 40
        Layout.fillWidth: true
    }
    Rectangle {
        color: "#0f0" // Green
        Layout.fillHeight: true
        Layout.fillWidth: true
    }
    Rectangle {
        color: "#00f" // Blue
        Layout.fillHeight: true
        Layout.fillWidth: true
    }
    Rectangle {
        color: "#ff0" // Yellow
        height: 40
        Layout.fillWidth: true
    }
}
{% endhighlight %}
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}Spacing between items in a Layout{% endcapture %}
{% capture sectionLeft %}

In the last screenshot you might have noticed how there is still spacing between the items. That's because the default [ColumnLayout.spacing](http://doc.qt.io/qt-5/qml-qtquick-layouts-columnlayout.html#spacing-prop) property is set to `5`. Assigning it to `0` will remove the extra whitespace.

{% endcapture %}{% capture sectionRight %}
{% highlight qml %}
// main.qml
import QtQuick 2.0
import QtQuick.Layouts 1.0

ColumnLayout {
    spacing: 0

    Rectangle {
        color: "#f00" // Red
        height: 40
        Layout.fillWidth: true
    }
    Rectangle {
        color: "#0f0" // Green
        Layout.fillHeight: true
        Layout.fillWidth: true
    }
    Rectangle {
        color: "#00f" // Blue
        Layout.fillHeight: true
        Layout.fillWidth: true
    }
    Rectangle {
        color: "#ff0" // Yellow
        height: 40
        Layout.fillWidth: true
    }
}
{% endhighlight %}
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}

{% capture label %}Other Layouts{% endcapture %}
{% capture sectionLeft %}

There's also [RowLayout](http://doc.qt.io/qt-5/qml-qtquick-layouts-rowlayout.html) and [GridLayout](http://doc.qt.io/qt-5/qml-qtquick-layouts-gridlayout.html). Lastly there's [Flow](http://doc.qt.io/qt-5/qml-qtquick-flow.html) which will treat it's contents as if they all had the CSS `display: inline-block`.

{% endcapture %}{% capture sectionRight %}
{% highlight qml %}
// main.qml
import QtQuick 2.0
import QtQuick.Layouts 1.0
import org.kde.plasma.components 2.0 as PlasmaComponents

RowLayout {
    PlasmaComponents.Label {
        text: "Item 1"
    }
    PlasmaComponents.Label {
        text: "Item 2"
    }
}
{% endhighlight %}
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}



<!-- ------- -->
{% include docHeader.html label="Plasma's QML API" %}


{% capture label %}Intro{% endcapture %}
{% capture sectionLeft %}
KDE Frameworks ships with a number of useful extensions to Qt's QML. The [API documentation](https://api.kde.org/frameworks/plasma-framework/html/index.html) is a good start if you need to know what a specific property does. If you want to browse any of the sources easier, it's also [mirrored on GitHub](https://github.com/KDE/plasma-framework/tree/master/src/declarativeimports/).


{% endcapture %}{% capture sectionRight %}
...
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}PlasmaComponents.Label{% endcapture %}
{% capture sectionLeft %}

QML ships with a [Text]() type, but Plasma extends by asigning a number of defaults. One thing is defaulting to using the color scheme's text color. For the specifics, you can read in the [`Label.qml` source code](https://github.com/KDE/plasma-framework/blob/master/src/declarativeimports/plasmacomponents/qml/Label.qml).

{% endcapture %}{% capture sectionRight %}
{% highlight qml %}
// main.qml
import QtQuick 2.0
import org.kde.plasma.components 2.0 as PlasmaComponents

PlasmaComponents.Label {
    text: "Hello World"
}
{% endhighlight %}
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


<!-- ------- -->
{% include docHeader.html label="Configuration" %}


{% capture label %}contents/config/main.xml{% endcapture %}
{% capture sectionLeft %}
...
{% endcapture %}{% capture sectionRight %}
...
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}contents/config/config.qml{% endcapture %}
{% capture sectionLeft %}
...
{% endcapture %}{% capture sectionRight %}
...
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}

{% capture label %}contents/ui/configGeneral.qml{% endcapture %}
{% capture sectionLeft %}
...
{% endcapture %}{% capture sectionRight %}
...
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}



<!-- ------- -->
{% include docHeader.html label="Translations/i18n" %}


{% capture label %}ki18n{% endcapture %}
{% capture sectionLeft %}
...
{% endcapture %}{% capture sectionRight %}
...
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}i18n(){% endcapture %}
{% capture sectionLeft %}
...
{% endcapture %}{% capture sectionRight %}
...
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}template.pot{% endcapture %}
{% capture sectionLeft %}
...
{% endcapture %}{% capture sectionRight %}
...
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}


{% capture label %}en.po{% endcapture %}
{% capture sectionLeft %}
...
{% endcapture %}{% capture sectionRight %}
...
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}



<!-- ------- -->
{% include docHeader.html label="Examples" %}


{% capture label %}Time DataSource{% endcapture %}
{% capture sectionLeft %}
...
{% endcapture %}{% capture sectionRight %}
...
{% endcapture %}{% include docSection.html label=label sectionLeft=sectionLeft sectionRight=sectionRight %}
