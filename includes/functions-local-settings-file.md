---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: e319356d555f26354ea29dc7be068bf6168abb17
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455153"
---
## <a name="local-settings-file"></a>Fil för lokala inställningar

Filen local.settings.json lagrar appinställningar, anslutningssträngar och inställningarna som används av verktyg för lokal utveckling. Inställningarna i filen local.settings.json används bara när du kör lokalt. Den lokala filen har följande struktur:

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

Följande inställningar stöds när du kör lokalt:

| Inställning      | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | När värdet `true`, alla värden som krypteras med hjälp av en lokal dator-nyckel. Används med `func settings` kommandon. Standardvärdet är `false`. |
| **`Values`** | Matris med programinställningar och anslutningssträngar som används när du kör lokalt. Dessa (string-string) nyckelvärdepar motsvarar programinställningar i funktionsappen i Azure, till exempel [ `AzureWebJobsStorage` ]. Många utlösare och bindningar har en egenskap som refererar till en appinställning för anslutningssträngen, till exempel `Connection` för den [Blob storage-utlösare](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration). För egenskaper, behöver du en programinställning som definierats i den `Values` matris. <br/>[`AzureWebJobsStorage`] är en obligatorisk app inställning för utlösare än HTTP. <br/>Version 2.x av funktionskörningen kräver den [`FUNCTIONS_WORKER_RUNTIME`] som genereras för ditt projekt med Core Tools. <br/> När du har den [Azure storage-emulatorn](../articles/storage/common/storage-use-emulator.md) installerat lokalt kan du ange [ `AzureWebJobsStorage` ] till `UseDevelopmentStorage=true` och Core Tools använder emulatorn. Detta är användbart under utvecklingen, men du bör testa med en faktisk lagringsanslutning före distributionen.<br/> Värden måste vara strängar och inte JSON-objekt eller matriser. Ange namn får inte innehålla kolon (`:`) eller en dubbel understrykning (`__`); de är reserverade av körningen.  |
| **`Host`** | Inställningarna i det här avsnittet Anpassa värdprocessen funktioner när du kör lokalt. Dessa skiljer sig från host.json-inställningar, som gäller även när de körs i Azure. |
| **`LocalHttpPort`** | Anger standardporten som används när du kör den lokala Functions-värden (`func host start` och `func run`). Den `--port` kommandoradsalternativet har företräde framför det här värdet. |
| **`CORS`** | Definierar de ursprung som får för [cross-origin resource sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Ursprung tillhandahålls som en kommaavgränsad lista med utan blanksteg. Jokertecknet (\*) stöds, vilket gör att begäranden från valfri origin. |
| **`CORSCredentials`** |  Ändra det till SANT för att låta `withCredentials` begäranden. |
| **`ConnectionStrings`** | Använd inte den här samlingen för anslutningssträngar som används av din funktionsbindning. Den här samlingen används endast av ramverk som kommer vanligtvis anslutningssträngar från den `ConnectionStrings` avsnitt i en konfiguration av fil, som [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Anslutningssträngar i det här objektet läggs till i miljön med providertyp av [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Objekt i den här samlingen inte har publicerats till Azure med andra appinställningar. Du måste uttryckligen lägga till dessa värden till den `Connection strings` insamling av din funktionsappinställningarna. Om du skapar en [ `SqlConnection` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) i Funktionskoden, bör du lagra Anslutningssträngens värde i **programinställningar** i portalen med dina andra anslutningar. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
