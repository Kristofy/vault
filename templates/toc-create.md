---

>[!SUMMARY]+ Table of Contents
<%*
// Amended from: https://github.com/SilentVoid13/Templater/discussions/888

// Another alternative method of getting headers from file (uses different way to get current file reference):
//const outlineArray = await app.metadataCache.getFileCache(await tp.file.find_tfile(await tp.file.title)).headings;

// Change to 1 to output useful debugging info in console (Ctrl + Shift + i)
// Most often outputs a breakdown of system commands to check they are working.
var deBug = 0;

// Get header level where TOC should stop from user
let header_limit = await tp.system.prompt("Show Contents Down to Which Header Level (1-6)?", "3");

// Get headers from file
if ( deBug == 1) { console.log("tp.config.active_file \n\n", tp.config.active_file) };
if ( deBug == 1) { console.log("tp.config.active_file.name \n\n", tp.config.active_file.name) };
if ( deBug == 1) { console.log("app.workspace.activeLeaf.view.file \n\n",app.workspace.activeLeaf.view.file) };
if ( deBug == 1) { console.log("this.app.workspace.getActiveFile \n\n",await this.app.workspace.getActiveFile()) };
const activeFile = await this.app.workspace.getActiveFile();
if ( deBug == 1) { console.log("this.app.metadataCache.getFileCache(activeFile)) \n\n",await this.app.metadataCache.getFileCache(activeFile)) };
const mdCache = await this.app.metadataCache.getFileCache(activeFile);
if ( deBug == 1) { console.log("mdCache.headings \n\n",mdCache.headings) };
const mdCacheListItems = mdCache.headings;

// Build TOC from headers
let TOC = "";
mdCache.headings.forEach( item => {
	var header_text = item.heading;
	var header_level = item.level;
	if ( deBug == 1) { console.log("array item header info \n\n",header_text, " (level:", header_level,")") };

	// Wikilinks style output:
	//let file_title= tp.file.title;
	//let header_url = header_text;         
	//let header_link = `[[${file_title}#${header_url}|${header_text}]]`

	// Non-wikilinks style output:
	let file_title= tp.file.title.replace(/ /g, '%20');   // Replace spaces in file names with '%20'
	let header_url = header_text.replace(/ /g, '%20');    // Replace spaces in urls with '%20'
	let header_link = `[${header_text}](${file_title}.md#${header_url})`;

	// Only output headers if level below specified limit
	if ( header_level <= header_limit) {
		TOC += `>${'    '.repeat(header_level - 1) + '- ' + header_link + "\n"}`;
	};

})
if ( deBug == 1) { console.log("TOC \n\n",TOC) };
%><% TOC %>
---
