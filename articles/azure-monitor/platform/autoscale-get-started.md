---
title: Kom igång med autoskalning i Azure
description: Lär dig hur du skalar resurswebbappen, molntjänsten, den virtuella datorn eller den virtuella datorns skalningsuppsättning i Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 0a40496e4d496d0062c6ee7a6ab935c1ad9b35a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75396353"
---
# <a name="get-started-with-autoscale-in-azure"></a>Komma igång med automatisk skalning i Azure
I den här artikeln beskrivs hur du konfigurerar inställningarna för automatisk skalning för din resurs i Microsoft Azure-portalen.

Automatisk skalning av Azure Monitor gäller endast för [skalningsuppsättningar för virtuella](https://azure.microsoft.com/services/virtual-machine-scale-sets/)datorer, [Molntjänster](https://azure.microsoft.com/services/cloud-services/), [App Service – Webbappar](https://azure.microsoft.com/services/app-service/web/)och [API Management-tjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Upptäck inställningarna för automatisk skalning i din prenumeration
Du kan identifiera alla resurser som automatisk skalning gäller för i Azure Monitor. Gör så här för en steg-för-steg-genomgång:

1. Öppna [Azure-portalen.][1]
1. Klicka på Azure Monitor-ikonen i den vänstra rutan.
  ![Öppna Azure Monitor][2]
1. Klicka på **Automatisk skalning** om du vill visa alla resurser som automatisk skalning gäller för, tillsammans med deras aktuella status för automatisk skalning.
  ![Upptäck automatisk skalning i Azure Monitor][3]

Du kan använda filterfönstret överst för att begränsa listan för att välja resurser i en viss resursgrupp, specifika resurstyper eller en viss resurs.

För varje resurs hittar du det aktuella antalet instanser och statusen Automatisk skalning. Statusen Automatisk skalning kan vara:

- **Inte konfigurerad**: Du har inte aktiverat Automatisk skalning ännu för den här resursen.
- **Aktiverad:** Du har aktiverat Automatisk skalning för den här resursen.
- **Inaktiverad**: Du har inaktiverat automatisk skalning för den här resursen.

## <a name="create-your-first-autoscale-setting"></a>Skapa din första inställning för automatisk skalning

Låt oss nu gå igenom en enkel steg-för-steg genomgång för att skapa din första automatisk skalning inställning.

1. Öppna bladet **Automatisk skalning** i Azure Monitor och välj en resurs som du vill skala. (Följande steg använder en App Service-plan som är associerad med en webbapp. Du kan [skapa din första ASP.NET webbapp i Azure på 5 minuter.][4])
1. Observera att det aktuella antalet instanser är 1. Klicka på **Aktivera automatisk skalning**.
  ![Skalningsinställning för ny webbapp][5]
1. Ange ett namn för skalningsinställningen och klicka sedan på **Lägg till en regel**. Lägg märke till de skalningsregelalternativ som öppnas som ett sammanhangsfönster till höger. Som standard anger detta alternativet att skala antalet instanser med 1 om processorprocenten för resursen överstiger 70 procent. Lämna den på standardvärdena och klicka på **Lägg till**.
  ![Skapa skalningsinställning för en webbapp][6]
1. Du har nu skapat din första skalningsregel. Observera att användarupplevelsen rekommenderar bästa praxis och säger att "Det rekommenderas att ha minst en skala i regel." Gör så här:

    a. Klicka på **Lägg till en regel**.

    b. Ange **operator** till **mindre än**.

    c. Ange **tröskelvärdet** till **20**.

    d. Ange **operation** till **Minska antalet med**.

   Du bör nu ha en skalningsinställning som skalar ut/skalar in baserat på CPU-användning.
   ![Skala baserad på CPU][8]
1. Klicka på **Spara**.

Grattis! Du har nu skapat din första skalningsinställning för att automatiskt skala din webbapp baserat på CPU-användning.

> [!NOTE]
> Samma steg gäller för att komma igång med en skalningsuppsättning för virtuella datorer eller molntjänstroll.

## <a name="other-considerations"></a>Andra överväganden
### <a name="scale-based-on-a-schedule"></a>Skala baserat på ett schema
Förutom skala baserat på CPU kan du ställa in din skala på olika sätt för specifika dagar i veckan.

1. Klicka på **Lägg till ett skalningsvillkor**.
1. Att ange skalningsläge och regler är detsamma som standardvillkoret.
1. Välj **Upprepa specifika dagar** för schemat.
1. Välj de dagar och den start-/sluttid för när skalningsvillkoret ska tillämpas.

![Skalningsvillkor baserat på schema][9]
### <a name="scale-differently-on-specific-dates"></a>Skala olika på specifika datum
Förutom skala baserat på CPU kan du ställa in din skala på olika sätt för specifika datum.

1. Klicka på **Lägg till ett skalningsvillkor**.
1. Att ange skalningsläge och regler är detsamma som standardvillkoret.
1. Välj **Ange start-/slutdatum** för schemat.
1. Välj start-/slutdatum och start-/sluttid för när skalningsvillkoret ska tillämpas.

![Skalningsvillkor baserat på datum][10]

### <a name="view-the-scale-history-of-your-resource"></a>Visa resursens skalhistorik
När resursen skalas upp eller ned loggas en händelse i aktivitetsloggen. Du kan visa resursens skalhistorik under de senaste 24 timmarna genom att växla till fliken **Körhistorik.**

![Körningshistorik][11]

Om du vill visa hela skalningshistoriken (i upp till 90 dagar) väljer **du Klicka här för att se mer information**. Aktivitetsloggen öppnas, med automatisk skalning förvald för din resurs och kategori.

### <a name="view-the-scale-definition-of-your-resource"></a>Visa skalningsdefinitionen för din resurs
Automatisk skalning är en Azure Resource Manager-resurs. Du kan visa skalningsdefinitionen i JSON genom att växla till fliken **JSON.**

![Definition av skalning][12]

Du kan göra ändringar i JSON direkt, om det behövs. Dessa ändringar visas när du har sparat dem.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Inaktivera Automatisk skalning och skala instanserna manuellt
Det kan finnas tillfällen när du vill inaktivera den aktuella skalningsinställningen och skala resursen manuellt.

Klicka på knappen **Inaktivera automatisk skalning** högst upp.
![Inaktivera automatisk skalning][13]

> [!NOTE]
> Det här alternativet inaktiverar konfigurationen. Du kan dock återgå till det när du har aktiverat Automatisk skalning igen.

Du kan nu ange hur många instanser som du vill skala till manuellt.

![Ange manuell skala][14]

Du kan alltid återgå till Automatisk skalning genom att klicka på **Aktivera automatisk skalning** och sedan **spara**.

## <a name="next-steps"></a>Nästa steg
- [Skapa en aktivitetsloggavisering för att övervaka alla motoråtgärder för automatisk skalning i din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Skapa en aktivitetsloggavisering för att övervaka alla misslyckade inskalnings-/skalningsåtgärder för automatisk skalning på din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

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

