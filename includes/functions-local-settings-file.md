---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205734"
---
## <a name="local-settings-file"></a>Fil för lokala inställningar

Filen local.settings.json lagrar appinställningar, anslutningssträngar och inställningar som används av lokala utvecklingsverktyg. Inställningar i filen local.settings.json används bara när du kör projekt lokalt. Den lokala inställningsfilen har den här strukturen:

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
| **`IsEncrypted`** | När den här `true`inställningen är inställd på krypteras alla värden med en lokal datornyckel. Används `func settings` med kommandon. Standardvärdet `false`är . |
| **`Values`** | Matris med programinställningar och anslutningssträngar som används när ett projekt körs lokalt. Dessa nyckelvärde (string-string) par motsvarar programinställningar i din funktionsapp i Azure, till exempel [`AzureWebJobsStorage`]. Många utlösare och bindningar har en egenskap som refererar `Connection` till en anslutningssträngappinställning, till exempel för [Blob-lagringsutlösaren](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). För dessa egenskaper behöver du en `Values` programinställning som definierats i matrisen. <br/>[`AzureWebJobsStorage`]är en obligatorisk appinställning för andra utlösare än HTTP. <br/>Version 2.x och högre av funktionskörningen kräver inställningen [`FUNCTIONS_WORKER_RUNTIME`] som genereras för projektet av Core Tools. <br/> När du har [Azure-lagringsemulatorn](../articles/storage/common/storage-use-emulator.md) installerad `UseDevelopmentStorage=true`lokalt och du ställer in på [`AzureWebJobsStorage`] använder Core Tools emulatorn. Emulatorn är användbar under utvecklingen, men du bör testa med en faktisk lagringsanslutning före distribution.<br/> Värden måste vara strängar och inte JSON-objekt eller matriser. Det går inte att ange`:`namn som innehåller`__`ett kolon ( ) eller en dubbel understrykning ( ). Dessa tecken är reserverade av körningen.  |
| **`Host`** | Inställningarna i det här avsnittet anpassar värdprocessen för funktioner när du kör projekt lokalt. Dessa inställningar är separata från host.json-inställningarna, som också gäller när du kör projekt i Azure. |
| **`LocalHttpPort`** | Anger den standardport som används när`func host start` `func run`den lokala functions-värden (och ). Kommandoradsalternativet `--port` har företräde framför den här inställningen. |
| **`CORS`** | Definierar ursprunget för [resursdelning mellan ursprung (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Ursprung levereras som en kommaavgränsad lista utan mellanslag. Jokertecknets värde\*( ) stöds, vilket tillåter begäranden från alla ursprung. |
| **`CORSCredentials`** |  När den `true`är `withCredentials` inställd på , tillåter begäranden. |
| **`ConnectionStrings`** | En samling. Använd inte den här samlingen för de anslutningssträngar som används av funktionsbindningarna. Den här samlingen används endast av ramverk som `ConnectionStrings` vanligtvis hämtar anslutningssträngar från avsnittet i en konfigurationsfil, till exempel [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Anslutningssträngar i det här objektet läggs till i miljön med providertypen [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Objekt i den här samlingen publiceras inte i Azure med andra appinställningar. Du måste uttryckligen lägga `Connection strings` till dessa värden i samlingen av funktionsappinställningarna. Om du skapar [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) en i funktionskoden bör du lagra anslutningssträngvärdet med dina andra anslutningar i **Programinställningar** i portalen. |

["AzureWebJobsStorage"]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
