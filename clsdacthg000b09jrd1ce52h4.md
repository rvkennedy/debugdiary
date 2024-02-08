---
title: "How to make a custom Wizard for Unreal Editor"
seoTitle: "How to make a custom Wizard for Unreal Editor"
datePublished: Thu Jun 21 2018 13:13:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdacthg000b09jrd1ce52h4
slug: how-to-make-a-custom-wizard-for-unreal-editor-1
canonical: https://roderickkennedy.com/dbgdiary/how-to-make-a-custom-wizard-for-unreal-editor
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707397856372/a7646835-5748-4938-a400-ea4163a459a3.png

---

I wanted to create a wizard in the [trueSKY](https://simul.co/truesky) Unreal plugin that would make it easier for users to add trueSKY to UE scenes. I was following [this video](https://www.youtube.com/watch?v=zg_VstBxDi8&t=1482s) where Epic's Michael Noland describes various ways to modify the Editor. So I made a custom Property Editor window with settings to select a sky sequence, create a TrueSkyLight etc.

But it didn't look very friendly. And implementing a wizard-style Apply button just put a button in amongst the other settings - not great. After some searching in the UE codebase, I discovered the SWizard class that Unreal Editor uses for its own wizards. Here's what you do:

1\. Create a class derived from SCompoundWidget containing a TSharedPtr. Mine looks like this:

DECLARE\_DELEGATE\_FourParams( FOnTrueSkySetup, bool, ADirectionalLight\* ,bool, UTrueSkySequenceAsset \*);

#define S\_DECLARE\_CHECKBOX(name)  
bool name;  
ECheckBoxState Is##name##Checked() const { return name ? ECheckBoxState::Checked:ECheckBoxState::Unchecked;}  
void On##name##Changed(ECheckBoxState InCheckedState) {name=(InCheckedState==ECheckBoxState::Checked);}

class STrueSkySetupTool : public SCompoundWidget { public: SLATE\_BEGIN\_ARGS( STrueSkySetupTool ) :\_CreateTrueSkyLight(false) ,\_DirectionalLight(nullptr) ,\_CreateDirectionalLight(nullptr) ,\_Sequence(nullptr) {} /\*\* A TrueSkyLight actor performs real-time ambient lighting.*/ SLATE\_ARGUMENT(bool,CreateTrueSkyLight) /\*\* TrueSKY can drive a directional light to provide sunlight and moonlight.*/ SLATE\_ARGUMENT(ADirectionalLight\*,DirectionalLight) /\*\* If there's no directional light in the scene, you can create one with this checkbox.*/ SLATE\_ARGUMENT(bool,CreateDirectionalLight) /\*\* The TrueSKY Sequence provides the weather state to render.*/ SLATE\_ARGUMENT(UTrueSkySequenceAsset *,Sequence) /*\* Event called when code is successfully added to the project */ SLATE\_EVENT( FOnTrueSkySetup, OnTrueSkySetup ) SLATE\_END\_ARGS() /*\* Constructs this widget with InArgs \*/ void Construct( const FArguments& InArgs );

/\*\* Handler for when cancel is clicked \*/ void CancelClicked();

/\*\* Returns true if Finish is allowed \*/ bool CanFinish() const;

/\*\* Handler for when finish is clicked \*/ void FinishClicked();

...

S\_DECLARE\_CHECKBOX(CreateTrueSkyLight) S\_DECLARE\_CHECKBOX(ShowAllSequences)

void SetupSequenceAssetItems();

void CloseContainingWindow(); private: /\*\* The wizard widget \*/ TSharedPtr MainWizard; FOnTrueSkySetup OnTrueSkySetup; ... };

The SLATE\_ARGUMENT macros allow initialization of named parameters in this style:

TSharedRef TrueSkySetupTool = SNew(STrueSkySetupTool).OnTrueSkySetup(OnTrueSkySetup1).CreateTrueSkyLight(true);

etc. This is super-useful.

2\. Create a callback for the wizard to execute:

FOnTrueSkySetup OnTrueSkySetupDelegate;

3\. Create a window for the widget. This function is called when the menu option to start the wizard is selected:

void FTrueSkyEditorPlugin::OnAddSequence() { TrueSkySetupWindow = SNew(SWindow) .Title( NSLOCTEXT("InitializeTrueSky", "WindowTitle", "Initialize trueSKY") ) .ClientSize( FVector2D(600, 550) ) .SizingRule( ESizingRule::FixedSize ) .SupportsMinimize(false).SupportsMaximize(false); OnTrueSkySetupDelegate.BindRaw(this,&FTrueSkyEditorPlugin::OnTrueSkySetup); TSharedRef TrueSkySetupTool = SNew(STrueSkySetupTool).OnTrueSkySetup(OnTrueSkySetupDelegate); TrueSkySetupWindow-&gt;SetContent( TrueSkySetupTool );

If the main frame exists parent the window to it. The main frame should always exist...

TSharedPtr&lt; SWindow &gt; ParentWindow; if( FModuleManager::Get().IsModuleLoaded( "MainFrame" ) ) { IMainFrameModule& MainFrame = FModuleManager::GetModuleChecked( "MainFrame" ); ParentWindow = MainFrame.GetParentWindow(); }

bool modal=false; if (modal) { FSlateApplication::Get().AddModalWindow(TrueSkySetupWindow.ToSharedRef(), ParentWindow); } else if (ParentWindow.IsValid()) { FSlateApplication::Get().AddWindowAsNativeChild(TrueSkySetupWindow.ToSharedRef(), ParentWindow.ToSharedRef()); } else { FSlateApplication::Get().AddWindow(TrueSkySetupWindow.ToSharedRef()); } TrueSkySetupWindow-&gt;ShowWindow(); }

4\. Implement the setup tool:

BEGIN\_SLATE\_FUNCTION\_BUILD\_OPTIMIZATION void STrueSkySetupTool::Construct( const FArguments& InArgs ) { OnTrueSkySetup = InArgs.\_OnTrueSkySetup; CreateTrueSkyLight=InArgs.\_CreateTrueSkyLight; DirectionalLight=InArgs.\_DirectionalLight; Sequence=InArgs.\_Sequence; ...

The interface to build the actual UI is really interesting. By overloading the \[\] and + operators, Epic lets you specify the widget structure like so:

ChildSlot \[ SNew(SBorder) .Padding(18) .BorderImage( FEditorStyle::GetBrush("Docking.Tab.ContentAreaBrush") ) \[ SNew(SVerticalBox) +SVerticalBox::Slot() \[ SAssignNew( MainWizard, SWizard) .ShowPageList(false) .CanFinish(this, &STrueSkySetupTool::CanFinish) .FinishButtonText( LOCTEXT("TrueSkyFinishButtonText", "Initialize") ) .OnCanceled(this, &STrueSkySetupTool::CancelClicked) .OnFinished(this, &STrueSkySetupTool::FinishClicked) .InitialPageIndex( 0) +SWizard::Page() \[ SNew(SVerticalBox) +SVerticalBox::Slot() .AutoHeight() \[ SNew(STextBlock) .TextStyle( FEditorStyle::Get(), "NewClassDialog.PageTitle" ) .Text( LOCTEXT( "WeatherStateTitle", "Choose a Sequence Asset" ) ) \] +SVerticalBox::Slot() .AutoHeight() .Padding(0) \[ SNew(SHorizontalBox) +SHorizontalBox::Slot() .FillWidth(1.f) .VAlign(VAlign\_Center) \[ SNew(STextBlock) .Text(LOCTEXT("TrueSkySetupToolDesc", "Choose which weather sequence to use initially.") ) .AutoWrapText(true) .TextStyle(FEditorStyle::Get(), "NewClassDialog.ParentClassItemTitle") \] \] \] +SWizard::Page() \[ ... \] \] \] \];

}

So by adding new +SWizard::Page() elements we add pages to the wizard.

5\. Finally, implement the callback that the delegate calls when you click "Finish":

void FTrueSkyEditorPlugin::OnTrueSkySetup(bool CreateDirectionalLight, ADirectionalLight\* DirectionalLight,bool CreateTrueSkyLight,UTrueSkySequenceAsset \*Sequence) { ... }

The end result looks like this:

![image (4).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397852805/889d116c-37c9-4085-9989-227694749c8c.png align="left")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397853784/163ba7a2-1ffa-4c00-9a22-fabe66b5ead3.png align="left")

Full source for this is at our UE branch, (register at [Simul](https://simul.co/register) to access).