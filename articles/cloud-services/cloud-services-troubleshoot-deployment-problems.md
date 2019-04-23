---
title: Felsök distributionsproblem med cloud service | Microsoft Docs
description: Det finns några vanliga problem som kan uppstå när du distribuerar en tjänst i molnet till Azure. Den här artikeln innehåller lösningar på några av dem.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: cc2a0177525013736445db5fd1befa478dc9b9b8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798578"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Felsök distributionsproblem med cloud service
När du distribuerar ett program för molntjänstpaket till Azure kan du kan få information om hur du distribuerar från den **egenskaper** fönstret i Azure-portalen. Du kan använda informationen i det här fönstret för att felsöka problem med Molntjänsten och du kan lämna denna information till supporten för Azure när du öppnar en ny supportbegäran.

Du hittar den **egenskaper** fönstret på följande sätt:

* I Azure-portalen klickar du på distributionen av din molntjänst, klickar du på **alla inställningar**, och klicka sedan på **egenskaper**.

> [!NOTE]
> Du kan kopiera innehållet i den **egenskaper** fönstret till Urklipp genom att klicka på ikonen i det övre högra hörnet i fönstret.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problem: Jag kan inte komma åt min webbplats, men min distribution har startats och alla rollinstanser är redo
Webbplats-URL-länk som visas i portalen innehåller inte porten. Standardporten för webbplatser är 80. Om ditt program har konfigurerats för att köras i en annan port måste du lägga till rätt portnummer till URL: en vid åtkomst till webbplatsen.

1. Klicka på distributionen av din molntjänst i Azure-portalen.
2. I den **egenskaper** rutan i Azure-portalen, Kontrollera portar för rollinstanserna (under **indata slutpunkter**).
3. Om porten inte är 80, lägger du till rätt Portvärdet i URL: en när du har åtkomst till programmet. Om du vill ange en icke-standardport skriver du URL, följt av ett kolon (:), följt av portnummer, utan blanksteg.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problem: Min rollinstanser har återvunnits utan mig gör ingenting
Tjänsten återställning sker automatiskt när Azure upptäcker problem noder och därför flyttar rollinstanser till nya noder. När detta inträffar kan du se dina rollinstanser återvinning automatiskt. Att ta reda på om tjänståterställning inträffade:

1. Klicka på distributionen av din molntjänst i Azure-portalen.
2. I den **egenskaper** rutan i Azure-portalen, granskar du informationen och avgöra om tjänståterställning uppstod under den tid som du såg i rollåteranvändning.

Roller kommer också Papperskorgen ungefär en gång per månad under värd-OS och Gäst-OS-uppdateringar.  
Mer information finns i bloggposten [rollen instansen startas om på grund av OS-uppgraderingar](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problem: Jag kan inte utföra en VIP-växling och får ett felmeddelande
En VIP-växling är inte tillåtet om en uppdatering av distribution pågår. Distribution av uppdateringar kan ske automatiskt när:

* Ett nytt gästoperativsystem är tillgängligt och du har konfigurerats för automatiska uppdateringar.
* Tjänståterställning inträffar.

Om du vill ta reda på om en automatisk hindrar uppdatering dig från att utföra en VIP-växling:

1. Klicka på distributionen av din molntjänst i Azure-portalen.
2. I den **egenskaper** fönstret i Azure Portal, titta på värdet för **Status**. Om det är **redo**, kontrollera **senaste åtgärden** att se om en nyligen inträffade som kan förhindra att VIP-växling.
3. Upprepa steg 1 och 2 för distributionen i produktionsmiljön.
4. Om en automatisk uppdatering pågår, vänta tills den har slutförts innan du försöker att utföra VIP-växling.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problem: En rollinstans körs i en loop mellan startad, initierar, upptagen och Stoppad
Det här tillståndet kan tyda på ett problem med programkoden, paketet eller konfigurationsfilen. I så fall kan du ska kunna se status ändra några minuters mellanrum och Azure-portalen kan stå något som liknar **återanvänder**, **upptagen**, eller **initierar**. Detta anger att det är något fel med det program som hindrar rollinstansen från att köras.

Mer information om hur du felsöker för det här problemet finns i blogginlägget [Compute diagnostikdata i Azure PaaS](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) och [vanliga problem som gör att roller återvinns](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problem: Mitt program som har slutat fungera
1. I Azure-portalen klickar du på rollinstansen.
2. I den **egenskaper** rutan i Azure-portalen, ta hänsyn till följande för att lösa problemet:
   * Om rollinstansen nyligen har stoppats (du kan kontrollera värdet för **antal avbrutna**), det gick att uppdatera distributionen. Vänta med att se om rollinstansen återupptar fungerar på egen hand.
   * Om rollinstansen är **upptagen**, kontrollera din programkod för att se om den [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) händelsen hanteras. Du kan behöva lägga till eller åtgärda kod som hanterar den här händelsen.
   * Gå igenom diagnostiska data och felsökning i blogginlägget [Compute diagnostikdata i Azure PaaS](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Om du återanvänder din molntjänst återställa du egenskaperna för distributionen, effektivt Radera information för det ursprungliga problemet.
>
>

## <a name="next-steps"></a>Nästa steg
Visa mer [felsökningsartiklar](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) för molntjänster.

Om du vill lära dig mer om felsökning av problem med cloud service rollen genom att använda diagnostikdata för Azure PaaS-dator, se [Kevin Williamson bloggserie](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
