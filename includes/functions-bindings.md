I följande tabell visas de bindningar som stöds i de två viktigaste versionerna av Azure Functions-runtime.

| Typ | 1.x | 2.x | Utlösare | Indata | Resultat |  
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔<sup>1</sup>|✔|✔|✔|  
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|  
| [Händelsen rutnätet](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |  
| [Event Hubs](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|  
| [Den externa filen](../articles/azure-functions/functions-bindings-external-file.md)<sup>2</sup>    |✔|| |✔|✔|  
| [Extern tabell](../articles/azure-functions/functions-bindings-external-table.md)<sup>2</sup>  |✔|| |✔|✔|  
| [HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔<sup>1</sup>|✔| |✔|
| [Microsoft Graph<br/>Excel tabeller](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft Graph<br/>OneDrive-filer](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft Graph<br/>Outlook-e-post](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Microsoft Graph<br/>Events](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft Graph<br/>Auth-token](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Mobile Apps](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔|✔| |✔|✔|  
| [Notification Hubs](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔<sup>1</sup>|✔| |✔|  
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Service Bus](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|  
| [Table Storage](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔<sup>1</sup>| |✔|✔|  
| [Timer](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|
| [Webhooks](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔||✔| |✔|
  
<sup>1</sup> i 2.x, alla bindningar utom HTTP-Timer- och Azure Storage måste vara registrerad. Se [registrera bindning tillägg](../articles/azure-functions/functions-triggers-bindings.md#register-binding-extensions).

<sup>2</sup> Experimental &mdash; stöds inte och kan överges i framtiden.
