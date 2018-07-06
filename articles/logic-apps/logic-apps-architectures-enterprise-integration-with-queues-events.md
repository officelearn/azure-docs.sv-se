---
title: Azure Integration Services-Referensarkitektur
description: Referensarkitektur visar hur du implementerar ett mönster med enterprise-integrering med Logic Apps, API Management, Service Bus och Event Grid
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
ms.openlocfilehash: 7d14bbd587b5086348026c41f6551b10809b939a
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859650"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>Enterprise-integration med köer och händelser: Referensarkitektur

## <a name="overview"></a>Översikt

Denna Referensarkitektur visas en uppsättning beprövade metoder för ett integration-program som använder Azure Integration Services. Den här arkitekturen kan fungera som den här grunden för många olika mönster som kräver HTTP APIs, arbetsflöde och dirigering.

*Det finns många möjliga program av integreringsteknik, från ett enkelt att punkt till punkt-program till en fullständig enterprise service bus. Den här arkitekturen-serien innehåller återanvändbara komponenter som kan vara relevanta för att skapa ett allmänt integration-program – arkitekter bör tänka på vilka specifika kompontener som de behöver du implementera för sina program och infrastruktur.*

![Arkitekturdiagram - enterprise-integration med köer och händelser](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>Arkitektur

Den här arkitekturen **bygger på** den [enkel enterprise-integration](logic-apps-architectures-simple-enterprise-integration.md) arkitektur. Den [enkel enterprise arkitektur rekommendationer](logic-apps-architectures-simple-enterprise-integration.md#recommendations) också gäller här, men har tagits bort från den [rekommendationer](#recommendations) i det här dokumentet av utrymmesskäl. Den har följande komponenter:

- Resursgrupp. En [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) är en logisk behållare för Azure-resurser.
- Azure API Management. [Azure API Management](https://docs.microsoft.com/azure/api-management/) är en fullständigt hanterad plattform för att publicera, skydda och omvandla HTTP APIs.
- Utvecklarportalen i Azure API Management. Varje instans av Azure API Management som levereras med en [Utvecklarportalen](https://docs.microsoft.com/azure/api-management/api-management-customize-styles), vilket ger åtkomst till dokumentation, kodexempel och möjlighet att testa ett API.
- Azure Logic Apps. [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) är en plattform utan server för att skapa enterprise arbetsflödet och integrationskontot.
- Kopplingar. [Kopplingar](https://docs.microsoft.com/azure/connectors/apis-list) används av Logic Apps för att ansluta till tjänster som ofta används. Logic Apps har redan hundratals olika anslutningar, men de kan även skapas med hjälp av en anpassad anslutningsapp.
- Azure Service Bus. [Service Bus](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) erbjuder säker och tillförlitlig meddelandehantering. Meddelanden kan användas för att ta bort koppla program från varandra och integrera med andra meddelande-baserade system.
- Azure Event Grid. [Event Grid](https://docs.microsoft.com/azure/event-grid/overview) är en plattform utan server för att publicera och leverans av programhändelser.
- IP-adress. Azure API Management-tjänsten har en fast offentlig [IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) och ett domännamn. Domännamnet är en underdomän till azure-api.net, till exempel contoso.azure-api.net. Logic Apps och Service Bus också ha en offentlig IP-adress; i den här arkitekturen kan vi dock begränsa åtkomsten för att anropa Logic apps slutpunkter till endast de IP-adress för API Management (för säkerhet). Anrop till Service Bus skyddas av en signatur för delad åtkomst.
- Azure DNS. [Azure DNS](https://docs.microsoft.com/azure/dns/) är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster. Skapa DNS-poster som mappar det anpassade domännamnet till IP-adressen om du vill använda ett anpassat domännamn (t.ex contoso.com). Mer information finns i Konfigurera ett anpassat domännamn i Azure API Management.
- Azure Active Directory (AD Azure). Använd [Azure AD](https://docs.microsoft.com/azure/active-directory/) eller en annan identitetsleverantör för autentisering. Azure AD tillhandahåller autentisering för att komma åt API-slutpunkter (genom att skicka en [JSON Web Token för API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) att verifiera) och kan skydda åtkomst till Utvecklarportalen för API Management (endast Standard och Premium-nivåer).

Den här arkitekturen har vissa grundläggande mönster i dess drift:

1. Befintlig serverdel HTTP APIs publiceras via API Management Developer-portalen, vilket gör att utvecklare (antingen interna för din organisation, externt eller båda) att integrera anrop till dessa API: er i program.
2. Sammansatta API: er skapas med hjälp av Logic Apps; samordna anrop till SAAS-system, Azure-tjänster och alla API: er som publiceras till API Management. Den [Logic Apps publiceras även](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) via Utvecklarportalen för API Management.
3. Program [skaffa ett OAuth 2.0-säkerhetstoken](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) krävs för att få åtkomst till ett API med Azure Active Directory.
4. Azure API Management [verifierar säkerhetstoken](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad), och skickar begäran till API/Logic App-serverdelen.
5. Service Bus-köer är vana vid [frikoppla](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) programaktivitet och [jämna ut toppar i belastningen](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Meddelanden läggs till köer av Logic Apps, 3 partsprogram, eller (visas inte) genom att publicera kö som ett HTTP-API via API Management.
6. När meddelanden läggs till i en Service Bus-kö, utlöser en händelse. En Logikapp som utlöses av den här händelsen och bearbetar meddelandet.
7. På samma sätt kan publicera andra Azure-tjänster (t.ex. Blob Storage, Event Hub) även händelser till Event Grid. Dessa utlösa Logikappar för att ta emot händelsen och utföra efterföljande åtgärder.

## <a name="recommendations"></a>Rekommendationer

Dina specifika krav kan skilja sig från den allmänna arkitektur som beskrivs här. Använd rekommendationerna i det här avsnittet som en utgångspunkt.

### <a name="service-bus-tier"></a>Service Bus-nivå

Använda Service Bus premium-nivån som fungerar med event grid-meddelanden för närvarande (support oavsett nivå förväntas). Se [priser för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Priser för Event Grid

Event Grid fungerar med hjälp av en serverlös modell – debiteringen beräknas baserat på antalet åtgärder (händelsekörningen). Se [priser för Event Grid](https://azure.microsoft.com/pricing/details/event-grid/) för mer information. Det finns inga nivån överväganden för Event Grid.

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>Använd PeekLock när Service Bus-meddelanden

När du skapar Logikappar för att använda Service Bus-meddelanden använder [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) i Logikappen att få åtkomst till en grupp med meddelanden. Logikappen kan sedan utföra stegen för att verifiera varje meddelande innan slutföra eller lämna. Den här metoden skyddar mot oavsiktlig meddelandet går förlorade.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Sök efter flera objekt när en Event Grid-utlösaren utlöses

Event Grid-utlösare aktiveringen bara innebär det att ”minst 1 detta hände”. Till exempel när Event Grid utlöser en logikapp på ett meddelande som visas i en Service Bus-kö, logikappen bör utgå alltid från att det kan finnas ett eller flera meddelanden som är tillgängliga för att bearbeta.

### <a name="region"></a>Region

Etablera API Management, Logic Apps och Service Bus i samma region för att minimera nätverkssvarstiden. Du väljer normalt den region som är närmast dina användare.

Resursgruppen har också en region som anger var metadata för distribution lagras, och där distributionsmallen körs från. Placera resursgruppen och dess resurser i samma region. Detta kan förbättra tillgängligheten under distributionen.

## <a name="scalability-considerations"></a>Skalbarhetsöverväganden

Azure Service Bus premium-nivån kan skala ut antalet meddelandefunktionsenheter att uppnå högre skalbarhet. Premium kan ha 1, 2 eller 4 meddelandefunktionsenheter. Ytterligare vägledning om att skala Azure Service Bus finns i [Metodtips för prestandaförbättringar med hjälp av Service Bus-meddelanden](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability-considerations"></a>Överväganden för tillgänglighet

I det här dokumentet skrivs är serviceavtalet (SLA) för Azure API Management 99,9% för nivåerna Basic, Standard och Premium. Premium-nivån konfigurationer med distribution av minst en enhet i två eller fler regioner har inget eget serviceavtal på 99,95%.

I det här dokumentet skrivs är serviceavtalet (SLA) för Azure Logic Apps 99,9%.

### <a name="disaster-recovery"></a>Haveriberedskap

Överväg att implementera Geo-haveriberedskap i Service Bus premium för att aktivera redundans vid ett allvarligt strömavbrott. Läs mer om [Azure Service Bus geohaveriberedskap](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability-considerations"></a>Överväganden för hantering

Skapa separata resursgrupper för produktion, utveckling och testmiljöer. Det här gör det enklare att hantera distributioner, ta bort testdistributioner och tilldela åtkomsträttigheter.
Tänk på följande när du tilldelar resurser till resursgrupper:

- Livscykel. I allmänhet ska du placera resurser med samma livscykel i samma resursgrupp.
- Åtkomst. Du kan använda [Role-Based Access Control](../role-based-access-control/overview.md) (RBAC) för att tillämpa principer för åtkomst till resurser i en grupp.
- Fakturering. Du kan visa de samlade kostnaderna för resursgruppen.
- Prisnivå för API Management – vi rekommenderar att du använder Developer-nivån för utvecklings- och testmiljöer. För Förproduktion rekommenderar vi distribuerar en replik av produktionsmiljön, kör tester och stänger sedan för att minimera kostnader.

Mer information finns i [resursgrupp](../azure-resource-manager/resource-group-overview.md) Översikt.

### <a name="deployment"></a>Distribution

Vi rekommenderar att du använder [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md) att distribuera Azure API Management, Logic Apps, Event Grid och Service Bus. Mallar gör det enklare att automatisera distribution via PowerShell eller kommandoradsgränssnittet (CLI).

Vi rekommenderar dig Azure API Management, alla enskilda Logic Apps, Event Grid-ämnen och Service Bus-namnområden i sina egna separata Resource Manager-mallar. Detta gör att lagra dem i källkontrollsystem. Dessa mallar kan sedan distribueras tillsammans eller separat som en del av en process för kontinuerlig integrering/kontinuerlig (CI/CD).

### <a name="diagnostics-and-monitoring"></a>Diagnostik och övervakning

Service Bus, som API Management och Logikappar kan övervakas med hjälp av Azure Monitor. Azure Monitor är aktiverat som standard och ger information baserat på de olika mått som konfigurerats för varje tjänst.

## <a name="security-considerations"></a>Säkerhetsöverväganden

Skydda Service Bus med hjälp av en signatur för delad åtkomst. [SAS-autentisering](../service-bus-messaging/service-bus-sas.md) gör det möjligt att ge en användaråtkomst till Service Bus-resurser med specifika rättigheter. Läs mer om [Service Bus, autentisering och auktorisering](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Dessutom bör en Service Bus-kö måste visas som en HTTP-slutpunkt (så att publicera nya meddelanden), API Management bör användas för att skydda den med fronting slutpunkten. Detta kan sedan skyddas med certifikat eller OAuth efter behov. Det enklaste sättet att göra det använder en Logikapp med en HTTP-begäran/svar-utlösare som en mellanhand.

Event Grid skyddar händelseleverans via en kod för verifiering. Om du använder LogicApps förbruka händelsen utförs detta automatiskt. Läs mer om [Event Grid säkerhet och autentisering](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Nästa steg

- [Enkel Enterprise-Integration](logic-apps-architectures-simple-enterprise-integration.md)