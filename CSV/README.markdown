# CHCSVParser

`CHCSVParser` is a Cocoa reader and writer for CSV files.

## Things missing on the 3.0.0 branch

- encoding sniffing
- csv writing

## Requirements

- Xcode 8
- Mac OS X 10.9+
- iOS 8.0+
- watchOS 2.0+

## Usage

`CSVParser` is packaged as a Swift framework, and there are several ways to install it:

1. Add CSVParser.xcodeproj to your workspace and link CSVParser.framework into your app.
1. Install using Carthage.
1. Install using CocoaPods.

###Parsing
A `CSVParser` works very similarly to an `NSXMLParser`, in that it synchronously parses the data and invokes delegate callback methods to let you know that it has found a field, or has finished reading a line, or has encountered a syntax error.

A `CSVParser` can be created in one of three ways:

1. With a URL to a file
2. With the contents of an `NSString`
3. With an `NSInputStream`

`CSVParser` can be configured to parse other "character-separated" file formats, such as "TSV" (tab-separated).  You can specify the delimiter of the parser during initialization.  The delimiter can only be one character in length, and cannot be any newline character or `"`. Additionally, depending on which options you set on the parser, you may not use `#`, `\`, or `=` as the delimiter either

By default, `CSVParser` will not sanitize the output of the fields; in other words, individual fields will be returned exactly as they are found in the CSV file.  However, if you wish the fields to be cleaned (surrounding double quotes stripped, characters unescaped, etc), you can specify this by setting the `sanitizesFields` property to `YES`.

`CSVParser` has other properties to alter the parsing behavior:

- `recognizesBackslashesAsEscapes` allows you to parse delimited files where special characters (the delimiter, newlines, etc) are escaped using a backslash. When this option is enabled, you may not use a backslash as a delimiter. This option is disabled by default.

- `recognizesComments` will skip parsing fields that being with an octothorpe (`#`). These fields are reported to the parser delegate as comments, and comments are terminated by an unescaped newline character. This option is disabled by default.

- `recognizesLeadingEqualSign` allows quoted fields to begin with an `=`. Some programs use a leading equal sign to indicate that the contents of the field should be interpreted explicitly, and things like insignificant digits should not be removed. This option is disabled by default.

###Writing
A `CSVWriter` has several methods for constructing CSV files:

`-writeField:` accepts an object and writes its `-description` (after being properly escaped) out to the CSV file.  It will also write field separator (`,`) if necessary.  You may pass an empty string (`@""`) or `nil` to write an empty field.

`-finishLine` is used to terminate the current CSV line.  If you do not invoke `-finishLine`, then all of your CSV fields will be on a single line.

`-writeLineOfFields:` accepts an array of objects, sends each one to `-writeField:`, and then invokes `-finishLine`.

`-writeComment:` accepts a string and writes it out to the file as a CSV-style comment.

If you wish to write CSV directly into an `NSString`, you should create an `NSOutputStream` for writing to memory and use that as the output stream of the `CSVWriter`.  For an example of how to do this, see the `-[NSArray(CSVAdditions) CSVString]` method.

Like `CSVParser`, `CSVWriter` can be customized with a delimiter other than `,` during initialization.

###Convenience Methods

There are a couple of category methods on `NSArray` and `NSString` to simplify the common reading and writing of delimited files.

In addition, the convenience APIs allow for additional parsing options beyond what is provided by `CSVParser`. When you specify the `CSVParserOptionUsesFirstLineAsKeys` option, parsing will return an array of `CSVOrderedDictionary` instances, instead of an array of arrays of strings.

A `CSVOrderedDictionary` is an `NSDictionary` subclass that maintains a specific order to its key-value pairs, and allows you to look up keys and values by index.


##Data Encoding
`CSVParser` relies on knowing the encoding of the content.  It should work with pretty much any kind of file encoding, if you can provide what that encoding is.  If you do not know the encoding of the file, then `CSVParser` can make a naïve guess.  `CSVParser` will try to guess the encoding of the file from among these options:

 - MacOS Roman (`NSMacOSRomanStringEncoding`; the default/fallback encoding)
 - UTF-8 (`NSUTF8StringEncoding`)
 - UTF-16BE (`NSUTF16BigEndianStringEncoding`)
 - UTF-16LE (`NSUTF16LittleEndianStringEncoding`)
 - UTF-32BE (`NSUTF32BigEndianStringEncoding`)
 - UTF-32LE (`NSUTF32LittleEndianStringEncoding`)
 - ISO 2022-KR (`kCFStringEncodingISO_2022_KR`)
 
##Performance
`CSVParser` is conscious of low-memory environments, such as the iPhone or iPad.  It can safely parse very large CSV files, because it only loads portions of the file into memory at a single time.
 
##Credits & Contributors

`CSVParser` was written by [Dave DeLong][1] and has accepted patches from [several other contributors](https://github.com/davedelong/CSVParser/graphs/contributors).

`CSVParser` uses code to discover file encoding that was provided by [Rainer Brockerhoff][2].

  [1]: http://davedelong.com
  [2]: http://brockerhoff.net
  
##License

`CSVParser` is licensed under the MIT license, which is reproduced in its entirety here:


>Copyright (c) 2014 Dave DeLong
>
>Permission is hereby granted, free of charge, to any person obtaining a copy
>of this software and associated documentation files (the "Software"), to deal
>in the Software without restriction, including without limitation the rights
>to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
>copies of the Software, and to permit persons to whom the Software is
>furnished to do so, subject to the following conditions:
>
>The above copyright notice and this permission notice shall be included in
>all copies or substantial portions of the Software.
>
>THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
>IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
>FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
>AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
>LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
>OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
>THE SOFTWARE.
