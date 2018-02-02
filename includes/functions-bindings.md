I följande tabell visas de bindningar som stöds i de två viktigaste versionerna av Azure Functions-runtime.

| Typ | 1.x | 2.x | Utlösare | Indata | Resultat |  
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|  
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔<sup>1</sup>|✔|✔|✔|  
| [Händelsen rutnätet](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |  
| [Event Hubs](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|  
| [Den externa filen](../articles/azure-functions/functions-bindings-external-file.md)<sup>2</sup>    |✔|| |✔|✔|  
| [Extern tabell](../articles/azure-functions/functions-bindings-external-table.md)<sup>2</sup>  |✔|| |✔|✔|  
| [HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Microsoft Graph<br/>Excel tabeller](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔<sup>1</sup>| |✔|✔|
| [Microsoft Graph<br/>OneDrive-filer](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔<sup>1</sup>| |✔|✔|
| [Microsoft Graph<br/>Outlook-e-post](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔<sup>1</sup>| | |✔|
| [Microsoft Graph<br/>Events](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔<sup>1</sup>|✔|✔|✔|
| [Microsoft Graph<br/>Auth-token](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔<sup>1</sup>| |✔| |
| [Mobile Apps](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔|✔<sup>1</sup>| |✔|✔|  
| [Notification Hubs](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|  
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔<sup>1</sup>| | |✔|
| [Service Bus](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔<sup>1</sup>|✔| |✔|  
| [Table Storage](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|  
| [Timer](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔<sup>1</sup>| | |✔|
| [Webhooks](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔||✔| |✔|
  
<sup>1</sup> måste registreras som en bindningstillägg i 2.x. Se [kända problem i 2.x](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

<sup>2</sup> Experimental &mdash; stöds inte och kan överges i framtiden.

