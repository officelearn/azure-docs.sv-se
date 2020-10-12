---
title: Felsök distributions problem i moln tjänsten | Microsoft Docs
description: Det finns några vanliga problem som du kan stöta på när du distribuerar en moln tjänst till Azure. Den här artikeln innehåller lösningar på några av dem.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 3b86ff205981cfe7bb00ecb3ca58804fba3e80b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460107"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Felsöka problem med distribution av moln tjänster
När du distribuerar ett moln tjänst program paket till Azure kan du hämta information om distributionen från fönstret **Egenskaper** i Azure Portal. Du kan använda informationen i det här fönstret för att felsöka problem med moln tjänsten och du kan ge den här informationen till Azure-supporten när du öppnar en ny supportbegäran.

Du hittar fönstret **Egenskaper** på följande sätt:

* Klicka på distributionen av moln tjänsten i Azure Portal, klicka på **alla inställningar**och klicka sedan på **Egenskaper**.

> [!NOTE]
> Du kan kopiera innehållet i fönstret **Egenskaper** till Urklipp genom att klicka på ikonen i det övre högra hörnet i fönstret.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problem: Jag kan inte komma åt min webbplats, men distributionen har startats och alla roll instanser är klara
URL-länken för webbplatsen som visas i portalen omfattar inte porten. Standard porten för webbplatser är 80. Om programmet har kon figurer ATS för att köras i en annan port måste du lägga till rätt port nummer till URL: en när du ansluter till webbplatsen.

1. Klicka på distributionen av moln tjänsten i Azure Portal.
2. I rutan **Egenskaper** för Azure Portal kontrollerar du portarna för roll instanserna (under **ingångs slut punkter**).
3. Om porten inte är 80 lägger du till rätt port värde till URL: en när du öppnar programmet. Om du vill ange en port som inte är standard, anger du URL: en följt av ett kolon (:) följt av port numret, utan blank steg.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problem: mina roll instanser återvanns utan mig att göra något
Tjänst återställning sker automatiskt när Azure identifierar felnoder och därmed flyttar roll instanser till nya noder. När detta inträffar kan du se att dina roll instanser åter använder automatiskt. Ta reda på om tjänst återställning har skett:

1. Klicka på distributionen av moln tjänsten i Azure Portal.
2. I rutan **Egenskaper** för Azure Portal granskar du informationen och avgör om tjänstens återställning uppstod under den tid som du noterade rollernas åter användning.

Roller kommer också att återanvändas ungefär en gång per månad under uppdatering av värd operativ system och gäst operativ system.  
Mer information finns i blogg inläggets [roll instans startar om på grund av OS-uppgraderingar](https://docs.microsoft.com/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problem: Jag kan inte göra en VIP-växling och får ett fel meddelande
En VIP-växling tillåts inte om en distributions uppdatering pågår. Distributions uppdateringar kan ske automatiskt när:

* Ett nytt gäst operativ system är tillgängligt och du har kon figurer ATS för automatiska uppdateringar.
* Tjänstens återställning sker.

Ta reda på om en automatisk uppdatering hindrar dig från att göra en VIP-växling:

1. Klicka på distributionen av moln tjänsten i Azure Portal.
2. I rutan **Egenskaper** för Azure Portal tittar du på värdet för **status**. Om den är **klar**kan du kontrol lera den **senaste åtgärden** för att se om ett nyligen hänt som kan förhindra VIP-växling.
3. Upprepa steg 1 och 2 för produktions distributionen.
4. Om en automatisk uppdatering pågår väntar du tills den har slutförts innan du försöker göra VIP-växlingen.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problem: en roll instans upprepas mellan startad, initieras, upptagen och stoppas
Det här tillståndet kan tyda på ett problem med programkoden, paketet eller konfigurationsfilen. I så fall bör du kunna se status ändringen med några minuters mellanrum och Azure Portal kan säga något som t. ex. **återvinning**, **upptagen**eller **initiering**. Det tyder på att det är något fel på det program som hindrar roll instansen från att köras.

Mer information om hur du felsöker det här problemet finns i blogg inlägget [Azure-PaaS Compute diagnostikdata](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data) och [vanliga problem som orsakar att roller återanvänds](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problem: mitt program slutade fungera
1. Klicka på roll instansen i Azure Portal.
2. I rutan **Egenskaper** för Azure Portal bör du ta hänsyn till följande villkor för att lösa problemet:
   * Om roll instansen nyligen har stoppats (du kan kontrol lera värdet för **antal avbrott**) kan distributionen uppdateras. Vänta på att se om roll instansen fortsätter att fungera på egen hand.
   * Om roll instansen är **upptagen**kontrollerar du program koden för att se om [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) -händelsen hanteras. Du kan behöva lägga till eller åtgärda viss kod som hanterar den här händelsen.
   * Gå igenom diagnostiska data och fel söknings scenarier i blogg inlägget [Azure PaaS Compute Diagnostic data](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

> [!WARNING]
> Om du återanvänder moln tjänsten återställer du egenskaperna för distributionen så att informationen för det ursprungliga problemet raderas.
>
>

## <a name="next-steps"></a>Nästa steg
Visa fler [fel söknings artiklar](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) för Cloud Services.

Information om hur du felsöker problem med moln tjänst roller med hjälp av Azure PaaS Computer Diagnostics-data finns i [blogg serien för Kevin Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
