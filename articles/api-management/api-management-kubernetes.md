---
title: Använda Azure API Management med mikrotjänster som distribueras i Azure Kubernetes-tjänsten | Microsoft-dokument
description: I den här artikeln beskrivs alternativen för att distribuera API Management med AKS
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 1d6773b4daac256234c33bf50fb3736d585ac505
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75481001"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Använda Azure API Management med mikrotjänster som distribueras i Azure Kubernetes Service

Mikrotjänster är perfekta för att bygga API:er. Med [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS) kan du snabbt distribuera och driva en [mikrotjänstbaserad arkitektur](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices) i molnet. Du kan sedan använda [Azure API Management](https://aka.ms/apimrocks) (API Management) för att publicera dina mikrotjänster som API:er för intern och extern förbrukning. I den här artikeln beskrivs alternativen för att distribuera API Management med AKS. Det förutsätter grundläggande kunskaper om Kubernetes, API Management och Azure-nätverk. 

## <a name="background"></a>Bakgrund

När du publicerar mikrotjänster som API:er för förbrukning kan det vara svårt att hantera kommunikationen mellan mikrotjänsterna och klienterna som förbrukar dem. Det finns en mängd övergripande problem som autentisering, auktorisering, begränsning, cachelagring, omvandling och övervakning. Dessa problem gäller oavsett om mikrotjänsterna är exponerade för interna eller externa klienter. 

[API Gateway-mönstret](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) åtgärdar dessa problem. En API-gateway fungerar som en ytterdörr till mikrotjänsterna, frikopplar klienter från dina mikrotjänster, lägger till ytterligare ett säkerhetslager och minskar komplexiteten i dina mikrotjänster genom att ta bort bördan av att hantera korsskärningsproblem. 

[Azure API Management](https://aka.ms/apimrocks) är en nyckelfärdig lösning för att lösa dina API-gatewaybehov. Du kan snabbt skapa en konsekvent och modern gateway för dina mikrotjänster och publicera dem som API:er. Som en fullständig API-hanteringslösning för livscykel ger den också ytterligare funktioner, inklusive en självbetjäningsutvecklare för API-identifiering, API-livscykelhantering och API-analys.

När AKS och API Management används tillsammans finns en plattform för distribution, publicering, säkring, övervakning och hantering av mikrotjänstbaserade API:er. I den här artikeln kommer vi att gå igenom några alternativ för att distribuera AKS tillsammans med API Management. 

## <a name="kubernetes-services-and-apis"></a>Kubernetes tjänster och API:er

I ett Kubernetes-kluster distribueras behållare i [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/), som är tillfälliga och har en livscykel. När en arbetarnod dör försvinner poddar som körs på noden. Därför kan IP-adressen för en pod ändras när som helst. Vi kan inte lita på att den kommunicerar med kapseln. 

För att lösa detta problem introducerade Kubernetes begreppet [Tjänster](https://kubernetes.io/docs/concepts/services-networking/service/). En Kubernetes-tjänst är ett abstraktionslager som definierar en logikgrupp av poddar och möjliggör extern trafikexponering, belastningsutjämning och tjänstidentifiering för dessa poddar. 

När vi är redo att publicera våra mikrotjänster som API:er via API Management måste vi tänka på hur vi mappar våra tjänster i Kubernetes till API:er i API Management. Det finns inga fastställda regler. Det beror på hur du har utformat och partitionerat dina affärsfunktioner eller domäner i mikrotjänster i början. Om poddar bakom en tjänst till exempel är ansvariga för alla åtgärder på en viss resurs (t.ex. kund) kan Tjänsten mappas till ett API. Om åtgärder på en resurs delas in i flera mikrotjänster (t.ex. GetOrder, PlaceOrder) kan flera tjänster logiskt aggregeras till ett enda API i API-hantering (se fig. 1). 

Mappningarna kan också utvecklas. Eftersom API Management skapar en fasad framför mikrotjänsterna, gör det att vi kan refactor och rätt storlek våra mikrotjänster över tiden. 

![Mappa tjänster till API:er](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Distribuera API-hantering framför AKS

Det finns några alternativ för att distribuera API Management framför ett AKS-kluster. 

Ett AKS-kluster distribueras alltid i ett virtuellt nätverk (VNet), men en API Management-instans behöver inte distribueras i ett virtuellt nätverk. När API Management inte finns i klustretS virtuella nätverk måste AKS-klustret publicera offentliga slutpunkter för API Management att ansluta till. I så fall finns det ett behov av att skydda anslutningen mellan API Management och AKS. Med andra ord måste vi se till att klustret endast kan nås exklusivt via API Management. Låt oss gå igenom alternativen. 

### <a name="option-1-expose-services-publicly"></a>Alternativ 1: Exponera tjänster offentligt

Tjänster i ett AKS-kluster kan exponeras offentligt med hjälp av [tjänsttyper](https://docs.microsoft.com/azure/aks/concepts-network) av NodePort, LoadBalancer eller ExternalName. I det här fallet är tjänsterna tillgängliga direkt från offentligt internet. När du har distribuerat API Management framför klustret måste vi se till att all inkommande trafik går via API Management genom att använda autentisering i mikrotjänsterna. API Management kan till exempel innehålla en åtkomsttoken i varje begäran som görs till klustret. Varje mikrotjänst ansvarar för att validera token innan begäran bearbetas. 


Detta kan vara det enklaste alternativet för att distribuera API Management framför AKS, särskilt om du redan har autentiseringslogik implementerad i dina mikrotjänster. 

![Publicera tjänster direkt](./media/api-management-aks/direct.png)

Proffsen:
* Enkel konfiguration på API Management-sidan eftersom den inte behöver injiceras i klustretS virtuella nätverk
* Ingen ändring på AKS-sidan om tjänsterna redan är exponerade offentligt och autentiseringslogik redan finns i mikrotjänster

Nackdelar:
* Potentiell säkerhetsrisk på grund av allmänhetens synlighet för tjänstslutpunkter
* Ingen enskild startpunkt för inkommande klustertrafik
* Komplicerar mikrotjänster med dubblettautentiseringslogik

### <a name="option-2-install-an-ingress-controller"></a>Alternativ 2: Installera en ingress controller

Även alternativ 1 kan vara lättare, det har anmärkningsvärda nackdelar som nämnts ovan. Om en API Management-instans inte finns i klustretS VNet är ömsesidig TLS-autentisering (mTLS) ett robust sätt att säkerställa att trafiken är säker och betrodd i båda riktningarna mellan en API Management-instans och ett AKS-kluster. 

Ömsesidig TLS-autentisering [stöds internt](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) av API Management och kan aktiveras i Kubernetes genom [att installera en ingressstyrenhet](https://docs.microsoft.com/azure/aks/ingress-own-tls) (fig. 3). Därför utförs autentisering i ingress controller, vilket förenklar mikrotjänsterna. Dessutom kan du lägga till IP-adresserna för API Management i den tillåtna listan av Ingress för att se till att endast API Management har åtkomst till klustret.  

 
![Publicera via en ingress controller](./media/api-management-aks/ingress-controller.png)


Proffsen:
* Enkel konfiguration på API Management-sidan eftersom den inte behöver injiceras i klustret VNet och mTLS stöds internt
* Centraliserar skydd för inkommande klustertrafik vid ingress Controller-lagret
* Minskar säkerhetsrisken genom att minimera offentligt synliga klusterslutpunkter

Nackdelar:
* Ökar komplexiteten i klusterkonfigurationen på grund av extra arbete för att installera, konfigurera och underhålla ingressstyrenheten och hantera certifikat som används för mTLS
* Säkerhetsrisk på grund av allmänhetens synlighet för ingångskontrollantens slutpunkter


När du publicerar API:er via API Management är det enkelt och vanligt att skydda åtkomsten till dessa API:er med hjälp av prenumerationsnycklar. Utvecklare som behöver använda de publicerade API:erna måste inkludera en giltig prenumerationsnyckel i HTTP-begäranden när de ringer anrop till dessa API:er. Annars avvisas anropen omedelbart av API Management-gatewayen. De vidarebefordras inte till backend-tjänsterna.

För att få en prenumerationsnyckel för åtkomst till API:er krävs en prenumeration. En prenumeration är i huvudsak en namngiven behållare för ett par prenumerationsnycklar. Utvecklare som behöver använda de publicerade API:erna kan få prenumerationer. Och de behöver inte godkännande från API-utgivare. API-utgivare kan också skapa prenumerationer direkt för API-konsumenter.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Alternativ 3: Distribuera APIM i klustretS virtuella nätverk

I vissa fall kan kunder med lagstadgade begränsningar eller strikta säkerhetskrav hitta alternativ 1 och 2 inte genomförbara lösningar på grund av offentligt exponerade slutpunkter. I andra kan AKS-klustret och de program som använder mikrotjänsterna finnas inom samma virtuella nätverk, varför det inte finns någon anledning att exponera klustret offentligt eftersom all API-trafik kommer att finnas kvar i det virtuella nätverket. I dessa scenarier kan du distribuera API-hantering till klustretS virtuella nätverk. [API Management Premium-nivån](https://aka.ms/apimpricing) stöder VNet-distribution. 

Det finns två lägen [för att distribuera API Management till ett virtuella nätverk](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) – externt och internt. 

Om API-konsumenter inte finns i klustrets virtuella nätverk ska det externa läget (fig. 4) användas. I det här läget injiceras API Management-gatewayen i klustretS virtuella nätverk men är tillgänglig från offentligt internet via en extern belastningsutjämnare. Det hjälper till att dölja klustret helt och samtidigt tillåta externa klienter att använda mikrotjänster. Dessutom kan du använda Azure-nätverksfunktioner som NSG (Network Security Groups) för att begränsa nätverkstrafiken.

![Externt VNet-läge](./media/api-management-aks/vnet-external.png)

Om alla API-konsumenter finns i klustrets virtuella nätverk kan det interna läget (fig. 5) användas. I det här läget injiceras API Management-gatewayen i klustretS VNET och är endast tillgänglig inifrån det här virtuella nätverket via en intern belastningsutjämnare. Det finns inget sätt att nå API Management gateway eller AKS-klustret från offentligt internet. 

![Internt VNet-läge](./media/api-management-aks/vnet-internal.png)

 I båda fallen är AKS-klustret inte offentligt synligt. Jämfört med alternativ 2 kanske ingressstyrenheten inte är nödvändig. Beroende på ditt scenario och konfiguration kan det fortfarande krävas autentisering mellan API Management och dina mikrotjänster. Om till exempel ett servicenät används krävs alltid ömsesidig TLS-autentisering. 

Proffsen:
* Det säkraste alternativet eftersom AKS-klustret inte har någon offentlig slutpunkt
* Förenklar klusterkonfiguration eftersom den inte har någon offentlig slutpunkt
* Möjlighet att dölja både API Management och AKS i det virtuella nätverket med hjälp av internt läge
* Möjlighet att styra nätverkstrafiken med hjälp av Azure-nätverksfunktioner som NSG (Network Security Groups)

Nackdelar:
* Ökar komplexiteten i att distribuera och konfigurera API Management för att fungera i det virtuella nätverket

## <a name="next-steps"></a>Nästa steg

* Läs mer om [nätverkskoncept för program i AKS](https://docs.microsoft.com/azure/aks/concepts-network)
* Läs mer om [hur du använder API Management med virtuella nätverk](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)





