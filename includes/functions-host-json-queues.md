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

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|maxPollingInterval|60000|Maximalt intervall i millisekunder mellan kön avsöker.| 
|visibilityTimeout|0|Det går inte att tidsintervall mellan försök vid bearbetning av ett meddelande.| 
|batchSize|16|Antal Kömeddelanden som Functions-körning hämtar samtidigt och bearbetar parallellt. När antalet bearbetas kommer ned till den `newBatchThreshold`, körningen får en annan batch och påbörjar bearbetningen av dessa meddelanden. Så det maximala antalet samtidiga meddelanden som bearbetas per funktion är `batchSize` plus `newBatchThreshold`. Den här gränsen gäller separat för varje funktion som utlöses av kön. <br><br>Om du vill undvika parallell körning för meddelanden som tas emot i en kö kan du ange `batchSize` till 1. Den här inställningen eliminerar dock samtidighet bara så länge som din funktionsapp körs på en enskild virtuell dator (VM). Om funktionsappen skalas ut till flera virtuella datorer, kan varje virtuell dator kör en instans av varje funktion som utlöses av kön.<br><br>Maximalt `batchSize` är 32. | 
|maxDequeueCount|5|Antal gånger att försöka bearbetar ett meddelande innan du flyttar den till skadliga kön.| 
|newBatchThreshold|batchSize/2|När antalet meddelanden som bearbetades samtidigt hamnar till det här talet, hämtar körningen en annan batch.| 



