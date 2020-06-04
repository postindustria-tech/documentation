@page DeviceDetection_Features_TacLookup TAC Lookup

# Introduction

A TAC or [Type Allocation Code](https://en.wikipedia.org/wiki/Type_Allocation_Code) is an 8 
digit numeric code that is used to identify mobile device models.
It is part of the [IMEI](https://en.wikipedia.org/wiki/International_Mobile_Equipment_Identity) 
number and is generally only available to code running with sufficient privileges on the 
user's device.

TAC lookup is currently available in two forms:
1. Requests to the 51Degrees cloud service, fully integrated into the Pipeline API.
2. Using the 'TAC CSV' data file to create a local lookup solution based on SQL or some 
other data query mechanism.

# Cloud service

Requests to the cloud service can include a 'tac' query parameter. As long as the [resource key](@term{ResourceKey}) 
includes one or more 'hardware device' properties, the cloud service will return a result set with 
the details of any devices that match the supplied TAC.

For language specific details, see the [example](@ref Examples_DeviceDetection_TacLookup_Cloud). 

# Local lookup

Currently, the recommended solution for a local lookup is to use the TAC CSV file.
This is a data file that is available from the 51Degrees [distributor](@term{Distributor}) service (assuming you 
have a valid license key that includes access to this product). 

This data file includes all values for all properties and can be used to populate a database or 
similar. The database can then be used to perform indexed lookups on given property values, 
such as TAC.