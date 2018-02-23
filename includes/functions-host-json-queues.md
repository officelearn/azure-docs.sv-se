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
|batchSize|16|Antal meddelanden i kö som Functions-runtime samtidigt hämtar och bearbetar parallellt. När antalet bearbetas hämtar ned till den `newBatchThreshold`, körningsmiljön hämtar en annan batch och påbörjar bearbetningen av dessa meddelanden. Så är det maximala antalet samtidiga meddelanden som bearbetas per funktion `batchSize` plus `newBatchThreshold`. Den här gränsen gäller separat för varje funktion som utlöses av kön. <br><br>Om du vill undvika parallell körning för meddelanden som tas emot i en kö kan du ange `batchSize` till 1. Den här inställningen eliminerar dock samtidighet endast så länge funktionen appen körs på en enskild virtuell dator (VM). Om funktionsapp skalas ut till flera virtuella datorer, kan varje virtuell dator kör en instans av varje funktion som utlöses av kön.<br><br>Maximalt `batchSize` är 32. | 
|maxDequeueCount|5|Antal gånger för bearbetning av ett meddelande innan du flyttar till skadligt kön.| 
|newBatchThreshold|batchSize/2|När antalet meddelanden som bearbetas samtidigt hämtar till det här numret, hämtar en annan batch i körningsmiljön.| 



