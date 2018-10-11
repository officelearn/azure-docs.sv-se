---
title: Vad är Azure DNS?
description: Översikt över DNS-värdtjänst på Microsoft Azure. Värd för din domän på Microsoft Azure.
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: overview
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: e3e04bf7e35b22a56465810f476323ed217e047a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967633"
---
# <a name="what-is-azure-dns"></a>Vad är Azure DNS?

Azure DNS är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster.

Du kan inte använda Azure DNS för att köpa ett domännamn. För en årlig avgift kan du köpa ett domännamn med hjälp av [Azure Web Apps](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app#buy-the-domain) eller en domännamnsregistrator från tredje part. Dina domäner kan sedan hanteras i Azure DNS för posthantering. Mer information finns i [Delegera en domän till Azure DNS](dns-domain-delegation.md).

Följande funktioner ingår med Azure DNS:

## <a name="reliability-and-performance"></a>Tillförlitlighet och prestanda

DNS-domäner i Azure DNS finns på Azures globala nätverk med DNS-namnservrar. Azure DNS använder anycast-nätverk så att varje DNS-fråga besvaras av den närmast tillgängliga DNS-servern. Detta ger både snabb prestanda och hög tillgänglighet för din domän.

## <a name="security"></a>Säkerhet

Azure DNS-tjänsten bygger på Azure Resource Manager. Därmed får du Resource Manager-funktioner som:

* [rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#access-control) – för att kontrollera vem som har åtkomst till specifika åtgärder för din organisation.

* [aktivitetsloggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#activity-logs) – för att övervaka hur en användare i organisationen ändrar en resurs eller hitta ett fel vid felsökning.

* [resurslåsning](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) – för att låsa en prenumeration, resursgrupp eller resurs för att förhindra att andra användare i organisationen av misstag tar bort eller ändrar viktiga resurser.

Mer information finns i [Så skyddar du DNS-zoner och -poster](dns-protect-zones-recordsets.md). 


## <a name="ease-of-use"></a>Användbarhet

Azure DNS-tjänsten kan hantera DNS-poster för dina Azure-tjänster och kan även tillhandahålla DNS för din externa resurser. Azure DNS är integrerat i Azure-portalen och använder samma autentiseringsuppgifter, supportkontrakt och fakturering som dina andra Azure-tjänster. 

DNS-debitering baseras på det antal DNS-zoner som tillhandahålls i Azure och på antalet DNS-frågor. Mer information om prissättning finns på sidan om [prisinformation för Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

Dina domäner och poster kan hanteras med hjälp av Azure-portalen, Azure PowerShell-cmdlets och plattformsoberoende Azure CLI. Program som kräver automatisk DNS-hantering kan integreras med tjänsten med hjälp av REST API och SDK:er.

## <a name="customizable-virtual-networks-with-private-domains"></a>Anpassningsbara virtuella nätverk med privata domäner

Azure DNS stöder även privata DNS-zoner, som för närvarande är i förhandsversion. På så sätt kan du använda dina egna anpassade domännamn i dina privata virtuella nätverk i stället för de namn som tillhandahålls av Azure och är tillgängliga i dag.

Mer information finns på sidan om att [använda Azure DNS för privata domäner](private-dns-overview.md).

## <a name="alias-records"></a>Aliasposter

Azure DNS stöder aliaspostuppsättningar. Du kan använda en aliaspostuppsättning för att referera till en Azure-resurs, till exempel en offentlig IP-adress i Azure eller en Traffic Manager-profil. Om IP-adressen för den underliggande resursen ändras uppdateras aliaspostuppsättningen sömlöst under DNS-matchningen. Aliaspostuppsättningen pekar på tjänstinstansen och tjänstinstansen associeras med en IP-adress. 

Dessutom kan du nu peka din apex eller en domän utan www (t.ex. contoso.com) mot en Traffic Manager-profil med hjälp av en aliaspost.

Mer information finns i [översikten över Azure DNS-aliasposter](dns-alias.md).


## <a name="next-steps"></a>Nästa steg

* Läs mer om DNS-zoner och poster: [översikt över DNS-zoner och poster](dns-zones-records.md).

* Lär dig hur du skapar en zon i Azure DNS: [Skapa en DNS-zon](./dns-getstarted-create-dnszone-portal.md).

* Vanliga frågor om Azure DNS finns i [vanliga frågor och svar för Azure DNS](dns-faq.md).

