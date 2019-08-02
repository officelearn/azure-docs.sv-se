---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: fef5cd38461fec67790fb67faf8e466d46b247fc
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669844"
---
## <a name="local-settings-file"></a>Fil för lokala inställningar

Den lokala. Settings. JSON-filen lagrar inställningar för appar, anslutnings strängar och inställningar som används av lokala utvecklingsverktyg. Inställningarna i filen Local. Settings. JSON används endast när du kör projekt lokalt. Den lokala inställnings filen har den här strukturen:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

De här inställningarna stöds när du kör projekt lokalt:

| Inställning      | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | När den här inställningen är inställd `true`på krypteras alla värden med en lokal dator nyckel. Används med `func settings` -kommandon. Standardvärdet `false`är. |
| **`Values`** | Matris med program inställningar och anslutnings strängar som används när ett projekt körs lokalt. Dessa nyckel värdes par (sträng sträng) motsvarar program inställningarna i din Function-app i Azure, t. [`AzureWebJobsStorage`]ex. Många utlösare och bindningar har en egenskap som refererar till en app-inställning för `Connection` anslutnings strängen, till exempel för [Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration)-utlösaren. För dessa egenskaper behöver du en program inställning som definierats i `Values` matrisen. <br/>[`AzureWebJobsStorage`]är en obligatorisk app-inställning för andra utlösare än HTTP. <br/>Version 2. x av Functions-körningen kräver`FUNCTIONS_WORKER_RUNTIME`inställningen [], som genereras för ditt projekt med Core-verktyg. <br/> När du har installerat [Azure Storage](../articles/storage/common/storage-use-emulator.md) -emulatorn lokalt och du [`AzureWebJobsStorage`] har `UseDevelopmentStorage=true`angett, använder kärn verktygen emulatorn. Emulatorn är användbar under utvecklingen, men du bör testa med en faktisk lagrings anslutning före distributionen.<br/> Värden måste vara strängar och inte JSON-objekt eller matriser. Inställnings namn får inte innehålla kolon`:`() eller dubbel understrykning (`__`). Dessa tecken reserveras av körnings miljön.  |
| **`Host`** | Inställningarna i det här avsnittet anpassar funktionens värd process när du kör projekt lokalt. Inställningarna är separata från Host. JSON-inställningarna, som också gäller när du kör projekt i Azure. |
| **`LocalHttpPort`** | Anger standard porten som används när du kör den lokala funktionens`func host start` värd `func run`(och). `--port` Kommando rads alternativet har företräde framför den här inställningen. |
| **`CORS`** | Definierar de ursprung som tillåts för [resurs delning mellan ursprung (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Ursprung anges som en kommaavgränsad lista utan blank steg. Jokertecknet (\*) stöds, vilket tillåter begär Anden från alla ursprung. |
| **`CORSCredentials`** |  När den är `true`inställd på `withCredentials` tillåter begär Anden. |
| **`ConnectionStrings`** | En samling. Använd inte den här samlingen för de anslutnings strängar som används av funktions bindningarna. Den här samlingen används endast av ramverk som vanligt vis hämtar anslutnings strängar från `ConnectionStrings` avsnittet i en konfigurations fil, t. ex. [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Anslutnings strängar i det här objektet läggs till i miljön med providertypen för [system. data. SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Objekt i den här samlingen publiceras inte i Azure med andra AppData. Du måste uttryckligen lägga till de här värdena `Connection strings` i samlingen av dina funktions program inställningar. Om du skapar en [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) i funktions koden bör du lagra värdet för anslutnings strängen med dina andra anslutningar i **program inställningarna** i portalen. |

[`AzureWebJobsStorage`]: ./articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
