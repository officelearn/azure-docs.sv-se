---
title: Azure Integration Services enkel enterprise integration-Referensarkitektur
description: Beskriver referensarkitekturen som visar hur du implementerar en enkel enterprise integration-mönstret med Azure Logic Apps och Azure API Management.
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
ms.openlocfilehash: f73a9e59c0add664128b506172182afe566ca670
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444518"
---
# <a name="reference-architecture-simple-enterprise-integration"></a>Referensarkitektur: enkel enterprise-integration

Följande Referensarkitektur visas en uppsättning beprövade metoder som du kan använda för ett integration-program som använder Azure Integration Services. Arkitekturen kan fungera som bas för många olika mönster som kräver HTTP APIs, arbetsflöde och samordning.

![Arkitekturdiagram - enkel enterprise-integration](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

*Det finns många möjliga program för integreringsteknik. De röra sig om en enkel point-to-point programmet till en fullständig enterprise Azure Service Bus-programmet. Arkitektur-serien beskriver återanvändbara komponenter som kan användas för att skapa ett allmänt integration-program. Arkitekter bör överväga vilka komponenter som de behöver för att implementera för sina program och infrastruktur.*

## <a name="architecture"></a>Arkitektur

Arkitekturen har följande komponenter:

- **Resursgrupp**. En [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) är en logisk container för Azure-resurser.
- **Azure API Management**. [API Management](https://docs.microsoft.com/azure/api-management/) är en fullständigt hanterad plattform som används för att publicera, skydda och transformera HTTP APIs.
- **Azure API Management-utvecklarportalen**. Varje instans av Azure API Management som levereras med åtkomst till den [utvecklarportalen](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). API Management Developer-portalen får du tillgång till exempel dokumentation och kodexempel. Du kan testa API: er i Developer-portalen.
- **Azure Logic Apps**. [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) är en plattform utan server som används för att skapa enterprise arbetsflödet och integrationskontot.
- **Kopplingar**. Logic Apps använder [kopplingar](https://docs.microsoft.com/azure/connectors/apis-list) att ansluta till ofta används för tjänster. Logic Apps har redan hundratals olika anslutningar, men du kan också skapa en anpassad anslutningsapp.
- **IP-adress**. Azure API Management-tjänsten har en fast offentlig [IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) och ett domännamn. Standarddomännamnet är en underdomän till azure-api.net, till exempel contoso.azure-api.net, men [anpassade domäner](https://docs.microsoft.com/azure/api-management/configure-custom-domain) kan också konfigureras. Logic Apps och Service Bus även ha en offentlig IP-adress. I den här arkitekturen kan vi dock begränsa åtkomsten för att anropa Logic Apps-slutpunkter som endast IP-adressen för API Management (för säkerhet). Anrop till Service Bus skyddas av en signatur för delad åtkomst (SAS).
- **Azure DNS**. [Azure DNS](https://docs.microsoft.com/azure/dns/) är en värdtjänst för DNS-domäner. Azure DNS ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Du kan hantera dina DNS-poster genom att använda samma autentiseringsuppgifter, API: er, verktyg och fakturering att du använder för dina andra Azure-tjänster genom som värd för domäner i Azure. För att använda ett anpassat domännamn, t.ex. contoso.com, skapar du DNS-poster som mappar det anpassade domännamnet till IP-adressen. Mer information finns i [konfigurera ett anpassat domännamn i API Management](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD)**. Använd [Azure AD](https://docs.microsoft.com/azure/active-directory/) eller en annan identitetsleverantör för autentisering. Azure AD tillhandahåller autentisering för att komma åt API-slutpunkter genom att skicka en [JSON Web Token för API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) att verifiera. Azure AD kan skydda åtkomst till API Management Developer-portalen (endast Standard och Premium-nivåer).

Arkitekturen har vissa mönster som är grundläggande för dess drift:

- Sammansatta API: er skapas med hjälp av logikappar. De dirigera anrop till programvara som en tjänst (SaaS)-system, till Azure-tjänster och till alla API: er som publiceras till API Management. [Logikappar publiceras även](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) via API Management Developer-portalen.
- Program använder Azure AD för att [skaffa ett OAuth 2.0-säkerhetstoken](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) som krävs för att få åtkomst till ett API.
- Azure API Management [verifierar säkerhetstoken](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) och skickar begäran till backend-API: et eller logic app.

## <a name="recommendations"></a>Rekommendationer

Dina specifika krav kan skilja sig från den allmänna arkitektur som beskrivs i den här artikeln. Använd rekommendationerna i det här avsnittet som en utgångspunkt.

### <a name="azure-api-management-tier"></a>Azure API Management-nivå

Använd API Management Basic, Standard eller Premium-nivå. Nivåerna erbjuder ett produktion servicenivåavtal (SLA) och stöd för skalbar inom Azure-region (hur många enheter varierar beroende på nivån). Premium-nivån har även stöd för skalbar över flera Azure-regioner. Basera den nivå du väljer på dataflödet som krävs och din funktionsuppsättning. Mer information finns i [API Management-priser](https://azure.microsoft.com/pricing/details/api-management/).

Du debiteras för alla API Management-instanser när de körs. Om du har skalats och behöver inte den här nivån av prestanda hela tiden, Överväg att dra nytta av fakturering per timme API Management och skala ned.

### <a name="logic-apps-pricing"></a>Prissättning för Logic Apps

Logic Apps använder en [serverlös](logic-apps-serverless-overview.md) modellen. Fakturering beräknas baserat på åtgärder och anslutningsprogram körning. Mer information finns i [Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps/). Det finns för närvarande inga nivån överväganden för Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Logic Apps för asynkron API-anrop

Logikappar fungerar bäst i scenarier som inte kräver låg fördröjning. Till exempel den fungerar bäst för asynkron eller halvstrukturerade långvariga API-anrop. Om låg latens krävs (t.ex, ett anrop som blockerar ett användargränssnitt), rekommenderar vi att API: et eller åtgärden med hjälp av en annan teknik. Till exempel använda Azure Functions eller ett webb-API som du distribuerar med hjälp av Azure App Service. Vi rekommenderar ändå att du klientdelens API till API-konsumenter med hjälp av API Management.

### <a name="region"></a>Region

Etablera API Management och Logikappar i samma region för att minimera nätverkssvarstiden. I allmänhet väljer du regionen som är närmast dina användare.

Resursgruppen har också en region. Regionen anger var metadata för distribution lagras och där distributionsmallen kör från. Placera resursgruppen och dess resurser i samma region för att förbättra tillgängligheten under distributionen.

## <a name="scalability"></a>Skalbarhet

API Management-administratörer bör lägga till [cachelagringsprinciper](../api-management/api-management-howto-cache.md) där det är lämpligt för att öka skalbarheten i tjänsten. Cachelagring kan också minska belastningen på backend-tjänster.

Azure API Management Basic, Standard och Premium-nivåerna kan skaländras ut i en Azure-region som erbjuder större kapacitet. Administratörer kan använda den **kapacitet mått** alternativet i den **mått** menyn för att analysera användandet av deras tjänster och sedan skala upp eller efter behov.

Rekommendationer för att skala API Management-tjänsten:

- Skalning måste ta hänsyn till trafikmönster. Kunder med mer föränderliga trafikmönster har större behov av ökad kapacitet.
- Konsekvent kapacitet över 66% kan tyda på ett behov av att skala upp.
- Konsekvent kapacitet under 20% kan tyda på en möjlighet att skala ned.
- Vi rekommenderar alltid för att belastningstest API Management-tjänsten med en representativ belastning innan du aktiverar belastningen i produktion.

Premium-nivån-tjänster kan skalas ut över flera Azure-regioner. Kunder som distribuerar genom att skala tjänster över flera Azure-regioner få ett högre serviceavtal (99,95% jämfört med 99,9%) och kan tillhandahålla tjänster nära användare i flera regioner.

Logikappar serverlös modellen innebär att administratörer behöver inte planera för tjänstskalbarhet. Tjänsten skalas automatiskt för att möta efterfrågan.

## <a name="availability"></a>Tillgänglighet

SERVICEAVTALET för Azure API Management är för närvarande 99,9% för nivåerna Basic, Standard och Premium. Premium-nivån konfigurationer med distribution av minst en enhet i två eller fler regioner har inget eget serviceavtal på 99,95%.

SERVICEAVTALET för Azure Logic Apps är för närvarande 99,9%.

### <a name="backups"></a>Säkerhetskopior

Azure API Management-konfigurationen ska vara [säkerhetskopieras regelbundet](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (baserat på korrekthet ändring). Säkerhetskopiera filer ska lagras på en plats eller Azure-region som skiljer sig från där tjänsten finns. Kunder kan sedan välja ett av två alternativ för sin strategi för katastrofåterställning:

- En ny API Management-instans etableras i en disaster recovery-händelse, säkerhetskopian återställs till den nya instansen och DNS-poster är repointed.
- Kunder behålla en passiv kopia av deras tjänster i en annan Azure-region (genererar ytterligare kostnad). Säkerhetskopior återställs regelbundet till kopian. I en disaster recovery-händelse, behöver du repointed DNS-poster för att återställa tjänsten.

Eftersom logic apps kan skapas igen snabbt och är utan server, säkerhetskopieras de genom att spara en kopia av den associerade Azure Resource Manager-mallen. Mallar kan sparas till källkontroll och de kan integreras med en kunds processen för kontinuerlig integrering/kontinuerlig distribution (CI/CD).

Om en logikapp som publicerats via API Management flyttas till ett annat datacenter, kan du uppdatera dess plats. Om du vill uppdatera appens plats, använder du grundläggande PowerShell.skript för att uppdatera den **serverdel** egenskapen för API: et.

## <a name="manageability"></a>Hanterbarhet

Skapa separata resursgrupper för produktion, utveckling och testmiljöer. Med hjälp av separata resursgrupper gör det enklare att hantera distributioner, ta bort testdistributioner och tilldela åtkomsträttigheter.

Tänk på följande faktorer när du tilldelar resurser till resursgrupper:

- **Livscykel**. I allmänhet Lägg till resurser som har samma livscykel i samma resursgrupp.
- **Åtkomst**. Du kan använda [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC) för att tillämpa principer för åtkomst till resurser i en grupp.
- **Fakturering**. Du kan visa samlade kostnaderna för resursgruppen.
- **Prisnivå för API Management**. Vi rekommenderar att du använder Developer-nivån för utveckling och testmiljöer. För Förproduktion rekommenderar vi distribuerar en replik av produktionsmiljön, kör tester och stänger sedan för att minimera kostnader.

Mer information finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Distribution

Vi rekommenderar att du använder [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md) att distribuera API Management och Logikappar. Mallar gör det enklare att automatisera distribution via PowerShell eller Azure CLI.

Vi rekommenderar att ange Azure API Management och alla enskilda logikappar i sina egna, separata Resource Manager-mallar. När du använder separata mallar kan du lagra resurser i källkontrollsystem. Du kan också distribuera resurser tillsammans eller separat som en del av en process för CI/CD.

### <a name="versions"></a>Versioner

Varje gång du gör en konfiguration ändras till en logikapp (eller distribuera en uppdatering via Resource Manager-mall), sparas en kopia av den här versionen för din bekvämlighet (alla versioner som har en körningshistoriken hålls). Du kan använda dessa versioner spåra historiska ändringar och för att främja en version som den aktuella konfigurationen av logikappen. Exempelvis kan återställa du effektivt en logikapp.

API Management har två distinkta (men kostnadsfri) [versionshantering begrepp](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

- Versioner som används för att ge konsumenterna API ett urval av de kan använda API: et utifrån deras behov (till exempel v1, v2 eller beta, produktion).
- Revisioner som gör att API-administratörer kan göra ändringar på ett säkert sätt till ett API och sedan distribuera ändringarna till användare med valfria kommentarer.

I samband med distributionen är det en bra idé att tänka på API Management revisioner som ett sätt att göra ändringar på ett säkert sätt att behålla en ändring och göra API-kunderna medveten om dessa ändringar. En ändring kan skapas i en utvecklingsmiljö och distribueras mellan andra miljöer med hjälp av Resource Manager-mallar.

Men du kan använda revideringar för att testa ett API innan du gör den ”aktuella” och är tillgängliga för användare, rekommenderar vi inte den här mekanismen för belastning eller testning. Använd i stället separata test eller preproduktionsmiljöer.

### <a name="configuration"></a>Konfiguration

Aldrig checka in lösenord, åtkomstnycklar eller anslutningssträngar till källkontroll. Om dessa värden krävs kan du använda lämplig metod för att distribuera och skydda dessa värden. 

I Logic Apps, alla känsliga värden som behövs i logikappen (det inte går att skapa i form av en anslutning) lagras i Azure Key Vault och refereras till från en Resource Manager-mall. Vi rekommenderar också att med hjälp av distributionsparametrarna för mallen och parameterfilerna för varje miljö. Mer information finns i [skydda parametrar och indata i ett arbetsflöde](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

I API Management hemligheter hanteras med hjälp av objekt som kallas *namngivna värden* eller *egenskaper*. Objekten lagra på ett säkert sätt värden som kan användas i API Management-principer. Mer information finns i [hantera hemligheter i API Management](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnostik och övervakning

[API Management](../api-management/api-management-howto-use-azure-monitor.md) och [Logikappar](logic-apps-monitor-your-logic-apps.md) båda har stöd för operativa övervakning via [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor innehåller information baserat på mått som är konfigurerade för varje tjänst. Azure Monitor är aktiverat som standard.

Dessa alternativ är också tillgängliga för varje tjänst:

- Logic Apps loggar kan skickas till [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) för djupare analys och dashboarding.
- Konfigurera Azure Application Insights för DevOps övervakning har stöd för API Management.
- Har stöd för API Management på [Power BI-lösningsmall för anpassade API-analytics](http://aka.ms/apimpbi). Kunder kan använda lösningsmallen för att skapa egna anpassade analyslösning. Rapporter är tillgängliga i Power BI för företagsanvändare.

## <a name="security"></a>Säkerhet

Det här avsnittet innehåller säkerhetsaspekter som är specifika för Azure-tjänster som beskrivs i den här artikeln och som har distribuerats i arkitekturen som beskrivs. Det är inte en fullständig lista över säkerhetsmetoder.

- Använd rollbaserad åtkomstkontroll (RBAC) för att säkerställa rätt åtkomstnivå för användare.
- Skydda offentliga API-slutpunkter i API Management med hjälp av OAuth/OpenID Connect. Om du vill skydda offentliga API-slutpunkter, konfigurera en identitetsprovider och Lägg till en verifieringsprincip av JSON Web Token (JWT).
- Ansluta till backend-tjänster från API Management med hjälp av ömsesidig certifikat.
- Skydda HTTP utlösningsbaserade logikappar genom att skapa en godkänd IP-adress-lista som pekar till API Management IP-adressen. En godkänd IP-adress förhindrar att anropa logikappen från det offentliga internet utan att första gå via API Management.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [enterprise-integration med köer och händelser](logic-apps-architectures-enterprise-integration-with-queues-events.md).
