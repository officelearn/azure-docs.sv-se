```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|maxConcurrentCalls|16|Det maximala antalet samtidiga anrop till återanrop som meddelandet pump ska starta. Som standard bearbetar funktionskörningen flera meddelanden samtidigt. För att dirigera körning för att bearbeta en enskild kö eller ett ämne meddelande i taget, ange `maxConcurrentCalls` till 1. | 
|prefetchCount|Saknas|Standard PrefetchCount som ska användas av den underliggande MessageReceiver.| 
|autoRenewTimeout|00:05:00|Maximal varaktighet inom vilken meddelandelåset förnyas automatiskt.| 
