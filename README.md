# ebay-purchase-report
A Web Extension that generates a compact eBay purchase history report

The eBay purchase history doesn't look friendly when you have tons of purchases and you are interested to see a compact grouped list of what you have purchased, how much it costed, how many shipped or not, etc.
Such a list can be easily generated by traversing the page's DOM and collecting these info and eventually printing out a report at the browser's console.

This project provides a Firefox|Chrome|Opera Web Extension (add-on) that aids in generating such a report. The report is generated in a new tab as a HTML document.

## Requirements

Tested on Linux with Firefox 45+, Chrome 55+, Opera 36+. It should work also on Windows or MacOS. Not built/tested on Microsoft Edge, Android or iPhone but who knows, it might just work :-)

## Install the add-on

The `user` way:
 * for Firefox browsers use the Mozilla's [Add to Firefox](https://addons.mozilla.org/en-US/firefox/addon/ebay-purchase-report) button.
 * for Chrome use the Google's [ADD TO CHROME](https://chrome.google.com/webstore/detail/ebay-purchase-history-rep/ohoebnmmkndcieckfjblpdlfjpaeonbc) button.

If you want to install/test a certain version then go to the [dist](https://github.com/eugenmihailescu/ebay-purchase-report/tree/master/dist) folder and download and install the version which is right for you: 
1. Download the .zip add-on file from [dist](https://github.com/eugenmihailescu/ebay-purchase-report/tree/master/dist) folder.
2. Open your browser Tools -> `Extension`|`Add-ons` menu
	* On Firefox on the top-right (near the `Search add-on` input) there is a `tools` drop-down button. Click that button and choose `Install Add-on From File`. Choose the file downloaded at step (1)
	* On Chrome|Opera just drag & drop the .zip file downloaded at (1) over the `Extension` list. It will automatically suggest you to `Drop to install` 

The `developer` way:
 * review the source code, [build the package](#for-developers) yourself, then continue reading the `How to use it`.

## How to use it

1. Open the eBay page and log-in using your eBay account
2. Go to the My eBay -> `Purchase history` menu
3. When the page loads you will notice a golden `Quick Report` button on the Orders group box:
![alt text](http://mynixworld.info/wp-content/uploads/2013/04/ebay-purchase-history.png "Click the Quick Report button")
4. Click the `Quick Report` button. A new tab will open containing a compact HTML report of your eBay purchase history:
![alt text](http://mynixworld.info/wp-content/uploads/2013/04/ebay-purchase-report-2.png "Sample report")

Please note that the report will include only visible items. For instance, if you chose to see only 25 `Orders per page` then the report will include only these orders. To include the whole history make sure you choose the largest `Orders per page` option available (eg. 100).

## Shown columns

By default the report would include the following columns:
 - \# : the index of the report's row
 - Seller: the id of the eBay seller
 - Purchase date: the eBay `Order date` information
 - Item price : the eBay `Item price` value
 - Quantity : the eBay quantity for the item
 - Shipping status: the eBay order's item shipping status
 - Estimated delivery: the eBay order's item `Estimated delivery` date, if available
 - Item description : the eBay order's item description as shown on purchase history
 
By default the report would generate a group footer subtotal for each date/currency value. This group footer shows the item count within group, their total amount, how many are shipped/not-shipped and the average price per item.
The report will print also a grand total for the entire report.

Starting with v1.0.7 it will also show the item's thumbnail picture while moving the mouse over the item :-)
 
## Sort ordering

The report is generated by default using the natural order of the purchased items. If you want to sort the report by a given column then click the arrow icon that is shown near the column name. It works both ascendent and descendent. 

## Export data

The report allows you to export its dataset to tab-delimited, JSON and XML format. Just use the link shown on the report document. 

## Caveat Emptor

1. Due the fact that the add-on iterates the current shown order items the report will include exactly the items you see in the current `eBay Purchase History` page.
2. If you want to open an item's details page just click the item. The add-on will simulate the click of the respective item within the eBay purchase history page.  
3. While interacting with the report (sorting, opening item details, etc) make sure you dont' close the eBay purchase history tab/page otherwise an error message will be shown. 

## For Developers

If you want to fork this project then you might be interested in building automatically the .zip|.crx|.xpi bundles, right? Ok, the simplest way to do this is via the [Mozilla's web-ext](https://github.com/mozilla/web-ext) command line tool or, in case of Chrome|Opera via the [Chrome's crxmake](https://developer.chrome.com/extensions/crx) command line tool.

The building commands can be encapsulated within a Bourne shell script (that would normally work on Unix-like systems):
```bash
#!/bin/bash

EXT_NAME=ebay_purchase_report_history
EXT_PATH=${0%/*} # ;-)
EXT_SRC=$EXT_PATH/src/extension
EXT_DIST=$EXT_PATH/dist
EXT_VER=$(cat $EXT_SRC/manifest.json |grep '"version"'|grep -Eow "[0-9.]+")

web-ext -s $EXT_SRC -a $EXT_DIST --api-key=<your-addons.mozilla.org-api-key> --api-secret=<your-addons.mozilla.org-api-secret> --id=<your-addons.mozilla.org-api-UUID> sign

crxmake --pack-extension=$EXT_SRC --extension-output=$EXT_DIST/$EXT_NAME-$EXT_VER.crx --key-output=$EXT_DIST/$EXT_NAME-$EXT_VER.pem
```

Please note that in case of Firefox you should:
1. [generate your Mozilla API key](https://addons.mozilla.org/en-US/developers/addon/api/key/) (see `--api-key` and `--api-secret`)
2. manually submit only-once your forked add-on to [Mozilla's Add-ons aka AMO](https://addons.mozilla.org/en-US/developers/addons), grab the newly generated add-on `UUID` and use it on the above script.

Read more on [AMO](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Publishing_your_WebExtension) and/or on [Google](https://developer.chrome.com/extensions/packaging).

## Documentation
The JavaScript source files are annotated according to JSDoc specification and thus the source documentation can be produced on the fly by using the {@link http://usejsdoc.org/|JSDoc} tool.

In order to generate the JSDoc documentation you should follow the steps below. In case you want to use the JSDoc default template skip the step (1) below.
1. install your preferred JSDoc {@link http://bfy.tw/ABvL|template} (eg. `npm install {@link https://www.npmjs.com/package/interledger-jsdoc-template|minami}`)
  - make sure you update the `opts.template` property with the path to the installed JSDoc template in project's `conf.json` file
2. run the following command at the terminal: `jsdoc -c path-to-conf.json`  
  - this will create a `jsdoc` folder that will contain the auto-generated JSDoc documentation for the JavaScript source files
  
## What's Next

1. Make it work also for `Unpaid items`, `Canceled items`, `Canceled invoices` and `Returns and canceled orders` purchase history lists.