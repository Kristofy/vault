<%*
    // CHECK THAT THESE TOP THREE VARIABLES MATCH YOUR REQUIREMENTS

    // TOC template name
    // Set this to match your main TOC template name, NOT THE NAME OF THIS TEMPLATE.
    const tocTemplate = "toc-create";

    // String marking start and end of TOC - unique string at the start of your TOC to allow script to find it.
    // TOC end marker does not need to be unique, it will find the first occurrence of this string following the start of the TOC and assume that it marks the end of the TOC.
    // These values will work if you are using my v2 template. Otherwise update to match your TOC format.
    const markerTOCstart = '>[!SUMMARY]+ Table of Contents';
    const markerTOCend = '---';

    // VARIABLES BELOW DO NOT NEED TO BE AMENDED ==========================

    // Variables to hold line numbers for start/end of any existing TOC
    // These will get updated by the script, no need to change these manually.
    let TOCstart = 0;
    let TOCend = 0;
         
    // Variable to hold name of file that is being processed. 
    const file = tp.file.find_tfile(tp.file.title);

    // Split file contents into array, each array item is one line
    const fileContentSplit = await tp.file.content.split('\n');

    // Find start of any existing TOC
    TOCstart = fileContentSplit.indexOf(markerTOCstart);
    // Find end of TOC - second value specifies start position of search
    TOCend = fileContentSplit.indexOf(markerTOCend, TOCstart);
        console.log("TOC line start \n\n", TOCstart);
        console.log("TOC line end \n\n", TOCend);

    // If TOC found then 'Splice' out TOC section - creates new var containing spliced array elements and removes them from original array
    if ( TOCstart > 0 ) {
        // Adjusted to remove '---' from before & after TOC but may need tweaked depending on how your TOC template is specifically formatted.
        let splicedTOC = fileContentSplit.splice(TOCstart - 2, TOCend - TOCstart + 4);  
            console.log("splicedTOC \n\n", splicedTOC.join('\n'));
            console.log("fileContentSplit \n\n", fileContentSplit.join('\n'));
    }

    // ------------------------------------------------------------------------------------------------------
    
    // Write new content into file
    await app.vault.modify( file, fileContentSplit.join('\n') );
    
    // If existing TOC found, place cursor at it's original position before inserting new TOC
    // If no TOC found, place cursor at end of header line before inserting new TOC
    if ( TOCstart > 0 ) {
        // '-4' accounts for removed lines etc but may need tweaked depending on how your TOC template is specifically formatted
        this.app.workspace.getActiveViewOfType(tp.obsidian.MarkdownView).editor.setCursor(TOCstart - 4); 
    }
    else {
        // We have no existing TOC so find first header and position cursor after header line 
        // Extract first header info from file metadata
        const headerFirstHeading = await app.metadataCache.getFileCache(await tp.file.find_tfile(await tp.file.title)).headings[0].heading
        const headerFirstLevel = await app.metadataCache.getFileCache(await tp.file.find_tfile(await tp.file.title)).headings[0].level
        const headerFirst = '#'.repeat(headerFirstLevel) + ' ' + headerFirstHeading;
            console.log("first header \n\n", headerFirst);
        // Find header line in file content 
        const headerLine = await tp.file.content.split('\n').indexOf(headerFirst);
            console.log("first header Line # \n\n", headerLine);
        // Position cursor at end of header line
        this.app.workspace.getActiveViewOfType(tp.obsidian.MarkdownView).editor.setCursor(headerLine);
    }
%><% '\n\n' + await tp.file.include(`[[${tocTemplate}]]`) %>
