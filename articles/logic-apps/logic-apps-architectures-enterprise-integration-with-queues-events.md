---
title: Azure Integration Services enterprise integration-Referensarkitektur
description: Beskriver referensarkitekturen som visar hur du implementerar ett mönster för enterprise-integrering med Logic Apps, API Management, Service Bus och Event Grid.
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
ms.openlocfilehash: a86c4c4227795a712dd51ace1fbefe9d2b96518a
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116120"
---
# <a name="reference-architecture-enterprise-integration-with-queues-and-events"></a>Referensarkitektur: Enterprise-integration med köer och händelser

Följande Referensarkitektur visas en uppsättning beprövade metoder som du kan använda för ett integration-program som använder Azure Integration Services. Arkitekturen kan fungera som bas för många olika mönster som kräver HTTP APIs, arbetsflöde och samordning.

![Arkitekturdiagram - Enterprise-integration med köer och händelser](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

*Det finns många möjliga program för integreringsteknik. De röra sig om en enkel point-to-point programmet till en fullständig enterprise Azure Service Bus-programmet. Arkitektur-serien beskriver återanvändbara komponenter som kan användas för att skapa ett allmänt integration-program. Arkitekter bör överväga vilka komponenter som de behöver för att implementera för sina program och infrastruktur.*

## <a name="architecture"></a>Arkitektur

Arkitekturen *bygger på* den [enkel enterprise-integration](logic-apps-architectures-simple-enterprise-integration.md) arkitektur. [Rekommendationer för enkel företagsarkitektur](logic-apps-architectures-simple-enterprise-integration.md#recommendations) gäller här. De har utelämnats från den [rekommendationer](#recommendations) i den här artikeln av utrymmesskäl. 

Arkitekturen har följande komponenter:

- **Resursgrupp**. En [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) är en logisk container för Azure-resurser.
- **Azure API Management**. [API Management](https://docs.microsoft.com/azure/api-management/) är en fullständigt hanterad plattform som används för att publicera, skydda och transformera HTTP APIs.
- **Azure API Management-utvecklarportalen**. Varje instans av Azure API Management som levereras med åtkomst till den [utvecklarportalen](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). API Management Developer-portalen får du tillgång till exempel dokumentation och kodexempel. Du kan testa API: er i Developer-portalen.
- **Azure Logic Apps**. [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) är en plattform utan server som används för att skapa enterprise arbetsflödet och integrationskontot.
- **Kopplingar**. Logic Apps använder [kopplingar](https://docs.microsoft.com/azure/connectors/apis-list) att ansluta till ofta används för tjänster. Logic Apps har redan hundratals olika anslutningar, men du kan också skapa en anpassad anslutningsapp.
- **Azure Service Bus**. [Service Bus](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) erbjuder säker och tillförlitlig meddelandehantering. Meddelanden kan användas för att frikoppla program och integrera med andra meddelande-baserade system.
- **Azure Event Grid**. [Event Grid](https://docs.microsoft.com/azure/event-grid/overview) är en plattform utan server som används för att publicera och leverera programhändelser.
- **IP-adress**. Azure API Management-tjänsten har en fast offentlig [IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) och ett domännamn. Domännamnet är en underdomän till azure-api.net, till exempel contoso.azure-api.net. Logic Apps och Service Bus även ha en offentlig IP-adress. I den här arkitekturen kan vi dock begränsa åtkomsten för att anropa Logic Apps-slutpunkter som endast IP-adressen för API Management (för säkerhet). Anrop till Service Bus skyddas av en signatur för delad åtkomst (SAS).
- **Azure DNS**. [Azure DNS](https://docs.microsoft.com/azure/dns/) är en värdtjänst för DNS-domäner. Azure DNS ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Du kan hantera dina DNS-poster genom att använda samma autentiseringsuppgifter, API: er, verktyg och fakturering att du använder för dina andra Azure-tjänster genom som värd för domäner i Azure. Skapa DNS-poster som mappar det anpassade domännamnet till IP-adressen om du vill använda ett anpassat domännamn (t.ex. contoso.com). Mer information finns i [konfigurera ett anpassat domännamn i API Management](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD)**. Använd [Azure AD](https://docs.microsoft.com/azure/active-directory/) eller en annan identitetsleverantör för autentisering. Azure AD tillhandahåller autentisering för att komma åt API-slutpunkter genom att skicka en [JSON Web Token för API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) att verifiera. Azure AD kan skydda åtkomst till API Management Developer-portalen (endast Standard och Premium-nivåer).

Arkitekturen har vissa mönster som är grundläggande för dess drift:

* Befintliga backend-publiceras HTTP-API: er via API Management Developer-portalen. I portalen, utvecklare (antingen interna för din organisation eller den externa) kan integrera anrop till dessa API: er i program.
* Sammansatta API: er skapas med hjälp av logikappar och genom att samordna anrop till programvara som en tjänst (SaaS)-system, Azure-tjänster och alla API: er som publiceras till API Management. [Logikappar publiceras även](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) via API Management Developer-portalen.
- Program använder Azure AD för att [skaffa ett OAuth 2.0-säkerhetstoken](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) som krävs för att få åtkomst till ett API.
- API Management [verifierar säkerhetstoken](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) och skickar begäran till backend-API: et eller logic app.
- Service Bus-köer är vana vid [frikoppla](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) programaktivitet och [jämna ut toppar i belastningen](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Meddelanden läggs till köer av logic apps, program från tredje part, eller (visas inte) genom att publicera kö som ett HTTP-API via API Management.
- När meddelanden läggs till i en Service Bus-kö, utlöser en händelse. En logikapp utlöses av händelsen. Logikappen bearbetar sedan meddelandet.
- Andra Azure-tjänster (till exempel Azure Blob storage och Azure Event Hubs) kan du även publicera händelser till Event Grid. De här tjänsterna utlösa logikappar för att ta emot händelsen och sedan utföra efterföljande åtgärder.

## <a name="recommendations"></a>Rekommendationer

Dina specifika krav kan skilja sig från den allmänna arkitekturen som beskrivs i den här artikeln. Använd rekommendationerna i det här avsnittet som en utgångspunkt.

### <a name="service-bus-tier"></a>Service Bus-nivå

Använd Service Bus Premium-nivån. Premier nivå har stöd för Event Grid-meddelanden. Mer information finns i [priser för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Priser för Event Grid

Event Grid använder en serverlös modell. Fakturering beräknas baserat på antalet åtgärder (händelsekörningen). Mer information finns i [priser för Event Grid](https://azure.microsoft.com/pricing/details/event-grid/). Det finns för närvarande inga nivån överväganden för Event Grid.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>Använda PeekLock för att använda Service Bus-meddelanden

När du skapar en logikapp för att använda Service Bus-meddelanden, använda [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) i logikapp för att få åtkomst till en grupp med meddelanden. När du använder PeekLock kan logikappen utföra stegen för att verifiera varje meddelande innan du har slutfört eller lämna meddelandet. Den här metoden skyddar mot oavsiktlig meddelandet går förlorade.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Sök efter flera objekt när en Event Grid-utlösaren utlöses

När en Event Grid-utlösaren utlöses innebär helt enkelt att ”minst en av dessa saker har hänt”. När Event Grid utlöser en logikapp på ett meddelande som visas i en Service Bus-kö, bör logikappen alltid anta att det kan finnas ett eller flera meddelanden som är tillgängliga för att bearbeta.

### <a name="region"></a>Region

Etablera API Management, Logic Apps och Service Bus i samma region för att minimera nätverkssvarstiden. I allmänhet väljer du regionen som är närmast dina användare.

Resursgruppen har också en region. Regionen anger var metadata för distribution lagras och där distributionsmallen kör från. Placera resursgruppen och dess resurser i samma region för att förbättra tillgängligheten under distributionen.

## <a name="scalability"></a>Skalbarhet

Service Bus Premium-nivån kan skala ut antalet meddelandefunktionsenheter att uppnå högre skalbarhet. Konfigurationer för Premium-nivån kan ha en, två eller fyra meddelandefunktionsenheter. Mer information om skalning av Service Bus finns i [Metodtips för prestandaförbättringar med hjälp av Service Bus-meddelanden](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Tillgänglighet

Serviceavtal (SLA) för Azure API Management är för närvarande 99,9% för nivåerna Basic, Standard och Premium. Premium-nivån konfigurationer med distribution av minst en enhet i två eller fler regioner har inget eget serviceavtal på 99,95%.

SERVICEAVTALET för Azure Logic Apps är för närvarande 99,9%.

### <a name="disaster-recovery"></a>Haveriberedskap

Överväg att implementera geo-haveriberedskap i Service Bus Premium för att aktivera redundans om ett allvarligt strömavbrott uppstår. Mer information finns i [Azure Service Bus-geohaveriberedskap](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Hanterbarhet

Skapa separata resursgrupper för produktion, utveckling och testmiljöer. Separata resursgrupper gör det enklare att hantera distributioner, ta bort testdistributioner och tilldela åtkomsträttigheter.

Tänk på följande faktorer när du tilldelar resurser till resursgrupper:

- **Livscykel**. I allmänhet Lägg till resurser som har samma livscykel i samma resursgrupp.
- **Åtkomst**. Du kan använda [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC) för att tillämpa principer för åtkomst till resurser i en grupp.
- **Fakturering**. Du kan visa samlade kostnaderna för resursgruppen.
- **Prisnivå för API Management**. Vi rekommenderar att du använder Developer-nivån för utveckling och testmiljöer. För Förproduktion rekommenderar vi distribuerar en replik av produktionsmiljön, kör tester och stänger sedan för att minimera kostnader.

Mer information finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Distribution

Vi rekommenderar att du använder [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md) att distribuera API Management, Logic Apps, Event Grid och Service Bus. Mallar gör det enklare att automatisera distribution via PowerShell eller Azure CLI.

Vi rekommenderar att placera API Management, enskilda logikappar, Event Grid-ämnen och Service Bus-namnområden i sina egna, separata Resource Manager-mallar. När du använder separata mallar kan du lagra resurser i källkontrollsystem. Du kan sedan distribuera dessa mallar tillsammans eller separat som en del av en kontinuerlig integrering/kontinuerlig distribution (CI/CD).

### <a name="diagnostics-and-monitoring"></a>Diagnostik och övervakning

Du kan övervaka Service Bus med hjälp av Azure Monitor som API Management och Logikappar. Azure Monitor innehåller information baserat på mått som är konfigurerade för varje tjänst. Azure Monitor är aktiverat som standard.

## <a name="security"></a>Säkerhet

Skydda Service Bus med hjälp av en SAS. Du kan använda [SAS-autentisering](../service-bus-messaging/service-bus-sas.md) att ge en användaråtkomst till Service Bus-resurser med specifika rättigheter. Mer information finns i [Service Bus, autentisering och auktorisering](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Om en Service Bus-kö måste visas som en HTTP-slutpunkt (för att publicera nya meddelanden), bör du använda API Management för att skydda den av fronting slutpunkten. Slutpunkten kan sedan skyddas med certifikat eller OAuth efter behov. Det enklaste sättet att skydda en slutpunkt är med hjälp av en logikapp med en HTTP-begäran/svar-utlösare som en mellanhand.

Event Grid skyddar händelseleverans via en kod för verifiering. Om du använder Logic Apps för att använda händelsen utförs verifieringen automatiskt. Mer information finns i [Event Grid säkerhet och autentisering](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [enkel enterprise-integration](logic-apps-architectures-simple-enterprise-integration.md).