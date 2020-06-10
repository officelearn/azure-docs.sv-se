---
author: baanders
description: inkludera fil för Azure Digitals dubblare-självstudier – konfigurera ett exempel projekt
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: f7b3bf347b15ebad2403f3aa64c0f3fd6c4be052
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613538"
---
## <a name="configure-the-sample-project"></a>Konfigurera exempelprojektet

Konfigurera sedan ett exempel klient program som ska interagera med din Azure Digital-instansen. Om du inte redan har laddat ned exempelprojektet kan du hämta det nu genom [att ladda ned Azure Digitals-lagringsplatsen med exempel lagrings plats som en zip-fil](https://github.com/Azure-Samples/digital-twins-samples/archive/master.zip). 

Navigera till den nedladdade filen på din dator och packa upp den.

När du är i den zippade mappen, navigerar du till digital-endrives _-samples-Master/AdtSampleApp/_. Öppna _**AdtE2ESample. SLN**_ i Visual Studio 2019. 

I Visual Studio använder du fönstret *Solution Explorer* för att skapa en kopia av filen _SampleClientApp > **SERVICECONFIG. JSON. template** _ (du kan använda de högra menyerna för att kopiera och klistra in). Byt namn på Copy *serviceConfig. JSON*. Detta fungerar som en för hands uppsättnings-JSON-fil med nödvändiga konfigurationsfiler för att köra projektet.

Välj den nya filen för att öppna den i redigerings fönstret. Ändra `tenantId` till ditt *katalog-ID*, `clientId` till ditt *program-ID*och `instanceUrl` till din Azure Digital-instansen *värdnamn* -URL (med *https://* framför den som visas nedan).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Spara och stäng filen. 

Konfigurera sedan *serviceConfig. JSON* -filen som ska kopieras till utdatakatalogen när du skapar *SampleClientApp*. Det gör du genom att högerklicka på filen *serviceConfig. JSON* och välja *Egenskaper.* I *egenskaps* panelen ändrar du värdet för egenskapen *Kopiera till utdata-katalog* som ska *kopieras om senare*.

:::image type="content" source="../articles/digital-twins/media/include-tutorial/copy-config.png" alt-text="Utdrag från Visual Studio-fönstret som visar Solution Explorer fönstret med serviceConfig. JSON markerat och egenskaps fönstret med egenskapen kopiera till utgående katalog inställt på Kopiera om nyare" border="false":::

Se till att _**AdtE2ESample**_ -projektet är öppet i Visual Studio och fortsätt att använda det i självstudien.

