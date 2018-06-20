---
title: Azure Integration Services-Referensarkitektur
description: Referensarkitektur som visar hur du implementerar en enterprise integration-mönster med Logic Apps, API-hantering, Service Bus och händelsen rutnätet
author: mattfarm
manager: jonfan
editor: ''
services: logic-apps api-management
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/15/2018
ms.author: LADocs; estfan
ms.openlocfilehash: db3350b3c70f6e6f35949e8423334061849b7b37
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232588"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>Enterprise integration med köer och händelser: referera arkitektur

## <a name="overview"></a>Översikt

Denna Referensarkitektur visar en uppsättning beprövade metoder för ett integration program som använder Azure Integration Services. Den här arkitekturen kan fungera som grundval av många olika program mönster som kräver HTTP APIs, arbetsflöde och orchestration.

*Det finns många möjliga program av teknik, från en enkel punkt-till-platsen program till en fullständig enterprise service bus. Arkitektur serien anges de återanvändbara delarna för att skapa något integration program – arkitekter bör de komponenter som de behöver för sina program och infrastrukturer.*

![Arkitekturdiagram - enterprise integration med köer och händelser](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>Arkitektur

Den här arkitekturen bygger på det som visas på [enkel enterprise integration](logic-apps-architectures-simple-enterprise-integration.md). Den har följande komponenter:

- Resursgrupp. En resursgrupp är en logisk behållare för Azure-resurser.
- Azure API Management. Azure API Management är en helt hanterad plattform för att publicera, skydda och omvandla HTTP APIs.
- Azure API Management Developer-portalen. Varje instans av Azure API Management levereras med en Developer-portalen som ger åtkomst till dokumentation, kodexempel och möjlighet att testa en API.
- Azure Logic Apps. Logic Apps är en serverlösa plattform för att skapa enterprise arbetsflödet och integrering.
- Kopplingar. Kopplingar som används av Logic Apps för att ansluta till tjänster som ofta används. Logic Apps har redan hundratals olika kopplingar, men de kan även skapas med hjälp av en anpassad koppling.
- Azure Service Bus. Service Bus innehåller säkert och tillförlitligt meddelanden. Meddelanden kan användas för att ta bort ihop program från varandra och integrera med andra meddelande-baserade system.
- Azure händelse rutnät. Händelsen rutnätet är en serverlösa plattform för att publicera och leverera programhändelser.
- IP-adress. Azure API Management-tjänsten har en fast offentlig IP-adress och ett domännamn. Domännamnet är en underdomän till azure-api.net, till exempel contoso.azure api.net. Logic Apps och Service Bus också ha en offentlig IP-adress; i den här arkitekturen kan vi dock begränsa åtkomsten för att anropa Logic apps slutpunkter för endast IP-adress för API Management (för säkerhet). Anrop till Service Bus skyddas av en signatur för delad åtkomst.
- Azure DNS. Azure DNS är värdtjänsten för DNS-domäner som tillhandahåller namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster. Skapa DNS-poster som matchar det anpassade domännamnet till IP-adressen om du vill använda ett anpassat domännamn (t.ex contoso.com). Mer information finns i Konfigurera ett anpassat domännamn i Azure API Management.
- Azure Active Directory (AD Azure). Använd Azure AD eller en annan identitetsleverantör för autentisering. Azure AD tillhandahåller autentisering för åtkomst till API-slutpunkter (genom att skicka en JSON Web Token för API-hantering att verifiera) och kan säker åtkomst till API Management Developer Portal (endast Standard- och Premium-nivåer).

Den här arkitekturen har vissa grundläggande mönster med driften:

1. Befintlig serverdel HTTP APIs publiceras via API Management Developer-portalen, vilket gör att utvecklare (antingen interna för din organisation, externt eller båda) att integrera anrop till dessa API: er i program.
2. Sammansatta API: er skapas med hjälp av Logic Apps; samordna anrop till SAAS-system, Azure-tjänster och alla API: er som publicerats till API-hantering. Logic Apps publiceras också via API Management Developer-portalen.
3. Program får en säkerhetstoken för OAuth 2.0 krävs för att få åtkomst till en API som använder Azure Active Directory.
4. Azure API Management verifierar säkerhetstoken och skickar begäran till backend API/Logikapp.
5. Service Bus-köer för att frikoppla programmet aktivitet och smooth ökad belastning. Meddelanden läggs till köer med Logic Apps 3 partsprogram eller (inte avbildas) genom att publicera kön som en HTTP-API via API-hantering.
6. Händelsen utlöses när meddelanden läggs till en Service Bus-kö. En Logikapp utlöses av den här händelsen och bearbetar meddelandet.
7. På liknande sätt kan publicera andra Azure-tjänster (t.ex. Blob Storage, Event Hub) även händelser i händelsen rutnätet. Dessa utlösa Logic Apps att ta emot händelsen och utföra efterföljande åtgärder.

## <a name="recommendations"></a>Rekommendationer

Dina krav kan vara annorlunda från den arkitektur som beskrivs här. Använd rekommendationerna i det här avsnittet som en utgångspunkt.

### <a name="service-bus-tier"></a>Service Bus-nivå

Använda Service Bus premium-nivån som det för närvarande stöder rutnätet händelsemeddelanden (stöd över alla nivåer förväntas). Se [priser för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Priser för händelsen rutnätet

Händelsen rutnätet fungerar med hjälp av en serverlösa modell – fakturering beräknas baserat på antalet åtgärder (händelsekörningen). Se [händelse rutnätet priser](https://azure.microsoft.com/pricing/details/event-grid/) för mer information. Det finns inga nivå överväganden för händelsen rutnät.

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>Använd PeekLock när förbruka Service Bus-meddelanden

När du skapar Logic Apps att använda Service Bus-meddelanden använder [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) i Logikappen åtkomst till en grupp av meddelanden. Logikappen kan utföra stegen för att verifiera varje meddelande innan du slutför eller överges. Den här metoden skyddar mot förlust av misstag meddelandet.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Sök efter flera objekt när en händelse rutnätet utlösare utlöses

Rutnätet händelseutlösare startar innebär helt enkelt som ”minst 1 detta inträffade”. Anta till exempel när händelsen rutnätet utlöser en logikapp på ett meddelande som visas i en Service Bus-kö, logikappen bör alltid det kan finnas ett eller flera meddelanden som finns tillgängliga för bearbetning.

### <a name="region"></a>Region

Etablera API-hantering, Logic Apps och Service Bus i samma region för att minimera svarstider i nätverket. Du väljer normalt den region som är närmast dina användare.

Resursgruppen har också en region som anger var metadata för programdistribution lagras, och där distributionsmallen körs från. Placera resursgruppen och dess resurser i samma region. Detta kan förbättra tillgängligheten under distributionen.

## <a name="scalability-considerations"></a>Skalbarhetsöverväganden

Azure Service Bus premium-nivån kan skalbar antalet meddelandefunktionsenheter att uppnå högre skalbarhet. Premium kan ha 1, 2 eller 4 messaging enheter. Mer information om skalning Azure Service Bus finns [bästa praxis för bättre prestanda med hjälp av Service Bus-meddelanden](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability-considerations"></a>Överväganden för tillgänglighet

Vid tidpunkten för skrivning är servicenivåavtalet (SLA) för Azure API Management 99,9% för nivåerna Basic, Standard och Premium. Premium-nivån konfigurationer med distribution av minst en enhet i två eller fler regioner har ett SLA på 99,95%.

Servicenivåavtal (SLA) för Azure Logic Apps är 99,9% vid tidpunkten för skrivning.

### <a name="disaster-recovery"></a>Haveriberedskap

Överväg att implementera Geo katastrofåterställning i Service Bus premium för att aktivera redundans vid ett allvarligt avbrott. Läs mer om [Azure Service Bus Geo-återställning](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability-considerations"></a>Överväganden för hantering

Skapa separata resursgrupper för produktion, utveckling, och testa miljöer. Det här gör det enklare att hantera distributioner, ta bort testdistributioner och tilldela åtkomsträttigheter.
Tänk på följande när du tilldelar resurser till resursgrupper:

- Livscykel. I allmänhet ska du placera resurser med samma livscykel i samma resursgrupp.
- Åtkomst. Du kan använda [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC) för att tillämpa principer för åtkomst till resurser i en grupp.
- Fakturering. Du kan visa de samlade kostnaderna för resursgruppen.
- Prisnivån för API Management – bör du använda Developer nivån för utvecklings- och testmiljöer. För Förproduktion rekommenderar vi distribuera en replik av produktionsmiljön, köra tester och stänger sedan för att minimera kostnaderna.

Mer information finns i [resursgruppen](../azure-resource-manager/resource-group-overview.md) Översikt.

### <a name="deployment"></a>Distribution

Vi rekommenderar att du använder [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md) att distribuera Azure API Management, Logic Apps, händelse rutnätet och Service Bus. Mallarna gör det enklare att automatisera distribution via PowerShell eller Azure-kommandoradsgränssnittet (CLI).

Vi rekommenderar att placera Azure API Management alla enskilda Logic Apps, händelse rutnätet avsnitt och Service Bus-namnområden i sina egna separata Resource Manager-mallar. Detta gör att lagra dem i källan kontrollsystem för. Dessa mallar kan sedan distribueras tillsammans eller var för sig som en del av en Distributionsprocess för kontinuerlig integration/kontinuerlig (CI/CD).

### <a name="diagnostics-and-monitoring"></a>Diagnostik och övervakning

Service Bus som API Management och Logic Apps kan övervakas med hjälp av Azure-Monitor. Azure övervakaren är aktiverad som standard och ger information baserat på de olika mätvärden som är konfigurerad för varje tjänst.

## <a name="security-considerations"></a>Säkerhetsöverväganden

Skydda Service Bus med hjälp av en signatur för delad åtkomst. [SAS-autentisering](../service-bus-messaging/service-bus-sas.md) gör att du kan ge en användaråtkomst till Service Bus-resurser med specifika rättigheter. Läs mer om [Service Bus-autentisering och auktorisering](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Dessutom kan en Service Bus-kö behöver visas som en HTTP-slutpunkt (för att tillåta bokföring av nya meddelanden) API-hantering som ska användas för att skydda den med fronting slutpunkten. Detta kan sedan skyddas med certifikat eller OAuth efter behov. Det enklaste sättet att göra detta använder en Logikapp med en HTTP-begäranden/svar-utlösare som en mellanhand.

Händelsen rutnätet säkrar händelse leverans via en verifieringskoden. Om du använder LogicApps förbruka händelsen görs detta automatiskt. Se mer information [händelse rutnätet säkerhets- och autentiseringstjänster](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Nästa steg

* [Enkel Enterprise Integration](logic-apps-architectures-simple-enterprise-integration.md)
