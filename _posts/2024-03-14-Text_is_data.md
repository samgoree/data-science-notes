# Interlude: Text Data

A big topic in data science that does not come up in traditional statistics is how to work with text as data. In this chapter, we will cover some basic text data analysis using Python tools. Along the way we will encounter some more general computer science ideas like loops and string manipulation.

## Text is Data

In other disciplines, we tend to distinguish between text, which is qualitative and meant to be read by humans, from data which is quantitative and can be read by machines. But in data science, we tend to view data and text as interchangeable. This is because computers represent everything: documents, apps, movies, music, images, etc. as binary numbers. Computer hard drives can only store very very long sequences of binary digits, called bits. We call 8 bits a byte. You may have heard terms like "kilobyte" or "megabyte". These describe storage capacity in terms of the number of binary numbers they can store. A megabyte is 1 million bytes, or 8 million bits, 8 million ones or zeroes.

Let's see how text is encoded into binary. For example, imagine we have a sentence:

"I love data!"

This sentence is encoded down to 0s and 1s on your computer, just like everything else. The particular encoding that we use is an extension of the ASCII system. ASCII stands for American Standard Code for Information Interchange and assigns each symbol used in English to a numerical value, which can be encoded into binary. So this sentence is encoded as a list of numbers:

`[73, 32, 108, 111, 118, 101, 32, 100, 97, 116, 97, 33]`

Which are then converted into a binary string:

`1001001100000110110011011111110110110010110000011001001100001111010011000011000010`

The full table is shown below (reproduced from https://www.rapidtables.com/code/text/ascii-table.html):

<button type="button" onClick="this.classList.toggle('active'); var content = this.nextElementSibling; if (content.style.display   === 'block') {content.style.display =   'none';} else {content.style.display = 'block';}">Show Table</button>

<div class="content" style="display: 'none';">
<table>
<thead style="display: table-header-group;">
<tr>
<th scope="col">Decimal</th>
<th scope="col" style="display: table-cell;">Binary</th>
<th scope="col">Char</th>
<th scope="col" style="display: table-cell;">Description</th>
</tr>
</thead>
<tbody>
<tr>
<td style="font-size: unset; font-weight: normal;">0</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00000000</td>
<td style="font-size: unset; font-weight: normal;">NUL</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Null</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">1</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00000001</td>
<td style="font-size: unset; font-weight: normal;">SOH</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Start of Heading</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">2</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00000010</td>
<td style="font-size: unset; font-weight: normal;">STX</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Start of Text</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">3</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00000011</td>
<td style="font-size: unset; font-weight: normal;">ETX</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">End of Text</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">4</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00000100</td>
<td style="font-size: unset; font-weight: normal;">EOT</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">End of Transmission</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">5</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00000101</td>
<td style="font-size: unset; font-weight: normal;">ENQ</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Enquiry</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">6</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00000110</td>
<td style="font-size: unset; font-weight: normal;">ACK</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Acknowledge</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">7</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00000111</td>
<td style="font-size: unset; font-weight: normal;">BEL</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Bell</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">8</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00001000</td>
<td style="font-size: unset; font-weight: normal;">BS</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Backspace</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">9</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00001001</td>
<td style="font-size: unset; font-weight: normal;">HT</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Horizontal Tab</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">10</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00001010</td>
<td style="font-size: unset; font-weight: normal;">LF</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Line Feed</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">11</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00001011</td>
<td style="font-size: unset; font-weight: normal;">VT</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Vertical Tab</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">12</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00001100</td>
<td style="font-size: unset; font-weight: normal;">FF</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Form Feed</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">13</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00001101</td>
<td style="font-size: unset; font-weight: normal;">CR</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Carriage Return</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">14</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00001110</td>
<td style="font-size: unset; font-weight: normal;">SO</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Shift Out</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">15</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00001111</td>
<td style="font-size: unset; font-weight: normal;">SI</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Shift In</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">16</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00010000</td>
<td style="font-size: unset; font-weight: normal;">DLE</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Data Link Escape</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">17</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00010001</td>
<td style="font-size: unset; font-weight: normal;">DC1</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Device Control 1</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">18</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00010010</td>
<td style="font-size: unset; font-weight: normal;">DC2</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Device Control 2</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">19</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00010011</td>
<td style="font-size: unset; font-weight: normal;">DC3</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Device Control 3</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">20</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00010100</td>
<td style="font-size: unset; font-weight: normal;">DC4</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Device Control 4</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">21</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00010101</td>
<td style="font-size: unset; font-weight: normal;">NAK</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Negative Acknowledge</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">22</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00010110</td>
<td style="font-size: unset; font-weight: normal;">SYN</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Synchronize</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">23</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00010111</td>
<td style="font-size: unset; font-weight: normal;">ETB</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">End of Transmission Block</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">24</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00011000</td>
<td style="font-size: unset; font-weight: normal;">CAN</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Cancel</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">25</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00011001</td>
<td style="font-size: unset; font-weight: normal;">EM</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">End of Medium</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">26</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00011010</td>
<td style="font-size: unset; font-weight: normal;">SUB</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Substitute</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">27</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00011011</td>
<td style="font-size: unset; font-weight: normal;">ESC</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Escape</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">28</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00011100</td>
<td style="font-size: unset; font-weight: normal;">FS</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">File Separator</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">29</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00011101</td>
<td style="font-size: unset; font-weight: normal;">GS</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Group Separator</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">30</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00011110</td>
<td style="font-size: unset; font-weight: normal;">RS</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Record Separator</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">31</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00011111</td>
<td style="font-size: unset; font-weight: normal;">US</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Unit Separator</td>
</tr>
<tr id="row32">
<td style="font-size: unset; font-weight: normal;">32</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00100000</td>
<td style="font-size: unset; font-weight: normal;">space</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">Space</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">33</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00100001</td>
<td style="font-size: unset; font-weight: normal;">!</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">exclamation mark</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">34</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00100010</td>
<td style="font-size: unset; font-weight: normal;">"</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">double quote</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">35</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00100011</td>
<td style="font-size: unset; font-weight: normal;">#</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">number</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">36</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00100100</td>
<td style="font-size: unset; font-weight: normal;">$</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">dollar</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">37</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00100101</td>
<td style="font-size: unset; font-weight: normal;">%</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">percent</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">38</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00100110</td>
<td style="font-size: unset; font-weight: normal;">&amp;</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">ampersand</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">39</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00100111</td>
<td style="font-size: unset; font-weight: normal;">'</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">single quote</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">40</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00101000</td>
<td style="font-size: unset; font-weight: normal;">(</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">left parenthesis</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">41</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00101001</td>
<td style="font-size: unset; font-weight: normal;">)</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">right parenthesis</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">42</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00101010</td>
<td style="font-size: unset; font-weight: normal;">*</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">asterisk</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">43</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00101011</td>
<td style="font-size: unset; font-weight: normal;">+</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">plus</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">44</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00101100</td>
<td style="font-size: unset; font-weight: normal;">,</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">comma</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">45</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00101101</td>
<td style="font-size: unset; font-weight: normal;">-</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">minus</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">46</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00101110</td>
<td style="font-size: unset; font-weight: normal;">.</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">period</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">47</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00101111</td>
<td style="font-size: unset; font-weight: normal;">/</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">slash</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">48</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00110000</td>
<td style="font-size: unset; font-weight: normal;">0</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">zero</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">49</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00110001</td>
<td style="font-size: unset; font-weight: normal;">1</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">one</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">50</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00110010</td>
<td style="font-size: unset; font-weight: normal;">2</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">two</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">51</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00110011</td>
<td style="font-size: unset; font-weight: normal;">3</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">three</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">52</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00110100</td>
<td style="font-size: unset; font-weight: normal;">4</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">four</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">53</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00110101</td>
<td style="font-size: unset; font-weight: normal;">5</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">five</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">54</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00110110</td>
<td style="font-size: unset; font-weight: normal;">6</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">six</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">55</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00110111</td>
<td style="font-size: unset; font-weight: normal;">7</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">seven</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">56</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00111000</td>
<td style="font-size: unset; font-weight: normal;">8</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">eight</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">57</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00111001</td>
<td style="font-size: unset; font-weight: normal;">9</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">nine</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">58</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00111010</td>
<td style="font-size: unset; font-weight: normal;">:</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">colon</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">59</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00111011</td>
<td style="font-size: unset; font-weight: normal;">;</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">semicolon</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">60</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00111100</td>
<td style="font-size: unset; font-weight: normal;">&lt;</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">less than</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">61</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00111101</td>
<td style="font-size: unset; font-weight: normal;">=</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">equality sign</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">62</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00111110</td>
<td style="font-size: unset; font-weight: normal;">&gt;</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">greater than</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">63</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">00111111</td>
<td style="font-size: unset; font-weight: normal;">?</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">question mark</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">64</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01000000</td>
<td style="font-size: unset; font-weight: normal;">@</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">at sign</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">65</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01000001</td>
<td style="font-size: unset; font-weight: normal;">A</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">66</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01000010</td>
<td style="font-size: unset; font-weight: normal;">B</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">67</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01000011</td>
<td style="font-size: unset; font-weight: normal;">C</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">68</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01000100</td>
<td style="font-size: unset; font-weight: normal;">D</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">69</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01000101</td>
<td style="font-size: unset; font-weight: normal;">E</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">70</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01000110</td>
<td style="font-size: unset; font-weight: normal;">F</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">71</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01000111</td>
<td style="font-size: unset; font-weight: normal;">G</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">72</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01001000</td>
<td style="font-size: unset; font-weight: normal;">H</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">73</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01001001</td>
<td style="font-size: unset; font-weight: normal;">I</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">74</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01001010</td>
<td style="font-size: unset; font-weight: normal;">J</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">75</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01001011</td>
<td style="font-size: unset; font-weight: normal;">K</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">76</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01001100</td>
<td style="font-size: unset; font-weight: normal;">L</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">77</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01001101</td>
<td style="font-size: unset; font-weight: normal;">M</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">78</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01001110</td>
<td style="font-size: unset; font-weight: normal;">N</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">79</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01001111</td>
<td style="font-size: unset; font-weight: normal;">O</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">80</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01010000</td>
<td style="font-size: unset; font-weight: normal;">P</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">81</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01010001</td>
<td style="font-size: unset; font-weight: normal;">Q</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">82</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01010010</td>
<td style="font-size: unset; font-weight: normal;">R</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">83</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01010011</td>
<td style="font-size: unset; font-weight: normal;">S</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">84</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01010100</td>
<td style="font-size: unset; font-weight: normal;">T</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">85</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01010101</td>
<td style="font-size: unset; font-weight: normal;">U</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">86</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01010110</td>
<td style="font-size: unset; font-weight: normal;">V</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">87</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01010111</td>
<td style="font-size: unset; font-weight: normal;">W</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">88</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01011000</td>
<td style="font-size: unset; font-weight: normal;">X</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">89</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01011001</td>
<td style="font-size: unset; font-weight: normal;">Y</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">90</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01011010</td>
<td style="font-size: unset; font-weight: normal;">Z</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">91</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01011011</td>
<td style="font-size: unset; font-weight: normal;">[</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">left square bracket</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">92</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01011100</td>
<td style="font-size: unset; font-weight: normal;">\</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">backslash</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">93</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01011101</td>
<td style="font-size: unset; font-weight: normal;">]</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">right square bracket</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">94</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01011110</td>
<td style="font-size: unset; font-weight: normal;">^</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">caret / circumflex</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">95</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01011111</td>
<td style="font-size: unset; font-weight: normal;">_</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">underscore</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">96</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01100000</td>
<td style="font-size: unset; font-weight: normal;">`</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">grave / accent</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">97</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01100001</td>
<td style="font-size: unset; font-weight: normal;">a</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">98</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01100010</td>
<td style="font-size: unset; font-weight: normal;">b</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">99</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01100011</td>
<td style="font-size: unset; font-weight: normal;">c</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">100</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01100100</td>
<td style="font-size: unset; font-weight: normal;">d</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">101</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01100101</td>
<td style="font-size: unset; font-weight: normal;">e</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">102</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01100110</td>
<td style="font-size: unset; font-weight: normal;">f</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">103</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01100111</td>
<td style="font-size: unset; font-weight: normal;">g</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">104</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01101000</td>
<td style="font-size: unset; font-weight: normal;">h</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">105</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01101001</td>
<td style="font-size: unset; font-weight: normal;">i</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">106</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01101010</td>
<td style="font-size: unset; font-weight: normal;">j</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">107</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01101011</td>
<td style="font-size: unset; font-weight: normal;">k</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">108</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01101100</td>
<td style="font-size: unset; font-weight: normal;">l</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">109</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01101101</td>
<td style="font-size: unset; font-weight: normal;">m</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">110</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01101110</td>
<td style="font-size: unset; font-weight: normal;">n</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">111</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01101111</td>
<td style="font-size: unset; font-weight: normal;">o</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">112</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01110000</td>
<td style="font-size: unset; font-weight: normal;">p</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">113</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01110001</td>
<td style="font-size: unset; font-weight: normal;">q</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">114</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01110010</td>
<td style="font-size: unset; font-weight: normal;">r</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">115</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01110011</td>
<td style="font-size: unset; font-weight: normal;">s</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">116</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01110100</td>
<td style="font-size: unset; font-weight: normal;">t</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">117</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01110101</td>
<td style="font-size: unset; font-weight: normal;">u</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">118</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01110110</td>
<td style="font-size: unset; font-weight: normal;">v</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">119</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01110111</td>
<td style="font-size: unset; font-weight: normal;">w</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">120</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01111000</td>
<td style="font-size: unset; font-weight: normal;">x</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">121</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01111001</td>
<td style="font-size: unset; font-weight: normal;">y</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">122</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01111010</td>
<td style="font-size: unset; font-weight: normal;">z</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">&nbsp;</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">123</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01111011</td>
<td style="font-size: unset; font-weight: normal;">{</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">left curly bracket</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">124</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01111100</td>
<td style="font-size: unset; font-weight: normal;">|</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">vertical bar</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">125</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01111101</td>
<td style="font-size: unset; font-weight: normal;">}</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">right curly bracket</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">126</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01111110</td>
<td style="font-size: unset; font-weight: normal;">~</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">tilde</td>
</tr>
<tr>
<td style="font-size: unset; font-weight: normal;">127</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">01111111</td>
<td style="font-size: unset; font-weight: normal;">DEL</td>
<td style="display: table-cell; font-size: unset; font-weight: normal;">delete</td>
</tr>
</tbody>
</table>
</div>

## Data is Text

Similarly, all data on computers can be represented as text strings. While integers and floats are stored inside the computer in binary, if we want humans to look at them, we need to convert them to strings of digits. For example: the number 150 is stored in binary as `10010110`, but if we want to show that number to a person, we print out the string "150" literally the digit 1, the digit 5 and the digit 0.

We also often store data in text files. We saw this in chapter {{chapter_numpy}} where we saved data arrays to `txt` files. We can store the inherent binary representations of data as well, for example, 

```python
arr = np.array([[1, 0, 1],
				[0, 1, 0],
				[1, 0, 1]])
arr.save('myarr.npy')
```

When this code is run, the file `myarr.npy` now contains the data:

```
934e 554d 5059 0100 7600 7b27 6465 7363
7227 3a20 273c 6934 272c 2027 666f 7274
7261 6e5f 6f72 6465 7227 3a20 4661 6c73
652c 2027 7368 6170 6527 3a20 2833 2c20
3329 2c20 7d20 2020 2020 2020 2020 2020
2020 2020 2020 2020 2020 2020 2020 2020
2020 2020 2020 2020 2020 2020 2020 2020
2020 2020 2020 2020 2020 2020 2020 200a
0100 0000 0000 0000 0100 0000 0000 0000
0100 0000 0000 0000 0100 0000 0000 0000
0100 0000 
```

This is represented in hexadecimal, so each letter or number above represents four bits, and each block represents 16 bits.

If I decode this file with the UTF-8 encoding (a common extension of ascii), we see that the first few blocks there encode the string:

```
?NUMPY  v {'descr': '<i4', 'fortran_order': False, 'shape': (3, 3), }
```

Then the remaining blocks encode the values in the array, which are all either 0 or 1. Notice all the 0s and 1s in the bottom third of the file.

If I decode using another code, for instance UTF-16, I get a totally different result:

```
亓䵕奐v❻敤捳❲›㰧㑩Ⱗ✠潦瑲慲彮牯敤❲›慆獬ⱥ✠桳灡❥›
```

This is nonsensical, mixing various symbols with Chinese characters. Having the right encoding scheme is essential to convert from one data representation to another, and computers don't inherently know which one to use.

### Other Things are Text Too

Every single file on your computer, from the Jupyter program that we use to run our code to this website, is a text file. 