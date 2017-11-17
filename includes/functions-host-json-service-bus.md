```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|maxConcurrentCalls|16|Maximalt antal samtidiga anrop till vilket motanrop som meddelandet pump ska starta. Som standard bearbetar Functions-runtime flera meddelanden samtidigt. För att dirigera runtime att bearbeta en enskild kö eller ett ämne meddelande i taget ange `maxConcurrentCalls` till 1. | 
|prefetchCount|Saknas|Standard PrefetchCount som ska användas av den underliggande MessageReceiver.| 
|autoRenewTimeout|00:05:00|Maximal varaktighet inom vilken meddelandet låset förnyas automatiskt.| 
