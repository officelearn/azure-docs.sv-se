---
title: Distributionsproblem för vanliga frågor om Microsoft Azure Cloud Services | Microsoft Docs
description: Den här artikeln innehåller vanliga frågor om Microsoft Azure Cloud Services-distribution.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: adaed4c7af1d325f85d6fc349ac9a4faf73c1169
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078829"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Distributionsproblem för Azure Cloud Services: Vanliga frågor och svar (FAQ)

Den här artikeln innehåller vanliga frågor om distributionsproblem [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Du kan också läsa den [VM-storlek för Cloud Services-sidan](cloud-services-sizes-specs.md) storlek information.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Varför distribuerar en tjänst i molnet till mellanlagringsplatsen Ibland misslyckas med ett resursallokeringsfel om det finns redan en befintlig distribution på produktionsplatsen?
Om en tjänst i molnet har en distribution i antingen fack, fästa hela Molntjänsten på ett specifikt kluster. Det innebär att om en distributionen finns redan på produktionsplatsen, en ny distribution för mellanlagring kan endast allokeras i samma kluster som produktionsplatsen.

Allokeringsfel inträffa när klustret där Molntjänsten finns inte har tillräckligt med fysiska beräkningsresurser för att uppfylla begäran om din distribution.

Hjälp med att minimera sådana Allokeringsfel finns i [Allokeringsfel för Cloud Service: Lösningar](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Varför fungerar skala upp eller skala ut en molntjänstdistribution ibland resultera i Allokeringsfel?
När en molnbaserad tjänst har distribuerats kan hämtar det vanligtvis fästa på ett specifikt kluster. Det innebär att skala in/ut en befintlig molntjänst måste tilldela nya instanser i samma kluster. Om klustret närmar sig kapacitetsgränsen eller önskade VM-storlek/typ är inte tillgänglig, misslyckas begäran.

Hjälp med att minimera sådana Allokeringsfel finns i [Allokeringsfel för Cloud Service: Lösningar](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Varför distribuerar en tjänst i molnet till en tillhörighetsgrupp ibland resulterar i Allokeringsfel?
En ny distribution till en tom molntjänst kan allokeras av infrastrukturresurser i alla kluster i den regionen, såvida inte Molntjänsten som är fäst på en tillhörighetsgrupp. Distributioner till samma tillhörighetsgrupp görs i samma kluster. Om klustret närmar sig kapacitetsgränsen, misslyckas begäran.

Hjälp med att minimera sådana Allokeringsfel finns i [Allokeringsfel för Cloud Service: Lösningar](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Varför ändra VM-storlek eller lägga till en ny virtuell dator till en befintlig molntjänst ibland resulterar i Allokeringsfel?
Kluster i ett datacenter kan ha olika konfigurationer för typer av virtuella datorer (t.ex. en serien, Av2-serien, D-serien, Dv2-serien, G-serien, H-serien osv.). Men inte alla kluster behöver nödvändigtvis alla typer av virtuella datorer. Till exempel om du försöker lägga till D-serien VM till en molnbaserad tjänst som redan har distribuerats i en A-serien endast kluster får ett Allokeringsfel. Detta kommer också inträffa om du försöker ändra VM SKU-storlekar (till exempel byta från en A-serien till en D-serien).

Hjälp med att minimera sådana Allokeringsfel finns i [Allokeringsfel för Cloud Service: Lösningar](cloud-services-allocation-failures.md#solutions).

Du kan kontrollera storlekarna som finns i din region [Microsoft Azure: Produkttillgänglighet per region](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Varför distribuerar en tjänst i molnet en stund misslyckas på grund av begränsningar/kvoter/begränsningar i mitt abonnemang eller tjänst?
Distribution av en molnbaserad tjänst kan misslyckas om de resurser som krävs för att allokera överskrider standard eller maximal kvot som tillåts för din tjänst på nivån region/datacenter. Mer information finns i [molntjänster begränsar](../azure-subscription-service-limits.md#azure-cloud-services-limits).

Du kan också spåra aktuella användningskvot för din prenumeration på portalen: Azure-portalen = > prenumerationer = > \<lämpliga prenumeration > = > ”användning + kvoter”.

Användning/förbrukning-relaterade resursinformation kan också hämtas via Azure Billing API: erna. Se [Azure-Resursanvändning API (förhandsversion)](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Hur kan jag ändra storleken på en distribuerad molntjänst för virtuell dator utan att omdistribuera det?
Du kan inte ändra VM-storleken för en distribuerad molntjänst utan att omdistribuera den. VM-storleken är inbyggt i CSDEF, vilket kan bara uppdateras med en omdistribution.

Mer information finns i [så här uppdaterar du en molnbaserad tjänst](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Varför kan jag inte distribuera molntjänster via Service Management API: er eller PowerShell när du använder Azure Resource Manager-lagringskonto? 

Eftersom Cloud-Service är en klassisk resurs som inte är direkt kompatibla med Azure Resource Manager-modellen, kan du associera den med Azure Resource Manager-lagringskonton. Här följer några alternativ: 
 
- Distribuera via REST-API.

    När du distribuerar via Service Management REST API, kan du komma runt begränsningen genom att ange en SAS-URL till blob-lagring, som fungerar med både klassiska och Resource Manager-lagring för Azure-konto. Läs mer om egenskapen 'PackageUrl' [här](https://msdn.microsoft.com/library/azure/ee460813.aspx).
  
- Distribuera via [Azure-portalen](https://portal.azure.com).

    Detta fungerar från den [Azure-portalen](https://portal.azure.com) eftersom anropet genomgår en proxy/shim som tillåter kommunikation mellan resurser i Azure Resource Manager och klassisk. 
 
## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Varför kräver Azure-portalen mig att tillhandahålla ett storage-konto för distribution? 

Paketet har överförts till API hanteringslager direkt i den klassiska portalen och API-läger skulle tillfälligt sätts sedan paketet till en intern storage-konto.  Den här processen orsakar problem med prestanda och skalbarhet eftersom API-lager inte har utformats för att vara en tjänst för uppladdning av filen.  I Azure portal (Resource Manager-distributionsmodellen), har vi kringgås mellanliggande steg första överför i API-lagret, vilket ger snabbare och mer tillförlitlig distributioner. 

Det är mycket liten när det gäller kostnaden, och du kan återanvända samma lagringskonto i alla distributioner. Du kan använda den [kostnaden lagringsberäknaren](https://azure.microsoft.com/pricing/calculator/#storage1) ladda ned CSPKG för att fastställa kostnaden för att ladda upp service-paketet (CSPKG), och sedan ta bort CSPKG. 
