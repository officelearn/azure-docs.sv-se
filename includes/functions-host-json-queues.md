```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|maxPollingInterval|60000|Maximalt intervall i millisekunder mellan kön avsöker.| 
|visibilityTimeout|0|Det går inte att tidsintervallet mellan nya försök vid bearbetning av ett meddelande.| 
|batchSize|16|Antal meddelanden i kö att hämta och bearbeta parallellt. Det högsta antalet är 32.| 
|maxDequeueCount|5|Antal gånger för bearbetning av ett meddelande innan du flyttar till skadligt kön.| 
|newBatchThreshold|batchSize-2|Tröskelvärdet som en ny grupp med meddelanden hämtas.| 
