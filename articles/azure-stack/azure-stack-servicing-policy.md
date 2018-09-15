---
title: Azure Stack som hanteringsprincip | Microsoft Docs
description: Läs mer om Azure Stack servicing principen samt hur du håller ett integrerat system i ett läge som stöds.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: harik
ms.openlocfilehash: 523be365e8b66c157546470a70ddec73d3e1e5d1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630689"
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack som hanteringsprincip
Den här artikeln beskriver Underhåll principen för integrerade Azure Stack-system och vad du måste göra för att behålla ditt system i ett läge som stöds. 

## <a name="update-package-types"></a>Uppdatera pakettyper

Det finns två typer av uppdateringspaket för integrerade system: 

- **Microsoft-programuppdateringar**. Microsoft ansvarar för underhåll livscykeln slutpunkt till slutpunkt för Microsoft-programuppdateringspaket. Dessa paket kan innehålla de senaste säkerhetsuppdateringarna för Windows Server, säkerhetsuppdateringar som inte och uppdaterade funktioner i Azure Stack. Du kan hämta dessa paket direkt från Microsoft.

- **OEM-tillverkarens maskinvaruuppdateringar**. Azure Stack-maskinvarupartners ansvarar för slutpunkt till slutpunkt Underhåll livscykeln (inklusive vägledning) för maskinvarurelaterade inbyggd programvara och drivrutinspaket för uppdateringen. Dessutom Azure Stack maskinvarupartners äger och underhålla vägledning för alla program- och maskinvara på värden för maskinvara livscykel. OEM-maskinvaruleverantören är värd för dessa uppdateringspaket på sina egna hämtningsplats.


## <a name="update-package-release-cadence"></a>Uppdatera paketets versionstakt
Microsoft förväntar sig att frigöra programuppdateringspaket på en månatlig takt. Det är dock möjligt att ha flera och update-versioner under en månad. OEM-maskinvaruleverantörer publicera sina uppdateringar som det behövs. 

Hitta dokumentation om hur du planerar för och hantera uppdateringar och hur du fastställer din nuvarande version i [hantera uppdaterar översikt](azure-stack-updates.md). Information om uppdateringen inklusive hur du hämtar det, finns i viktig information för att uppdatera: 
- [Uppdatering av Azure Stack 1808](azure-stack-update-1808.md)
- [Uppdatering av Azure Stack 1807](azure-stack-update-1807.md)
- [Uppdatering av Azure Stack 1805](azure-stack-update-1805.md)


## <a name="hotfixes"></a>Snabbkorrigeringar
Ibland kan tillhandahåller Microsoft snabbkorrigeringar för Azure Stack adressen ett specifikt problem som ofta är förebyggande eller tid.  Varje snabbkorrigeringar släpps med en Microsoft Knowledge Base-artikel som beskriver problemet, orsak och lösning. 

Snabbkorrigeringar hämtas och installeras precis som vanliga fullständig uppdateringspaket för Azure Stack. Till skillnad från en fullständig uppdatering kan dock snabbkorrigeringar installera på några minuter. Vi rekommenderar Azure Stack-operatörer ange underhållsperioder när du installerar snabbkorrigeringar. Snabbkorrigeringar uppdatera versionen av Azure Stack-molnet så kan du enkelt se om snabbkorrigeringen har installerats. En separat snabbkorrigering har angetts för varje version av Azure Stack som fortfarande stöd. Varje korrigering för en viss iteration beräknas kumulativt tillsammans med de tidigare uppdateringarna för samma version. Du kan läsa mer om tillämpligheten hos en viss snabbkorrigering i en korrigeringar som motsvarande Knowledge Base-artikel.  


## <a name="keep-your-system-under-support"></a>Håll datorn under support
Om du vill fortsätta att få support, måste du spara distributionen av Azure Stack aktuella. Uppskjutningsperiod principen efter uppdateringar är: för din Azure Stack-distribution ska behållas i support måste den köra den uppdaterade versionen som nyligen utgiven eller köra något av de två föregående Uppdateringsversioner. Snabbkorrigeringar beaktas inte större Uppdateringsversioner. Om Azure Stack-molnet som skyddas av *fler än två uppdateringar*, den anses vara kompatibel och måste uppdatera till minst den lägsta versionen som stöds för att få support. 

Till exempel om den senaste tillgängliga uppdateringsversionen är 1805 och föregående två uppdateringspaket har version 1804 och 1803, både 1803 1804 finnas kvar i support. Det är dock 1802 support upphör. Principen gäller när det finns inga versionen för en månad eller två. Om den aktuella versionen är 1805 och det fanns inga 1804-versionen, till exempel kvar de föregående två uppdateringspaket 1803 och 1802 i support.

Microsoft programuppdateringspaket är kumulativ och kräver föregående uppdateringspaketet som ett krav. Om du vill skjuta upp en eller flera uppdateringar kan du överväga att övergripande runtime om du vill hämta till den senaste versionen. 

## <a name="get-support"></a>Få support
Azure Stack följer samma process som stöd för Azure. Enterprise-kunder kan följa processen som beskrivs i [så här skapar du en supportförfrågan för Azure](/azure/azure-supportability/how-to-create-azure-support-request). Om du är en kund med en Cloud Service Provider (CSP), kontaktar du din CSP för support.  Mer information finns i den [Azure stöd för vanliga frågor och svar](https://azure.microsoft.com/support/faq/). 


## <a name="next-steps"></a>Nästa steg

- [Hantera uppdateringar i Azure Stack](azure-stack-updates.md)


