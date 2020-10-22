---
author: baanders
description: inkludera fil för Azure Digitals dubblare-självstudier – konfigurera ett exempel projekt
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: ff44e4ae45f8b7c6bff3685522440ca7118f337e
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92346943"
---
## <a name="configure-the-sample-project"></a>Konfigurera exempelprojektet

Konfigurera sedan ett exempel klient program som ska interagera med din Azure Digital-instansen.

Navigera på datorn till filen som du laddade ned tidigare från [*Azure Digitals dubbla delar från slut punkt till slut punkt*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (och zippa upp den om du inte redan har gjort det).

När du är i mappen navigerar du till _AdtSampleApp_. Öppna _**AdtE2ESample. SLN**_ i Visual Studio 2019. 

I Visual Studio väljer du den _SampleClientApp-> **appsettings.jspå** _ filen för att öppna den i redigerings fönstret. Detta fungerar som en för hands uppsättnings-JSON-fil med nödvändiga konfigurationsfiler för att köra projektet.

I fil texten ändrar du `instanceUrl` till din Azure Digital-instansen med *hostname* -URL (med *https://* framför den som visas nedan).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Spara och stäng filen. 

Konfigurera sedan *appsettings.jspå* filen som ska kopieras till utdatakatalogen när du skapar *SampleClientApp*. Det gör du genom att högerklicka på *appsettings.jspå* filen och välja *Egenskaper.* I *egenskaps* panelen ändrar du värdet för egenskapen *Kopiera till utdata-katalog* som ska *kopieras om senare*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Utdrag från Visual Studio-fönstret som visar Solution Explorer fönstret med appsettings.jsmarkerat och egenskaps fönstret med egenskapen kopiera till utgående katalog inställt på Kopiera om nyare" border="false":::

Se till att _**AdtE2ESample**_ -projektet är öppet i Visual Studio och fortsätt att använda det i självstudien.

