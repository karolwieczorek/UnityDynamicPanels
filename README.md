# Dynamic Panels for Unity 3D

![screenshot](Images/1.png)
![screenshot2](Images/2.png)
*(used external assets in screenshots: [In-game Debug Console](https://www.assetstore.unity3d.com/en/#!/content/68068) and [Runtime Inspector](https://www.assetstore.unity3d.com/en/#!/content/111349))*

**Forum Thread:** https://forum.unity.com/threads/released-dynamic-panels-draggable-resizable-dockable-and-stackable-ui-panels.523106/

**WebGL Demo:** http://yasirkula.net/DynamicPanelsDemo/

## ABOUT

This asset helps you create dynamic panels using Unity's UI system. These panels can be dragged around, resized, docked to canvas edges or to one another and stacked next to each other as separate tabs.

## FEATURES

- Supports all canvas modes (Screen Space and World Space)
- Supports multiple canvases (panels can be moved between canvases by dragging)
- Has an extensive Scripting API to create/manipulate panels by code
- Each panel costs 3 additional batches (this number can increase with each tab using a custom icon)

## HOW TO

First, import **DynamicPanels.unitypackage** to your project. Afterwards, add **Dynamic Panels Canvas** component to the *RectTransform* that you want to move your panels inside. This RectTransform doesn't have to be the Canvas object. It can be any child of it and can be of any custom size. The only thing to note here is that, for *World Space* canvases to work with Dynamic Panels Canvas, canvas' **Event Camera** property should have a value (usually the Main Camera).

There are two ways to create panels: by using the GUI of Dynamic Panels Canvas or via Scripting API. There are also two types of panels: *free panels* that can be moved around and resized freely and *docked panels* that are moved by the layout system, depending on where it is docked to. A panel can have multiple tabs.

![dynamic_panels_canvas](Images/4.png)

To add a new free panel using the Dynamic Panels Canvas component, simply click the **Add New** button under the *Free Panels* section. Then, click the + button to start adding tabs to that panel. Each tab has 4 properties: the content (*RectTransform*) that will be displayed while the tab is active, a label, an optional icon, and the minimum size of the content associated to the tab. To remove a free panel, select a tab inside the panel and click the **Remove Selected** button.

You can create docked panels by using the buttons under the *Docked Panels* section. To create a panel that is docked to the edge of the Dynamic Panels Canvas, use the buttons next to "*Dock new panel to canvas:*". You can click a panel inside the preview zone (immediately under the *Docked Panels* section) and edit its tabs. You can also dock a panel to the selected panel using the buttons next to "*Dock new panel inside:*".

When you are done, click the Play button to see the magic happen!

There are a couple of settings in Dynamic Panels Canvas that you may want to play with:

- **Minimum Free Space**: if its value is zero, docked panels can fill the whole Dynamic Panels Canvas. Otherwise, there will always be some free space that docked panels can't expand to
- **Panel Resizable Area Length**: the length of the invisible area at each side of a panel that allows users to resize a panel
- **Canvas Anchor Zone Length**: the length of the dockable area of the Dynamic Panels Canvas. When a tab is dragged and dropped onto that area, it will be docked to the edge of the Dynamic Panels Canvas
- **Panel Anchor Zone Length**: the length of the dockable area inside a panel. When a tab is dragged and dropped onto that area, it will be docked to the panel. This area is enabled only for docked panels (you can't dock panels to free panels)

## SCRIPTING API

Before using the scripting API, import **DynamicPanels** namespace to your script(s): `using DynamicPanels;`

### PanelUtils

`static Panel PanelUtils.CreatePanelFor( RectTransform content, DynamicPanelsCanvas canvas )`: creates and returns a panel with a tab that is associated with the *content*. The panel is created inside the specified Dynamic Panels Canvas. If the content is already part of a panel, then the existing panel is returned

### Panel

`DynamicPanelsCanvas Canvas { get; }`: returns the Dynamic Panels Canvas that this panel currently belongs to

`PanelGroup Group { get; }`: returns the PanelGroup that this panel currently belongs to (more on that later)

`Vector2 Position { get; }`: returns the anchored position of the panel. Anchor of a panel is located at its bottom-left corner

`Vector2 Size { get; }`: returns the size of the panel

`Vector2 MinSize { get; }`: returns the minimum size of the panel. This value is calculated runtime using the minimum size values of the panel's tab(s)

`int NumberOfTabs { get; }`: returns the number of tabs on that panel

`int ActiveTab { get; set; }`: returns the index of the currently selected tab. Its value can be changed, as well

`bool IsDocked { get; } }`: returns whether the panel is a docked panel or a free panel

`int AddTab( RectTransform tabContent, int tabIndex = -1 )`: if *tabContent* is already associated to an existing tab, then moves that tab to this panel. Otherwise, creates a new tab and associates it with *tabContent*. If **tabIndex** is not specified, then the tab will be inserted to the end of the tabs list. Be aware that a newly created tab will have a default label/icon/minimum size and it is highly recommended to customize these values using the functions mentioned below

`void RemoveTab( int tabIndex )`: removes a tab from the panel. Be careful when calling this function because the content associated to the tab will also be destroyed!

`void SetTabTitle( int tabIndex, Sprite icon, string label )`: sets the label and the icon of a tab

`void SetTabMinSize( int tabIndex, Vector2 minSize )`: sets the minimum size of the content associated to a tab

`int GetTabIndex( RectTransform tabContent )`: returns the index of a tab, or -1 if the tab is not part of the panel

`RectTransform GetTabContent( int tabIndex )`: returns the content associated to a tab

`void DockToRoot( Direction direction )`: docks the panel to an edge of the Dynamic Panels Canvas

`void DockToPanel( IPanelGroupElement anchor, Direction direction )`: docks the panel to another panel or panel group. It is not possible to dock a panel to a free panel

`void Detach()`: if the panel is docked, detaches it so that it becomes a free panel

`Panel DetachTab( int tabIndex )`: detaches a tab from the panel, creates a new panel with it and returns the new panel. If the tab is the only tab on the panel, then the panel itself is detached and returned

`void BringForward()`: if the panel is free, brings it forwards so that it is drawn above all other panels

`void MoveTo( Vector2 screenPoint )`: moves the panel to the specified point on the screen (panel's center will be aligned to the point)

`void ResizeTo( Vector2 newSize )`: resizes the panel. If it is docked, then this change may affect other panels that this panel is docked to, as well

`IPanelGroupElement GetSurroundingElement( Direction direction )`: if the panel is docked and if there is a panel/panel group in its specified vicinity, returns it

### PanelGroup

Panel groups are used to hold docked panels together. In a complex hierarchy, a panel group can have child panel groups as well. A panel group can be either horizontal or vertical. After a panel group is created and populated, it should be docked to a docked panel or panel group to be part of the layout.

`PanelGroup( DynamicPanelsCanvas canvas, Direction direction )`: creates a new panel group inside the specified DynamicPanelsCanvas. A *Left* or *Right* **direction** means a horizontal group, whereas a *Top* or *Bottom* direction means a vertical group. Panels in horizontal groups are always arranged from left to right and panels in vertical groups are always arranged from bottom to top; so it doesn't matter if the direction is Left or Right, or Top or Bottom. Only make sure that you don't set it to *None*

`DynamicPanelsCanvas Canvas { get; private set; }`: returns the Dynamic Panels Canvas that this group currently belongs to

`PanelGroup Group { get; }`: returns the parent PanelGroup that this group currently belongs to. Each Dynamic Panels Canvas has a horizontal panel group at its root that is the root of all docked panels/panel groups in the layout. Free panels, on the other hand, belong to a special panel group called *UnanchoredPanelGroup*, which should never be touched

`Vector2 Position { get; }`: returns the anchored position of the group. Anchor of a group is located at its bottom-left corner

`Vector2 Size { get; }`: returns the size of the group

`Vector2 MinSize { get; }`: returns the minimum size of the group. This value is calculated runtime recursively using the minimum size values of the docked panels' tabs

`int Count { get; }`: returns the number of elements (panels or child panel groups) in this group

`IPanelGroupElement this[int index] { get; }`: returns the element at the specified index in this group

`void AddElement( IPanelGroupElement element )`: adds an element to the end of the elements list. There is no *RemoveElement* function. Instead, when something changes in the layout (a panel is created/destroyed/moved between groups etc.), Dynamic Panels Canvas is notified and the whole layout is recalculated in *LateUpdate*. During this calculation, elements that belong to other groups are removed from their previous groups automatically

`void AddElementBefore( IPanelGroupElement pivot, IPanelGroupElement element )`: inserts an element in front of a specified element in this group 

`void AddElementAfter( IPanelGroupElement pivot, IPanelGroupElement element )`: inserts an element after a specified element in this group

`void DockToRoot( Direction direction )`: docks the panel group to an edge of the Dynamic Panels Canvas

`void DockToPanel( IPanelGroupElement anchor, Direction direction )`: docks the panel group to another panel or panel group. It is not possible to dock a panel group to a free panel

`void ResizeTo( Vector2 newSize )`: resizes the panel group. This change affects the panels inside and may affect other panels that this panel group is docked to. Make sure to dock the panel group to an element in the layout before calling this function

`bool IsInSameDirection( Direction direction )`: if this group is horizontal, returns *true* if *direction* is Left or Right. Otherwise, returns *true* if *direction* is Top or Bottom

`IPanelGroupElement GetSurroundingElement( Direction direction )`: if there is an element in the group's specified vicinity, returns it

`void PrintHierarchy()`: prints the hierarchical tree of this group to the console. May be useful for debugging purposes

### DynamicPanelsCanvas

`void ForceRebuildLayoutImmediate()`: immediately rebuilds the layout of the Dynamic Panels Canvas, if it is dirty. This process involves a couple of steps: elements in groups are validated (elements that no longer belong to that group are removed, empty child groups are deleted, etc.), minimum sizes are calculated, bounds of the elements are recalculated and so on. This step is quite processor intensive and therefore is not immediately called when e.g. a group is changed. Rather, it is called on LateUpdate to process everything in a batch (it is only called if something has changed). But this brings out an issue: if you are modifying the layout via Scripting API, you won't be able to access the correct size/position/minimum size values of layout elements (panels/panel groups), resize them in the same frame that you modify them (resizes happen instantly and do not count as modification, but the layout should be up-to-date) or iterate through the elements of a panel group correctly. To solve this issue, you can simply call this function after modifying the layout to rebuild it immediately.

## EXAMPLE CODE

The following example code creates 3 panels, one with 2 tabs, and creates a layout with them in a L-shape. The final output can be seen in the following picture:

![dynamic_panels_canvas](Images/5.png)

```csharp
using UnityEngine;
using DynamicPanels;

public class PanelCreator : MonoBehaviour 
{
	public DynamicPanelsCanvas canvas;

	public RectTransform content1, content2, content3, content4;
	public string tabLabel1, tabLabel2, tabLabel3, tabLabel4;
	public Sprite tabIcon1, tabIcon2, tabIcon3, tabIcon4;

	void Start()
	{
		// Create 3 panels
		Panel panel1 = PanelUtils.CreatePanelFor( content1, canvas );
		Panel panel2 = PanelUtils.CreatePanelFor( content2, canvas );
		Panel panel3 = PanelUtils.CreatePanelFor( content3, canvas );

		// Add a second tab to the first panel
		panel1.AddTab( content4 );

		// Set the labels and the (optional) icons of the tabs
		panel1.SetTabTitle( 0, tabIcon1, tabLabel1 ); // first tab
		panel1.SetTabTitle( 1, tabIcon4, tabLabel4 ); // second tab

		panel2.SetTabTitle( 0, tabIcon2, tabLabel2 );
		panel3.SetTabTitle( 0, tabIcon3, tabLabel3 );

		// Set the minimum sizes of the contents associated to the tabs
		panel1.SetTabMinSize( 0, new Vector2( 300f, 300f ) ); // first tab
		panel1.SetTabMinSize( 1, new Vector2( 300f, 300f ) ); // second tab

		panel2.SetTabMinSize( 0, new Vector2( 300f, 300f ) );
		panel3.SetTabMinSize( 0, new Vector2( 300f, 300f ) );

		// Create a vertical panel group
		PanelGroup groupLeftVertical = new PanelGroup( canvas, Direction.Top ); // elements are always arranged from bottom to top
		groupLeftVertical.AddElement( panel1 ); // bottom panel
		groupLeftVertical.AddElement( panel2 ); // top panel

		// Dock the elements to the Dynamic Panels Canvas (the order is important)
		panel3.DockToRoot( Direction.Bottom );
		groupLeftVertical.DockToRoot( Direction.Left );

		// Rebuild the layout before attempting to resize elements or read their sizes/minimum sizes correctly
		canvas.ForceRebuildLayoutImmediate();

		// It is recommended to manually resize layout elements that are created by code and docked.
		// Otherwise, their sizes will not be deterministic. In this case, we are resizing them to their minimum size
		groupLeftVertical.ResizeTo( new Vector2( groupLeftVertical.MinSize.x, groupLeftVertical.Size.y ) );
		panel3.ResizeTo( new Vector2( panel3.Size.x, panel3.MinSize.y ) );
	}
}
```
