---
author: Roger
comments: true
date: 2012-02-08 02:35:34+00:00
layout: post
link: http://www.rogernoble.com/2012/02/08/item-counts-in-pivotviewer/
slug: item-counts-in-pivotviewer
title: Item counts in PivotViewer
wordpress_id: 87
categories:
- Technology
tags:
- PivotViewer
---

As much as I like the PivotViewer control, displaying a total count of filtered items is a useful feature that does not exist in the current control, which is why a while ago I added it as part of the breadcrumb at [http://pivot.lobsterpot.com.au](http://pivot.lobsterpot.com.au).

Today I saw this post on the forums [http://forums.silverlight.net/t/247694.aspx/1?Get+the+information+that+are+currently+on+the+filter+panel](http://forums.silverlight.net/t/247694.aspx/1?Get+the+information+that+are+currently+on+the+filter+panel) and it reminded me about a feature I never got around to finishing, but I thought I might post here. This function drills through all the controls to return the count displayed next the to facet item name:

    
    /// <summary> /// Gets the current count of a facet item /// </summary>
    private int GetFacetItemCount(string facetName, string facetItemName)
    {
    	int count = 0;
    	Grid partContainer = (Grid)this.GetTemplateChild("PART_Container");
    	var GridFilter = ControlHelper.GetChildObject(partContainer, "FilterPaneRoot");
    	var categories = ControlHelper.GetChildObject(GridFilter, "PART_CategoriesContainer");
    	var accordionItems = ControlHelper.GetChildObjects(GridFilter);
    	foreach (var accordionItem in accordionItems)
    	{
    		//facet name
    		var categoryText = ControlHelper.GetChildObjects(accordionItem, "m_categoryName");
    		foreach (var cTxt in categoryText)
    		{
    			var facetTextBlock = ControlHelper.GetChildObject(cTxt, "PART_MainTextBlock");
    			if (facetTextBlock.Text == facetName)
    			{
    				var itemsControl = ControlHelper.GetChildObjects(accordionItem, "m_facetItemsControl");
    				foreach (var item in itemsControl)
    				{
    					var btns = ControlHelper.GetChildObjects<button>(item, "PART_FacetSelectorButton"); foreach (var b in btns) { var textBlocks = ControlHelper.GetChildObjects(b, "PART_MainTextBlock"); foreach (var textBlock in textBlocks) { if (textBlock.Text == facetItemName) { var cardinalityCount = ControlHelper.GetChildObject(b, "PART_CardinalityCount"); Int32.TryParse(cardinalityCount.Text, out count); return count; } } } } } } } return count; }</button>
