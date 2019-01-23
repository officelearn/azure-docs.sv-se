---
title: Kom igång med automatisk skalning i Azure
description: Lär dig att skala din resurs Web App, molntjänst, virtuell dator eller virtuell Datorskalning som angetts i Azure.
author: rajram
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: rajram
ms.subservice: autoscale
ms.openlocfilehash: 0535c84a8ee0776c2c35a46d3c7510a2cd615cf6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54469499"
---
# <a name="get-started-with-autoscale-in-azure"></a>Kom igång med automatisk skalning i Azure
Den här artikeln beskriver hur du konfigurerar dina inställningar för automatisk skalning för din resurs i Microsoft Azure-portalen.

Automatisk skalning i Azure Monitor gäller endast [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [molntjänster](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/), och [API Management-tjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Identifiera inställningarna för automatisk skalning i din prenumeration
Du kan identifiera alla resurser som automatisk skalning gäller i Azure Monitor. Använd följande steg för en stegvis genomgång:

1. Öppna den [Azure-portalen.][1]
1. Klicka på ikonen Azure Monitor i den vänstra rutan.
  ![Öppna Azure Monitor][2]
1. Klicka på **Autoskala** att visa alla resurser som automatisk skalning är tillämplig, tillsammans med deras aktuella status för automatisk skalning.
  ![Identifiera automatisk skalning i Azure Monitor][3]

Du kan använda filterfönstret längst upp till omfånget nedåt i listan för att välja resurser i en specifik resursgrupp, specifika resurstyper eller en specifik resurs.

För varje resurs hittar du det aktuella instansantalet och status för autoskalning. Status för autoskalning kan vara:

- **Inte konfigurerad**: Du har inte aktiverat automatisk skalning ännu för den här resursen.
- **Aktiverad**: Du har aktiverat automatisk skalning för den här resursen.
- **Inaktiverad**: Du har inaktiverat automatisk skalning för den här resursen.

## <a name="create-your-first-autoscale-setting"></a>Skapa din första autoskalningsinställning

Nu ska vi gå via en enkel stegvis genomgång för att skapa din första autoskalningsinställning.

1. Öppna den **Autoskala** bladet i Azure Monitor och välj en resurs som du vill skala. (Följande använda en App Service-plan som är associerade med en webbapp. Du kan [skapa din första ASP.NET-webbapp i Azure på fem minuter.] [4])
1. Observera att det aktuella instansantalet är 1. Klicka på **aktivera autoskalning**.
  ![Skalinställningen för ny webbapp][5]
1. Ange ett namn för skalinställningen och klicka sedan på **lägga till en regel**. Observera regeln skalningsalternativ som öppnas som en kontext-rutan på höger sida. Som standard anger det här alternativet för att skala din instansantalet med 1 om CPU-procent på resursen överskrider 70 procent. Lämnar det på standardvärdena och klicka på **Lägg till**.
  ![Skapa skalinställningen för en webbapp][6]
1. Nu har du skapat din första skalningsregeln. Observera att UX rekommenderar metodtips och säger att ”rekommenderar vi att du har minst en skala i regeln”. Gör så här:

    a. Klicka på **lägga till en regel**.

    b. Ange **operatorn** till **mindre än**.

    c. Ange **tröskelvärdet** till **20**.

    d. Ange **åtgärden** till **minska antal med**.

   Du bör nu ha en skalinställningen att skalas ut/skalar i baserat på CPU-användning.
   ![Skala baserat på CPU][8]
1. Klicka på **Spara**.

Grattis! Du har nu har skapat din första skalinställningen för automatisk skalning webbappen baserat på CPU-användning.

> [!NOTE]
> Samma steg kan användas för att komma igång med en skalningsuppsättning för virtuell dator eller ett molnkluster rollen.

## <a name="other-considerations"></a>Annat att tänka på
### <a name="scale-based-on-a-schedule"></a>Skala baserat på ett schema
Förutom skala baserat på CPU, kan du ange nivå på olika sätt för särskilda dagar i veckan.

1. Klicka på **lägga till en skalningsvillkoret**.
1. Ställa in Skalningsläge och reglerna är samma som standard-villkor.
1. Välj **Upprepa specifika dagar** för schemat.
1. Välj dagarna och Starttid/Sluttid för när skalningsvillkoret tillämpas.

![Skalningsvillkoret baserat på schema][9]
### <a name="scale-differently-on-specific-dates"></a>Skala på olika sätt på specifika datum
Förutom skala baserat på CPU, kan du ange nivå på olika sätt för specifika datum.

1. Klicka på **lägga till en skalningsvillkoret**.
1. Ställa in Skalningsläge och reglerna är samma som standard-villkor.
1. Välj **ange start/slutdatum** för schemat.
1. Välj start/slut-datum och Starttid/Sluttid för när skalningsvillkoret tillämpas.

![Skalningsvillkoret utifrån datum][10]

### <a name="view-the-scale-history-of-your-resource"></a>Visa historiken för skala för din resurs
När din resurs skalas upp eller ned loggas en händelse i aktivitetsloggen. Du kan visa historiken skala för din resurs för de senaste 24 timmarna genom att växla till den **Körningshistorik** fliken.

![Körningshistorik][11]

Om du vill visa fullständig skala historik (för upp till 90 dagar), Välj **Klicka här om du vill se mer information om**. Aktivitetsloggen öppnas med automatisk skalning förvalt för din resurs och kategori.

### <a name="view-the-scale-definition-of-your-resource"></a>Visa skala definitionen av din resurs
Automatisk skalning är en Azure Resource Manager-resurs. Du kan visa skala definitionen i JSON genom att växla till den **JSON** fliken.

![Skala definition][12]

Du kan göra ändringar i JSON direkt, om det behövs. Dessa ändringar visas när du har sparat.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Inaktivera automatisk skalning och manuellt skala dina instanser
Det kan finnas tillfällen när du vill inaktivera din aktuella skalinställningen och manuellt skala din resurs.

Klicka på den **inaktivera automatisk skalning** längst upp.
![Inaktivera automatisk skalning][13]

> [!NOTE]
> Det här alternativet inaktiverar din konfiguration. Men kan du gå tillbaka till den när du har aktiverat automatisk skalning igen.

Du kan nu ange antalet instanser som du vill skala till manuellt.

![Ange manuell skala][14]

Du kan alltid återgå till automatisk skalning genom att klicka på **aktivera autoskalning** och sedan **spara**.

## <a name="next-steps"></a>Nästa steg
- [Skapa en aktivitet Log aviseringen om du vill övervaka alla Autoskala motorn åtgärder för din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Skapa en aktivitet Log aviseringen om du vill övervaka alla misslyckade Autoskala skala-/ skalbar åtgärder på din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png

