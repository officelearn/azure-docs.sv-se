---
title: Enkel enterprise integration-arkitekturmönster - integreringstjänster för Azure
description: Den här referensen för arkitekturen visar hur du kan implementera ett mönster för enkel enterprise-integration med hjälp av Azure Logic Apps och Azure API Management
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 7081c9e4f6e6deee196255f04180a8f2cc792876
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122503"
---
# <a name="simple-enterprise-integration-architecture"></a>Enkel integrering företagsarkitektur

Den här artikeln beskrivs en enterprise integration-arkitektur som använder beprövade metoder som du kan använda för ett program för integrering med Azure Integration Services. Du kan använda den här arkitekturen som bas för många olika mönster som kräver HTTP APIs, arbetsflöde och samordning.

![Arkitekturdiagram - enkel enterprise-integration](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

Den här serien beskriver återanvändbara komponenter som kan användas för att skapa ett allmänt integration-program. Eftersom integreringsteknik har många möjliga program som sträcker sig från enkla point-to-point appar till fullständig Azure Service Bus företagsappar, Överväg vilka komponenter som du behöver implementera för dina appar och infrastruktur.

## <a name="architecture-components"></a>Arkitektur för komponenter

Den här arkitekturen för enterprise-integration innehåller följande komponenter:

- **Resursgrupp**: A [resursgrupp](../azure-resource-manager/resource-group-overview.md) är en logisk behållare för Azure-resurser.

- **Azure API Management**: den [API Management](https://docs.microsoft.com/azure/api-management/) service är en fullständigt hanterad plattform för att publicera, skydda och omvandla HTTP APIs.

- **Azure API Management-utvecklarportalen**: varje instans av Azure API Management ger åtkomst till den [utvecklarportalen](../api-management/api-management-customize-styles.md). Den här portalen får du tillgång till exempel dokumentation och kodexempel. Du kan också testa API: er i Developer-portalen.

- **Med Azure Logic Apps**: [Logikappar](../logic-apps/logic-apps-overview.md) är en plattform utan server för att skapa enterprise arbetsflöden och integreringar.

- **Kopplingar**: Logic Apps använder [kopplingar](../connectors/apis-list.md) för anslutning till ofta används för tjänster. Logikappar ger hundratals anslutningsappar, men du kan också skapa en anpassad anslutningsapp.

- **IP-adress**: Azure API Management-tjänsten har en fast offentlig [IP-adress](../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett domännamn. Standarddomännamnet är en underdomän till azure-api.net, till exempel contoso.azure-api.net, men du kan också konfigurera [anpassade domäner](../api-management/configure-custom-domain.md). Logic Apps och Service Bus även ha en offentlig IP-adress. Dock begränsar den här arkitekturen för säkerhet, åtkomst för att anropa Logic Apps-slutpunkter som endast IP-adressen för API Management. Anrop till Service Bus skyddas av en signatur för delad åtkomst (SAS).

- **Azure DNS**: [Azure DNS](https://docs.microsoft.com/azure/dns/) är en värdtjänst för DNS-domäner. Azure DNS ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Du kan hantera dina DNS-poster genom att använda samma autentiseringsuppgifter, API: er, verktyg och fakturering att du använder för dina andra Azure-tjänster genom som värd för domäner i Azure. För att använda ett anpassat domännamn, till exempel contoso.com, skapar du DNS-poster som mappar det anpassade domännamnet till IP-adressen. Mer information finns i [konfigurera ett anpassat domännamn i API Management](../api-management/configure-custom-domain.md).

- **Azure Active Directory (Azure AD)**: du kan använda [Azure AD](https://docs.microsoft.com/azure/active-directory/) eller en annan identitetsleverantör för autentisering. Azure AD tillhandahåller autentisering för att komma åt API-slutpunkter genom att skicka en [JSON Web Token för API Management](../api-management/policies/authorize-request-based-on-jwt-claims.md) att verifiera. För nivåerna Standard och Premium och skydda Azure AD åtkomst till API Management Developer-portalen.

## <a name="patterns"></a>Mönster 

Den här arkitekturen använder vissa mönster som är grundläggande för åtgärden:

* Sammansatta API: er skapas med hjälp av logic apps, som dirigerar anrop till programvara som en tjänst (SaaS)-system, Azure-tjänster och alla API: er som publiceras till API Management. Logikappar är också [publiceras via API Management utvecklarportalen](../api-management/import-logic-app-as-api.md).

* Program använder Azure AD för [skaffa ett OAuth 2.0-säkerhetstoken](../api-management/api-management-howto-protect-backend-with-aad.md) som krävs för att få åtkomst till ett API.

* Azure API Management [verifierar säkerhetstoken](../api-management/api-management-howto-protect-backend-with-aad.md) och skickar begäran till backend-API: et eller logic app.

## <a name="recommendations"></a>Rekommendationer

Dina specifika krav kan skilja sig från den allmänna arkitekturen som beskrivs i den här artikeln. Använd rekommendationerna i det här avsnittet som en utgångspunkt.

### <a name="azure-api-management-tier"></a>Azure API Management-nivå

Använd API Management Basic, Standard eller Premium-nivå. Dessa nivåer erbjuder ett produktion servicenivåavtal (SLA) och stöd för skalning i Azure-region. Hur många enheter varierar beroende på nivån. Premium-nivån har även stöd för skalning över flera Azure-regioner. Välj nivå baserat på dina funktioner och nödvändig dataflödesnivå. Mer information finns i [API Management-priser](https://azure.microsoft.com/pricing/details/api-management/).

Du debiteras för alla API Management-instanser när de körs. Om du har skalats och behöver inte den här nivån av prestanda hela tiden, Överväg att dra nytta av fakturering per timme API Management och skala ned.

### <a name="logic-apps-pricing"></a>Prissättning för Logic Apps

Logic Apps använder en [serverlös](../logic-apps/logic-apps-serverless-overview.md) modellen. Fakturering beräknas baserat på åtgärder och anslutningsprogram körning. Mer information finns i [Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps/). Det finns för närvarande inga nivån överväganden för Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Logic Apps för asynkron API-anrop

Logikappar fungerar bäst i scenarier som inte kräver låg fördröjning. Till exempel Logic Apps fungerar bäst för asynkron eller halvstrukturerade långvariga API-anrop. Om låg latens krävs, till exempel ett anrop som blockerar användargränssnittet, implementera dina API: et eller åtgärden med hjälp av en annan teknik. Till exempel använda Azure Functions eller ett webb-API som du distribuerar med hjälp av Azure App Service. Använda API Management för klientdelens API: et till din API-kunder.

### <a name="region"></a>Region

Välj samma region för API Management, Logic Apps och Service Bus för att minimera nätverkssvarstiden. I allmänhet väljer du den region som ligger närmast dina användare.

Resursgruppen har också en region. Den här regionen anger var du vill lagra metadata för distribution och var du vill köra distributionsmallen. Placera resursgruppen och resurser i samma region för att förbättra tillgängligheten under distributionen.

## <a name="scalability"></a>Skalbarhet

För att öka skalbarheten när du administrerar en API Management-tjänsten kan du lägga till [cachelagringsprinciper](../api-management/api-management-howto-cache.md) där det är lämpligt. Cachelagring kan också minska belastningen på backend-tjänster.

Du kan skala ut Azure API Management Basic, Standard och Premium-nivåerna i en Azure-region för att erbjuda större kapacitet. Analysera användningen för din tjänst, på den **mått** menyn och välj den **kapacitet mått** alternativet och sedan skala upp eller efter behov.

Rekommendationer för att skala API Management-tjänsten:

- Överväg att trafikmönster vid skalning. Kunder med mer föränderliga trafikmönster behöver större kapacitet.

- Konsekvent kapacitet som är större än 66% kan tyda på ett behov av att skala upp.

- Konsekvent kapaciteten som finns under 20% kan tyda på en möjlighet att skala ned.

- Innan du aktiverar belastningen i produktion alltid belastningstest API Management-tjänsten med en representativ belastning.

Du kan skala ut nivån Premium-tjänster över flera Azure-regioner. Om du distribuerar genom att skala tjänster över flera Azure-regioner kan få du ett högre serviceavtal (99,95% jämfört med 99,9%) och etablera tjänster nära användare i flera regioner.

Logikappar serverlös modellen innebär att administratörer inte planera för tjänstskalbarhet. Tjänsten skalas automatiskt för att möta efterfrågan.

## <a name="availability"></a>Tillgänglighet

* Serviceavtal (SLA) för Azure API Management är för närvarande 99,9% för Basic, Standard och Premium-nivåerna. För premium-nivån konfigurationer med en distribution som har minst en enhet i två eller fler regioner är serviceavtalet 99,95%.

* SERVICEAVTALET för Azure Logic Apps är för närvarande 99,9%.

### <a name="backups"></a>Säkerhetskopior

Baserat på korrekthet ändringen, [regelbundet säkerhetskopiera](../api-management/api-management-howto-disaster-recovery-backup-restore.md) din Azure API Management-konfiguration. Store säkerhetskopiorna på en plats eller Azure-region som skiljer sig från där tjänsten finns. Du kan sedan välja något av alternativen som din strategi för katastrofåterställning:

* Etablera en ny API Management-instans i en disaster recovery-händelse, återställa säkerhetskopian till den nya instansen och domänleverantören DNS-poster.

* Håll en passiv kopia av din tjänst i en annan Azure-region som tillkommer ytterligare kostnader. Regelbundet återställa säkerhetskopior till kopian. Om du vill återställa tjänsten under en händelse för återställning av haveriberedskap, behöver du bara domänleverantören DNS-poster.

Eftersom du snabbt kan återskapa säkerhetskopiera logic apps, som är utan Server kan dem genom att spara en kopia av den associerade Azure Resource Manager-mallen. Du kan spara mallar i källkontrollen och du kan integrera mallar med kontinuerlig integrering/kontinuerlig distribution (CI/CD) processen.

Om du har publicerat en logic app via Azure API Management, och den logikappen som flyttas till ett annat datacenter, kan du uppdatera dess plats. Du kan uppdatera dina API: er **serverdel** egenskap med ett grundläggande PowerShell.skript.

## <a name="manageability"></a>Hanterbarhet

Skapa separata resursgrupper för produktion, utveckling och testmiljöer. Separata resursgrupper gör det enklare att hantera distributioner, ta bort testdistributioner och tilldela åtkomsträttigheter.

När du tilldelar resurser till resursgrupper, Tänk på följande:

* **Livscykel**: generellt sett lägger till resurser som har samma livscykel i samma resursgrupp.

* **Åtkomst**: du kan använda för att tillämpa principer för åtkomst till resurser i en grupp, [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md).

* **Fakturering**: du kan visa samlade kostnaderna för resursgruppen.

* **Prisnivå för API Management**: använda Developer-nivån för din utvecklings- och testmiljöer. För att minimera kostnaderna under Förproduktion, distribuerar en replik av produktionsmiljön, köra dina tester och sedan stänga.

Mer information finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="deployment"></a>Distribution

* Om du vill distribuera API Management och Logikappar, använda den [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md). Mallar gör automatiserar distributioner enklare med hjälp av PowerShell eller Azure CLI.

* Placera API Management och alla enskilda logic apps i sina egna separata Resource Manager-mallar. Med hjälp av separata mallar, kan du lagra resurser i källkontrollsystem. Du kan sedan distribuera dessa mallar tillsammans eller separat som en del av en kontinuerlig integrering/kontinuerlig distribution (CI/CD).

### <a name="versions"></a>Versioner

Varje gång du ändrar konfigurationen för en logikapp eller distribuera en uppdatering via Resource Manager-mall, Azure ser till att en kopia av den versionen för din bekvämlighet och ser till att alla versioner som har en körningshistoriken. Du kan använda dessa versioner för historiska ändringsspårningen eller uppgradera en version som den logikapp aktuella konfiguration. Exempelvis kan återställa du effektivt en logikapp.

Azure API Management har de olika men kompletterande [versionshantering begrepp](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

* Versioner som ger din API-kunderna möjligheten att välja en API-version som är baserat på deras behov, till exempel, v1, v2, beta eller produktion

* Revisioner som gör att API-administratörer för att på ett säkert sätt göra ändringar i ett API och distribuera ändringarna till användare med valfria kommentarer

Överväg att API Management revisioner som ett sätt att göra ändringar på ett säkert sätt att behålla en ändring och kommunicera ändringarna dina API: er som använder för distributionen. Du kan göra en ändring i en utvecklingsmiljö och distribuera den ändringen i andra miljöer med hjälp av Resource Manager-mallar.

Men du kan använda revideringar för att testa ett API innan du gör dessa ändringar ”aktuella” och är tillgängliga för användare, rekommenderas inte den här metoden för belastning eller testning. Använd i stället separata test eller preproduktionsmiljöer.

### <a name="configuration-and-sensitive-information"></a>Konfiguration och känslig information

Lägg aldrig in lösenord, åtkomstnycklar eller anslutningssträngar i källkontrollen. Om dessa värden krävs, skydda och distribuera dessa värden med hjälp av lämpliga metoder. 

I Logic Apps, om en logikapp kräver känsliga värden som du inte kan skapa inom en anslutning kan lagra dessa värden i Azure Key Vault och referera till dem från en Resource Manager-mall. Använd mallparametrar för distribution och -parameterfiler för varje miljö. Mer information finns i [skydda parametrar och indata i ett arbetsflöde](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

API Management hanterar hemligheter med hjälp av objekt som kallas *namngivna värden* eller *egenskaper*. De här objekten lagra på ett säkert sätt värden som du kommer åt via API Management-principer. Mer information finns i [hantera hemligheter i API Management](../api-management/api-management-howto-properties.md).

## <a name="diagnostics-and-monitoring"></a>Diagnostik och övervakning

Du kan använda [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) för Operativ övervakning i både [API Management](../api-management/api-management-howto-use-azure-monitor.md) och [Logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md). Azure Monitor tillhandahåller information baserat på de mått som konfigurerats för varje tjänst och är aktiverad som standard.

Varje tjänst har även dessa alternativ:

* För djupare analys och dashboarding, kan du skicka loggar med Logic Apps som [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md).

* För DevOps övervakning, kan du konfigurera Azure Application Insights för API Management.

* Har stöd för API Management på [Power BI-lösningsmall för anpassade API-analytics](http://aka.ms/apimpbi). Du kan använda den här lösningsmallen för att skapa en egen analyslösning. För företagsanvändare gör Power BI rapporter som är tillgängliga.

## <a name="security"></a>Säkerhet

Den här listan inte helt beskrivs alla rekommenderade säkerhetsmetoder, är här några säkerhetsaspekter som gäller specifikt för Azure-tjänster som distribueras i arkitekturen som beskrivs i den här artikeln:

* Använd rollbaserad åtkomstkontroll (RBAC) för att se till att användarna har lämplig åtkomstnivåer.

* Skydda offentliga API-slutpunkter i API Management med hjälp av OAuth eller OpenID Connect. Konfigurera en identitetsprovider för att skydda offentliga API-slutpunkter och Lägg till en verifieringsprincip av JSON Web Token (JWT).

* Ansluta till backend-tjänster från API Management med hjälp av ömsesidig certifikat.

* Skydda HTTP utlösningsbaserade logikappar genom att skapa en godkänd IP-adress-lista som pekar till API Management IP-adressen. En godkänd IP-adress förhindrar att anropa logikappen från det offentliga internet utan att första gå via API Management.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [enterprise-integration med köer och händelser](../logic-apps/logic-apps-architectures-enterprise-integration-with-queues-events.md)
