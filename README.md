# TextMeta

TextMeta allows for processing of PDF files, it extracts the text & metadata using rules (Regular Expressions) which is then returned in the format `{ "text": "extracted_text", meta: {}}`.

## Usage

    var textmeta = require('textmeta');

    var rules = [
      {
        key: "Author",
        type: "FirstSingle",
        expression: "Author:\\s+([^\\n]+)",
        default: "Unknown"
      }
    ];

    textmeta.extractFromPDFFile("samples/sample_doc.pdf", rules).then((result) => {
      console.log("Text => " + result.text);
      console.log("Metadata => " + JSON.stringify(result.meta, null, 4));
    });

## API

### extractFromPDFFile(file, rules, options)

* Extracts text from the source PDF `file` and metadata using the supplied `rules`.
* The `options` parameter is optional, for more details see below.
* Returns `{ text: "...", meta: {} }`.

### extractFromPDFBuffer(buffer, rules, options)


* Extracts text from the supplied `buffer` and metadata using the supplied `rules`.
* The `options` parameter is optional, for more details see below.
* Returns `{ text: "...", meta: {} }`.

### processText(file, rules, options)

* Returns metadata using the supplied `text`.
* The `options` parameter is optional, for more details see below.

## Rules

The format for a rule is

    {
      "key": "<Metadata Name>",
      "type": "<Match Type>",
      "expression": "<Regular Expression>",
      "default": "<Default Value if no matches>"
      "startKeyword": "Optional: <Keyword for substring match start>",
      "endKeyword": "Optional: <Keyword for substring match end>",
      "options": {
        "flags": "<Optional RegularExpression Flags>"
      }
    }

### Rule Types:

* "FirstSingle" : Will capture the first match.
* "All" : Will capture all matches.
* "AllUnique" : Will capture all matches and return the list of unique strings.

### Start/End Keywords

If you want to run your expression on a subset of the text, then specify the start/end keywords and only the text in between will be used.

## Options

The default `options` are

    {
        "numericPrecision": 0,
        "allWhitespaceRegexp": "/^\s+$/"
        "replaceMultipleSpaces": true,
        "orderByYPos": true,
        "distanceToAddSpace": 1
    }

* `numericPrecision` : specifies the decimal precision when doing position (X, Y) comparisons.
* `allWhitespaceRegexp`: regular expression used (represented as a string) to determine if the text contains all whitespace characters.
* `replaceMultipleSpaces`: boolean indicating if multiple spaces should be replaced with just a single space.
* `orderByYPos`: boolean indicating if the Y position of the text should be used to determine order. Some PDF files can be generated with text out of sequence (since the actual position of the text is determined by it's the transformation matrix which contains the X & Y positions). When set to true, TextMeta will first extract all lines on a page keeping track of their Y position then sort them.
* `distanceToAddSpace`: Sometimes text in the PDF can be chucked (that is to say instead of full sentances sometimes words are broken up), this value indicates the minimum distance required between two text elements before a space ' ' is applied between them.


## Sample PDF

The result of processing the sample file [sample_doc.pdf](https://github.com/m-gagne/PDF2AzSearch/blob/master/sample/sample_doc.pdf) using the sample [rules.json](https://github.com/m-gagne/PDF2AzSearch/blob/master/functions/pdfmetafunc/rules.json) is the following result:


### Text

      Title: PDF to Text Function
      Author: Marc Gagne 
      
      Description: 
      An azure function that extracts text from PDFs, runs the regular expression captures found in rules.json 
      against the text and stores the results in DocumentDB. 
      
      Technologies used: 
       Azure Functions 
       pdf.js 
       JavaScript 
       Node.js 
      
      GitHub: https://github.com/m-gagne/PDF2AzSearch 


Meta Data

        {
          "id": "dafe8948ef379e6aef78cc1b059122cebcae436d7dd878375f16094a99a9243b",
          "name": "sample_doc.pdf",
          "text": "Title: PDF to Text Function \nAuthor: Marc Gagne \n \nDescription:  \nAn azure function that extracts text from PDFs, runs the regular expression captures found in rules.json \nagainst the text and stores the results in DocumentDB. \n \nTechnologies used: \n• Azure Functions \n• pdf.js \n• JavaScript \n• Node.js \n \nGitHub: https://github.com/m-gagne/PDF2AzSearch \n ",
          "last_updated": "2017-05-23T20:10:31.653Z",
          "meta": {
            "Title": "PDF to Text Function",
            "Author": "Marc Gagne",
            "Description": "An azure function that extracts text from PDFs, runs the regular expression captures found in rules.json",
            "Technologies": [
              "Azure Functions",
              "pdf.js",
              "JavaScript",
              "Node.js"
            ]
          }
        }