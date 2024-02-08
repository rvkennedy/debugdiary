---
title: "How to make a custom Wizard for Unreal Editor"
datePublished: Thu Jun 21 2018 13:13:00 GMT+0000 (Coordinated Universal Time)
cuid: clsd6r8fs000609l741rd2zqg
slug: how-to-make-a-custom-wizard-for-unreal-editor
canonical: https://roderickkennedy.com/dbgdiary/how-to-make-a-custom-wizard-for-unreal-editor
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707394758660/662df265-4967-4470-9630-7256449cd79d.png

---

How to make a custom Wizard for Unreal Editor
=============================================

21 Jun

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

I wanted to create a wizard in the [trueSKY](https://simul.co/truesky) Unreal plugin that would make it easier for users to add trueSKY to UE scenes. I was following [this video](https://www.youtube.com/watch?v=zg_VstBxDi8&t=1482s) where Epic's Michael Noland describes various ways to modify the Editor. So I made a custom Property Editor window with settings to select a sky sequence, create a TrueSkyLight etc.  
  
But it didn't look very friendly. And implementing a wizard-style Apply button just put a button in amongst the other settings - not great. After some searching in the UE codebase, I discovered the SWizard class that Unreal Editor uses for its own wizards. Here's what you do:  
  
1\. Create a class derived from SCompoundWidget containing a TSharedPtr&lt;SWizard&gt;. Mine looks like this:  
  
  
DECLARE\_DELEGATE\_FourParams( FOnTrueSkySetup, bool, ADirectionalLight\* ,bool, UTrueSkySequenceAsset \*);  

    #define S_DECLARE_CHECKBOX(name) \
     bool name; \
     ECheckBoxState Is##name##Checked() const { return name ? ECheckBoxState::Checked:ECheckBoxState::Unchecked;} \
     void On##name##Changed(ECheckBoxState InCheckedState) {name=(InCheckedState==ECheckBoxState::Checked);}
    
    class STrueSkySetupTool : public SCompoundWidget
    {
    public:
     SLATE_BEGIN_ARGS( STrueSkySetupTool )
      :_CreateTrueSkyLight(false)
      ,_DirectionalLight(nullptr)
      ,_CreateDirectionalLight(nullptr)
      ,_Sequence(nullptr)
     {}
     /** A TrueSkyLight actor performs real-time ambient lighting.*/
     SLATE_ARGUMENT(bool,CreateTrueSkyLight)
     /** TrueSKY can drive a directional light to provide sunlight and moonlight.*/
     SLATE_ARGUMENT(ADirectionalLight*,DirectionalLight)
     /** If there's no directional light in the scene, you can create one with this checkbox.*/
     SLATE_ARGUMENT(bool,CreateDirectionalLight)
     /** The TrueSKY Sequence provides the weather state to render.*/
     SLATE_ARGUMENT(UTrueSkySequenceAsset *,Sequence)
     /** Event called when code is successfully added to the project */
     SLATE_EVENT( FOnTrueSkySetup, OnTrueSkySetup )
     SLATE_END_ARGS()
     /** Constructs this widget with InArgs */
     void Construct( const FArguments& InArgs );
     
     /** Handler for when cancel is clicked */
     void CancelClicked();
    
     /** Returns true if Finish is allowed */
     bool CanFinish() const;
    
     /** Handler for when finish is clicked */
     void FinishClicked();
    
    ...
     
     S_DECLARE_CHECKBOX(CreateTrueSkyLight)
     S_DECLARE_CHECKBOX(ShowAllSequences)
    
     void SetupSequenceAssetItems();
     
     void CloseContainingWindow();
    private:
     /** The wizard widget */
     TSharedPtr<SWizard> MainWizard;
     FOnTrueSkySetup OnTrueSkySetup;
    ...
    };
    
    

  
The SLATE\_ARGUMENT macros allow initialization of named parameters in this style:  
  

    TSharedRef<STrueSkySetupTool> TrueSkySetupTool = SNew(STrueSkySetupTool).OnTrueSkySetup(OnTrueSkySetup1).CreateTrueSkyLight(true);
    

  
etc. This is super-useful.  
  
2\. Create a callback for the wizard to execute:  

    FOnTrueSkySetup OnTrueSkySetupDelegate;
    

3\. Create a window for the widget. This function is called when the menu option to start the wizard is selected:  
  

    void FTrueSkyEditorPlugin::OnAddSequence()
    {
     TrueSkySetupWindow = SNew(SWindow)
       .Title( NSLOCTEXT("InitializeTrueSky", "WindowTitle", "Initialize trueSKY") )
       .ClientSize( FVector2D(600, 550) )
       .SizingRule( ESizingRule::FixedSize )
       .SupportsMinimize(false).SupportsMaximize(false);
     OnTrueSkySetupDelegate.BindRaw(this,&FTrueSkyEditorPlugin::OnTrueSkySetup);
     TSharedRef TrueSkySetupTool = SNew(STrueSkySetupTool).OnTrueSkySetup(OnTrueSkySetupDelegate);
     TrueSkySetupWindow->SetContent( TrueSkySetupTool );
    

  
If the main frame exists parent the window to it. The main frame should always exist...  

     TSharedPtr< SWindow > ParentWindow;
     if( FModuleManager::Get().IsModuleLoaded( "MainFrame" ) )
     {
      IMainFrameModule& MainFrame = FModuleManager::GetModuleChecked<imainframemodule>( "MainFrame" );
      ParentWindow = MainFrame.GetParentWindow();
     }
     
     bool modal=false;
     if (modal)
     {
      FSlateApplication::Get().AddModalWindow(TrueSkySetupWindow.ToSharedRef(), ParentWindow);
     }
     else if (ParentWindow.IsValid())
     {
      FSlateApplication::Get().AddWindowAsNativeChild(TrueSkySetupWindow.ToSharedRef(), ParentWindow.ToSharedRef());
     }
     else
     {
      FSlateApplication::Get().AddWindow(TrueSkySetupWindow.ToSharedRef());
     }
     TrueSkySetupWindow->ShowWindow();
    }
    

4\. Implement the setup tool:  

    BEGIN_SLATE_FUNCTION_BUILD_OPTIMIZATION
    void STrueSkySetupTool::Construct( const FArguments& InArgs )
    {
     OnTrueSkySetup = InArgs._OnTrueSkySetup;
     CreateTrueSkyLight=InArgs._CreateTrueSkyLight;
     DirectionalLight=InArgs._DirectionalLight;
     Sequence=InArgs._Sequence;
    ...
    

The interface to build the actual UI is really interesting. By overloading the \[\] and + operators, Epic lets you specify the widget structure like so:  
  

     ChildSlot
     [
      SNew(SBorder)
      .Padding(18)
      .BorderImage( FEditorStyle::GetBrush("Docking.Tab.ContentAreaBrush") )
      [
       SNew(SVerticalBox)
       +SVerticalBox::Slot()
       [
        SAssignNew( MainWizard, SWizard)
        .ShowPageList(false)
        .CanFinish(this, &STrueSkySetupTool::CanFinish)
        .FinishButtonText(  LOCTEXT("TrueSkyFinishButtonText", "Initialize") )
        .OnCanceled(this, &STrueSkySetupTool::CancelClicked)
        .OnFinished(this, &STrueSkySetupTool::FinishClicked)
        .InitialPageIndex( 0)
        +SWizard::Page()
        [
         SNew(SVerticalBox)
         +SVerticalBox::Slot()
         .AutoHeight()
         [
          SNew(STextBlock)
          .TextStyle( FEditorStyle::Get(), "NewClassDialog.PageTitle" )
          .Text( LOCTEXT( "WeatherStateTitle", "Choose a Sequence Asset" ) )
         ]
         +SVerticalBox::Slot()
         .AutoHeight()
         .Padding(0)
         [
          SNew(SHorizontalBox)
          +SHorizontalBox::Slot()
          .FillWidth(1.f)
          .VAlign(VAlign_Center)
          [
           SNew(STextBlock)
           .Text(LOCTEXT("TrueSkySetupToolDesc", "Choose which weather sequence to use initially.") )
           .AutoWrapText(true)
           .TextStyle(FEditorStyle::Get(), "NewClassDialog.ParentClassItemTitle")
          ]
         ]
        ]
        +SWizard::Page()
        [
         ...
        ]
       ]
      ]
     ];
    
    }
    

  
So by adding new +SWizard::Page() elements we add pages to the wizard.  
  
5\. Finally, implement the callback that the delegate calls when you click "Finish":  
  

    void FTrueSkyEditorPlugin::OnTrueSkySetup(bool CreateDirectionalLight, ADirectionalLight* DirectionalLight,bool CreateTrueSkyLight,UTrueSkySequenceAsset *Sequence)
    {
    ...
    }
    

  
The end result looks like this:

![image (4).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1707394754040/040e4471-660e-45c0-847f-5b93286e79f8.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707394755359/404b4d59-d3aa-4472-9d32-2fdaaea150e2.png)

Full source for this is at our UE branch, (register at [Simul](https://simul.co/register) to access).

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

How to make a custom Wizard for Unreal Editor
=============================================

21 Jun

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

I wanted to create a wizard in the [trueSKY](https://simul.co/truesky) Unreal plugin that would make it easier for users to add trueSKY to UE scenes. I was following [this video](https://www.youtube.com/watch?v=zg_VstBxDi8&t=1482s) where Epic's Michael Noland describes various ways to modify the Editor. So I made a custom Property Editor window with settings to select a sky sequence, create a TrueSkyLight etc.  
  
But it didn't look very friendly. And implementing a wizard-style Apply button just put a button in amongst the other settings - not great. After some searching in the UE codebase, I discovered the SWizard class that Unreal Editor uses for its own wizards. Here's what you do:  
  
1\. Create a class derived from SCompoundWidget containing a TSharedPtr&lt;SWizard&gt;. Mine looks like this:  
  
  
DECLARE\_DELEGATE\_FourParams( FOnTrueSkySetup, bool, ADirectionalLight\* ,bool, UTrueSkySequenceAsset \*);  

    #define S_DECLARE_CHECKBOX(name) \
     bool name; \
     ECheckBoxState Is##name##Checked() const { return name ? ECheckBoxState::Checked:ECheckBoxState::Unchecked;} \
     void On##name##Changed(ECheckBoxState InCheckedState) {name=(InCheckedState==ECheckBoxState::Checked);}
    
    class STrueSkySetupTool : public SCompoundWidget
    {
    public:
     SLATE_BEGIN_ARGS( STrueSkySetupTool )
      :_CreateTrueSkyLight(false)
      ,_DirectionalLight(nullptr)
      ,_CreateDirectionalLight(nullptr)
      ,_Sequence(nullptr)
     {}
     /** A TrueSkyLight actor performs real-time ambient lighting.*/
     SLATE_ARGUMENT(bool,CreateTrueSkyLight)
     /** TrueSKY can drive a directional light to provide sunlight and moonlight.*/
     SLATE_ARGUMENT(ADirectionalLight*,DirectionalLight)
     /** If there's no directional light in the scene, you can create one with this checkbox.*/
     SLATE_ARGUMENT(bool,CreateDirectionalLight)
     /** The TrueSKY Sequence provides the weather state to render.*/
     SLATE_ARGUMENT(UTrueSkySequenceAsset *,Sequence)
     /** Event called when code is successfully added to the project */
     SLATE_EVENT( FOnTrueSkySetup, OnTrueSkySetup )
     SLATE_END_ARGS()
     /** Constructs this widget with InArgs */
     void Construct( const FArguments& InArgs );
     
     /** Handler for when cancel is clicked */
     void CancelClicked();
    
     /** Returns true if Finish is allowed */
     bool CanFinish() const;
    
     /** Handler for when finish is clicked */
     void FinishClicked();
    
    ...
     
     S_DECLARE_CHECKBOX(CreateTrueSkyLight)
     S_DECLARE_CHECKBOX(ShowAllSequences)
    
     void SetupSequenceAssetItems();
     
     void CloseContainingWindow();
    private:
     /** The wizard widget */
     TSharedPtr<SWizard> MainWizard;
     FOnTrueSkySetup OnTrueSkySetup;
    ...
    };
    
    

  
The SLATE\_ARGUMENT macros allow initialization of named parameters in this style:  
  

    TSharedRef<STrueSkySetupTool> TrueSkySetupTool = SNew(STrueSkySetupTool).OnTrueSkySetup(OnTrueSkySetup1).CreateTrueSkyLight(true);
    

  
etc. This is super-useful.  
  
2\. Create a callback for the wizard to execute:  

    FOnTrueSkySetup OnTrueSkySetupDelegate;
    

3\. Create a window for the widget. This function is called when the menu option to start the wizard is selected:  
  

    void FTrueSkyEditorPlugin::OnAddSequence()
    {
     TrueSkySetupWindow = SNew(SWindow)
       .Title( NSLOCTEXT("InitializeTrueSky", "WindowTitle", "Initialize trueSKY") )
       .ClientSize( FVector2D(600, 550) )
       .SizingRule( ESizingRule::FixedSize )
       .SupportsMinimize(false).SupportsMaximize(false);
     OnTrueSkySetupDelegate.BindRaw(this,&FTrueSkyEditorPlugin::OnTrueSkySetup);
     TSharedRef TrueSkySetupTool = SNew(STrueSkySetupTool).OnTrueSkySetup(OnTrueSkySetupDelegate);
     TrueSkySetupWindow->SetContent( TrueSkySetupTool );
    

  
If the main frame exists parent the window to it. The main frame should always exist...  

     TSharedPtr< SWindow > ParentWindow;
     if( FModuleManager::Get().IsModuleLoaded( "MainFrame" ) )
     {
      IMainFrameModule& MainFrame = FModuleManager::GetModuleChecked<imainframemodule>( "MainFrame" );
      ParentWindow = MainFrame.GetParentWindow();
     }
     
     bool modal=false;
     if (modal)
     {
      FSlateApplication::Get().AddModalWindow(TrueSkySetupWindow.ToSharedRef(), ParentWindow);
     }
     else if (ParentWindow.IsValid())
     {
      FSlateApplication::Get().AddWindowAsNativeChild(TrueSkySetupWindow.ToSharedRef(), ParentWindow.ToSharedRef());
     }
     else
     {
      FSlateApplication::Get().AddWindow(TrueSkySetupWindow.ToSharedRef());
     }
     TrueSkySetupWindow->ShowWindow();
    }
    

4\. Implement the setup tool:  

    BEGIN_SLATE_FUNCTION_BUILD_OPTIMIZATION
    void STrueSkySetupTool::Construct( const FArguments& InArgs )
    {
     OnTrueSkySetup = InArgs._OnTrueSkySetup;
     CreateTrueSkyLight=InArgs._CreateTrueSkyLight;
     DirectionalLight=InArgs._DirectionalLight;
     Sequence=InArgs._Sequence;
    ...
    

The interface to build the actual UI is really interesting. By overloading the \[\] and + operators, Epic lets you specify the widget structure like so:  
  

     ChildSlot
     [
      SNew(SBorder)
      .Padding(18)
      .BorderImage( FEditorStyle::GetBrush("Docking.Tab.ContentAreaBrush") )
      [
       SNew(SVerticalBox)
       +SVerticalBox::Slot()
       [
        SAssignNew( MainWizard, SWizard)
        .ShowPageList(false)
        .CanFinish(this, &STrueSkySetupTool::CanFinish)
        .FinishButtonText(  LOCTEXT("TrueSkyFinishButtonText", "Initialize") )
        .OnCanceled(this, &STrueSkySetupTool::CancelClicked)
        .OnFinished(this, &STrueSkySetupTool::FinishClicked)
        .InitialPageIndex( 0)
        +SWizard::Page()
        [
         SNew(SVerticalBox)
         +SVerticalBox::Slot()
         .AutoHeight()
         [
          SNew(STextBlock)
          .TextStyle( FEditorStyle::Get(), "NewClassDialog.PageTitle" )
          .Text( LOCTEXT( "WeatherStateTitle", "Choose a Sequence Asset" ) )
         ]
         +SVerticalBox::Slot()
         .AutoHeight()
         .Padding(0)
         [
          SNew(SHorizontalBox)
          +SHorizontalBox::Slot()
          .FillWidth(1.f)
          .VAlign(VAlign_Center)
          [
           SNew(STextBlock)
           .Text(LOCTEXT("TrueSkySetupToolDesc", "Choose which weather sequence to use initially.") )
           .AutoWrapText(true)
           .TextStyle(FEditorStyle::Get(), "NewClassDialog.ParentClassItemTitle")
          ]
         ]
        ]
        +SWizard::Page()
        [
         ...
        ]
       ]
      ]
     ];
    
    }
    

  
So by adding new +SWizard::Page() elements we add pages to the wizard.  
  
5\. Finally, implement the callback that the delegate calls when you click "Finish":  
  

    void FTrueSkyEditorPlugin::OnTrueSkySetup(bool CreateDirectionalLight, ADirectionalLight* DirectionalLight,bool CreateTrueSkyLight,UTrueSkySequenceAsset *Sequence)
    {
    ...
    }
    

  
The end result looks like this:

![image (4).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1707394756415/1bdbf5d3-e8f2-42d0-a137-bbbc945e5689.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707394757430/32001bdf-224f-4bc3-8ff7-8b45e3c0157f.png)

Full source for this is at our UE branch, (register at [Simul](https://simul.co/register) to access).

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)