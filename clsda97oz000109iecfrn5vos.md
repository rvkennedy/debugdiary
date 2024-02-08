---
title: "Advanced custom Qt Container Widgets and Qt Designer"
seoTitle: "Advanced custom Qt Container Widgets and Qt Designer"
datePublished: Sat Jun 03 2017 13:07:00 GMT+0000 (Coordinated Universal Time)
cuid: clsda97oz000109iecfrn5vos
slug: advanced-custom-qt-container-widgets-and-qt-designer-1
canonical: https://roderickkennedy.com/dbgdiary/advanced-custom-qt-container-widgets-and-qt-designer
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707397865579/a060371c-5310-4a07-b4c4-86326b3104ff.png

---

3 Jun

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Qt has a nice UI editor called Designer, and you can create custom widgets that go in Designer's toolkit. But the only example I've ever found is [this one](http://doc.qt.io/qt-5/designer-creating-custom-widgets.html) in the Qt docs, which doesn't explain how to create container widgets.

The problem is to create a widget that contains some decoration or controls, but also has a sub-window where people can put their own widgets.

For example, I wanted an "accordion" control that had a checkbox at the top to open and close it, then to be able to put any other control inside this.

The way it will be structured is a QAccordion, with a VBoxLayout, will contain a QCheckbox and a QWidget called the content widget. This content widget will have its own VBoxLayout, where the controls will go.

You will create two classes: one called (say) QAccordion, which implements the widget, and one called QAccordionInterface, which tells Designer that it's available.

&lt;#include #include "GeneratedFiles/ui\_QAccordion.h" #include "Export.h"

class SIMUL\_QT\_WIDGETS\_EXPORT QAccordion : public QWidget { Q\_OBJECT

Q\_PROPERTY(QString title READ title WRITE setTitle DESIGNABLE true) Q\_PROPERTY(bool open READ isOpen WRITE setOpen DESIGNABLE true) public: QAccordion(QWidget \*parent = 0); ~QAccordion(); void setTitle(QString f); QString title() const; void setOpen(bool o); bool isOpen() const; public slots: void on\_accordionCheckBox\_toggled(); void setSearchText(const QString &); signals: protected: void childEvent ( QChildEvent \* event ) override; void paintEvent(QPaintEvent \*) override; private: Ui::Accordion ui; bool setup\_complete; QWidget \*contentsWidget; void hookupContentsWidget(); };

The subclass Ui::Accordion shows that I created the basic class in Designer itself. This is optional, but the QAccordion.ui file is just:

?xml version="1.0" encoding="UTF-8"? Accordion0067133600AccordionAccordiontrue

By putting the layout and checkbox in the ui file, they will be created in code, in Ui::Accordion.

But: if we were to create the whole thing, including the contents widget in here, after we built the class the contents widget would NOT be accessible in Designer, and neither would its layout be recognized. So instead we put these in a function called domXml in QAccordionInterface.

QString QAccordionInterface::domXml() const { return "&lt;ui language="c++"&gt;\\n" " &lt;widget class="QAccordion" name="accordion"&gt;\\n" " &lt;property name="geometry"&gt;\\n" " \\n" " 0\\n" " 0\\n" " 100\\n" " 24\\n" " \\n" " \\n" " &lt;property name="toolTip" &gt;\\n" " \\n" " \\n" " &lt;property name="whatsThis" &gt;\\n" " .\\n" " \\n" " &lt;widget class="QWidget" name="contentsWidget" native="true" &gt;\\n" " &lt;layout class="QVBoxLayout" name="accContentsVLayout"&gt;\\n" " &lt;property name="spacing"&gt;\\n" " 2\\n" " \\n" " &lt;property name="leftMargin"&gt;\\n" " 2\\n" " \\n" " &lt;property name="topMargin"&gt;\\n" " 2\\n" " \\n" " &lt;property name="rightMargin"&gt;\\n" " 2\\n" " \\n" " &lt;property name="bottomMargin"&gt;\\n" " 2\\n" " \\n" " \\n" " \\n" " \\n" "\\n"; }

By specifying the contents widget and its layout here, Designer will know to dynamically create them when you add a QAccordion, so they'll appear in the editor. You can then drag any control into the contents widget, and it will be correctly positioned. Be careful that you drag it to the contents widget and not the QAccordion itself or a subcontrol. Designer doesn't properly obey its "isContainer" function, so it sees any custom control as a container, not just the ones you indicate.

So now in designer, we can add QAccordions. Without styling they just look like checkboxes with a space below where you can drag controls:

![designer.PNG](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397861638/e622321c-fe37-4800-8d71-3dc4cc59475e.png align="left")

After applying some styling, the final result looks like this:

![accordion.PNG](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397862855/37014f18-d923-40bf-9696-44c9a865b528.png align="left")

The accordion elements - Cloud Window, Precipitation etc are inside a searchable property panel, implemented on the same principles.

And here are the files for the final class:

[QAccordion.zip](https://simul.co/wp-content/uploads/blog-files/QAccordion.zip)

[Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

# Advanced custom Qt Container Widgets and Qt Designer

3 Jun

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Qt has a nice UI editor called Designer, and you can create custom widgets that go in Designer's toolkit. But the only example I've ever found is [this one](http://doc.qt.io/qt-5/designer-creating-custom-widgets.html) in the Qt docs, which doesn't explain how to create container widgets.

The problem is to create a widget that contains some decoration or controls, but also has a sub-window where people can put their own widgets.

For example, I wanted an "accordion" control that had a checkbox at the top to open and close it, then to be able to put any other control inside this.

The way it will be structured is a QAccordion, with a VBoxLayout, will contain a QCheckbox and a QWidget called the content widget. This content widget will have its own VBoxLayout, where the controls will go.

You will create two classes: one called (say) QAccordion, which implements the widget, and one called QAccordionInterface, which tells Designer that it's available.

&lt;#include #include "GeneratedFiles/ui\_QAccordion.h" #include "Export.h"

class SIMUL\_QT\_WIDGETS\_EXPORT QAccordion : public QWidget { Q\_OBJECT

Q\_PROPERTY(QString title READ title WRITE setTitle DESIGNABLE true) Q\_PROPERTY(bool open READ isOpen WRITE setOpen DESIGNABLE true) public: QAccordion(QWidget \*parent = 0); ~QAccordion(); void setTitle(QString f); QString title() const; void setOpen(bool o); bool isOpen() const; public slots: void on\_accordionCheckBox\_toggled(); void setSearchText(const QString &); signals: protected: void childEvent ( QChildEvent \* event ) override; void paintEvent(QPaintEvent \*) override; private: Ui::Accordion ui; bool setup\_complete; QWidget \*contentsWidget; void hookupContentsWidget(); };

The subclass Ui::Accordion shows that I created the basic class in Designer itself. This is optional, but the QAccordion.ui file is just:

?xml version="1.0" encoding="UTF-8"? Accordion0067133600AccordionAccordiontrue

By putting the layout and checkbox in the ui file, they will be created in code, in Ui::Accordion.

But: if we were to create the whole thing, including the contents widget in here, after we built the class the contents widget would NOT be accessible in Designer, and neither would its layout be recognized. So instead we put these in a function called domXml in QAccordionInterface.

QString QAccordionInterface::domXml() const { return "&lt;ui language="c++"&gt;\\n" " &lt;widget class="QAccordion" name="accordion"&gt;\\n" " &lt;property name="geometry"&gt;\\n" " \\n" " 0\\n" " 0\\n" " 100\\n" " 24\\n" " \\n" " \\n" " &lt;property name="toolTip" &gt;\\n" " \\n" " \\n" " &lt;property name="whatsThis" &gt;\\n" " .\\n" " \\n" " &lt;widget class="QWidget" name="contentsWidget" native="true" &gt;\\n" " &lt;layout class="QVBoxLayout" name="accContentsVLayout"&gt;\\n" " &lt;property name="spacing"&gt;\\n" " 2\\n" " \\n" " &lt;property name="leftMargin"&gt;\\n" " 2\\n" " \\n" " &lt;property name="topMargin"&gt;\\n" " 2\\n" " \\n" " &lt;property name="rightMargin"&gt;\\n" " 2\\n" " \\n" " &lt;property name="bottomMargin"&gt;\\n" " 2\\n" " \\n" " \\n" " \\n" " \\n" "\\n"; }

By specifying the contents widget and its layout here, Designer will know to dynamically create them when you add a QAccordion, so they'll appear in the editor. You can then drag any control into the contents widget, and it will be correctly positioned. Be careful that you drag it to the contents widget and not the QAccordion itself or a subcontrol. Designer doesn't properly obey its "isContainer" function, so it sees any custom control as a container, not just the ones you indicate.

So now in designer, we can add QAccordions. Without styling they just look like checkboxes with a space below where you can drag controls:

![designer.PNG](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397863858/6d59cfae-a8e2-4f08-8a6a-32d0a40b11a1.png align="left")

After applying some styling, the final result looks like this:

![accordion.PNG](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397864607/8f42c0f9-4ae0-481c-af2a-b0cc528fa9ab.png align="left")

The accordion elements - Cloud Window, Precipitation etc are inside a searchable property panel, implemented on the same principles.

And here are the files for the final class:

[QAccordion.zip](https://simul.co/wp-content/uploads/blog-files/QAccordion.zip)

[Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)