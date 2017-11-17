```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|maxBatchSize|64|Den maximala händelseantal tas emot varje receive-loop.|
|prefetchCount|Saknas|Standard PrefetchCount som ska användas av den underliggande EventProcessorHost.| 
|batchCheckpointFrequency|1|Antal händelsebatchar med att bearbeta innan du skapar en kontrollpunkt för EventHub-markör.| 
