---
title: Azure-integreringstjänster – enkel Enterprise-Integration
description: Referensarkitektur visar hur du implementerar en enkel enterprise integration-mönstret med Azure Logic Apps och Azure API Management
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
ms.openlocfilehash: df86ca5aed405ab5eda05c1dd207f0b6656bfd96
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860205"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>Enkel Enterprise-Integration - Referensarkitektur

## <a name="overview"></a>Översikt

Denna Referensarkitektur visas en uppsättning beprövade metoder för ett integration-program som använder Azure Integration Services. Den här arkitekturen kan fungera som den här grunden för många olika mönster som kräver HTTP APIs, arbetsflöde och dirigering.

*Det finns många möjliga program av integreringsteknik, från ett enkelt att punkt till punkt-program till en fullständig enterprise service bus. Den här arkitekturen-serien innehåller återanvändbara komponenter som kan vara relevanta för att skapa ett allmänt integration-program – arkitekter bör tänka på vilka specifika kompontener som de behöver du implementera för sina program och infrastruktur.*

   ![Arkitekturdiagram - enkel enterprise-integration](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>Arkitektur

Arkitekturen har följande komponenter:

- Resursgrupp. En [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) är en logisk behållare för Azure-resurser.
- Azure API Management. [Azure API Management](https://docs.microsoft.com/azure/api-management/) är en fullständigt hanterad plattform för att publicera, skydda och omvandla HTTP APIs.
- Utvecklarportalen i Azure API Management. Varje instans av Azure API Management som levereras med en [Utvecklarportalen](https://docs.microsoft.com/azure/api-management/api-management-customize-styles), vilket ger åtkomst till dokumentation, kodexempel och möjlighet att testa ett API.
- Azure Logic Apps. [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) är en plattform utan server för att skapa enterprise arbetsflödet och integrationskontot.
- Kopplingar. [Kopplingar](https://docs.microsoft.com/azure/connectors/apis-list) används av Logic Apps för att ansluta till tjänster som ofta används. Logic Apps har redan hundratals olika anslutningar, men de kan även skapas med hjälp av en anpassad anslutningsapp.
- IP-adress. Azure API Management-tjänsten har en fast offentlig [IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) och ett domännamn. Domännamnet är en underdomän till azure-api.net, till exempel contoso.azure-api.net. Logic Apps och Service Bus också ha en offentlig IP-adress; i den här arkitekturen kan vi dock begränsa åtkomsten för att anropa Logic apps slutpunkter till endast de IP-adress för API Management (för säkerhet). Anrop till Service Bus skyddas av en signatur för delad åtkomst.
- Azure DNS. [Azure DNS](https://docs.microsoft.com/azure/dns/) är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster. Skapa DNS-poster som mappar det anpassade domännamnet till IP-adressen om du vill använda ett anpassat domännamn (t.ex contoso.com). Mer information finns i Konfigurera ett anpassat domännamn i Azure API Management.
- Azure Active Directory (AD Azure). Använd [Azure AD](https://docs.microsoft.com/azure/active-directory/) eller en annan identitetsleverantör för autentisering. Azure AD tillhandahåller autentisering för att komma åt API-slutpunkter (genom att skicka en [JSON Web Token för API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) att verifiera) och kan skydda åtkomst till Utvecklarportalen för API Management (endast Standard och Premium-nivåer).

Den här arkitekturen har vissa grundläggande mönster i dess drift:

2. Sammansatta API: er skapas med hjälp av Logic Apps; samordna anrop till SAAS-system, Azure-tjänster och alla API: er som publiceras till API Management. Den [Logic Apps publiceras även](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) via Utvecklarportalen för API Management.
3. Program [skaffa ett OAuth 2.0-säkerhetstoken](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) krävs för att få åtkomst till ett API med Azure Active Directory.
4. Azure API Management [verifierar säkerhetstoken](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad), och skickar begäran till API/Logic App-serverdelen.

## <a name="recommendations"></a>Rekommendationer

Dina specifika krav kan skilja sig från den allmänna arkitektur som beskrivs här. Använd rekommendationerna i det här avsnittet som en utgångspunkt.

### <a name="azure-api-management-tier"></a>Azure API Management-nivå

Använd Basic, Standard eller Premium-nivåerna eftersom de erbjuder ett serviceavtal (SLA) för produktion och stöd för skalbar inom Azure-region (antal enheter varierar beroende på nivån). Premium-nivån har även stöd för skalbar över flera Azure-regioner. Basera den nivå som du valde på din dataflödesnivå som krävs och funktionsuppsättning. Mer information finns i [API Management-priser](https://azure.microsoft.com/pricing/details/api-management/).

Du debiteras för alla API Management-instanser när de körs. Om du har skalats och behöver inte den här nivån av prestanda hela tiden, Överväg att utnyttja API Management timvis debitering och skala.

### <a name="logic-apps-pricing"></a>Prissättning för Logic Apps

Logikappar fungerar som en [serverlös](logic-apps-serverless-overview.md) modellen – debiteringen beräknas baserat på åtgärder och anslutningsprogram körning. Se [Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps/) för mer information. Det finns inga nivån överväganden för Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Logic Apps för asynkron API-anrop

Logikappar fungerar bäst i scenarier som inte kräver låg latens – t.ex. asynkrona eller halvautomatisk lång körs API-anrop. Om låg latens krävs (t.ex. ett anrop som blockerar ett användargränssnitt) rekommenderar vi att API: et eller igen med en annan teknik, t.ex. Azure Functions eller webb-API som distribueras med App Service. Vi rekommenderar ändå att detta API är fronted med hjälp av API Management API som använder.

### <a name="region"></a>Region

Etablera API Management och Logikappar i samma region för att minimera nätverkssvarstiden. Du väljer normalt den region som är närmast dina användare.

Resursgruppen har också en region som anger var metadata för distribution lagras, och där distributionsmallen körs från. Placera resursgruppen och dess resurser i samma region. Detta kan förbättra tillgängligheten under distributionen.

## <a name="scalability-considerations"></a>Skalbarhetsöverväganden

API Management-administratörer bör lägga till [cachelagringsprinciper](../api-management/api-management-howto-cache.md) där det är lämpligt att öka skalbarheten för tjänsten och minska belastningen på sina serverdelstjänster.

Azure API Management Basic, Standard och Premium-nivåerna kan skaländras ut med i en Azure-region som erbjuder större kapacitet. Administratörer kan använda kapacitet mått i mått-menyn för att analysera användandet av deras tjänster och skala upp eller efter behov.

Rekommendationer för att skala API Management-tjänsten:

- Skalningsbehov med hänsyn till trafikmönster – kunder med mer föränderliga trafikmönster har större behov av ökad kapacitet.
- Konsekvent kapacitet över 66% kan tyda på ett behov av att skala upp.
- Konsekvent kapacitet under 20% kan indikera en möjlighet att skala ned.
- Vi rekommenderar alltid att läsa in testa API Management-tjänsten med en representativ belastning innan du aktiverar i produktion.

Premium-nivån-tjänster kan skalas ut över flera Azure-regioner. Kunder som distribuerar på det här sättet kommer få ett högre serviceavtal (99,95% till skillnad från 99,9%) och kan tillhandahålla tjänster nära användare i flera regioner.

Logic Apps serverlös modellen innebär att administratörer inte behöver göra extra bör tänka på vid tjänstskalbarhet; tjänsten skalas automatiskt för att möta efterfrågan.

## <a name="availability-considerations"></a>Överväganden för tillgänglighet

I det här dokumentet skrivs är serviceavtalet (SLA) för Azure API Management 99,9% för nivåerna Basic, Standard och Premium. Premium-nivån konfigurationer med distribution av minst en enhet i två eller fler regioner har inget eget serviceavtal på 99,95%.

I det här dokumentet skrivs är serviceavtalet (SLA) för Azure Logic Apps 99,9%.

### <a name="backups"></a>Säkerhetskopior

Azure API Management-konfigurationen ska vara [säkerhetskopieras regelbundet](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (korrekt baserat på korrekthet ändring), och de säkerhetskopiera filerna som lagras på en plats eller Azure-Region olika där tjänsten finns. Kunder kan sedan välja ett av två alternativ för sin strategi för Haveriberedskap:

1. En ny API Management-instans etableras i en DR-händelse, säkerhetskopian återställs till den och DNS-poster är repointed.
2. Håll kunderna en passiv kopia av deras tjänster i en annan Azure-region (genererar ytterligare kostnad) säkerhetskopieringar återställs regelbundet till den. I en DR-händelse, behöver du repointed DNS-poster för att återställa tjänsten.

Eftersom Logic Apps kan skapas på nytt snabbt och utan server, säkerhetskopieras de genom att spara en kopia av den associerade Azure Resource Manager-mallen. Dessa kan sparas till källan kontroll/integreras i en kunders processen för kontinuerlig integrering/kontinuerlig distribution (CI/CD).

Logic Apps som har publicerats via API Management måste deras platser uppdateras bör de flyttar till ett annat datacenter. Detta kan åstadkommas via ett enkelt PowerShell-skript för att uppdatera egenskapen serverdel för API: et.

## <a name="manageability-considerations"></a>Överväganden för hantering

Skapa separata resursgrupper för produktion, utveckling och testmiljöer. Det här gör det enklare att hantera distributioner, ta bort testdistributioner och tilldela åtkomsträttigheter.
Tänk på följande när du tilldelar resurser till resursgrupper:

- Livscykel. I allmänhet ska du placera resurser med samma livscykel i samma resursgrupp.
- Åtkomst. Du kan använda [Role-Based Access Control](../role-based-access-control/overview.md) (RBAC) för att tillämpa principer för åtkomst till resurser i en grupp.
- Fakturering. Du kan visa de samlade kostnaderna för resursgruppen.
- Prisnivå för API Management – vi rekommenderar att du använder Developer-nivån för utvecklings- och testmiljöer. För Förproduktion rekommenderar vi distribuerar en replik av produktionsmiljön, kör tester och stänger sedan för att minimera kostnader.

Mer information finns i [resursgrupp](../azure-resource-manager/resource-group-overview.md) Översikt.

### <a name="deployment"></a>Distribution

Vi rekommenderar att du använder [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md) att distribuera både Azure API Management och Azure Logic Apps. Mallar gör det enklare att automatisera distribution via PowerShell eller kommandoradsgränssnittet (CLI).

Vi rekommenderar att ange Azure API Management och alla enskilda Logikappar i sina egna separata Resource Manager-mallar. Detta gör att lagra dem i källkontrollsystem. Dessa mallar kan sedan distribueras tillsammans eller separat som en del av en process för kontinuerlig integrering/kontinuerlig (CI/CD).

### <a name="versions"></a>Versioner

Varje gång du gör en konfiguration ändras till en Logikapp (eller distribuera en uppdatering via Resource Manager-mall), sparas en kopia av den här versionen för din bekvämlighet (alla versioner som har en körningshistoriken sparas). Du kan använda dessa versioner för att spåra ändringar av historiska och främjar också en version som den aktuella konfigurationen av logikappen; Gör innebär du kan effektivt återställa en Logikapp, till exempel.

API Management har två distinkta (men kostnadsfri) [versionshantering begrepp](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

- Versioner som används för att ge konsumenterna API ett urval av de kan använda API: et utifrån deras behov (t.ex. v1, v2 eller beta, produktion).
- Revisioner som administratörerna har API på ett säkert sätt göra ändringar i ett API och distribuera dem till användare med valfria kommentarer.

Att behålla en ändring i samband med distributionen – API Management revisioner ska betraktas som ett sätt att göra ändringar på ett säkert sätt, och göra API-kunderna medveten om dessa ändringar. En revision skapas i en utvecklingsmiljö och distribueras mellan andra miljöer med hjälp av Resource Manager-mallar.

Även om revisioner kan används för att testa ett API innan den görs ”aktuella” och göras tillgänglig för användare, rekommenderar vi inte den här mekanismen för belastning eller testning – separat test- eller testmiljöer ska användas istället.

### <a name="configuration"></a>Konfiguration

Sök aldrig lösenord, åtkomstnycklar eller anslutningssträngar i till källkontroll. Om de behövs, kan du använda lämplig metod för att distribuera och skydda dessa värden. 

I Logic Apps, alla känsliga värden som behövs i logikappen (det inte går att skapa i form av en anslutning) lagras i Azure Key Vault och refereras till från en Resource Manager-mall. Vi rekommenderar också med hjälp av mallen distributionsparametrarna tillsammans med parameterfiler för varje miljö. Mer information om hur [skydda parametrar och indata i ett arbetsflöde](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

I API Management hanteras hemligheter med hjälp av objekt som kallas med namnet värden och egenskaper. Dessa lagra på ett säkert sätt värden som kan användas i API Management-principer. Se hur du [hantera hemligheter i API Management](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnostik och övervakning

Båda [API Management](../api-management/api-management-howto-use-azure-monitor.md) och [Logikappar](logic-apps-monitor-your-logic-apps.md) stöder driftövervakning via [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor är aktiverat som standard och ger information baserat på de olika mått som konfigurerats för varje tjänst.

Det finns dessutom ytterligare alternativ för varje tjänst:

- Logic Apps loggar kan skickas till [Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) för djupare analys och dashboarding.
- Konfigurera Application Insights för övervakning av Dev Ops har stöd för API Management.
- Har stöd för API Management på [Power BI-Lösningsmall för anpassade API-analytics](http://aka.ms/apimpbi). Den här lösningsmallen kan kunderna skapa sina egna anpassade analyslösning med rapporter som är tillgängliga i Power BI för företagsanvändare.

## <a name="security-considerations"></a>Säkerhetsöverväganden

Det här avsnittet innehåller säkerhetsaspekter som är specifika för Azure-tjänster som beskrivs i den här artikeln har distribuerats i arkitekturen som beskrivs. Det är inte en fullständig lista över säkerhetsmetoder.

- Använd rollbaserad åtkomstkontroll (RBAC) för att säkerställa rätt åtkomstnivå för användare.
- Skydda offentliga API-slutpunkter i API Management med hjälp av OAuth/öppna IDConnect. Du kan göra detta genom att konfigurera en identitetsprovider och lägger till en princip för JWT-verifiering.
- Anslut till serverdelstjänster från API Management med hjälp av ömsesidig certifikat
- Skydda HTTP utlösningsbaserade Logic Apps genom att skapa en IP-adress-vitlistan som pekar på IP-adressen för API Management. Detta förhindrar att anropa logikappen från det offentliga internet utan att första gå via API Management.

Den här referensarkitekturen visar hur du skapar en enkel företagsplattform på integrering med Azure Integration Services.

## <a name="next-steps"></a>Nästa steg

- [Enterprise-Integration med köer och händelser](logic-apps-architectures-enterprise-integration-with-queues-events.md)
