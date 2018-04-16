# Nokē Core API Documentation

## Overview

The Nokē Core API is a quick and simple way to integrate Nokē products with your existing software systems. This documentation covers the initial closed beta release. 

##  Locks

###  ``` POST /lock/ ```


Used to view information about locks. Supports finding a single lock, multiple locks, or all locks associated with a company.


#### HEADERS


|  Key | Value  |
|--|--|
|Content-Type | application/json  |
|Authorization | Bearer *api_key* |


#### BODY

|Parameter|Description|
|--|--|
|```macs``` | An array of mac addresses from the lock(s)|


##### Single Lock

```json
{
    "macs": [
		"XX:XX:XX:XX:XX"
	]
}
```




##### Multiple Locks

```json
{ 
    "macs": [
        "XX:XX:XX:XX:XX", 
        "XX:XX:XX:XX:XY"
	]
}
```
##### All Locks

```json
{ 
	//empty body
}
```

#### EXAMPLE RESPONSE

```json
{
    "result": "success",
    "message": "Command successfully completed",
    "error_code": 0,
    "data": {
        "locks": [
            {
                "mac": "XX:XX:XX:XX:XX:XX",
                "created_by": "J Doe",
                "created_date": "2018-04-04T16:00:00Z",
                "setup_count": 0,
                "hw_version": "3p",
                "fw_version": "2.10",
                "serial": "XXX-XXX-XXXX",
                "internal_battery": 0,
                "external_battery": 0,
                "status": "ready",
                "flags": "none"
            }
        ]
    },
    "request": "lock"
}
```

|Parameter|Description|
|--|--|
|```result``` | String value representing the result of the call. Either ```success``` or ```failure```|
|```message``` | Readable description of the error|
|```error_code``` | Int value of the error thrown|
|```locks``` | Array of locks contain information about all the requested locks|
|```mac``` | Mac address of the lock|
|```created_by``` | Name of the user who registered the lock to the company|
|```created_date``` | Date that the lock was registered to the company|
|```setup_count``` | Number of times the lock has been set up. Used for tracking logs|
|```hw_version``` | Hardware version of the lock|
|```fw_version``` | Current firmware version of the lock|
|```serial``` | Serial number of the lock.  This can also be found laser-etched onto the lock|
|```internal_battery``` | Value of the internal battery of the lock in millivolts|
|```external_battery``` | Value of the external battery of the lock in millivolts|
|```status``` | Status of the lock. Indicates if the lock is ready to be used or awaiting setup|
|```flags``` | An array of flags that indicate if any data needs to be synced to the lock (ie: quick-clicks, keys, etc). Syncing is handled automatically on the unlock endpoint|
|```request``` | Name of the request|


###  ``` POST  /unlock/ ```


Used to unlock a lock. Requires a session string from the lock (*see [Nokē Mobile library documentation](https://github.com/noke-inc/noke-mobile-library-ios#nok%C4%93-mobile-library-for-ios)), a mac address, and can optionally take a tracking key to associate to lock activity. 


#### HEADERS


|  Key | Value  |
|--|--|
|Content-Type | application/json  |
|Authorization | Bearer *api_key* |


#### BODY

|Parameter|Description|
|--|--|
|```mac``` | The mac address of the lock|
|```session```| Unique session generated by the lock and read by the phone when connecting. (*see [Nokē Mobile library documentation](https://github.com/noke-inc/noke-mobile-library-ios#nok%C4%93-mobile-library-for-ios))|
|```tracking_key``` | An optional string used to associate to lock activity|

```json
{
    "mac": "XX:XX:XX:XX:XX:XX",
    "session": "8675309j3nNy1V3G0ty0uRnUMB3r",
    "tracking_key": "bob@gmail.com"
}
```
#### EXAMPLE RESPONSE

```json
{
    "result": "success",
    "message": "Command successfully completed",
    "error_code": 0,
    "data": {
        "commands": "COMMAND_STRING_HERE"
    },
    "token": "TOKEN_HERE",
    "request": "unlock"
}
```

|Parameter|Description|
|--|--|
|```result``` | String value representing the result of the call. Either ```success``` or ```failure```|
|```message``` | Readable description of the error|
|```error_code``` | Int value of the error thrown|
|```commands``` | A string of commands sent to the lock by the [Nokē Mobile library](https://github.com/noke-inc/noke-mobile-library-ios)|
|```request``` | Name of the request|




##  Activity

###  ``` POST  /upload/ ``` 


Used to upload lock activity logs from a phone app using the  *Nokē Mobile library*. Requires a session string from the lock (see [Nokē Mobile library documentation](https://github.com/noke-inc/noke-mobile-library-ios#nok%C4%93-mobile-library-for-ios)), a mac address, and can optionally take a tracking key to associate to lock activity. 


**NOTE:** this route uses a mobile api key rather than the server api key.


#### HEADERS


|  Key | Value  |
|--|--|
|Content-Type | application/json  |
|Authorization | Bearer *mobile_api_key* |


#### BODY

```json
{
    "logs": [
        {
            "session": "8675309j3nNy1V3G0ty0uRnUMB3r",
            "mac": "XX:XX:XX:XX:XX:XX",
            "received_time": 1518141482,
            "responses": ["8675f1v3tHr3309867", "530931gHTs1x75309"]
        }
	]
}
```


###  ``` POST  /activity/ ``` 


Used to view human-readable activity logs. Can find specific activity logs via filters or can be used to find the most recent activity for a company. Use as many or as few filters to narrow down specific activity data.


#### HEADERS


|  Key | Value  |
|--|--|
|Content-Type | application/json  |
|Authorization | Bearer *api_key* |


#### BODY


###### All activity

```json
{
	//empty body
}
```

###### Filtered

```json
{
    "lock_macs": [
        "XX:XX:XX:XX:XX:XX", 
        "XX:XX:XX:XX:XX:XY"
    ],
    "actions": [
        "unlock", 
        "alarm_triggered"
    ],
    "tracking_keys": [
        "bill@gmail.com", 
        "ted@gmail.com"
    ]
}
```

#### EXAMPLE RESPONSE

```json
{
    "result": "success",
    "message": "Command successfully completed",
    "error_code": 0,
    "data": {
        "activity": [
            {
                "lock_mac": "XX:XX:XX:XX:XX:XX",
                "action": "unlocked",
                "recorded_time": "2018-03-15T17:26:41Z",
                "range_date_start": "2018-03-15T17:20:43Z",
                "range_date_end": "2018-03-15T17:20:43Z",
                "tracking_key": ""
            },
            {
                "lock_mac": "XX:XX:XX:XX:XX:XX",
                "action": "locked",
                "recorded_time": "2018-03-16T13:36:37Z",
                "range_date_start": "2018-02-14T17:21:09Z",
                "range_date_end": "2018-03-16T13:36:27Z",
                "tracking_key": ""
            }
        ]
    },
    "request": "activity"
}
```

|Parameter|Description|
|--|--|
|```result``` | String value representing the result of the call. Either ```success``` or ```failure```|
|```message``` | Readable description of the error|
|```error_code``` | Int value of the error thrown|
|```activity``` | Array of activity logs|
|```lock_mac``` | Mac address of the lock|
|```action``` | Action type of the log (ie: unlocked, locked)|
|```recorded_time``` | Date that the log was received by the mobile device|
|```range_date_start``` | If the exact time is not known (offline unlock or quick-click) this is the known earliest date the event could have occured|
|```range_date_end``` | If the exact time is not known (offline unlock or quick-click) this is the known latest date the event could have occured|
|```tracking_key``` | A string sent up on the unlock endpoint that can be used to associate activity|
|```request``` | Name of the request|