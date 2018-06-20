---
title: Azure Integration Services - enkel Enterprise Integration
description: Referensarkitektur som visar hur du implementerar en enkel enterprise integration mönster med Azure Logikappar och Azure API Management
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
ms.openlocfilehash: 17f591a470bf65d3c9365bca9c5ae2d5a6c9574f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232437"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>Enkel Enterprise Integration - Referensarkitektur

## <a name="overview"></a>Översikt

Denna Referensarkitektur visar en uppsättning beprövade metoder för ett integration program som använder Azure Integration Services. Den här arkitekturen kan fungera som grundval av många olika program mönster som kräver HTTP APIs, arbetsflöde och orchestration.

*Det finns många möjliga program av teknik, från en enkel punkt-till-platsen program till en fullständig enterprise service bus. Arkitektur serien anges de återanvändbara delarna för att skapa något integration program – arkitekter bör de komponenter som de behöver för sina program och infrastrukturer.*

   ![Arkitekturdiagram - enkla enterprise integration](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>Arkitektur

Arkitekturen har följande komponenter:

- Resursgrupp. En resursgrupp är en logisk behållare för Azure-resurser.
- Azure API Management. Azure API Management är en helt hanterad plattform för att publicera, skydda och omvandla HTTP APIs.
- Azure API Management Developer-portalen. Varje instans av Azure API Management levereras med en Developer-portalen som ger åtkomst till dokumentation, kodexempel och möjlighet att testa en API.
- Azure Logic Apps. Logic Apps är en serverlösa plattform för att skapa enterprise arbetsflödet och integrering.
- Kopplingar. Kopplingar som används av Logic Apps för att ansluta till tjänster som ofta används. Logic Apps har redan hundratals olika kopplingar, men de kan även skapas med hjälp av en anpassad koppling.
- IP-adress. Azure API Management-tjänsten har en fast offentlig IP-adress och ett domännamn. Domännamnet är en underdomän till azure-api.net, till exempel contoso. Azure-api.net. Logic Apps har också en offentlig IP-adress; i den här arkitekturen kan vi dock begränsa åtkomsten för att anropa Logic apps slutpunkter för endast IP-adress för API Management (för säkerhet).
- Azure DNS. Azure DNS är värdtjänsten för DNS-domäner som tillhandahåller namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster. Skapa DNS-poster som matchar det anpassade domännamnet till IP-adressen om du vill använda ett anpassat domännamn (t.ex contoso.com). Mer information finns i Konfigurera ett anpassat domännamn i Azure API Management.
- Azure Active Directory (AD Azure). Använd Azure AD eller en annan identitetsleverantör för autentisering. Azure AD tillhandahåller autentisering för åtkomst till API-slutpunkter (genom att skicka en JSON Web Token för API-hantering att verifiera) och kan säker åtkomst till API Management Developer Portal (endast Standard- och Premium-nivåer).

Den här arkitekturen har vissa grundläggande mönster med driften:

1. Befintlig serverdel HTTP APIs publiceras via API Management Developer-portalen, vilket gör att utvecklare (antingen interna för din organisation, externt eller båda) att integrera anrop till dessa API: er i program.
2. Sammansatta API: er skapas med hjälp av Logic Apps; samordna anrop till SAAS-system, Azure-tjänster och alla API: er som publicerats till API-hantering. Logic Apps publiceras också via API Management Developer-portalen.
3. Program får en säkerhetstoken för OAuth 2.0 krävs för att få åtkomst till en API som använder Azure Active Directory.
4. Azure API Management verifierar säkerhetstoken och skickar begäran till backend API/Logikapp.

## <a name="recommendations"></a>Rekommendationer

Dina krav kan vara annorlunda från den arkitektur som beskrivs här. Använd rekommendationerna i det här avsnittet som en utgångspunkt.

### <a name="azure-api-management-tier"></a>Azure API Management-nivå

Använd Basic, Standard eller Premium nivåer eftersom de erbjuder ett SLA för produktion och stöd för skalbar i Azure-regionen (antal enheter varierar beroende på nivån). Premium-nivån har också stöd för skalbar över flera Azure-regioner. Basera den nivå som du valde på din nivå av dataflödet som krävs och funktionen. Mer information finns i [priser för API Management](https://azure.microsoft.com/pricing/details/api-management/).

Du debiteras för alla instanser av API-hantering när de körs. Om du har skalats ut och behöver inte den nivån av prestanda hela tiden, Överväg att dra nytta av API-hantering timvis fakturerings- och skala.

### <a name="logic-apps-pricing"></a>Logic Apps priser

Logic Apps fungerar som en [serverlösa](logic-apps-serverless-overview.md) modellen – fakturering beräknas baserat på körning av åtgärden och anslutningen. Se [Logic Apps priser](https://azure.microsoft.com/pricing/details/logic-apps/) för mer information. Det finns inga nivå överväganden för Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Logic Apps för asynkron API-anrop

Logic Apps fungerar bäst i scenarier som inte kräver låg latens – t.ex. asynkrona eller halvautomatisk lång körs API-anrop. Låg latens krävs (t.ex. ett anrop som blockerar ett användargränssnitt) rekommenderar vi att API eller igen med en annan teknik, t.ex. Azure Functions eller webb-API som distribueras med Apptjänst. Vi rekommenderar ändå att detta API är fronted använda API Management API konsumenterna.

### <a name="region"></a>Region

Etablera API Management och Logic Apps i samma region för att minimera svarstider i nätverket. Du väljer normalt den region som är närmast dina användare.

Resursgruppen har också en region som anger var metadata för programdistribution lagras, och där distributionsmallen körs från. Placera resursgruppen och dess resurser i samma region. Detta kan förbättra tillgängligheten under distributionen.

## <a name="scalability-considerations"></a>Skalbarhetsöverväganden

API Management-administratörer bör lägga till [cachelagring principer](../api-management/api-management-howto-cache.md) där det är lämpligt att öka skalbarheten för tjänsten och minska belastningen på deras backend-tjänster.

Azure API Management Basic, Standard och Premium nivåer kan skaländras ut med i en Azure-region att ge större kapacitet. Administratörer kan använda kapacitet mått i menyn mått för att analysera användning av tjänsten och skala upp eller efter behov.

Rekommendationer för att skala en API Management-tjänst:

- Skalning behöver ta hänsyn till trafikmönster – kunder med mer obeständigt trafikmönster har större behov av ökade kapacitet.
- Konsekvent kapacitet än 66% kan indikera behöver skala upp.
- Konsekvent kapacitet under 20% kan indikera en möjlighet att skala.
- Det rekommenderas att läsa in alltid testa dina API Management-tjänsten med ett representativt belastning innan du aktiverar i produktion.

Premium-nivån tjänster kan skaländras ut över flera Azure-regioner. Kunder distribuera på det här sättet kommer att få en högre SLA (99,95% i stället för 99,9%) och kan tillhandahålla tjänster nära användare i flera områden.

Logic Apps serverlösa modellen innebär att administratörer inte behöver göra extra faktor för tjänsten skalbarhet; tjänsten skalas automatiskt för att uppfylla begäran.

## <a name="availability-considerations"></a>Överväganden för tillgänglighet

Vid tidpunkten för skrivning är servicenivåavtalet (SLA) för Azure API Management 99,9% för nivåerna Basic, Standard och Premium. Premium-nivån konfigurationer med distribution av minst en enhet i två eller fler regioner har ett SLA på 99,95%.

Servicenivåavtal (SLA) för Azure Logic Apps är 99,9% vid tidpunkten för skrivning.

### <a name="backups"></a>Säkerhetskopior

Konfigurationen av Azure API Management ska vara [säkerhetskopieras regelbundet](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (korrekt utifrån regelbundenhet förändring), och de säkerhetskopiera filerna som lagras på en plats eller Azure-Region olika där tjänsten finns. Kunder kan sedan välja ett av två alternativ för sina strategi för Katastrofåterställning:

1. En ny API Management-instans har etablerats i en DR-händelse, säkerhetskopieringen har återställts till den och DNS-poster är repointed.
2. Kunder Behåll en passiv kopia från tjänsten i en annan Azure-region (medför ytterligare kostnad) säkerhetskopieringar regelbundet återställs till den. DNS-poster måste vara repointed om du vill återställa tjänsten i en DR-händelse.

Då Logic Apps kan skapas snabbt och är serverlösa, säkerhetskopieras de genom att spara en kopia av den associerade Azure Resource Manager-mallen. De kan sparas till källa kontrollen/integreras i en kunder kontinuerlig integration/kontinuerlig (CI/CD) distributionsprocessen.

Logic Apps som publicerats via API-hantering måste deras platser uppdateras bör de flyttas till ett annat datacenter. Detta kan åstadkommas genom ett enkla PowerShell-skript för att uppdatera egenskapen serverdelen för API: et.

## <a name="manageability-considerations"></a>Överväganden för hantering

Skapa separata resursgrupper för produktion, utveckling, och testa miljöer. Det här gör det enklare att hantera distributioner, ta bort testdistributioner och tilldela åtkomsträttigheter.
Tänk på följande när du tilldelar resurser till resursgrupper:

- Livscykel. I allmänhet ska du placera resurser med samma livscykel i samma resursgrupp.
- Åtkomst. Du kan använda [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC) för att tillämpa principer för åtkomst till resurser i en grupp.
- Fakturering. Du kan visa de samlade kostnaderna för resursgruppen.
- Prisnivån för API Management – bör du använda Developer nivån för utvecklings- och testmiljöer. För Förproduktion rekommenderar vi distribuera en replik av produktionsmiljön, köra tester och stänger sedan för att minimera kostnaderna.

Mer information finns i [resursgruppen](../azure-resource-manager/resource-group-overview.md) Översikt.

### <a name="deployment"></a>Distribution

Vi rekommenderar att du använder [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md) att distribuera både Azure API Management och Azure Logic Apps. Mallarna gör det enklare att automatisera distribution via PowerShell eller Azure-kommandoradsgränssnittet (CLI).

Vi rekommenderar att placera Azure API Management och eventuella appar som enskilda logik i sina egna separata Resource Manager-mallar. Detta gör att lagra dem i kontrollen källsystem. Dessa mallar kan sedan distribueras tillsammans eller var för sig som en del av en Distributionsprocess för kontinuerlig integration/kontinuerlig (CI/CD).

### <a name="versions"></a>Versioner

Varje gång du gör en konfiguration ändra till en Logikapp (eller distribuerar en uppdatering via en Resource Manager-mall), behålla en kopia av den här versionen för din bekvämlighet (alla versioner som kör tidigare sparas). Du kan använda dessa versioner för att spåra historiska ändringar och även befordra en version som den aktuella konfigureringen av logikappen; Gör innebär du kan effektivt återställning en Logikapp, till exempel.

API Management har två distinkta (men kostnadsfri) [versionshantering begrepp](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

- Versioner som används för att ge dina API-användare med ett urval av de kan använda API: et utifrån deras behov (t.ex. v1, v2 eller beta, produktion).
- Revisioner administratörerna API på ett säkert sätt göra ändringar i en API och distribuera dem till användare med valfria kommentarer.

Behåll en ändringshistoriken i samband med distributionen – API-hantering revisioner bör betraktas som ett sätt att göra ändringar på ett säkert sätt, och se API konsumenter medveten om dessa ändringar. En ändring kan skapas i en utvecklingsmiljö och distribueras mellan andra miljöer med Resource Manager-mallar.

Medan revisioner kan används för att testa en API innan den görs 'current- och göras tillgänglig för användare, rekommenderar vi inte använder den här mekanismen för inläsning eller Integreringstestning – separat test eller förproduktionsmiljöer ska användas i stället.

### <a name="configuration"></a>Konfiguration

Sök aldrig lösenord, snabbtangenter eller anslutningssträngar i till källkontroll. Om de behövs använder du lämplig metod för att distribuera och skydda dessa värden. 

I Logic Apps alla känsliga värden som behövs i logikappen (som inte kan skapas i form av en anslutning) lagras i Azure Key Vault och refereras till från en Resource Manager-mall. Vi rekommenderar också använder distribution mallparametrar tillsammans med parametern-filer för varje miljö. Mer information om [skydda parametrar och indata i ett arbetsflöde](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

I API Management hanteras hemligheter med hjälp av objekt som kallas namngivna värden och egenskaper. Dessa lagras säkert värden som kan användas i API Management-principer. Se hur du [hantera hemligheter i API Management](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnostik och övervakning

Båda [API Management](../api-management/api-management-howto-use-azure-monitor.md) och [Logikappar](logic-apps-monitor-your-logic-apps.md) stöder Operativ övervakning via [Azure-Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure övervakaren är aktiverad som standard och ger information baserat på de olika mätvärden som är konfigurerad för varje tjänst.

Det finns dessutom ytterligare alternativ för varje tjänst:

- Logic Apps loggar kan skickas till [logganalys](logic-apps-monitor-your-logic-apps-oms.md) för djupare analys och dashboarding.
- API Management har stöd för att konfigurera Application Insights för Dev Ops övervakning.
- API-hantering stöder den [Power BI-Lösningsmall för anpassade API analytics](http://aka.ms/apimpbi). Den här lösningen mallen kan kunderna skapa sina egna anpassade analytics-lösning med rapporter som är tillgängliga i Power BI för användare i verksamheten.

## <a name="security-considerations"></a>Säkerhetsöverväganden

Det här avsnittet innehåller säkerhetsaspekter som är specifika för Azure-tjänster i den här artikeln som distribuerats i arkitekturen som beskrivs. Det är inte en fullständig lista över säkerhetsmetoder.

- Använda rollbaserad åtkomstkontroll (RBAC) för att säkerställa lämpliga nivåer för användare.
- Skydda offentliga API-slutpunkter i API Management med hjälp av OAuth/öppna IDConnect. Du kan göra detta genom att konfigurera en identitetsleverantör och lägga till en princip för JWT-verifiering.
- Ansluta till backend-tjänster från API Management med hjälp av ömsesidig certifikat
- Skydda http-utlösaren-baserade Logic Apps genom att skapa en IP-adress godkända pekar på IP-adress för API Management. Detta förhindrar att anropa logikappen från det offentliga internet utan att först gå via API-hantering.

Denna Referensarkitektur visades hur du skapar en enkel enterprise integration-plattform med Azure Integration Services.

## <a name="next-steps"></a>Nästa steg

* [Enterprise Integration med köer och händelser](logic-apps-architectures-enterprise-integration-with-queues-events.md)
