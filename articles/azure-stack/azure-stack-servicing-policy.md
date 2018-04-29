---
title: Azure-stacken behandling av princip | Microsoft Docs
description: Läs mer om Azure-stacken behandling av princip för och hur du hålla ett integrerat system i ett läge som stöds.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: brenduns
ms.reviewer: harik
ms.openlocfilehash: 77cc2f80588a104880e8149daccc6debd1ec43bc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="azure-stack-servicing-policy"></a>Azure-stacken behandling av princip
Den här artikeln beskriver Underhåll princip för Azure-stacken integrerat system och vad du måste göra för att hålla datorn i ett läge som stöds. 

## <a name="update-package-types"></a>Uppdatera pakettyper

Det finns två typer av uppdateringspaket för integrerade system: 

- **Microsoft-programuppdateringar**. Microsoft ansvarar för slutpunkt till slutpunkt Underhåll livscykeln för uppdateringspaket för Microsoft-program. Dessa paket kan innehålla senaste säkerhetsuppdateringarna från Windows Server, icke-säkerhetsuppdateringar och funktionsuppdateringar för Azure-stacken. Du kan hämta de här uppdateringspaket direkt från Microsoft.

- **OEM-tillverkarens maskinvaruuppdateringar**. Azure Stack maskinvarupartners ansvarar för slutpunkt till slutpunkt Underhåll livscykeln (inklusive vägledning) för maskinvarurelaterade inbyggd programvara och uppdatera drivrutinspaket. Dessutom Azure Stack maskinvarupartners äger och underhåll för alla program- och maskinvara maskinvara livscykel värden. Maskinvaruleverantören OEM-värd för dessa uppdateringspaket på sina egna hämtningsplatsen.


## <a name="update-package-release-cadence"></a>Uppdatera paketet versionen takt
Microsoft räknar med att släppa programuppdateringspaket i en månatlig takt. Det är dock möjligt att ha flera och update-versioner i en månad. OEM-maskinvaruleverantörer släpp uppdateringarna som behövs. 

Hitta dokumentation om hur du planerar för och hantera uppdateringar och hur du fastställer din nuvarande version i [hantera uppdateringar översikt](azure-stack-updates.md). Information om en viss uppdatering, inklusive hur du hämtar det, finns i viktig information för att uppdatera: 
- [Azure-stacken 1803 uppdatering](azure-stack-update-1803.md)
- [Azure-stacken 1802 uppdatering](azure-stack-update-1802.md)
- [Azure-stacken 1712 uppdatering](azure-stack-update-1712.md)



## <a name="hotfixes"></a>Snabbkorrigeringar
Ibland kan tillhandahåller Microsoft snabbkorrigeringar för Azure-Stack adressen ett specifikt problem som är ofta förebyggande eller tid.  Varje snabbkorrigeringar släpps med en Microsoft Knowledge Base-artikel som beskriver problemet, orsak och lösning. 

Snabbkorrigeringar hämtas och installeras precis som regelbunden fullständig uppdateringspaket för Azure-stacken. Till skillnad från en fullständig uppdatering kan dock snabbkorrigeringar installera i minuter. Vi rekommenderar Azure Stack operatörer underhållsfönster när du installerar snabbkorrigeringar. Snabbkorrigeringar uppdatera versionen av Azure Stack-molnet så kan du enkelt se om snabbkorrigeringen har tillämpats. En separat snabbkorrigering har angetts för varje version av Azure-stacken som fortfarande stöd. Varje korrigering för en specifik iteration är kumulativa och innehåller tidigare uppdateringar för samma version. Du kan läsa mer om tillämplighet för en specifik snabbkorrigering i en korrigeringar motsvarande Knowledge Base-artikel.  


## <a name="keep-your-system-under-support"></a>Hålla systemet under stöd
Om du vill fortsätta få support, måste du behålla distributionen av Azure-stacken aktuella. Avstängning policy för uppdateringar: för distributionen Azure Stack hålls kvar i stöd måste köra den uppdaterade versionen som nyligen utgivna eller köra något av de två föregående Uppdateringsversioner. Snabbkorrigeringar inte anses vara viktiga Uppdateringsversioner. Om ditt Azure Stack-moln som skyddas av *fler än två uppdateringar*, den anses vara inkompatibel och måste uppdatera till minst den lägsta versionen som stöds för att få support. 

Till exempel om den senaste tillgängliga uppdateringsversionen är 1805 och de föregående två uppdateringspaket har version 1804 och 1803, både 1803 1804 finnas kvar i stöd. 1802 är dock inte längre stöds. Principen gäller när det finns inga versionen för en månad eller två. Om den aktuella versionen är 1805 och det fanns inga 1804 versionen, till exempel kvar de föregående två uppdateringspaket 1803 och 1802 i stöd.

Microsoft programuppdateringspaket är kumulativ och kräver tidigare uppdateringspaketet som ett krav. Om du vill skjuta upp en eller flera uppdateringar du övergripande runtime om du vill hämta till den senaste versionen. 

## <a name="get-support"></a>Få support
Azure-stacken följer samma process för support som Azure. Enterprise-kunder kan följa processen som beskrivs i [hur du skapar en Azure-supportbegäran](/azure/azure-supportability/how-to-create-azure-support-request). Om du är i en molntjänst-providers (CSP)-kund kontaktar du din Kryptografiprovider för support.  Mer information finns i [Azure svar](https://azure.microsoft.com/support/faq/). 


## <a name="next-steps"></a>Nästa steg

- [Hantera uppdateringar i Azure-stacken](azure-stack-updates.md)


