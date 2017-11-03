---
title: "Kom igång med Autoskala i Azure | Microsoft Docs"
description: "Lär dig mer om att skala din resurs i Azure."
author: rajram
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: rajram
ms.openlocfilehash: 84ce3af2bef1e3204ad91567d155d7cbf90e9c7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-autoscale-in-azure"></a>Kom igång med Autoskala i Azure
Den här artikeln beskriver hur du ställer in Autoskala inställningarna för din resurs i Microsoft Azure-portalen.

Azure övervakaren Autoskala gäller bara för virtuella datorer, cloud services, Azure App Service-planer och apptjänstmiljöer. 

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Identifiera Autoskala inställningarna i din prenumeration
Du kan identifiera alla resurser som autoskalning kan tillämpas i Azure-Monitor. Använd följande steg för steg för steg:

1. Öppna den [Azure-portalen.][1]
2. Klicka på ikonen Azure i den vänstra rutan.
  ![Öppna Azure Övervakare][2]
3. Klicka på **Autoskala** att visa alla resurser som autoskalning kan tillämpas, tillsammans med deras aktuella Autoskala status.
  ![Identifiera Autoskala i Azure-Monitor][3]

Du kan använda filterfönstret längst upp till scope nedåt i listan välja resurser i en viss resursgrupp, specifika resurstyper eller en viss resurs.

Du kommer märka aktuella standardinstansantalet och Autoskala status för varje resurs. Autoskala status kan vara:

- **Inte konfigurerad**: du har inte aktiverat Autoskala ännu för den här resursen.
- **Aktiverad**: du har aktiverat Autoskala för den här resursen.
- **Inaktiverad**: du har inaktiverat Autoskala för den här resursen.

## <a name="create-your-first-autoscale-setting"></a>Skapa din första autoskalningsinställning

Nu ska vi gå via en enkla steg för steg att skapa din första autoskalningsinställning.

1. Öppna den **Autoskala** bladet i Azure-Monitor och välj en resurs som du vill skala. (Följande använda en App Service-plan som är associerade med ett webbprogram. Du kan [skapa din första ASP.NET-webbapp i Azure på fem minuter.] [4])
2. Observera att den aktuella standardinstansantalet är 1. Klicka på **aktivera Autoskala**.
  ![Skalinställningen för ny webbapp][5]
3. Ange ett namn för inställningen skala och klicka sedan på **lägga till en regel**. Lägg märke till regelalternativ för skalan som öppnas som en kontext rutan till höger. Som standard anger det här alternativet för att skala din instansantalet av 1 om CPU-procent av resursen överskrider 70 procent. Lämna till sina standardvärden och klicka på **Lägg till**.
  ![Skapa skalinställningen för en webbapp][6]
4. Nu har du skapat din första skalningsregeln. Observera att UX rekommenderar metodtips och säger att ”bör ha minst en skala i regeln”. Gör så här:
  
    a. Klicka på **lägga till en regel**. 

    b. Ange **operatorn** till **mindre än**.

    c. Ange **tröskelvärdet** till **20**.

    d. Ange **åtgärden** till **minska antalet av**.

   Du bör nu ha en skalinställningen att skala ut/skalar i baserat på CPU-användning.
   ![Skala baserat på CPU][8]
5. Klicka på **Spara**.

Grattis! Du har nu skapat din första skalinställningen Autoskala ditt webbprogram baserat på CPU-användning.

> [!NOTE] 
> Samma steg kan användas för att komma igång med en skaluppsättning för virtuell dator eller moln rolltjänst.

## <a name="other-considerations"></a>Andra överväganden
### <a name="scale-based-on-a-schedule"></a>Skala enligt ett schema
Förutom att skala baserat på CPU, kan du ange nivå på olika sätt för särskilda dagar i veckan.

1. Klicka på **Lägg till ett villkor för skala**.
2. Skala-läge och regler är densamma som standard villkor.
3. Välj **Upprepa särskilda dagar** för schemat.
4. Välj dagarna och Starttid/Sluttid för när villkoret skala tillämpas.

![Skala villkor baserat på schema][9]
### <a name="scale-differently-on-specific-dates"></a>Skala annorlunda på specifika datum
Förutom att skala baserat på CPU, kan du ange nivå på olika sätt för specifika datum.

1. Klicka på **Lägg till ett villkor för skala**.
2. Skala-läge och regler är densamma som standard villkor.
3. Välj **ange start-/ slutdatum** för schemat.
4. Välj start-/ slutdatum och Starttid/Sluttid för när villkoret skala tillämpas.

![Skala villkor baserat på datum][10]

### <a name="view-the-scale-history-of-your-resource"></a>Visa historiken skala för din resurs
När din resurs skalas upp eller ned loggas en händelse i aktivitetsloggen. Du kan visa historiken skala för din resurs för de senaste 24 timmarna genom att växla till den **kör historik** fliken.

![Kör historik][11]

Om du vill visa historiken för fullständig skala (för upp till 90 dagar) Välj **Klicka här om du vill se mer information**. Aktivitetsloggen öppnas med Autoskala markerat för din resurs och kategori.

### <a name="view-the-scale-definition-of-your-resource"></a>Visa skala definition av din resurs
Autoskala är en Azure Resource Manager-resurs. Du kan visa skala-definition i JSON genom att växla till den **JSON** fliken.

![Skala definition][12]

Du kan göra ändringar i JSON direkt, om det behövs. Dessa ändringar visas när du har sparat.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Inaktivera Autoskala och skala dina instanser manuellt
Det kan finnas tillfällen när du vill inaktivera din aktuella skalinställningen och skala resursen manuellt.

Klicka på den **inaktivera Autoskala** längst upp.
![Inaktivera Autoskala][13]

> [!NOTE] 
> Det här alternativet inaktiverar konfigurationen. Men kan du få tillgång till den när du har aktiverat Autoskala igen. 

Du kan nu ange antalet instanser som du vill skala till manuellt.

![Ange manuell skala][14]

Du kan alltid gå tillbaka till Autoskala genom att klicka på **aktivera Autoskala** och sedan **spara**.

## <a name="next-steps"></a>Nästa steg
- [Skapa en aktivitet loggen aviseringen om du vill övervaka alla Autoskala motorn åtgärder för din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Skapa en aktivitet loggen aviseringen om du vill övervaka alla misslyckade Autoskala skala-/ skalbar åtgärder för din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png

