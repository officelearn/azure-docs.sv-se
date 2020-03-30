---
title: Vanliga frågor om distributionsproblem för Microsoft Azure Cloud Services| Microsoft-dokument
description: I den här artikeln visas vanliga frågor om distribution för Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 63a219078927e9001a8eb4085c722e7ec8d2fac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980631"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Distributionsproblem för Azure Cloud Services: Vanliga frågor och svar (vanliga frågor)

Den här artikeln innehåller vanliga frågor och svar om distributionsproblem för [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Du kan också läsa [sidan VM-storlek](cloud-services-sizes-specs.md) för molntjänster för storleksinformation.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Varför misslyckas distributionen av en molntjänst till mellanlagringsplatsen ibland med ett resursallokeringsfel om det redan finns en befintlig distribution i produktionsplatsen?
Om en molntjänst har en distribution i någon av kortplatserna, är hela molntjänsten fäst på ett visst kluster. Det innebär att om det redan finns en distribution i produktionsplatsen kan en ny mellanlagringsdistribution endast allokeras i samma kluster som produktionsplatsen.

Allokeringsfel uppstår när klustret där molntjänsten finns inte har tillräckligt med fysiska beräkningsresurser för att uppfylla din distributionsbegäran.

Mer information om hur du minskar sådana allokeringsfel finns i [Cloud Service allocation failure: Solutions](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Varför leder skalning eller skalning av en molntjänstdistribution ibland till allokeringsfel?
När en molntjänst distribueras fästs den vanligtvis på ett visst kluster. Detta innebär att skala upp/ut en befintlig molntjänst måste allokera nya instanser i samma kluster. Om klustret närmar sig kapacitet eller om önskad vm-storlek/typ inte är tillgänglig kan begäran misslyckas.

Mer information om hur du minskar sådana allokeringsfel finns i [Cloud Service allocation failure: Solutions](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Varför leder distribution av en molntjänst till en tillhörighetsgrupp ibland till allokeringsfel?
En ny distribution till en tom molntjänst kan allokeras av infrastrukturen i alla kluster i den regionen, såvida inte molntjänsten är fäst vid en tillhörighetsgrupp. Distributioner till samma tillhörighetsgrupp görs på samma kluster. Om klustret närmar sig kapacitet kan begäran misslyckas.

Mer information om hur du minskar sådana allokeringsfel finns i [Cloud Service allocation failure: Solutions](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Varför leder byte av vm-storlek eller lägger till en ny virtuell dator till en befintlig molntjänst ibland i allokeringsfel?
Klustren i ett datacenter kan ha olika konfigurationer av maskintyper (till exempel A-serien, Av2-serien, D-serien, Dv2-serien, G-serien, H-serien osv.). Men inte alla kluster nödvändigtvis skulle ha alla typer av virtuella datorer. Om du till exempel försöker lägga till en virtuell dator i D-serien i en molntjänst som redan har distribuerats i ett kluster med endast en serie i A-serien, uppstår ett allokeringsfel. Detta händer också om du försöker ändra VM SKU storlekar (till exempel byta från en A-serie till en D-serie).

Mer information om hur du minskar sådana allokeringsfel finns i [Cloud Service allocation failure: Solutions](cloud-services-allocation-failures.md#solutions).

Information om hur du kontrollerar de storlekar som är tillgängliga i din region finns i [Microsoft Azure: Produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Varför misslyckas distributionen av en molntjänst någon gång på grund av begränsningar/kvoter/begränsningar för min prenumeration eller tjänst?
Distribution av en molntjänst kan misslyckas om de resurser som måste allokeras överskrider standard- eller högsta kvot som tillåts för din tjänst på region-/datacenternivå. Mer information finns i [Molntjänstgränser](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits).

Du kan också spåra den aktuella användningen/kvoten för din prenumeration på portalen: \<Azure portal => Prenumerationer => lämplig prenumeration> => "Användning + kvot".

Information om resursanvändning/förbrukning kan också hämtas via Azure Billing API:er. Se [Azure Resource Usage API (Preview)](../cost-management-billing/manage/usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Hur kan jag ändra storleken på en distribuerad virtuell molntjänst utan att distribuera om den?
Du kan inte ändra den virtuella datorns storlek för en distribuerad molntjänst utan att distribuera om den. Vm-storleken är inbyggd i CSDEF, som endast kan uppdateras med en omdistribution.

Mer information finns i [Så här uppdaterar du en molntjänst](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Varför kan jag inte distribuera molntjänster via API:er för tjänsthantering eller PowerShell när jag använder Azure Resource Manager Storage-konto? 

Eftersom Molntjänsten är en klassisk resurs som inte är direkt kompatibel med Azure Resource Manager-modellen kan du inte associera den med Azure Resource Manager Storage-kontona. Här är några alternativ: 

- Distribuera via REST API.

    När du distribuerar via REST-API för tjänsthantering kan du komma runt begränsningen genom att ange en SAS-URL till blob-lagringen, som fungerar med både Classic- och Azure Resource Manager Storage-konto. Läs mer om egenskapen PackageUrl [här](/previous-versions/azure/reference/ee460813(v=azure.100)).

- Distribuera via [Azure Portal](https://portal.azure.com).

    Detta kommer att fungera från [Azure-portalen](https://portal.azure.com) när samtalet går via en proxy /shims som möjliggör kommunikation mellan Azure Resource Manager och Classic-resurser. 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Varför kräver Azure-portalen att jag tillhandahåller ett lagringskonto för distribution?

I den klassiska portalen laddades paketet upp till api-lagret för hantering direkt och sedan skulle API-lagret tillfälligt placera paketet i ett internt lagringskonto.  Den här processen orsakar prestanda- och skalbarhetsproblem eftersom API-lagret inte har utformats för att vara en filöverföringstjänst.  I Azure-portalen (Resource Manager-distributionsmodellen) har vi gått förbi det interimistiska steget för första uppladdningen till API-lagret, vilket resulterar i snabbare och mer tillförlitliga distributioner.

När det gäller kostnaden är den mycket liten och du kan återanvända samma lagringskonto för alla distributioner. Du kan använda [lagringskostnadskalkylatorn](https://azure.microsoft.com/pricing/calculator/#storage1) för att fastställa kostnaden för att ladda upp servicepaketet (CSPKG), ladda ner CSPKG och sedan ta bort CSPKG.
