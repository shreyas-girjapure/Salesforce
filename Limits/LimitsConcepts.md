# Limits 

## What are some well known limits in apex ?

1. SOQL Queries
    1. Synchronous - 100
    1. Asynchronous - 200
1. SOQL Rows
    1. Synchronous - 50,000
    1. Asynchronous - 50,000
1. SOSL Queries
    1. Synchronous - 20
    1. Asynchronous - 20
1. SOSL Rows
    1. Synchronous - 2000
    1. Asynchronous - 2000
1. DML Statements
    1. Synchronous - 150
    1. Asynchronous - 150
1. DML Rows
    1. Synchronous - 10,000
    1. Asynchronous - 10,000
1. Stack Deapth
    1. Both - 16
1. Heap
    1. Synchronous - 6 MB
    1. Asynchronous - 12 MB
1. CPU Timeout
    1. Synchronous - 10s
    1. Asynchronous - 60s
1. Future allowed
    1. Sync - 50
    1. Async - 0 in batch and future , 50 in queueable 
1. Max jobs enqueue with `System.enqueue` job
    1. Sync - 50
    1. Async - 1
1. Max time for single transaction
    1. Both - 10 min
1. Send Emails Methods
    1. Both - 10 
1. Callouts
    1. Synchronous - 
    1. Asynchronous -
1. Inbound Calls to Org
    1. Synchronous - 
    1. Asynchronous -
1. Emails Per day
    1. Synchronous - 
    1. Asynchronous -
1. Bulk API Limits
    1. Synchronous - 
    1. Asynchronous -
1. Platform Events publish immediately 
    1. Synchronous - 150
    1. Asynchronous - 150

## What are lightning Platform Limits
1. The maximum number of asynchronous Apex method executions (batch Apex, future methods, Queueable Apex, and scheduled Apex) per a 24-hour period
    1. 250,000 or the number of user licenses in your org multiplied by 200, whichever is greater
1. Number of synchronous concurrent transactions for long-running transactions that last longer than 5 seconds for each org
    1. 10
1. Maximum number of Apex classes scheduled concurrently
    1. 100. In Developer Edition orgs, the limit is 5.
1. Maximum number of batch Apex jobs in the Apex flex queue that are in Holding status	
    1. 100
1. Maximum number of batch Apex jobs queued or active concurrently
    1. 5
1. Maximum number of batch Apex job start method concurrent executions
    1. 1

## Static Apex Limits
1. Default timeout of callouts (HTTP requests or Web services calls) in a transaction
    1. 10 seconds
1. Maximum size of callout request or response (HTTP request or Web services call)
    1. 6 MB for synchronous Apex or 12 MB for asynchronous Apex
1. Maximum SOQL query run time before Salesforce cancels the transaction
    1. 120 seconds
1. Maximum number of class and trigger code units in a deployment of Apex
    1. 7500
1. Apex trigger batch size 
    1. 200
1. For loop list batch size	
    1. 200
1. Maximum number of records returned for a Batch Apex query in Database.QueryLocator	
    1. 50 million
1. The Apex trigger batch size for platform events and Change Data Capture events
    1. 2,000


## References
1. [Apex Governer Limits](https://developer.salesforce.com/docs/atlas.en-us.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_apexgov.htm)
