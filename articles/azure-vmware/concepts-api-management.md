---
title: Koncept – API Management
description: 'Lär dig hur API Management skyddar API: er som körs på virtuella Azure VMware-lösningar (virtuella datorer)'
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 62112bf3c0bf551232e09e5910e3eaae228dc202
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85306950"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-avs-based-vms"></a>API Management att publicera och skydda API: er som körs på AVS-baserade virtuella datorer

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) låter utvecklare och DevOps-team publicera på ett säkert sätt antingen till interna eller externa konsumenter.

Även om det finns flera SKU: er är det bara utvecklare och Premium-SKU: er som tillåter Azure Virtual Network-integrering att publicera API: er som körs på Azures arbets belastningar för VMware-lösningar. Dessa två SKU: er aktiverar säker anslutning mellan API Management tjänst och Server delen. Developer SKU är avsedd för utveckling och testning medan Premium-SKU: n är avsedd för produktions distributioner.

För Server dels tjänster som körs ovanpå virtuella AVS-datorer (VM: ar) är konfigurationen i API Management som standard densamma som för lokala server dels tjänster. För både interna och externa distributioner konfigurerar API Management den virtuella IP-adressen (VIP) för belastningsutjämnaren som backend-slutpunkt när backend-servern placeras bakom en NSX Load Balancer på AVS-sidan.

## <a name="external-deployment"></a>Extern distribution

En extern distribution publicerar API: er som används av externa användare med hjälp av en offentlig slut punkt. Utvecklare och DevOps-tekniker kan hantera API: er via Azure Portal eller PowerShell och API Management Developer-portalen.

Det externa distributions diagrammet visar hela processen och de aktörer som ingår (visas överst). Aktörerna är:

- **Administratör (er):** Representerar administratörs-eller DevOps-teamet som hanterar AVS genom Azure Portal-och automation-mekanismer som PowerShell eller Azure DevOps.

- **Användare:**  Representerar användare av exponerade API: er och representerar både användare och tjänster som konsumerar API: erna.

Trafikflödet går genom API Management instans, som sammanfattar backend-tjänsterna, anslutna till hubbens virtuella nätverk. ExpressRoute-gatewayen dirigerar trafiken till ExpressRoute Global Reachs kanal och når en NSX Load Balancer distribuerar inkommande trafik till de olika instanserna för Server dels tjänster.

API Management har ett offentligt Azure-API och aktivera Azure DDOS Protection Service rekommenderas. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Extern distribution – API Management för AVS":::


## <a name="internal-deployment"></a>Intern distribution

En intern distribution publicerar API: er som används av interna användare eller system. DevOps team-och API-utvecklare använder samma hanterings verktyg och utvecklings portal som i den externa distributionen.

Interna distributioner kan vara [med Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) för att skapa en offentlig och säker slut punkt för API: et som utnyttjar funktionerna och skapar en hybrid distribution som möjliggör olika scenarier.  API: et utnyttjar funktionerna och skapar en hybrid distribution som möjliggör olika scenarier.

* Använd samma API Management-resurs för användning av både interna och externa konsumenter.

* Ha en enda API Management resurs med en delmängd av API: er definierade och tillgängliga för externa användare.

* Ger ett enkelt sätt att byta åtkomst till API Management från det offentliga Internet på och av.

I distributions diagrammet nedan visas konsumenter som kan vara interna eller externa, och varje typ använder samma eller olika API: er.

I en intern distribution kommer API: er att exponeras för samma API Management-instans. Framför API Management, Application Gateway distribueras med WAF-funktionen (Azure Web Application Firewall) aktive rad och en uppsättning HTTP-lyssnare och regler för att filtrera trafiken, exponera endast en delmängd av Server dels tjänsterna som körs på AVS.

* Intern trafik dirigeras genom ExpressRoute Gateway till Azure-brandväggen och sedan för att API Management om trafik regler upprättas eller direkt till API Management.  

* Extern trafik går in i Azure via Application Gateway, som använder det externa skydds skiktet för API Management.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Intern distribution – API Management för AVS":::