---
author: Roger
comments: true
date: 2011-06-02 05:48:59+00:00
layout: post
link: http://www.rogernoble.com/2011/06/02/customising-pivotviewer-adding-custom-views/
slug: customising-pivotviewer-adding-custom-views
title: 'Customising PivotViewer: Adding custom views'
wordpress_id: 68
categories:
- Technology
tags:
- PivotViewer
---

This is the second post in a series on customising PivotViewer. In the[ last post ](http://www.rogernoble.com/2011/06/01/customising-pivotviewer-events/)I covered how to attach to the UI events, in this post I'll  show the process of adding a custom view.

The key steps in creating a custom view is first adding the view change button to the control bar, second is adding the view control and finally hooking it all up.

The button for the view can be any UIElement type, for the Grid View on the [LobsterPot PivotViewer](http://pivot.lobsterpot.com.au) I created a simple UserControl. Adding the new button can be done in the overloaded OnApplyTemplate() method by inserting it into the GridControlBar. I've created a mini example below that contains the key elements for adding the button - I've left out the definition for GridControlBar for brevity, to see how it's defined have a look at the post on [Events](http://www.rogernoble.com/2011/06/01/customising-pivotviewer-events/).

    
    public class LobsterViewer : System.Windows.Pivot.PivotViewer
    {
        private GridViewButton _pivotGridButton { get; set; }
    
        public LobsterViewer()
        {
            _pivotGridButton = new GridViewButton();
            // Click event
            _pivotGridButton.Click += new EventHandler(_pivotGridButton_Click);
        }
    
        public override void OnApplyTemplate()
        {
            base.OnApplyTemplate();
            var controlBarPanel = (DockPanel)VisualTreeHelper.GetChild(GridControlBar, 2);
            controlBarPanel.Children.Insert(1, _pivotGridButton);
        }
    
        ///
        /// Turn on Grid and deselect pivot view controls
        ///
        private void _pivotGridButton_Click(object sender, EventArgs e)
        {
            // Disable other views
            // Enable custom view
        }
    }


The next step is to add the custom view control to the PivotViewer. This is done in exactly the same way as the button, it's a simple UserControl and is inserted in the overloaded OnApplyTemplate method. Again I've left out the definition of PartContainer for brevity.

    
    private PivotDataGrid _pivotGrid { get; set; }
    public LobsterViewer()
    {
        _pivotGrid = new PivotDataGrid();
    }
    public Grid GridViews
    {
        get
        {
            return ((Grid)
                    ((CollectionViewerView)(PartContainer)
                        .Children[0])
                        .Content)
                        .Children[0];
        }
    }
    
    public override void OnApplyTemplate()
    {
        ...
        //Insert grid control
        _pivotGrid.Visibility = System.Windows.Visibility.Collapsed;
        _pivotGrid.SetValue(Grid.ColumnProperty, 1);
        _pivotGrid.SetValue(Grid.ColumnSpanProperty, 2);
        _pivotGrid.SetValue(Grid.RowProperty, 0);
        _pivotGrid.SetValue(Grid.RowSpanProperty, 1);
        GridViews.Children.Add(_pivotGrid);
    }


It's worth noting that I've set the initial visibility to Collapsed as I didn't want it to be visible when then PivotViewer is first loaded.

The final step is to display the new view when the button is clicked and to hide the tiles. From the previous post we already have the view change events so it's straight forward to set the visibility of the new view to Collapsed. Hiding the tiles can be done by getting the first child control in GridViews and setting its visibility to Collapsed.

    
    ///
    /// Visibility of tiles
    ///
    public Visibility TileVisibility
    {
        get
        {
            return GridViews.Children[0].Visibility;
        }
    }
    
    ///
    /// Turn on Grid and deselect pivot view controls
    ///
    private void _pivotGridButton_Click(object sender, EventArgs e)
    {
        TileVisibility = System.Windows.Visibility.Collapsed;
        _pivotGrid.Visibility = System.Windows.Visibility.Visible;
    }


That's it for now, but please let me know if this is useful. Next post will be on adding a map control as a view and passing the latitude and longitude coordinates via the CXML.
