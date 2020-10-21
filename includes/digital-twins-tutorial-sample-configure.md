---
author: baanders
description: inkludera fil för Azure Digitals dubblare-självstudier – konfigurera ett exempel projekt
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: d442c5782af1f55cd5d894823945d517dbc48091
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92322850"
---
## <a name="configure-the-sample-project"></a>Konfigurera exempelprojektet

Konfigurera sedan ett exempel klient program som ska interagera med din Azure Digital-instansen.

Navigera på datorn till filen som du laddade ned tidigare från [*Azure Digitals dubbla delar från slut punkt till slut punkt*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (och zippa upp den om du inte redan har gjort det).

När du är i mappen navigerar du till _AdtSampleApp_. Öppna _**AdtE2ESample. SLN**_ i Visual Studio 2019. 

I Visual Studio använder du fönstret *Solution Explorer* för att skapa en kopia av den _SampleClientApp > **serviceConfig.js. MALLFIL** _ (du kan använda de högra menyerna för att kopiera och klistra in). Byt namn på kopian *serviceConfig.jspå*. Detta fungerar som en för hands uppsättnings-JSON-fil med nödvändiga konfigurationsfiler för att köra projektet.

Välj *serviceConfig.jspå* filen för att öppna den i redigerings fönstret. Ändra `instanceUrl` till din Azure Digital-instansen med *värdnamn* -URL (med *https://* framför den som visas nedan).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```



Spara och stäng filen. 

Konfigurera sedan *serviceConfig.jspå* filen som ska kopieras till utdatakatalogen när du skapar *SampleClientApp*. Det gör du genom att högerklicka på *serviceConfig.jspå* filen och välja *Egenskaper.* I *egenskaps* panelen ändrar du värdet för egenskapen *Kopiera till utdata-katalog* som ska *kopieras om senare*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Utdrag från Visual Studio-fönstret som visar Solution Explorer fönstret med serviceConfig.jsmarkerat och egenskaps fönstret med egenskapen kopiera till utgående katalog inställt på Kopiera om nyare" border="false":::

Se till att _**AdtE2ESample**_ -projektet är öppet i Visual Studio och fortsätt att använda det i självstudien.

