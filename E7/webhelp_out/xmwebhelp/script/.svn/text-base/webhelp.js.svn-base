// ======================================================================                                             
// Product      :                                                                                                     
// Notes        :                                                                                                     
//                                                                                                                    
// ======================================================================                                             
//                                                                                                                    
//  (c) 2009 Justsystems Canada Inc. All Rights Reserved                                                                          
//                                                                                                                    
// ======================================================================                                             
// Revision History: (last five changes)                                                                              
// Name             Date       Description                                                                            
// =========================                                                                                          
// | asmith                    mith  3-Nov-09 PROD00000000: XMEE 6.0 onto MAIN                                        
// ======================================================================                                             
/*

webhelp.js
v. 2009 10 13

version history:
	
2009 10 13
Bugfix: single topic "hit" in gSearchIndexes array did not display in search results.
	
2009 10 02
Initial checkin
*/

$(document).ready(function() {
    // Init ToC
    var tocTitle = "<h4>" + gMapTitle + "</h4>";
    var tocTree = whBuildToC(gTocFiles, 0, "");
    var whToc = $("#whToC");
    whToc.html(tocTitle);
    whToc.append(tocTree[0]);
    $("#whTocTree").treeview({
        collapsed: true
    });


    $("#whTocTree a:last").attr("rel", "last");
    $("#whTocTree a:first").attr("rev", "first");

    // Update iframe from ToC
    $("#whTocTree a").click(function() {
        whContentUpdate(this.href);
        $("#whTocTree .current").removeClass("current");
        $(this).addClass("current");
        // Added for inner linking
        window.location.hash = encodeURI(this.title);
        return false;
    });

    // Update ToC when iframe source changes
    function whTocUpdate(tocID) {
        var newToc = $("#whTocTree a[id = '" + tocID + "']");
        $("#whTocTree .current").removeClass("current");
        newToc.addClass("current");
        newToc.parents(".expandable").each(
			function(i) {
			    $(this).children(".hitarea").click();
			});
    } // whTocUpdate

    // Called from prev/next button; increment = 0 (previous) or 1 (next)
    function whTriggerToc(increment) {
        var current = $("#whTocTree .current");
        var tocID = increment ? current.attr("rel") : current.attr("rev");
        if ((tocID != "first") && (tocID != "last")) {
            current.removeClass("current");
            var newToc = $("#whTocTree a[id = '" + tocID + "']");
            newToc.click();
            newToc.parents(".expandable").each(
				function(i) {
				    $(this).children(".hitarea").click();
				});
        }
    }
    // Init ToC


    // Init title
    $("#whTitlebar > h1").text(gMapTitle);
    $("#whTitlebar > h1").attr("title", gMapTitle);

    // Init iframe
    var iframeContent = whInitContentFrame(gInitURL);
    $("#whContent").html(iframeContent);
    $("#whContentFrame").attr("src", gInitURL);


    // iframe onload event
    $("#whContentFrame").load(function(event) {
        var currentLoc = window.frames["whContentFrame"].document.URL.replace(/\\/g, "/");
        currentLoc = currentLoc.replace(/#.*/, "");
        var whPath = window.location.pathname;
        var webHelpFilename = whPath.substring(whPath.lastIndexOf("/") + 1);
        var commonPath = window.location.href.replace(webHelpFilename, "");
        commonPath = commonPath.replace(/#.*/, "");
        var tocID = null;
        // normalize URL across browsers
        if (currentLoc.indexOf("file:///") == -1) {
            currentLoc = currentLoc.replace(/file:\/\//, "file:///");
        }
        currentLoc = currentLoc.replace(commonPath, "");
        var tocListing = "";
        // check toc links, and update if there's a match
        $("#whTocTree a").each(
			function(i) {
			    var tocHref = $(this).attr("href");
			    tocHref = tocHref.replace(/\\/g, "/");
			    tocHref = tocHref.replace(commonPath, "");
			    if (tocHref == currentLoc) {
			        tocID = $(this).attr("id");
			        whTocUpdate($(this).attr("id"));
			        // Update address bar to reflect new #location
			        window.location.hash = encodeURI($(this).attr("title"));
			    }
			}); // check toc links
    }); // iframe onload


    // location.hash handling 
    function whLocHashTitle() {
        if (window.location.hash != "") {
            var innerLink = window.location.hash.substring(1);
            // Check for unsafe characters as per http://www.ietf.org/rfc/rfc1738.txt
            // " ", "<", ">", "#", "'", "{", "}", "|", "\", "^", "~","[", "]", and "`"
            // cannot test for space using "\s" as some browsers encode/decode space chars in the address bar
            // so replace any spaces with "%20"
            innerLink = innerLink.replace(/ /g, "%20");
            var unsafeHash = /[\<\>\#\'\{\}\|\\\^\~\[\]\`]+/;
            var badHash = unsafeHash.test(innerLink);
            if (!badHash) {
                var frameTitle = decodeURI(innerLink);
                // Return first (if any) title that matches from ToC
                var matchFromHash = $("#whTocTree a[title = '" + frameTitle + "']:first");
                if (matchFromHash.length == 1) {
                    whTocUpdate(matchFromHash.attr("id"));
                    matchFromHash.addClass("current");
                    matchFromHash.click();
                    var hashHREF = matchFromHash.attr("href");
                    whContentUpdateFromHash(hashHREF);
                }
                else {
                    // No match from hash value - fallback to first item in ToC
                    $("#whTocTree a:first").addClass("current");
                }
            }
            else {
                // Bad hash value - fallback to first item in ToC
                $("#whTocTree a:first").addClass("current");
            }
        }
        else {
            // No hash value - fallback to first item in ToC	
            $("#whTocTree a:first").addClass("current");
        }
    } // whLocHashTitle


    // Handle any hash values in URL
    whLocHashTitle();


    // Init Toolbars
    $("#whToolbar #home").click(function() {
        window.location = encodeURI(gHomePage);
        return false;
    });

    $("#whToolbar #contents").click(function() {
        $("#whTools > div").hide();
        $("#whToC").show();
        $("#whContentFrame").focus();
        return false;
    });

    $("#whToolbar #index").click(function() {
        $("#whTools > div").hide();
        $("#whIndex").show();
        return false;
    });

    $("#whToolbar #search").click(function(event) {
        $("#whTools > div").hide();
        $("#whSearch").show();
        // Recalculate div heights once search is visible
        var winHeight = $(window).height();
        var headerHeight = $("#whHeader").height() + 6;
        var contentHeight = winHeight - headerHeight;
        $("#whSearch").css("height", contentHeight);
        var searchResultsHeight = contentHeight - $("#searchInput").height() - 5;
        $("#searchResults").css("height", searchResultsHeight);
        $("#searchQuery").focus();
        return false;
    });

    $("#whNavbar #print").click(function() {
        var content = window.frames["whContentFrame"];
        content.focus();
        content.print();
    });

    $("#whNavbar #previous").click(function() {
        whTriggerToc(0);
        $("#whContentFrame").focus();
        return false;
    });

    $("#whNavbar #next").click(function() {
        whTriggerToc(1);
        $("#whContentFrame").focus();
        return false;
    });
    // Init Toolbars

    // Init Search
    $("#whSearch form").submit(function() {
        var query = $("#whSearch #searchQuery").val();
        query = jQuery.trim(query.toLowerCase());
        if (query != "") {
            whSearchQuery(query);
            return true;
        }
        else {
            return false;
        }
    }); // Init Search


    // Init Index  
    var indexTitle = "<h4>Index</h4>\n";
    var indexTree = whBuildIndex(gIndexData);
    var whIndex = $("#whIndex");
    whIndex.html(indexTitle);
    whIndex.append(indexTree);

    // Add onclick behaviour to index list
    var whIndexList = $("#whIndex a");
    whIndexList.click(function() {
        var indexLink = this.href;
        indexLink = indexLink.replace(/#.*/, "");
        whContentUpdate(indexLink);
        whIndexList.removeClass("current");
        $(this).addClass("current");
        this.blur();
        return false;
    }); // searchResults.click
    // Init Index 


    // Set initial size of iframe container and tools pane
    var winHeight = $(window).height();
    var headerHeight = $("#whHeader").height() + 6;
    var contentHeight = winHeight - headerHeight;
    $("#whContent").css("height", contentHeight);
    $("#whContent").css("top", headerHeight);
    $("#whTools > div").css("height", contentHeight - 5);

    // Resize iframe container and tools pane to full height
    $(window).resize(function() {
        setTimeout(afterOnResize, 5);
        return false;
    });

    function afterOnResize() {
        var winHeight = $(window).height();
        var headerHeight = $("#whHeader").height() + 6;
        var contentHeight = winHeight - headerHeight;
        var tocHREF = $("#whTocTree .current").attr("href");
        $("#whContentFrame").attr("src", "");
        $("#whContent").css("height", contentHeight);
        $("#whContent").css("top", headerHeight);
        $("#whTools > div").css("height", contentHeight - 5);
        $("#whContentFrame").attr("height", (contentHeight - 5));
        // Keep ToC and iframe in sync, regardless of bad iframe @src reporting
        $("#whContentFrame").attr("src", tocHREF);
        var newSearchHeight = contentHeight - $("#searchInput").height() - 5;
        $("#searchResults").css("height", newSearchHeight);
        return false;
    }

}); // $(document).ready


function whContentUpdate(contentURL) {
    var iFrame = document.getElementById("whContentFrame");
    iFrame.src = contentURL;
}

function whContentUpdateFromHash(contentURL) {
    var iFrame = document.getElementById("whContentFrame");
    iFrame.src = contentURL;
    return false;
}


// Create iframe html from gInitURL
function whInitContentFrame(frameSRC) {
    try {
        var contentFrame = "<iframe src=\"{SRC}\" scrolling=\"auto\" frameborder=\"0\" id=\"whContentFrame\" name=\"whContentFrame\" height=\"100%\" width=\"100%\"/>";
        contentFrame = contentFrame.replace(/{SRC}/, frameSRC);
    }
    catch (e) {
        alert("Cannot load iframe\n" + frameSRC + "\n" + e.description);
    }
    return contentFrame;
} // whInitContentFrame


function whIframeNotify() {
    var whContentFrame = window.frames["whContentFrame"];
    var frameLoc = whContentFrame.document.URL;
    window.status = "whIframeNotify() " + frameLoc;
    whUpdateToc(frameLoc);
} // whFrameNotify


// Sync ToC to iframe location
function whUpdateToc(frameLoc) {
    var toc = document.getElementById("whToC");
    var tocLinks = toc.getElementsByTagName("a");
    var currentLoc = frameLoc.replace(/\\/g, "/");
    var tocID = null;
    // normalize URL across browsers
    if (currentLoc.indexOf("file:///") == -1) {
        currentLoc = currentLoc.replace(/file:\/\//, "file:///");
    }

    for (var i = 0; i < tocLinks.length; i++) {
        var tocHref = tocLinks[i].getAttribute("href");
        tocHref = tocHref.replace(/\\/g, "/");
        if (currentLoc.indexOf(tocHref) == 0) {
            tocID = tocLinks[i].getAttribute("id");
        }
    }
} // whUpdateToc


// Build the ToC from gTocFiles array
function whBuildToC(tocArray, counter, prevItem) {
    var list = [];
    list.push("<ul id=\"whTocTree\">\n");
    var resultArray = new Array;
    var itemCount = counter;
    var listItem;
    for (var i = 0; i < tocArray.length; i++) {
        list.push("<li>");
        // Account for topichead / empty topicref case with no href 
        if (tocArray[i][1] != null) {
            listItem = "<a id=\"{ID}\" href=\"{HREF}\" rel=\"{REL}\" rev=\"{REV}\" title=\"{TITLE}\">{TITLE}</a>";
            itemCount++;
        }
        else {
            listItem = "<span class=\"whTocHeader\">{TITLE}</span>";
        }

        var listID = "whtoc_" + itemCount;
        var listRel = "whtoc_" + (itemCount + 1);
        var listRev = "whtoc_" + (itemCount - 1);

        listItem = listItem.replace(/{ID}/, listID);
        listItem = listItem.replace(/{HREF}/, tocArray[i][1]);
        listItem = listItem.replace(/{REL}/, listRel);
        listItem = listItem.replace(/{REV}/, listRev);
        listItem = listItem.replace(/{TITLE}/g, tocArray[i][0]);
        list.push(listItem);

        // if there are extra (child) items, 
        // build a temp array for the extra (child) items 
        // and call buildToC recursively
        if (tocArray[i].length > 2) {
            var itemLength = tocArray[i].length;
            var childArray = new Array;
            for (var y = 2; y < itemLength; y++) {
                childArray[y - 2] = tocArray[i][y];
            }
            // Add to list from temp array
            var childToc = whBuildToC(childArray, itemCount, listID);
            list.push(childToc[0]);
            list.push("</li>\n");
            itemCount = childToc[1];
        }
        else {
            list.push("</li>\n");
        }
    }
    list.push("</ul>\n");

    resultArray[0] = list.join("");
    resultArray[1] = itemCount;

    return resultArray;
} // whBuildToC


// Build Index from gIndexData array
function whBuildIndex(indexArray) {
    var result = [];

    result.push("<ul>\n");
    for (var i = 0; i < indexArray.length; i++) {
        result.push("<li>");
        if (indexArray[i][1] == "") {
            result.push(indexArray[i][0]);
        }
        else {
            var listAnchor = "<a href=\"{HREF}\" title=\"{TITLE}\">{TITLE}</a>";
            listAnchor = listAnchor.replace(/{HREF}/, indexArray[i][1]);
            listAnchor = listAnchor.replace(/{TITLE}/g, indexArray[i][0]);
            result.push(listAnchor);
        }
        // if there are extra (child) items, 
        // build a temp array for the extra (child) items 
        // and call whBuildIndex recursively
        if (indexArray[i].length > 2) {
            var itemLength = indexArray[i].length;
            var childArray = new Array;
            for (var y = 2; y < itemLength; y++) {
                childArray[y - 2] = indexArray[i][y];
            }
            // Add to list from temp array
            var childIndex = whBuildIndex(childArray);
            result.push(childIndex);
            result.push("</li>\n");
        }
        else {
            result.push("</li>\n");
        }
    }
    result.push("</ul>\n");

    return result.join("");
} // whBuildIndex


// Search
function whSearchFormSubmit() {
    return;
}


// Query gSearchIndexes array
// see search_data.js
function whSearchQuery(query) {
    $("#searchResults").html("<img title=\"Loading...\" src=\"mwebhelp\/images\/loading.gif\"/>");
    var searchTerms = gSearchIndexes;
    var match = [];
    // debugger;
    for (var i = 0; i < searchTerms.length; i++) {
        var term = searchTerms[i][0];
        var titles = searchTerms[i][1];
        if ((query == term) || (term.indexOf(query) == 0)) {
            for (var j = 0; j < titles.length; j++) {
                var matchIndex = titles[j] + 0;
                match[matchIndex] = 1;
            }
        }
    }
    if (match.length > 0) {
        $("#searchResults").html("<ul><li class=\"whResultTitle\">Search results for \"<span id=\"whQuery\">" + query + "</span>\":</li></ul>");
        whDisplaySearchResults(match);
    }
    else {
        $("#searchResults").html("<ul><li>No results for \"" + query + "\"</li></ul>");
    }
} // whSearchQuery


// generates a results list from gSearchTitles and gSearchIndexes
function whDisplaySearchResults(resultArray) {
    var searchTitles = gSearchTitles;
    var searchURLs = gSearchFiles;
    if (resultArray.length && resultArray.length > 0) {
        var searchLinks = [];
        for (var i = 0; i < resultArray.length; i++) {
            if (resultArray[i] == 1) {
                var searchLink = "<li><a title=\"{TITLE}\" href=\"{HREF}\">{TITLE}</a></li>";
                searchLink = searchLink.replace(/{HREF}/, searchURLs[i]);
                searchLink = searchLink.replace(/{TITLE}/g, searchTitles[i]);
                searchLinks.push(searchLink);
            }
        }
        searchLinks.sort();
        searchLinks.unshift("<ul>");
        searchLinks.push("</ul>");
    }
    else {
        // No results / error; fail silently
    }
    // Display the searchLinks list
    $("#searchResults").append(searchLinks.join("\n"));

    // Add onclick behaviour to search results list
    var searchResults = $("#searchResults a");
    searchResults.click(function() {
        var searchTerm = $("#searchResults #whQuery").text();
        whContentUpdate(this.href);
        searchResults.removeClass("current");
        $(this).addClass("current");
        whSearchHiLite(this.href, searchTerm);
        this.blur();
        return false;
    }); // searchResults.click

    return true;
} // whDisplaySearchResults


// Hilite search term in result page
function whSearchHiLite(searchHREF, searchTerm) {
    var frameHREF = window.frames["whContentFrame"].document.URL;
    var searchFile = whGetFilename(searchHREF);

    function checkFrameLoad() {
        var frameFile = whGetFilename(window.frames["whContentFrame"].document.URL);
        if (searchFile != frameFile) {
            setTimeout(checkFrameLoad, 50);
        }
        else {
            // success
            whHiLite(searchTerm);
        }
    }
    checkFrameLoad();
} // whSearchHiLite


// return a filename from a URL
function whGetFilename(hrefText) {
    var result;
    hrefText = hrefText.replace(/\\/g, "/");
    result = hrefText.substring(hrefText.lastIndexOf("/") + 1);
    return result;
} // whGetFilename


// Add hilite to search terms in loaded iframe
function whHiLite(searchTerm) {
    // Get a jQuery reference to the iframe content
    var frame = $("#whContentFrame")[0];
    var frameDoc = frame.contentWindow ? frame.contentWindow.document : (frame.contentDocument ? frame.contentDocument : frame.document);

    // select all divs in the context of the iframe doc
    var frameDiv = $("div", frameDoc);

    var hilite = ">$1<b style=\"color:#000;font-weight:normal;background-color:yellow\">$2</b>$3<";
    var regex = new RegExp(">([^<]*)?(" + searchTerm + ")([^>]*)?<", "ig");

    frameDiv.each(function(i) {
        $(this).html($(this).html().replace(regex, hilite));
    });
    return false;
} // whHiLite