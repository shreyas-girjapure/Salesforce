## Batch Apex Concepts

## Write a batch for adding "-Test" in all the account names in the org.
```JAVA

public class SampleBatchClass implements Database.Batchable<SObject>,Database.Stateful {
    public integer processedRecords;

    public Database.Querylocator start (Database.BatchableContext BC){
        return [Select id , name from Account];
    }

    public void execute(Database.BatchableContext BC,List<Account> scope){
        for(Account acc : scope){
            acc.name += '-test';
            processedRecords++;
        }
        update scope;
    }
    public void finish(Database.BatchableContext BC){

    } 
}

String batchId = Database.executeBatch(new SampleBatchClass());

AsyncApexJob apexJobOfBatch = [SELECT Id, Status, JobItemsProcessed, TotalJobItems, NumberOfErrors 
    FROM AsyncApexJob WHERE ID =: batchId ];

```
## What are the limits of Batch Class ?
1. Up to 5 batch jobs can be queued or active concurrently.
1. Up to 100 Holding batch jobs can be held in the Apex flex queue.
1. The maximum number of batch Apex method executions per `24-hour` period is `250,000`, or the number of user licenses in your org multiplied by `200`—whichever is greater. Method executions include executions of the start, execute, and finish methods. This limit is for your entire org and is shared with `all asynchronous Apex`.
1. A maximum of 50 million records can be returned in the Database.QueryLocator object. If more than 50 million records are returned, the batch job is immediately terminated and marked as Failed.
1. If no size is specified with the optional scope parameter of Database.executeBatch, Salesforce chunks the records returned by the start method into batches of 200 records. The system then passes each batch to the execute method. Apex governor limits are reset for each execution of execute.
1. The start, execute, and finish methods can implement up to 100 callouts each.
1. Using FOR UPDATE in SOQL queries to lock records during update isn’t applicable to Batch Apex.

## Where can we monitor submitted batches ?
To monitor or stop the execution of the batch Apex job, from Setup, enter Apex Jobs in the Quick Find box, then select Apex Jobs.
## How many batch jobs can we queue or submit ? 
## How many batch jobs can we active at a time ?
## Can we write apex logic in start method ?
## How many records can we collect in start method's scope ?


## References
1. [Interview Questions for Batch Apex](https://medium.com/elevate-salesforce/interview-series-apex-batches-88c559ea75bd)
1. [Batch Apex Dev Guide](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_batch.htm)
1. [Async in force.com](https://resources.docs.salesforce.com/194/latest/en-us/sfdc/pdf/salesforce_async_processing.pdf?_ga=2.175255392.35875962.1706932478-2089452086.1662997945)