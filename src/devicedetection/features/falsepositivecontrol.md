@page DeviceDetection_Features_FalsePositiveControl False Positives

# Introduction

Most device detection solutions rely primarily on the User-Agent HTTP header. However, the content of this header can be any string at all. This inevitably leads to situations where finding an exact match to a previously observed User-Agent is not possible. 

A false positive occurs when the service tells you it has found a match but in reality the details provided are incorrect.

In these scenarios, different use-cases often demand different responses. 51Degrees Device Detection allows the developer to determine exactly what happens.

# HasValue

The Device Detection API returns many different properties. Every single one of these can either have a specified value or not.

Each language implements this differently but in all cases, there will be some form of `HasValue` method or property associated with each result property. Where `HasValue` is false, accessing the property value will cause an exception to be returned or thrown (depending on the language). 

The detail associated with this exception will explain why the property does not have a specified value.

# No Match Found

Both @Pattern and @Hash algorithms can theoretically fail to find any match at all for the supplied evidence. (In practice, this is far more likely to occur with @Hash than @Pattern due to the flexibility of @Pattern in finding matches that are not the same but similar in some way)

The `AllowUnmatched` setting can be used to control what happens in this situation.  
If `AllowUnmatched` is false (default) then no result will be returned and `HasValue` will be false.  
If `AllowUnmatched` is true then the default @profile will be returned.

The default @profile has specific values for properties but they may be incorrect. For example, the default hardware profile has `IsMobile = false`. In reality, if no match was found then we have no idea if the device is a mobile device or not.

# Bad Match

The @Pattern and @Hash algorithms have slightly different methods for controlling and measuring how different a potential match is from the supplied evidence.

In both cases, The `Difference` value is used to measure how different a potential match is from the supplied evidence.  
For example, if a substring within the User-Agent is present as expected but the ASCII value on a character is 1 higher than expected then the difference will be 1 (for @Pattern. The calculation of difference for @Hash is more complex).  
For @Pattern, the difference can also be affected by shifting the position of the expected sub-string within the User-Agent. @Hash uses a separate value for this called `Drift`.

The maximum `Difference` (and `Drift` for @Hash) value can be set when the engine is created. This is used to control how far a potential match is allowed to deviate from the supplied evidence.  
For example, if the maximum `Difference` value is set to 10 (default) then a match will only be returned if the difference value is 10 or less. 

If the maximum `Difference` or `Drift` setting prevents any result from being returned then `HasValue` will be false.

By default, `Difference` is set to 10 for @Pattern and both `Difference` and `Drift` are set to 0 for @Hash. (I.e. no deviation from the expected sub strings is permitted). Setting them to -1 will mean that there is no limit. (although this does not guarantee a match will always be found)

# Match Meta-Data

Regardless of the settings used, the result will return additional meta-data that can be queried to find out details about the match.

`Difference` - Contains the difference value between the supplied evidence and the returned match.  
`UserAgents` - Contains a list of the matching substrings from the User-Agent.  
`Drift` - (@Hash only) - Contains the drift value between the supplied evidence and the returned match.  
`MatchedNodes` - (@Hash only) - The number of 'nodes' in the @hash tree where a match was found. If this is zero then no match was found.  
`Method` - (@Pattern only) - The method used to find a match. 'EXACT' indicates an exact match. 'NONE' indicates no match. Any other value is the name of the technique used to find the result that best matches the supplied evidence.

# The 'Unknown' Value

Previous versions of the device detection API were unable to indicate that a property did not have a value.  
Therefore, in the past, the string values `Unknown` or `N/A` were used to indicate that the API did not have a value for a particular property.

These values are treated as distinct from `HasValue`. I.e. It is possible for `HasValue` to return `true` and `Value` to return `Unknown`.  
In the future, this may be changed so that `HasValue` will return false if `Value` is `Unknown`, `N/A` or similar but this is not currently planned.

# Use-Cases

> I only want a result if the API is reasonably sure it is correct. (Default configuration for @Pattern)

Set `AllowUnmatched` to false.  
Set `Difference` (and `Drift` if using Hash) to 10.

> I only want exact matches to previously observed User-Agents. (Default configuration for @Hash)

Set `AllowUnmatched` to false.  
Set `Difference` (and `Drift` if using @Hash) to 0.  
These settings will mean that a result is only returned if it exactly matches a unique sub-string from a User-Agent that has been included when building the data file.

> If there is no match found then I want the API to assume that the device is a desktop running an unknown operating system.

Set `AllowUnmatched` to true.  
`Difference` and `Drift` do not influence this use-case.  
This setting will cause the default (desktop) profiles to be returned if there is no match.

> I'm migrating from 51Degrees V3 device detection and want to retain the previous functionality.

OR

> I want to keep my as code simple as possible. I'm not worried about some false positives.

Set `AllowUnmatched` to true.
Set `Difference` (and `Drift` if using @Hash) to -1.   
These settings will mean that `HasValue` is never false. Each property will always have a value so you don't need to worry about dealing with a situation where they do not. 



