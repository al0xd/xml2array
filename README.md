xml2array
=========
 * Array2XML: A class to convert array in PHP to XML
 * It also takes into account attributes names unlike SimpleXML in PHP
 * It returns the XML in form of DOMDocument class for further manipulation.
 * It throws exception if the tag name or attribute name has illegal chars.
 *
 * Author : Lalit Patel
 * Website: http://www.lalit.org/lab/convert-php-array-to-xml-with-attributes
 * License: Apache License 2.0
 *          http://www.apache.org/licenses/LICENSE-2.0
 * Version: 0.1 (10 July 2011)
 * Version: 0.2 (16 August 2011)
 *          - replaced htmlentities() with htmlspecialchars() (Thanks to Liel Dulev)
 *          - fixed a edge case where root node has a false/null/0 value. (Thanks to Liel Dulev)
 * Version: 0.3 (22 August 2011)
 *          - fixed tag sanitize regex which didn't allow tagnames with single character.
 * Version: 0.4 (18 September 2011)
 *          - Added support for CDATA section using @cdata instead of @value.
 * Version: 0.5 (07 December 2011)
 *          - Changed logic to check numeric array indices not starting from 0.
 * Version: 0.6 (04 March 2012)
 *          - Code now doesn't @cdata to be placed in an empty array
 * Version: 0.7 (24 March 2012)
 *          - Reverted to version 0.5
 * Version: 0.8 (02 May 2012)
 *          - Removed htmlspecialchars() before adding to text node or attributes.
 *

 Usage:
 ======
```php
$xml = Array2XML::createXML('root_node_name', $php_array);
echo $xml->saveXML();
```

Important thing to note is that the $xml object returned is of type DOMDocument and hence you can perform further operations on it.

Optionally you can also set the version of XML and encoding by calling the Array2XML::init() function before calling the Array2XML::createXML() function.

```php
Array2XML::init($version /* ='1.0' */, $encoding /* ='UTF-8' */);
```
It throws exception if the tag name or attribute name has illegal chars as per W3C spec.

Array Structure conventions

The array passed to the Array2XML::createXML() function follows few conventions, which are quite literal and easy to learn/use. The examples below demonstrate their usage

Empty Nodes: Following will create an empty node.
```php
$books = array();  // or
$books = null;  // or
$books = '';
$xml = Array2XML::createXML('books', $books);
 
// all three cases above create <books/>
```
Attributes: Attributes can be added to any node by having a @attributes key in the array
```php
$books = array(
    '@attributes' => array(
        'type' => 'fiction',
        'year' => 2011,
        'bestsellers' => true
    )
);
$xml = Array2XML::createXML('books', $books);
 
// creates <books type="fiction" year="2011" bestsellers="true"/>
```
Node Value: For nodes without attributes, value can be assigned directly, else we need to have a @value key in the array. Following examples will make it clear
```php
$books = 1984;  // or
$books = array(
    '@value' = 1984
);
// creates <books>1984</books>
 
$books = array(
    '@attributes' => array(
        'type' => 'fiction'
    ),
    '@value' = 1984
);
// creates <books type="fiction">1984</books>
 
$books = array(
    '@attributes' => array(
        'type' => 'fiction'
    ),
    'book' => 1984
);
/* creates 
<books type="fiction">
  <book>1984</book>
</books>
*/
 
$books = array(
    '@attributes' => array(
        'type' => 'fiction'
    ),
    'book'=> array('1984','Foundation','Stranger in a Strange Land')
);
/* creates 
<books type="fiction">
  <book>1984</book>
  <book>Foundation</book>
  <book>Stranger in a Strange Land</book>
</books>
*/
```

Complex XML: Following example clarifies most of the usage of the library
```php
$books = array(
    '@attributes' => array(
        'type' => 'fiction'
    ),
    'book' => array(
        array(
            '@attributes' => array(
                'author' => 'George Orwell'
            ),
            'title' => '1984'
        ),
        array(
            '@attributes' => array(
                'author' => 'Isaac Asimov'
            ),
            'title' => array('@cdata'=>'Foundation'),
            'price' => '$15.61'
        ),
        array(
            '@attributes' => array(
                'author' => 'Robert A Heinlein'
            ),
            'title' =>  array('@cdata'=>'Stranger in a Strange Land'),
            'price' => array(
                '@attributes' => array(
                    'discount' => '10%'
                ),
                '@value' => '$18.00'
            )
        )
    )
);
/* creates 
<books type="fiction">
  <book author="George Orwell">
    <title>1984</title>
  </book>
  <book author="Isaac Asimov">
    <title><![CDATA[Foundation]]></title>
    <price>$15.61</price>
  </book>
  <book author="Robert A Heinlein">
    <title><![CDATA[Stranger in a Strange Land]]</title>
    <price discount="10%">$18.00</price>
  </book>
</books>
*/
```