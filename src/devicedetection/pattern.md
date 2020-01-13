@page DeviceDetection_Pattern Pattern

# Detection Process
With the Pattern method, User-Agent (UA) is the main HTTP header used to identify the requesting device. 
The device data file contains signatures which consist of the relevant characters of the User-Agent. Only the segments of the UA considered applicable to device detection are stored, characters which are not deemed necessary are omitted in order to reduce the size of the data file. For this reason detection results will rarely contain an entire User-Agent for the device returned.

The [51Degrees User-Agent Tester](https://51degrees.com/resources/user-agent-tester) can be used to show the relevant segments for a given User-Agent. 

Below, we will step through a number of User-Agent scenarios to illustrate the different ways in which the **pattern** method identifies a match.

## Exact Match

Consider the following User-Agent for the Nexus 7 running Android 4.3 and Chrome 29.

**Target UserAgent**  

[`Mozilla/5.0 (Linux; Android 4.3; Nexus 7 Build/JSS15Q) AppleWebKit/537.36 
(KHTML, like Gecko) Chrome/29.0.1547.72 Safari/537.36`][UA1]

When provided to the device detection routines the following sub strings relate exactly to the returned device information.

**Closest Sub Strings**
```
Mozilla/5.0 (Linux; Android 4.3; Nexus 7 Build/        AppleWebKit/
(KHTML, like Gecko) Chrome/29           Safari/537
```
**Rank** 60057  
**Difference** 0  
**Method** Exact

Those characters not relevant to the matching process could be changed and the same result returned. For example, the following User-Agent has the string "AAAAAA" in place of the characters "JSS15Q" which follow the "Build/" string.

**Target UserAgent**  

[`Mozilla/5.0 (Linux; Android 4.3; Nexus 7 Build/AAAAAA) AppleWebKit/537.36 
(Opera Mobi, Gecko) Chrome/29.0.1547.72 Safari/537.36`][UA2]
 
**Closest Sub Strings** 
```
Mozilla/5.0 (Linux; Android 4.3  Nexus 7 Build/        AppleWebKit              
             Gecko) Chrome/29           Safari/537
```       
**Rank** 60057  
**Difference** 0  
**Method** Exact

This revised User-Agent returns exactly the same device details as the original because the detection algorithm knows the presence of “AAAAAA” at these character positions is unimportant. Knowledge of the positions of relevant sub strings improves performance and accuracy as irrelevant characters do not provide distractions. 

In contrast, regular expression based solutions often suffer from considering these unimportant characters when determining the details of the device.

## Additional Matching Methods
Three different methods can be used to find a match for a User-Agent, the most commonly used is the **Exact** match as shown in the example above.

The method used to obtain the match is provided in all detection results along with a difference indicator. The difference value will always be zero when the Exact method has been used, as a precise match has been obtained against the signatures held in the data file and there is no difference between them.

If an Exact match cannot be found then a small subset of possible results will be searched using the following methods in turn. If a method finds a match the results will be returned and further methods not attempted.

### Nearest
Sometimes relevant sub strings may have been moved when irrelevant characters are added elsewhere in the User-Agent. The following User-Agent has an additional X character placed after the full version of Chrome. All subsequent characters that might be relevant are now moved 1 character position to the right of where the algorithm would expect to find them.

**Target UserAgent** 

[`Mozilla/5.0 (Linux; Android 4.3; Nexus 7 Build/JSS15Q) AppleWebKit/537.36
(KHTML, like Gecko) Chrome/29.0.1547.72X Safari/537.36`][UA3]

**Closest Sub Strings** 
```
Mozilla/5.0 (Linux; Android 4.3  Nexus 7 Build/        AppleWebKit/              
(KHTML, like Gecko) Chrome/29            Safari/537
```       
**Rank** 60057  
**Difference** 1  
**Method** Nearest

The correct device is returned but the fact that the relevant sub strings are not in exactly the same position as the matched signature can be determined via the match method and the difference values.

In this case the method used was **Nearest** indicating that all the relevant sub strings were present but not at the expected character positions. The variance in character positions is 1 as indicated in the difference value.

If an older data file is being used for device detection the **Nearest** method will be used more often as subtle differences are observed between the older data in the file and the format of current User-Agents.

### Closest

If the previous methods fail to detect the device, the **Closest** method is used. This method finds the device signature that most closely matches as many of the relevant sub strings as possible. For the majority of genuine UAs it will always return a result, although the difference value may be very high.

Consider the following User-Agent where the Chrome version has been set to 10:

**Target UserAgent** 

[`Mozilla/5.0 (Linux; Android 4.3; Nexus 7 Build/JSS15Q) AppleWebKit/537.36 
(KHTML, like Gecko) Chrome/10.0.1547.72 Safari/537.36`][UA4]

**Closest Sub Strings** 
```
Mozilla/5.0 (Linux; Android 4.3; Nexus 7 Build/        AppleWebKit/              
(KHTML, like Gecko) Chrome/28           Safari/537
```       
**Rank** 59047  
**Difference** 18  
**Method** Closest

The closest Chrome version in the file being used is 28. Therefore, this is the version that matches and the difference is set to 18. This approach ensures that the most appropriate detection result is returned even when the device data does not know about the most recent versions of devices.

In contrast, regular expression or tree structure based detection methods are usually unable to distinguish between the numeric differences in relevant sub strings and therefore become susceptible to accuracy problems.

A further example can be seen in this User-Agent where the u in Nexus has been changed to an i. The Nexus 7 is returned correctly but the difference score is 120. 

**Target UserAgent**

[`Mozilla/5.0 (Linux; Android 4.3; Nexis 7 Build/JSS15Q) AppleWebKit/537.36 
(KHTML, like Gecko) Chrome/29.0.1547.72 Safari/537.36`][UA5]

**Closest Sub Strings** 
```
Mozilla/5.0 (Linux; Android 4.3; Nexus 7 Build/        AppleWebKit/              
(KHTML, like Gecko) Chrome/29           Safari/537       
```
**Rank** 60057  
**Difference** 120  
**Method** Closest

The numeric difference between the [ASCII characters](http://www.asciitable.com/) i (ASCII value = 105) and u (ASCII value = 117) is 12. This is then multiplied by 10 to give the difference value of 120. The difference is multiplied by 10 as this method is the least accurate.

The difference in the character lengths of the closest sub strings and the target User-Agent is also a factor in the calculation of difference.

In our final example using a 'closest' match, the string "Nexus" has been replaced entirely by "iPhone". A Nexus device is still returned but the difference value is very high, indicating the result should be considered unreliable. 

**Target UserAgent** 

[`Mozilla/5.0 (Linux; Android 4.3; iPhone 7 Build/JSS15Q) AppleWebKit/537.36 
(KHTML, like Gecko) Chrome/29.0.1547.72 Safari/537.36`][UA6]

**Closest Sub Strings** 
```
Mozilla/5.0 (Linux; Android 4.3; Nexus 10 Build/        AppleWebKit/             
                    Chrome/29           Safari/537
```
**Rank** 60897  
**Difference** 1510  
**Method** Closest

Tree structures and regular expression solutions can not provide this level of intelligence concerning the validity of the result where unusual target User-Agents are encountered.

### None
Finally if none of the previous methods have resulted in a match, usually because the User-Agent is a random collection of characters, the None method will be returned.

**Target UserAgent** [`2sadsa%$^dfs`][UA7]  

**Relevant Sub Strings**  
**Difference** 0  
**Method** None

The default Desktop device profile will be used to populate device data when the none match method is used.

# Modes of operation
Some of our APIs , for example, Java and .NET, provide two modes of operation when working with Pattern algorithms.

**Stream mode** is very memory efficient as it only loads the necessary data file headers and locations of various entities within the file. Device detection will rely on retrieving information from the data file for most detections (If cache is used not every detection will result in reading from the data file). Using a data file with a stream provider locks the file for write access.

**Memory mode** loads the entire data file into main memory. This is faster than stream mode but requires considerably more memory and does not use cache. Device detection will retrieve data directly from memory. Once initialized there should be no further read requests for the data file.

# Summary
This page provides an overview of the pattern detection methods, the three matching methods available, and how to use the difference and match method indicators to understand the validity of the result. The source code used to implement the algorithms is licensed under the Mozilla Public License 2 and is available for inspection. As such we encourage those that would like to know more about pattern detection to explore the source code and comments contained within it.

51Degrees also provide a @Hash device detection method which is much faster than Pattern at the expense of being less able to cope with non-exact matches.

[UA1]: https://51degrees.com/Products/DeviceData/UserAgentTester/tabid/316/Products/DeviceData/UserAgentTester/Default.aspx?useragent=Mozilla%2f5.0+(Linux%3b+Android+4.3%3b+Nexus+7+Build%2fJSS15Q)+AppleWebKit%2f537.36+(KHTML%2c+like+Gecko)+Chrome%2f29.0.1547.72+Safari%2f537.36
[UA2]: https://51degrees.com/Products/DeviceData/UserAgentTester/tabid/316Products/DeviceData/UserAgentTester//TabId/316/Products/DeviceData/Default.aspx?UserAgentTester=Default.aspx&useragent=Mozilla%2f5.0+(Linux%3b+Android+4.3%3b+Nexus+7+Build%2fJSS15Q)+AppleWebKit%2f537.36+(Opera+Mobi%2c+Gecko)+Chrome%2f29.0.1547.72+Safari%2f537.36
[UA3]: https://51degrees.com/Products/DeviceData/UserAgentTester/TabId/316/Products/DeviceData/Default.aspx?UserAgentTester=Default.aspx&useragent=Mozilla%2f5.0+(Linux%3b+Android+4.3%3b+Nexus+7+Build%2fJSS15Q)+AppleWebKit%2f537.36+(KHTML%2c+like+Gecko)+Chrome%2f29.0.1547.72X+Safari%2f537.36
[UA4]: https://51degrees.com/Products/DeviceData/UserAgentTester/tabid/316Products/DeviceData/UserAgentTester//TabId/316/Products/DeviceData/Default.aspx?UserAgentTester=Default.aspx&useragent=Mozilla%2f5.0+(Linux%3b+Android+4.3%3b+Nexus+7+Build%2fJSS15Q)+AppleWebKit%2f537.36+(KHTML%2c+like+Gecko)+Chrome%2f51.0.1547.72+Safari%2f537.36
[UA5]: https://51degrees.com/Products/DeviceData/UserAgentTester/tabid/316Products/DeviceData/UserAgentTester/TabId/316/Products/DeviceData/Default.aspx?UserAgentTester=Default.aspx&useragent=Mozilla%2f5.0+(Linux%3b+Android+4.3%3b+Nexis+7+Build%2fJSS15Q)+AppleWebKit%2f537.36+(KHTML%2c+like+Gecko)+Chrome%2f29.0.1547.72+Safari%2f537.36
[UA6]: https://51degrees.com/Products/DeviceData/UserAgentTester/tabid/316Products/DeviceData/UserAgentTester/TabId/316/Products/DeviceData/Default.aspx?UserAgentTester=Default.aspx&useragent=Mozilla%2f5.0+(Linux%3b+Android+4.3%3b+iPhone+7+Build%2fJSS15Q)+AppleWebKit%2f537.36+(KHTML%2c+like+Gecko)+Chrome%2f29.0.1547.72+Safari%2f537.36
[UA7]: https://51degrees.com/Products/DeviceData/UserAgentTester/tabid/316Products/DeviceData/UserAgentTester/TabId/316/Products/DeviceData/Default.aspx?UserAgentTester=Default.aspx&useragent=2sadsa%$^dfs