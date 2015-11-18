---
layout: post
title:  Generated content in a Google Document
date:   2015-11-19 21:57:00 +1000
---

I was using a Google doc, and I wanted to be able generate 60 pages that were largely the same content (a few headings), the one difference being on each page the title would increment the day number as well as print the date, for a set time - a diary so to speak (so that I can log my lifestyle habits over a given period of time - food, excercise, leisure). Needless to say, that can be very tedious doing it page by page.

First, I tried Googling but my results didn't yield any results I was looking for - but perhaps I wasn't using the correct search terms.

So, my next thought was just to use SQL. Oracle has a `dual` table that can be used as a row generator with the `connect by level` clause. I just need to get it to output my desired content, along with the correct number of blank lines between rows so that each diary entry would begin on a new page. So with that in mind, I came up with the following query:

```SQL
select
    'Day ' || level
    || ' (' || to_char(to_date('15-11-2015','dd-mm-yyyy')+level, 'Fmddth Month YYYY') || ')'
    || 'other page titles' text
from dual connect by level <= 60
```

This worked well enough. Until you start to add content, then the next page goes down. Then you have to hit `del` a bunch of times to get it to the top of the page again - and so on and so forth.

On the other hand, if you use Google App Script, you can programatically add content to a page, and also add page breaks. Page breaks is the crucial element here - by using these, the next diary page won't shift down as you expand the current page.

I had a quick skim over the reference, and it seemed like it would be quite possible - so I started hacking away. Since it directly relates to a `doc` I went straight to the `Document` section of the reference library - [https://developers.google.com/apps-script/reference/document/document-app]().

To start writing scripts for your documents, you just have to load up the script editor, which is accessed via the Tools menu (Script editor...). This resulted in the following code:

```js
function addLineToPage(body, text){

  var text = body.appendParagraph(text);
  text.setLineSpacing(2);

}

function addTemplatePages() {


  var d = DocumentApp.getActiveDocument();
  var docBody = d.getBody();

  docBody.appendPageBreak();

  for(var i = 0; i <= 10; i++){
    var startDate = new Date(2015,10,16);
    addLineToPage(docBody, getTitle(startDate, i));
    addLineToPage(docBody, "other page titles");
    docBody.appendPageBreak();

  }
}


function getTitle(startDate, offset){

  var months = [
    "January",
    "Febuary",
    "March",
    "April",
    "May",
    "June",
    "July",
    "August",
    "September",
    "October",
    "November",
    "December"
  ];


  var desiredDate = startDate;
  desiredDate.setDate(desiredDate.getDate() + offset);

  var dayNum = desiredDate.getDate();
  //Using an array instead of Utilites.formatDate as it was returning the first
  //of each month as previous month.... which I'm not quite sure why at this stage
  var monthName = months[desiredDate.getMonth()];

  return Utilities.formatString("Day %d (%s%s %s)", offset+1, dayNum, getDayOrdinal(dayNum), monthName);

}

function getDayOrdinal(n){
  //no ordinal in the date format string? bugger.
  //function sourced from: http://stackoverflow.com/a/4011232/3476713
  if (n >= 11 && n <= 13) {
        return "th";  
  }

  switch(n % 10){
    case 1:
      return "st";
    case 2:
      return "nd";
    case 3:
      return "rd";
    default:
      return "th";
  }

}
```

Then it's just a matter of running the code from the code editor, and your new content will be added.
