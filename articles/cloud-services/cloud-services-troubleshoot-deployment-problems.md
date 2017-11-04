---
title: "Felsökning av problem med cloud service-distribution | Microsoft Docs"
description: "Det finns några vanliga problem som kan uppstå när du distribuerar en tjänst i molnet till Azure. Den här artikeln innehåller lösningar på några av dem."
services: cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 944a29aebf7abfe32a7789ab239718b1cd2d7b15
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Felsökning av problem med cloud service-distribution
När du distribuerar ett cloud service-programpaket till Azure kan du få information om hur du distribuerar från den **egenskaper** rutan i Azure-portalen. Du kan använda informationen i det här fönstret för att felsöka problem med Molntjänsten och du kan ange den här informationen för stöd för Azure när du öppnar en ny supportförfrågan.

Du hittar den **egenskaper** rutan på följande sätt:

* Klicka på distributionen av Molntjänsten i Azure-portalen, **alla inställningar**, och klicka sedan på **egenskaper**.
* Klicka på distributionen av Molntjänsten i den klassiska Azure-portalen, **INSTRUMENTPANELEN**, som finns i det nedre högra hörnet på sidan (under **snabböversikten**). Tänk på att det finns ingen ”egenskaper” etikett på det här fönstret.

> [!NOTE]
> Du kan kopiera innehållet i den **egenskaper** fönstret till Urklipp genom att klicka på ikonen i det övre högra hörnet i fönstret.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problem: Jag kan inte komma åt min webbplats, men installationen startas och alla rollinstanser är klara
Webbplats-URL-länk som visas i portalen innehåller inte porten. Standardporten för webbplatser är 80. Om programmet är konfigurerat för att köras i en annan port måste du lägga till rätt portnummer Webbadressen vid åtkomst till webbplatsen.

1. Klicka på distributionen av Molntjänsten i Azure-portalen.
2. I den **egenskaper** rutan i Azure-portalen Kontrollera portar för rollinstanserna (under **indata slutpunkter**).
3. Om porten inte är 80, lägger du till rätt port-värdet till URL när du har åtkomst till programmet. Om du vill ange en annan port än standardporten, skriver du URL-Adressen, följt av ett kolon (:) följt av portnummer, utan blanksteg.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problem: Min rollinstanser återvinns utan mig gör någonting
Tjänsten återställning sker automatiskt när Azure identifierar problemet noder och därför flyttar rollinstanser till nya noder. När detta inträffar kan du se dina rollinstanser återvinning automatiskt. Ta reda på om tjänsten återställning uppstod:

1. Klicka på distributionen av Molntjänsten i Azure-portalen.
2. I den **egenskaper** rutan i Azure-portalen granska informationen och avgöra om tjänsten återställning uppstod under den tid som observeras av rollerna återvinning.

Roller kommer också att återanvändas ungefär en gång i månaden under värd-OS och Gäst-OS-uppdateringar.  
Mer information finns i bloggposten [roll-instansen startas om på grund av OS-uppgraderingar](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problem: Det går inte att jag göra en VIP-växling och får ett felmeddelande
En VIP-växling är inte tillåten om en av distributionsuppdatering pågår. Distribution av uppdateringar kan ske automatiskt när:

* En ny gästoperativsystemet är tillgänglig och du har konfigurerats för automatiska uppdateringar.
* Tjänsten återställning inträffar.

Ta reda på om en automatisk uppdatering hindrar dig från att utföra en VIP-växling:

1. Klicka på distributionen av Molntjänsten i Azure-portalen.
2. I den **egenskaper** rutan på Azure-portalen titta på värdet för **Status**. Om det är **klar**, kontrollera **senaste åtgärden** att se om en nyligen har hänt som kan förhindra att VIP-växling.
3. Upprepa steg 1 och 2 för produktionsdistributionen.
4. Om en automatisk uppdatering pågår, vänta tills den är klar innan du försöker göra VIP-växling.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problem: En rollinstans slingor mellan igång, initierar, upptagen och Stoppad
Det här tillståndet kan tyda på ett problem med programkoden, paketet eller konfigurationsfilen. I så fall kan du ska kunna se status för ändra några minuters mellanrum och Azure-portalen kan stå ungefär **återvinning**, **upptagen**, eller **initierar**. Detta anger att det är något fel med det program som hindrar instansen körs.

Mer information om hur du felsöker problemet finns i blogginlägget [Compute diagnostikdata i Azure PaaS](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) och [vanliga problem som orsakar återanvändning av roller](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problem: Mitt program stoppats
1. Klicka på instansen i Azure-portalen.
2. I den **egenskaper** rutan i Azure-portalen ta hänsyn till följande för att lösa problemet:
   * Om instansen nyligen har stoppats (du kan kontrollera värdet för **antal avbrott**), distributionen kunde uppdateras. Vänta på att se om instansen återupptas fungerar på sin egen.
   * Om instansen är **upptagen**, kontrollera din programkod för att se om den [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) händelsen hanterats. Du kan behöva lägga till eller åtgärda kod som hanterar den här händelsen.
   * Gå igenom diagnostikdata och felsökning i blogginlägget [Compute diagnostikdata i Azure PaaS](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Om du återanvänder Molntjänsten återställa du egenskaperna för distributionen, effektivt raderar information för det ursprungliga problemet.
>
>

## <a name="next-steps"></a>Nästa steg
Visa mer [felsökning artiklar](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-allocation-failures) för molntjänster.

Information om hur du felsöker cloud service rollen problem med hjälp av Azure PaaS datorn diagnostikdata finns [Kevin Williamson bloggserie](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
