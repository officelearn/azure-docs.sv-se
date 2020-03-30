---
title: Felsöka distributionsproblem för molntjänster | Microsoft-dokument
description: Det finns några vanliga problem som du kan stöta på när du distribuerar en molntjänst till Azure. Den här artikeln innehåller lösningar för några av dem.
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
ms.openlocfilehash: ccb08f853ae0f941dd5f9c0eca8c77f0f650905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71122755"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Felsöka distributionsproblem för molntjänst
När du distribuerar ett molntjänstprogrampaket till Azure kan du hämta information om distributionen från **fönstret Egenskaper** i Azure-portalen. Du kan använda informationen i det här fönstret för att felsöka problem med molntjänsten och du kan ange den här informationen till Azure Support när du öppnar en ny supportbegäran.

Du hittar fönstret **Egenskaper** på följande sätt:

* Klicka på distributionen av molntjänsten i Azure-portalen, klicka på **Alla inställningar**och klicka sedan på **Egenskaper**.

> [!NOTE]
> Du kan kopiera innehållet i **fönstret Egenskaper** till Urklipp genom att klicka på ikonen i fönstrets övre högra hörn.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problem: Jag kan inte komma åt min webbplats, men min distribution startas och alla rollinstanser är redo
Url-länken som visas i portalen innehåller inte porten. Standardporten för webbplatser är 80. Om programmet är konfigurerat för att köras i en annan port måste du lägga till rätt portnummer i URL:en när du öppnar webbplatsen.

1. Klicka på distributionen av molntjänsten i Azure-portalen.
2. I **fönstret Egenskaper** i Azure-portalen kontrollerar du portarna för rollinstanserna (under **Indataslutpunkter**).
3. Om porten inte är 80 lägger du till rätt portvärde i URL:en när du öppnar programmet. Om du vill ange en icke-standardport skriver du URL:en, följt av ett kolon (:), följt av portnumret, utan blanksteg.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problem: Mina rollinstanser återvinns utan att jag gör något
Tjänstläkning sker automatiskt när Azure identifierar problemnoder och därför flyttar rollinstanser till nya noder. När detta inträffar kan du se dina rollinstanser som automatiskt återvinns. Så här ta reda på om tjänstläkning inträffade:

1. Klicka på distributionen av molntjänsten i Azure-portalen.
2. I **fönstret Egenskaper** i Azure-portalen granskar du informationen och avgör om tjänstläkning inträffade under den tid som du observerade rollerna som kunde återvinnas.

Roller kommer också att återvinna ungefär en gång per månad under värd-OS och gäst-OS-uppdateringar.  
Mer information finns i omstarten [av blogginläggrollinstansen på grund av OS-uppgraderingar](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problem: Jag kan inte göra en VIP-swap och få ett felmeddelande
En VIP-swap tillåts inte om en distributionsuppdatering pågår. Distributionsuppdateringar kan ske automatiskt när:

* Ett nytt gästoperativsystem är tillgängligt och du är konfigurerad för automatiska uppdateringar.
* Tjänstläkning sker.

Så här ta reda på om en automatisk uppdatering hindrar dig från att göra en VIP-swap:

1. Klicka på distributionen av molntjänsten i Azure-portalen.
2. I **fönstret Egenskaper** i Azure-portalen tittar du på värdet **för Status**. Om det är **klart**kontrollerar du **Senaste åtgärden** för att se om en nyligen har hänt som kan förhindra VIP-bytet.
3. Upprepa steg 1 och 2 för produktionsdistributionen.
4. Om en automatisk uppdatering pågår väntar du tills den är klar innan du försöker göra VIP-bytet.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problem: En rollinstans loopar mellan Startat, Initierat, Upptagen och Stoppat
Det här tillståndet kan tyda på ett problem med programkoden, paketet eller konfigurationsfilen. I så fall bör du kunna se statusen ändras med några minuters mellanrum och Azure-portalen kan säga något i **stil med Återvinning,** **Upptagen**eller **Initiera**. Detta indikerar att det är något fel på programmet som hindrar rollinstansen från att köras.

Mer information om hur du felsöker problemet finns i blogginlägget [Azure PaaS Compute Diagnostics Data](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) och [Vanliga problem som gör att roller kan återvinnas](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problem: Mitt program slutade fungera
1. Klicka på rollinstansen i Azure-portalen.
2. I **fönstret Egenskaper** i Azure-portalen bör du tänka på följande villkor för att lösa problemet:
   * Om rollinstansen nyligen har stoppats (du kan kontrollera värdet **för Avbryt antal)** kan distributionen uppdateras. Vänta och se om rollinstansen fortsätter att fungera på egen hand.
   * Om rollinstansen är **Upptagen**kontrollerar du programkoden för att se om [StatusCheck-händelsen](/previous-versions/azure/reference/ee758135(v=azure.100)) hanteras. Du kan behöva lägga till eller åtgärda en kod som hanterar den här händelsen.
   * Gå igenom diagnostikdata och felsökningsscenarier i blogginlägget [Azure PaaS Compute Diagnostics Data](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Om du återvinner molntjänsten återställer du egenskaperna för distributionen och raderar effektivt informationen för det ursprungliga problemet.
>
>

## <a name="next-steps"></a>Nästa steg
Visa fler [felsökningsartiklar](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) för molntjänster.

Mer information om hur du felsöker problem med molntjänstroll med hjälp av Azure PaaS datordiagnostikdata finns i [Kevin Williamsons bloggserie](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
