---
author: Roger
comments: true
date: 2011-06-01 01:42:01+00:00
layout: post
link: http://www.rogernoble.com/2011/06/01/customising-pivotviewer-events/
slug: customising-pivotviewer-events
title: 'Customising PivotViewer: Events'
wordpress_id: 67
categories:
- Technology
tags:
- PivotViewer
---

This is the first post in a series of posts that I hope to put together on customising the PivotViewer control, which will give an insight into how the [LobsterPot pivot collections ](pivot.lobsterpot.com.au)were created. This post will be focused on how to attach to the UI events that will be needed when adding a custom view to the PivotViewer control.




Before I get started I'd like to mention that while I have no affiliation with [Silverlight Spy](http://firstfloorsoftware.com/silverlightspy/download-silverlight-spy) I recommend it highly and could not have done any of this easily without it. I'd also like to thank Xper360 ([blog](http://xpert360.wordpress.com) \| [twitter](http://twitter.com/Xpert360)) and Tony Champion ([blog](http://tonychampion.net/blog/) \| [twitter](http://twitter.com/tonychampion)) for their work in customising the PivotViewer control - It's well worth looking at the [series of posts by Xpert360](http://xpert360.wordpress.com/2011/05/17/xpert360-pivotviewer-blog-article-index/) which provide an excellent start for customising the PivotViewer control. Also make sure to download the lessons provided by Tony Champion - [http://pivotviewerlessons.codeplex.com/](http://pivotviewerlessons.codeplex.com/) which are also excellent.




One of the first challenges with customising the PivotViewer control is to get the right user events. Out of the box PivotViewer only exposes [basic events](http://i2.silverlight.net/content/pivotviewer/developer-info/api/html/AllMembers_T_System_Windows_Pivot_PivotViewer.htm#eventTableToggle), which means to get anything useful such as view change and filter we will have to create our own event handlers.




To add a custom view control the first event needed is to know when the views have been changed. This will then let us know when to show and hide the custom view. Lucky for us attaching to the view change events are fairly straight forward:




    
    ///
    /// The main Grid
    ///
    public Grid PartContainer
    {
        get
        {
            return (Grid)this.GetTemplateChild("PART_Container");
        }
    }
    
    ///
    /// Top control bar containing the view controls, sort list and zoom controls
    ///
    public ControlBarView ControlGrid
    {
        get
        {
            return ControlHelper.GetChildObject<ControlBarView>(PartContainer, "PART_ControlBar");
        }
    }
    
    ///
    /// Top control bar grid
    ///
    public Grid GridControlBar
    {
        get
        {
            return (Grid)VisualTreeHelper.GetChild(ControlGrid, 0);
        }
    }
    
    ///
    /// Bind to view switch events
    ///
    private void BindViewSwitcherEvent()
    {
        // ViewSwitcher contains the two Grid and Graph controls
        var viewSwitcher = ControlHelper.GetChildObject<ContentControl>(GridControlBar, "ViewSwitcher");
        var viewControls = ControlHelper.GetChildObject<StackPanel>(viewSwitcher);
        foreach (var viewBtn in viewControls.Children)
        {
            // First remove any events that might have already been attached
            ((ListBoxItem)viewBtn).MouseLeftButtonUp -= new System.Windows.Input.MouseButtonEventHandler(viewSwitcher_MouseLeftButtonUp);
            ((ListBoxItem)viewBtn).MouseLeftButtonUp += new System.Windows.Input.MouseButtonEventHandler(viewSwitcher_MouseLeftButtonUp);
        }
    }




The next step is to handle mouse events from the facet Category filters. What makes this slightly trickier is the facet category items are created and destroyed dynamically, which means we also have to attach and detach to the events dynamically.  To avoid potential memory leaks a stack is used to push current UI objects when attaching and pop them off again to detach and clean up. The BindAllAccordianButtons method below is called once the collection has been loaded, and every time the Facet Category filters change.




    
    // Memory management
    private Stack _uiObjects;
    private Stack _uiThumbObjects;
    
    ///
    /// Filter pane grid control
    ///
    public Grid GridFilter
    {
        get
        {
            return ControlHelper.GetChildObject(PartContainer, "FilterPaneRoot");
        }
    }
    
    ///
    /// Bind to click events for all visible accordion buttons
    ///
    private void BindAllAccordionButtons()
    {
        //cleanup old attached events
        while (_uiObjects.Count > 0)
        {
            _uiObjects.Pop().Click -= new RoutedEventHandler(fb_Checked);
        }
        while (_uiThumbObjects.Count > 0)
        {
            _uiThumbObjects.Pop().DragCompleted -= new DragCompletedEventHandler(t_DragCompleted);
        }
        // Get all CustomAccordionItems in the PART_CategoriesContainer
        var categories = ControlHelper.GetChildObject<StackPanel>(GridFilter, "PART_CategoriesContainer");
        var accordionItems = ControlHelper.GetChildObjects<CustomAccordionItem>(GridFilter);
        foreach (var accordionItem in accordionItems)
        {
            //clear buttons
            var clearButtons = ControlHelper.GetChildObjects<button>(accordionItem, "m_clearButton");
            foreach (var cBtn in clearButtons)
            {
                cBtn.Click -= new RoutedEventHandler(fb_Checked);
                cBtn.Click += new RoutedEventHandler(fb_Checked);
            }
    
            var filterButtonsWrapper = ControlHelper.GetChildObjects<ScrollContentPresenter>(accordionItem, "ScrollContentPresenter");
            foreach (var wrapper in filterButtonsWrapper)
            {
                var filterButtons = ControlHelper.GetChildObjects<button>(wrapper);
                var filterCheck = ControlHelper.GetChildObjects<ManualToggleButton>(wrapper);
                //attach click to buttons
                foreach (var fb in filterButtons)
                {
                    _uiObjects.Push(fb);
                    fb.Click += new RoutedEventHandler(fb_Checked);
                }
                //attach click to checkbox's
                foreach (var fc in filterCheck)
                {
                    _uiObjects.Push(fc);
                    fc.Click += new RoutedEventHandler(fb_Checked);
                }
            }
            //Range Slider
            var rangeSlider = ControlHelper.GetChildObjects<Thumb>(accordionItem);
            foreach (var t in rangeSlider)
            {
                _uiThumbObjects.Push(t);
                t.DragCompleted += new DragCompletedEventHandler(t_DragCompleted);
            }
        }
    }
    
    ///
    /// Collection filtered (via facet accordion button)
    ///
    private void fb_Checked(object sender, RoutedEventArgs e)
    {
        // Do something
    }
    
    ///
    /// Collection filtered (via range slider)
    ///
    public void t_DragCompleted(object sender, DragCompletedEventArgs e)
    {
        // Do something
    }




I've put in some place holders for the event handlers, but this is where any custom actions would be placed for updating the UI based on the current filter.




I've got more posts on the way, but by applying this code into your extended PivotViewer class it will make adding custom views a whole lot easier.
