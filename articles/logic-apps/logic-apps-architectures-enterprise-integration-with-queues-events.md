---
title: Enterprise integration-arkitekturmönster - integreringstjänster för Azure
description: Den här referensen för arkitekturen visar hur du kan implementera ett mönster med enterprise-integrering med Azure Logic Apps, Azure API Management, Azure Service Bus och Azure Event Grid
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 2ffb1f7edef0cf92cbbf7adc4314967858bcfeb1
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128651"
---
# <a name="enterprise-integration-architecture-with-queues-and-events"></a>Företagsarkitektur för integrering med köer och händelser

Den här artikeln beskrivs en enterprise integration-arkitektur som använder beprövade metoder som du kan använda för ett program för integrering med Azure Integration Services. Du kan använda den här arkitekturen som bas för många olika mönster som kräver HTTP APIs, arbetsflöde och samordning.

![Arkitekturdiagram - Enterprise-integration med köer och händelser](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

Den här serien beskriver återanvändbara komponenter som kan användas för att skapa ett allmänt integration-program. Eftersom integreringsteknik har många möjliga program som sträcker sig från enkla point-to-point appar till fullständig Azure Service Bus företagsappar, Överväg vilka komponenter som du behöver implementera för dina appar och infrastruktur.

## <a name="architecture-components"></a>Arkitektur för komponenter

Den här arkitekturen bygger på den arkitektur som beskrivs i artikeln [arkitektur referens: enkel enterprise-integration](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md). Den arkitekturen [rekommendationer](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md#recommendations) gäller även här, men kortfattat, utelämnar den här artikeln rekommendationerna från den [rekommendationer](#recommendations) avsnittet. Den här arkitekturen för enterprise-integration innehåller följande komponenter:

- **Resursgrupp**: A [resursgrupp](../azure-resource-manager/resource-group-overview.md) är en logisk behållare för Azure-resurser.

- **Azure API Management**: den [API Management](https://docs.microsoft.com/azure/api-management/) service är en fullständigt hanterad plattform för att publicera, skydda och omvandla HTTP APIs.

- **Azure API Management-utvecklarportalen**: varje instans av Azure API Management ger åtkomst till den [utvecklarportalen](../api-management/api-management-customize-styles.md). Den här portalen får du tillgång till exempel dokumentation och kodexempel. Du kan också testa API: er i Developer-portalen.

- **Med Azure Logic Apps**: [Logikappar](../logic-apps/logic-apps-overview.md) är en plattform utan server för att skapa enterprise arbetsflöden och integreringar.

- **Kopplingar**: Logic Apps använder [kopplingar](../connectors/apis-list.md) för anslutning till ofta används för tjänster. Logikappar ger hundratals anslutningsappar, men du kan också skapa en anpassad anslutningsapp.

- **Azure Service Bus**: [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) erbjuder säker och tillförlitlig meddelandehantering. Du kan använda meddelanden för Frikoppling program och integrera med andra meddelande-baserade system.

- **Azure Event Grid**: [Event Grid](../event-grid/overview.md) är en plattform utan server för att publicera och leverans av programhändelser.

- **IP-adress**: Azure API Management-tjänsten har en fast offentlig [IP-adress](../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett domännamn. Standarddomännamnet är en underdomän till azure-api.net, till exempel contoso.azure-api.net, men du kan också konfigurera [anpassade domäner](../api-management/configure-custom-domain.md). Logic Apps och Service Bus även ha en offentlig IP-adress. Dock begränsar den här arkitekturen för säkerhet, åtkomst för att anropa Logic Apps-slutpunkter som endast IP-adressen för API Management. Anrop till Service Bus skyddas av en signatur för delad åtkomst (SAS).

- **Azure DNS**: [Azure DNS](https://docs.microsoft.com/azure/dns/) är en värdtjänst för DNS-domäner. Azure DNS ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Du kan hantera dina DNS-poster genom att använda samma autentiseringsuppgifter, API: er, verktyg och fakturering att du använder för dina andra Azure-tjänster genom som värd för domäner i Azure. För att använda ett anpassat domännamn, till exempel contoso.com, skapar du DNS-poster som mappar det anpassade domännamnet till IP-adressen. Mer information finns i [konfigurera ett anpassat domännamn i API Management](../api-management/configure-custom-domain.md).

- **Azure Active Directory (Azure AD)**: du kan använda [Azure AD](https://docs.microsoft.com/azure/active-directory/) eller en annan identitetsleverantör för autentisering. Azure AD tillhandahåller autentisering för att komma åt API-slutpunkter genom att skicka en [JSON Web Token för API Management](../api-management/policies/authorize-request-based-on-jwt-claims.md) att verifiera. För nivåerna Standard och Premium och skydda Azure AD åtkomst till API Management Developer-portalen.

## <a name="patterns"></a>Mönster 

Den här arkitekturen använder vissa mönster som är grundläggande för åtgärden:

* Befintliga backend-publiceras HTTP-API: er via API Management Developer-portalen. I portalen, utvecklare, som antingen är interna för din organisation, externt eller båda,  
anrop till dessa API: er kan integrera i program.

* Sammansatta API: er skapas med hjälp av logic apps, som dirigerar anrop till programvara som en tjänst (SaaS)-system, Azure-tjänster och alla API: er som publiceras till API Management. Logikappar är också [publiceras via API Management utvecklarportalen](../api-management/import-logic-app-as-api.md).

* Program använder Azure AD för [skaffa ett OAuth 2.0-säkerhetstoken](../api-management/api-management-howto-protect-backend-with-aad.md) som krävs för att få åtkomst till ett API.

* Azure API Management [verifierar säkerhetstoken](../api-management/api-management-howto-protect-backend-with-aad.md) och skickar begäran till backend-API: et eller logic app.

* Azure Service Bus-köer används för [Frikoppling](../service-bus-messaging/service-bus-messaging-overview.md) programaktivitet och för [smoothing toppar i belastningen](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Meddelanden läggs till köer av logic apps, appar från tredje part, eller (visas inte) genom att publicera kö som ett HTTP-API via API Management.

* När meddelanden läggs till i en Service Bus-kö, utlöser en händelse. Händelsen utlöses en logikapp som bearbetar meddelandet.

* Andra Azure-tjänster, till exempel Azure Blob Storage och Azure Event Hubs, kan du även publicera händelser till Event Grid. De här tjänsterna utlösa logikappar för att ta emot händelsen och sedan utföra efterföljande åtgärder.

## <a name="recommendations"></a>Rekommendationer

Dina specifika krav kan skilja sig från den allmänna arkitekturen som beskrivs i den här artikeln. Använd rekommendationerna i det här avsnittet som en utgångspunkt.

### <a name="service-bus-tier"></a>Service Bus-nivå

Använd Service Bus Premium-nivån som har stöd för Event Grid-meddelanden. Mer information finns i [priser för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Priser för Event Grid

Event Grid använder en serverlös modell. Fakturering beräknas baserat på antalet åtgärder (händelse körningar). Mer information finns i [priser för Event Grid](https://azure.microsoft.com/pricing/details/event-grid/). Det finns för närvarande inga nivån överväganden för Event Grid.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>Använda PeekLock för att använda Service Bus-meddelanden

När du skapar en logikapp för att använda Service Bus-meddelanden, har din logikapp använda [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) för åtkomst till en grupp med meddelanden. När du använder PeekLock kan logikappen utföra stegen för att verifiera varje meddelande innan du har slutfört eller lämna meddelandet. Den här metoden skyddar mot oavsiktlig meddelandet går förlorade.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Sök efter flera objekt när en Event Grid-utlösaren utlöses

När en Event Grid-utlösare utlöses avses den här åtgärden att ”minst en av dessa saker har hänt”. När Event Grid utlöser en logikapp på ett meddelande som visas i en Service Bus-kö, bör logikappen alltid anta att det kan finnas ett eller flera meddelanden som är tillgängliga för att bearbeta.

### <a name="region"></a>Region

Välj samma region för API Management, Logic Apps och Service Bus för att minimera nätverkssvarstiden. I allmänhet väljer du den region som ligger närmast dina användare.

Resursgruppen har också en region. Den här regionen anger var du vill lagra metadata för distribution och var du vill köra distributionsmallen. Placera resursgruppen och resurser i samma region för att förbättra tillgängligheten under distributionen.

## <a name="scalability"></a>Skalbarhet

Service Bus Premium-nivån kan skala ut antalet meddelandefunktionsenheter för att uppnå högre skalbarhet. Konfigurationer för Premium-nivån kan ha en, två eller fyra meddelandefunktionsenheter. Mer information om skalning av Service Bus finns i [Metodtips för prestandaförbättringar med hjälp av Service Bus-meddelanden](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Tillgänglighet

* Serviceavtal (SLA) för Azure API Management är för närvarande 99,9% för Basic, Standard och Premium-nivåerna. För premium-nivån konfigurationer med en distribution som har minst en enhet i två eller fler regioner är serviceavtalet 99,95%.

* SERVICEAVTALET för Azure Logic Apps är för närvarande 99,9%.

### <a name="disaster-recovery"></a>Haveriberedskap

Överväg att implementera geo-haveriberedskap i Service Bus Premium för att aktivera redundans om ett allvarligt strömavbrott uppstår. Mer information finns i [Azure Service Bus-geohaveriberedskap](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Hanterbarhet

Skapa separata resursgrupper för produktion, utveckling och testmiljöer. Separata resursgrupper gör det enklare att hantera distributioner, ta bort testdistributioner och tilldela åtkomsträttigheter.

När du tilldelar resurser till resursgrupper, Tänk på följande:

* **Livscykel**: generellt sett lägger till resurser som har samma livscykel i samma resursgrupp.

* **Åtkomst**: du kan använda för att tillämpa principer för åtkomst till resurser i en grupp, [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md).

* **Fakturering**: du kan visa samlade kostnaderna för resursgruppen.

* **Prisnivå för API Management**: använda Developer-nivån för din utvecklings- och testmiljöer. För att minimera kostnaderna under Förproduktion, distribuerar en replik av produktionsmiljön, köra dina tester och sedan stänga.

Mer information finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="deployment"></a>Distribution

* Om du vill distribuera API Management, Logic Apps, Event Grid och Service Bus, använda den [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md). Mallar gör automatiserar distributioner enklare med hjälp av PowerShell eller Azure CLI.

* Placera API Management, enskilda logikappar, Event Grid-ämnen och Service Bus-namnområden i sina egna separata Resource Manager-mallar. Med hjälp av separata mallar, kan du lagra resurser i källkontrollsystem. Du kan sedan distribuera dessa mallar tillsammans eller separat som en del av en kontinuerlig integrering/kontinuerlig distribution (CI/CD).

## <a name="diagnostics-and-monitoring"></a>Diagnostik och övervakning

Du kan övervaka Service Bus med hjälp av Azure Monitor, som är aktiverad som standard som API Management och Logikappar. Azure Monitor innehåller information baserat på mått som är konfigurerade för varje tjänst. 

## <a name="security"></a>Säkerhet

Använda signatur för delad åtkomst (SAS) för att skydda Service Bus. Du kan till exempel ge en användaråtkomst till Service Bus-resurser med specifika rättigheter genom att använda [SAS-autentisering](../service-bus-messaging/service-bus-sas.md). Mer information finns i [Service Bus, autentisering och auktorisering](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Om du vill exponera en Service Bus-kö som en HTTP-slutpunkt, exempelvis om du vill publicera nya meddelanden använda API Management kan skydda kön genom fronting slutpunkten. Du kan sedan skydda slutpunkten med certifikat eller OAuth-autentisering efter behov. Det enklaste sättet att skydda en slutpunkt använder en logikapp med en HTTP-begäran/svar-utlösare som en mellanhand.

Event Grid-tjänsten skyddar händelseleverans via en kod för verifiering. Verifieringen utförs automatiskt om du använder Logic Apps för att använda händelsen. Mer information finns i [Event Grid säkerhet och autentisering](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [enkel enterprise-integration](logic-apps-architectures-simple-enterprise-integration.md)
