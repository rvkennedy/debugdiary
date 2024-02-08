---
title: "Advanced custom Qt Container Widgets and Qt Designer"
datePublished: Sat Jun 03 2017 13:07:00 GMT+0000 (Coordinated Universal Time)
cuid: clsd6rflw000609ladr2q8zut
slug: advanced-custom-qt-container-widgets-and-qt-designer
canonical: https://roderickkennedy.com/dbgdiary/advanced-custom-qt-container-widgets-and-qt-designer
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707394768308/02d0b4d3-af12-4461-98ab-64be705d0947.png

---

Advanced custom Qt Container Widgets and Qt Designer
====================================================

3 Jun

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Qt has a nice UI editor called Designer, and you can create custom widgets that go in Designer's toolkit. But the only example I've ever found is [this one](http://doc.qt.io/qt-5/designer-creating-custom-widgets.html) in the Qt docs, which doesn't explain how to create container widgets.  
  
The problem is to create a widget that contains some decoration or controls, but also has a sub-window where people can put their own widgets.  
  
For example, I wanted an "accordion" control that had a checkbox at the top to open and close it, then to be able to put any other control inside this.  
  
The way it will be structured is a QAccordion, with a VBoxLayout, will contain a QCheckbox and a QWidget called the content widget. This content widget will have its own VBoxLayout, where the controls will go.  
  
You will create two classes: one called (say) QAccordion, which implements the widget, and one called QAccordionInterface, which tells Designer that it's available.

    
    <#include <qwidget>
    #include "GeneratedFiles/ui_QAccordion.h"
    #include "Export.h"
    
    class SIMUL_QT_WIDGETS_EXPORT QAccordion : public QWidget
    {
     Q_OBJECT
      
     Q_PROPERTY(QString title READ title WRITE setTitle DESIGNABLE true)
     Q_PROPERTY(bool open READ isOpen WRITE setOpen DESIGNABLE true)
    public:
     QAccordion(QWidget *parent = 0);
     ~QAccordion();
     void setTitle(QString f);
     QString title() const;
     void setOpen(bool o);
     bool isOpen() const;
    public slots:
     void on_accordionCheckBox_toggled();
     void setSearchText(const QString &);
    signals:
    protected:
     void childEvent ( QChildEvent * event ) override;
     void paintEvent(QPaintEvent *) override;
    private:
     Ui::Accordion ui;
     bool setup_complete;
     QWidget *contentsWidget;
     void hookupContentsWidget();
    };
    

The subclass Ui::Accordion shows that I created the basic class in Designer itself. This is optional, but the QAccordion.ui file is just:

    
    <?xml version="1.0" encoding="UTF-8"?>
    <ui version="4.0">
     <class>Accordion</class>
     <widget class="QWidget" name="Accordion">
      <property name="geometry">
       <rect>
        <x>0</x>
        <y>0</y>
        <width>671</width>
        <height>336</height>
       </rect>
      </property>
      <property name="sizePolicy">
       <sizepolicy hsizetype="Preferred" vsizetype="Preferred">
        <horstretch>0</horstretch>
        <verstretch>0</verstretch>
       </sizepolicy>
      </property>
      <property name="windowTitle">
       <string>Accordion</string>
      </property>
      <layout class="QVBoxLayout" name="verticalLayout">
       <item>
        <widget class="QCheckBox" name="accordionCheckBox">
         <property name="text">
          <string>Accordion</string>
         </property>
         <property name="checked">
          <bool>true</bool>
         </property>
        </widget>
       </item>
      </layout>
     </widget>
     <resources/>
     <connections/>
    </ui>
    
    

By putting the layout and checkbox in the ui file, they will be created in code, in Ui::Accordion.  
  
But: if we were to create the whole thing, including the contents widget in here, after we built the class the contents widget would NOT be accessible in Designer, and neither would its layout be recognized. So instead we put these in a function called domXml in QAccordionInterface.  
  

     QString QAccordionInterface::domXml() const
     {
         return "<ui language=\"c++\">\n"
                " <widget class=\"QAccordion\" name=\"accordion\">\n"
                "  <property name=\"geometry\">\n"
                "   <rect>\n"
                "    <x>0</x>\n"
                "    <y>0</y>\n"
                "    <width>100</width>\n"
                "    <height>24</height>\n"
                "   </rect>\n"
                "  </property>\n"
                "  <property name=\"toolTip\" >\n"
                "   <string></string>\n"
                "  </property>\n"
                "  <property name=\"whatsThis\" >\n"
                "   <string>.</string>\n"
                "  </property>\n"
          "  <widget class=\"QWidget\" name=\"contentsWidget\" native=\"true\" >\n"
          "   <layout class=\"QVBoxLayout\" name=\"accContentsVLayout\">\n"
          "    <property name=\"spacing\">\n"
          "     <number>2</number>\n"
          "    </property>\n"
          "    <property name=\"leftMargin\">\n"
          "     <number>2</number>\n"
          "    </property>\n"
          "    <property name=\"topMargin\">\n"
          "     <number>2</number>\n"
          "    </property>\n"
          "    <property name=\"rightMargin\">\n"
          "     <number>2</number>\n"
          "    </property>\n"
          "    <property name=\"bottomMargin\">\n"
          "     <number>2</number>\n"
          "    </property>\n"
           "    </layout>\n"
          "  </widget>\n"
                " </widget>\n"
                "</ui>\n";
     }
    
    

By specifying the contents widget and its layout here, Designer will know to dynamically create them when you add a QAccordion, so they'll appear in the editor. You can then drag any control into the contents widget, and it will be correctly positioned. Be careful that you drag it to the contents widget and not the QAccordion itself or a subcontrol. Designer doesn't properly obey its "isContainer" function, so it sees any custom control as a container, not just the ones you indicate.  
  
So now in designer, we can add QAccordions. Without styling they just look like checkboxes with a space below where you can drag controls:  

![designer.PNG](https://cdn.hashnode.com/res/hashnode/image/upload/v1707394764072/a70c2842-61a5-406a-aa51-d59a761d7361.png)

After applying some styling, the final result looks like this:

![accordion.PNG](https://cdn.hashnode.com/res/hashnode/image/upload/v1707394765311/a2e50ac5-429b-41d6-93f5-08249519a1f1.png)

The accordion elements - Cloud Window, Precipitation etc are inside a searchable property panel, implemented on the same principles.  
  
And here are the files for the final class:  
  
[QAccordion.zip](https://simul.co/wp-content/uploads/blog-files/QAccordion.zip)

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Advanced custom Qt Container Widgets and Qt Designer
====================================================

3 Jun

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Qt has a nice UI editor called Designer, and you can create custom widgets that go in Designer's toolkit. But the only example I've ever found is [this one](http://doc.qt.io/qt-5/designer-creating-custom-widgets.html) in the Qt docs, which doesn't explain how to create container widgets.  
  
The problem is to create a widget that contains some decoration or controls, but also has a sub-window where people can put their own widgets.  
  
For example, I wanted an "accordion" control that had a checkbox at the top to open and close it, then to be able to put any other control inside this.  
  
The way it will be structured is a QAccordion, with a VBoxLayout, will contain a QCheckbox and a QWidget called the content widget. This content widget will have its own VBoxLayout, where the controls will go.  
  
You will create two classes: one called (say) QAccordion, which implements the widget, and one called QAccordionInterface, which tells Designer that it's available.

    
    <#include <qwidget>
    #include "GeneratedFiles/ui_QAccordion.h"
    #include "Export.h"
    
    class SIMUL_QT_WIDGETS_EXPORT QAccordion : public QWidget
    {
     Q_OBJECT
      
     Q_PROPERTY(QString title READ title WRITE setTitle DESIGNABLE true)
     Q_PROPERTY(bool open READ isOpen WRITE setOpen DESIGNABLE true)
    public:
     QAccordion(QWidget *parent = 0);
     ~QAccordion();
     void setTitle(QString f);
     QString title() const;
     void setOpen(bool o);
     bool isOpen() const;
    public slots:
     void on_accordionCheckBox_toggled();
     void setSearchText(const QString &);
    signals:
    protected:
     void childEvent ( QChildEvent * event ) override;
     void paintEvent(QPaintEvent *) override;
    private:
     Ui::Accordion ui;
     bool setup_complete;
     QWidget *contentsWidget;
     void hookupContentsWidget();
    };
    

The subclass Ui::Accordion shows that I created the basic class in Designer itself. This is optional, but the QAccordion.ui file is just:

    
    <?xml version="1.0" encoding="UTF-8"?>
    <ui version="4.0">
     <class>Accordion</class>
     <widget class="QWidget" name="Accordion">
      <property name="geometry">
       <rect>
        <x>0</x>
        <y>0</y>
        <width>671</width>
        <height>336</height>
       </rect>
      </property>
      <property name="sizePolicy">
       <sizepolicy hsizetype="Preferred" vsizetype="Preferred">
        <horstretch>0</horstretch>
        <verstretch>0</verstretch>
       </sizepolicy>
      </property>
      <property name="windowTitle">
       <string>Accordion</string>
      </property>
      <layout class="QVBoxLayout" name="verticalLayout">
       <item>
        <widget class="QCheckBox" name="accordionCheckBox">
         <property name="text">
          <string>Accordion</string>
         </property>
         <property name="checked">
          <bool>true</bool>
         </property>
        </widget>
       </item>
      </layout>
     </widget>
     <resources/>
     <connections/>
    </ui>
    
    

By putting the layout and checkbox in the ui file, they will be created in code, in Ui::Accordion.  
  
But: if we were to create the whole thing, including the contents widget in here, after we built the class the contents widget would NOT be accessible in Designer, and neither would its layout be recognized. So instead we put these in a function called domXml in QAccordionInterface.  
  

     QString QAccordionInterface::domXml() const
     {
         return "<ui language=\"c++\">\n"
                " <widget class=\"QAccordion\" name=\"accordion\">\n"
                "  <property name=\"geometry\">\n"
                "   <rect>\n"
                "    <x>0</x>\n"
                "    <y>0</y>\n"
                "    <width>100</width>\n"
                "    <height>24</height>\n"
                "   </rect>\n"
                "  </property>\n"
                "  <property name=\"toolTip\" >\n"
                "   <string></string>\n"
                "  </property>\n"
                "  <property name=\"whatsThis\" >\n"
                "   <string>.</string>\n"
                "  </property>\n"
          "  <widget class=\"QWidget\" name=\"contentsWidget\" native=\"true\" >\n"
          "   <layout class=\"QVBoxLayout\" name=\"accContentsVLayout\">\n"
          "    <property name=\"spacing\">\n"
          "     <number>2</number>\n"
          "    </property>\n"
          "    <property name=\"leftMargin\">\n"
          "     <number>2</number>\n"
          "    </property>\n"
          "    <property name=\"topMargin\">\n"
          "     <number>2</number>\n"
          "    </property>\n"
          "    <property name=\"rightMargin\">\n"
          "     <number>2</number>\n"
          "    </property>\n"
          "    <property name=\"bottomMargin\">\n"
          "     <number>2</number>\n"
          "    </property>\n"
           "    </layout>\n"
          "  </widget>\n"
                " </widget>\n"
                "</ui>\n";
     }
    
    

By specifying the contents widget and its layout here, Designer will know to dynamically create them when you add a QAccordion, so they'll appear in the editor. You can then drag any control into the contents widget, and it will be correctly positioned. Be careful that you drag it to the contents widget and not the QAccordion itself or a subcontrol. Designer doesn't properly obey its "isContainer" function, so it sees any custom control as a container, not just the ones you indicate.  
  
So now in designer, we can add QAccordions. Without styling they just look like checkboxes with a space below where you can drag controls:  

![designer.PNG](https://cdn.hashnode.com/res/hashnode/image/upload/v1707394766247/962b649a-d05b-4197-b022-f5ac577a179d.png)

After applying some styling, the final result looks like this:

![accordion.PNG](https://cdn.hashnode.com/res/hashnode/image/upload/v1707394767019/ef2db7a4-e9a1-421f-a302-b6bd739ea052.png)

The accordion elements - Cloud Window, Precipitation etc are inside a searchable property panel, implemented on the same principles.  
  
And here are the files for the final class:  
  
[QAccordion.zip](https://simul.co/wp-content/uploads/blog-files/QAccordion.zip)

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)