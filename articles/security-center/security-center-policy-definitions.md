---
title: Azure principdefinitioner som övervakas i Azure Security Center | Microsoft Docs
description: Azure principdefinitioner som övervakas i Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/19/2019
ms.author: monhaber
ms.openlocfilehash: 25ed9cb624474d5da56d385f4e9c155918ec8eab
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428331"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Azure säkerhetsprinciper som övervakas av Security Center
Den här artikeln innehåller en lista över Azure Policy-definitioner som du kan övervaka i Azure Security Center. Mer information om säkerhetsprinciper finns [arbeta med säkerhetsprinciper](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Tillgängliga säkerhetsprinciper

Mer om de inbyggda principer som övervakas av Security Center finns i följande tabell:

| Princip | Vad principen gör |
| --- | --- |
|Diagnostikloggar i Virtual Machine Scale Sets ska aktiveras|Vi rekommenderar att du aktiverar loggar så att en aktivitet spårning är tillgänglig för undersökning efter en incident eller kompromisser.|
|Alla regler förutom RootManageSharedAccessKey ska tas bort från namnområdet för Händelsehubben|Azure Event Hubs-klienter bör inte använda en åtkomstprincip för namnområdesnivån som ger åtkomst till alla köer och ämnen i ett namnområde. Du bör skapa åtkomstprinciper på enhetsnivå för köer och ämnen för att ge åtkomst till endast den specifika enheten så att den överensstämmer med den lägsta behörighet säkerhetsmodellen.|
|Auktoriseringsregler för Event Hub-entitet ska definieras|Granska förekomst av auktoriseringsregler på Händelsehubbar entiteter för att bevilja åtkomst med lägsta behörighet.|
|Åtkomst till lagringskonton med brandvägg och konfigurationer av virtuella ska vara begränsad|Granska obegränsad nätverksåtkomst i brandväggsinställningarna för storage-konto. Konfigurera regler så att endast program från tillåtna nätverk har åtkomst till lagringskontot. Bevilja åtkomst till trafik från specifika Azure-nätverk eller till offentliga internet, IP-adress-intervall för att tillåta anslutningar från specifika internet eller lokala klienter.|
|Granska användning av anpassade RBAC-regler|Granska inbyggda roller, som ”ägare, deltagare, läsare” i stället för anpassade rollbaserad åtkomstkontroll (RBAC) roller, som är felbenägna. Användning av anpassade roller behandlas som ett undantag och kräver omfattande granskning och hotmodellering.|
|Diagnostikloggar i Azure Stream Analytics ska aktiveras|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Säker överföring till storage-konton måste vara aktiverat|Granska kraven för säker överföring i ditt lagringskonto. Säker överföring är ett alternativ som tvingar ditt storage-konto för att acceptera begäranden endast från säkra anslutningar (HTTPS). Använda HTTPS säkerställer autentisering mellan servern och tjänsten. Det skyddar också data under överföring från nätverksattacker lager, till exempel man-in-the-middle, avlyssning och sessionskapning.|
|Azure AD-administratör för SQLServer som ska etableras|Granska etablering av en administratör för Azure Active Directory (Azure AD) för SQL Server för att aktivera Azure AD-autentisering. Azure AD-autentisering har stöd för förenklad behörighetshantering och centraliserad Identitetshantering för databasanvändare och andra Microsoft-tjänster.|
|Alla regler förutom RootManageSharedAccessKey ska tas bort från Service Bus-namnområde|Azure Service Bus-klienter bör inte använda en åtkomstprincip för namnområdesnivån som ger åtkomst till alla köer och ämnen i ett namnområde. Skapa principer för åtkomst på enhetsnivå för köer och ämnen för att ge åtkomst till endast den specifika enheten så att den överensstämmer med den lägsta behörighet säkerhetsmodellen.|
|Diagnostikloggar i Service Bus ska aktiveras|Granska aktivering av loggar och låt dem dig för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Egenskapen ClusterProtectionLevel till EncryptAndSign i Service Fabric ska anges|Service Fabric tillhandahåller tre skyddsnivåer för nod-till-nod-kommunikation som använder ett klustercertifikat för primär: Ingen, logga in och EncryptAndSign. Ställa in skydd så att alla nod-till-nod-meddelanden är krypterad och har signerats digitalt.|
|Klientautentisering bör använda Azure Active Directory|Granska användning av klientautentisering endast via Azure AD i Service Fabric.|
|Diagnostics-loggar i Search services ska aktiveras|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Endast säkra anslutningar till Redis-cachen ska aktiveras|Granska aktiverar endast anslutningar via SSL till Azure Cache för Redis. Användning av säkra anslutningar säkerställer autentisering mellan servern och tjänsten. Det skyddar också data under överföring från nätverksattacker lager, till exempel man-in-the-middle, avlyssning och sessionskapning.|
|Diagnostikloggar i Logic Apps ska aktiveras|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Diagnostikloggar i Key Vault ska aktiveras|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Diagnostikloggar i Event Hub ska aktiveras|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Diagnostikloggar i Azure Data Lake Store ska aktiveras|Granska aktivering av loggar och låt dem upp till ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Diagnostikloggar i Data Lake Analytics ska aktiveras|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Storage-konton som ska migreras till nya AzureRM-resurser|Använda Azure Resource Manager för storage-konton för att tillhandahålla förbättringar av säkerhet. Exempel på dessa är: <br>-Starkare åtkomstkontroll (RBAC)<br>– Granskning bättre<br>– Azure Resource Manager-baserade distribution och styrning<br>-Åtkomst till hanterade identiteter<br>-Åtkomst till Azure Key Vault för hemligheter<br>– Azure AD-baserad autentisering<br>-Stöd för taggar och resursgrupper för enklare säkerhetshantering|
|Virtuella datorer som ska migreras till nya AzureRM-resurser|Använda Azure Resource Manager för dina virtuella datorer för att tillhandahålla förbättringar av säkerhet.  Exempel på dessa är: <br>-Starkare åtkomstkontroll (RBAC)<br>– Granskning bättre<br>– Azure Resource Manager-baserade distribution och styrning<br>-Åtkomst till hanterade identiteter<br>-Åtkomst till Azure Key Vault för hemligheter<br>– Azure AD-baserad autentisering<br>-Stöd för taggar och resursgrupper för enklare säkerhetshantering|
|Måttvarningsregler som ska konfigureras på Batch-konton|Granska konfigurationen av måttaviseringsregler på Azure Batch-konton gör nödvändiga måttet.|
|Diagnostikloggar i Batch-konton måste vara aktiverat|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Kryptering måste vara aktiverad på Automation-konto variabler|Det är viktigt att aktivera kryptering av Azure Automation-konto variabler för tillgångar när du lagrar känsliga data.|
|Diagnostikloggar i App Services ska aktiveras|Granska aktiverar diagnostikloggar i appen. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Transparent datakryptering på SQL-databaser ska aktiveras|Granska status för transparent datakryptering för SQL-databaser.|
|SQL server-granskning ska aktiveras|Granska förekomst av SQL-granskning på servernivå.|
|\[Förhandsversion]: Övervaka okrypterade SQL-databas i Azure Security Center|Azure Security Center övervakar okrypterade SQL-servrar eller databaser som rekommenderas.|
|\[Förhandsversion]: Övervaka ogranskad SQL-databas i Azure Security Center|Azure Security Center övervakar SQL-servrar och databaser som inte har SQL-granskning aktiverat vilket rekommenderas.|
|\[Förhandsversion]: Systemuppdateringar bör vara installerad på dina datorer|Azure Security Center övervakar system säkerhetsuppdateringar som saknas på dina servrar, vilket rekommenderas.|
|\[Förhandsversion]: Granska blobbkryptering som saknas för lagringskonton|Granska lagringskonton som inte använder blobbkryptering. Detta gäller endast Microsoft storage resurstyper, inte storage från andra leverantörer. Azure Security Center övervakar möjlig just-in-time-nätverksåtkomst som rekommenderas.|
|\[Förhandsversion]: Just-In-Time-nätverksåtkomstkontroll ska tillämpas på virtuella datorer|Azure Security Center övervakar möjlig just-in-time-nätverksåtkomst som rekommenderas.|
|\[Förhandsversion]: Anpassningsbara programkontroller måste vara aktiverad på virtuella datorer|Azure Security Center övervakar möjliga godkända programkonfiguration.|
|\[Förhandsversion]: Saknade Nätverkssäkerhetsgrupper för virtuella datorer ska konfigureras|Azure Security Center övervakar nätverkssäkerhetsgrupper med alltför Tillåtande regler som rekommenderas.|
|\[Förhandsversion]: Säkerhetsproblem i Säkerhetskonfiguration på dina datorer bör åtgärdas|Azure Security Center övervakar servrar som inte uppfyller grundkonfigurationen enligt rekommendationer.| 
|\[Förhandsversion]: Endpoint protection ska installeras på virtuella datorer|Azure Security Center övervakar servrar som inte har en installerad agent för Microsoft System Center Endpoint Protection, vilket rekommenderas.|
|\[Förhandsversion]: Diskkryptering ska tillämpas på virtuella datorer|Azure Security Center övervakar virtuella datorer som inte har aktiverat enligt rekommendationer för diskkryptering.|
|\[Förhandsversion]: Sårbarheter som bör åtgärdas av en lösning för Sårbarhetsbedömning|Övervaka sårbarheter som identifieras av lösning för sårbarhetsbedömning och virtuella datorer som inte har en lösning för sårbarhetsbedömning i Azure Security Center enligt rekommendationer.|
|\[Förhandsversion]: Övervaka oskyddat webbprogram i Azure Security Center|Azure Security Center ska övervaka webbprogram som saknar Brandvägg för web application enligt rekommendationer.|
|\[Förhandsversion]: Endpoint protection-lösning bör installeras på virtuella datorer|Azure Security Center övervakar nätverks-slutpunkter som saknar nästa generations brandväggsskydd enligt rekommendationer.|
|\[Förhandsversion]: Säkerhetsproblem på SQL-databaser bör åtgärdas|Övervaka sårbarhetsbedömning skanningsresultat och rekommenderar så åtgärda sårbarheter i databasen.|
|\[Förhandsversion]: Högst 3 ägare bör anges för din prenumeration|Vi rekommenderar att du anger upp till tre prenumerationsägare att minska risken för intrång genom en komprometterad ägare.|
|\[Förhandsversion]: Det bör finnas fler än en ägare till prenumerationen|Vi rekommenderar att du anger mer än en prenumerationsägare så administratörsåtkomst redundans.|
|\[Förhandsversion]: MFA måste vara aktiverad på konton med ägarbehörigheter för din prenumeration |Multifaktorautentisering (MFA) bör aktiveras för alla prenumerationskonton som har ägarbehörighet för att förhindra att överträdelser görs avseende konton och resurser.|
|\[Förhandsversion]: MFA måste vara aktiverad på din prenumerationskonton med skrivbehörighet|Multifaktorautentisering ska aktiveras för alla prenumerationskonton med skrivbehörighet för att förhindra att överträdelser görs avseende konton och resurser.|
|\[Förhandsversion]: MFA måste vara aktiverad på din prenumerationskonton med läsbehörigheter|Multifaktorautentisering ska aktiveras för alla prenumerationskonton som har läsbehörighet till att förhindra att överträdelser görs avseende konton och resurser.|
|\[Förhandsversion]: Bör ta bort inaktuella konton med ägarbehörigheter från prenumerationen|Inaktuella konton som har ägarbehörighet bör tas bort från prenumerationen. Inaktuella konton har blockerats från att logga in.|
|\[Förhandsversion]: Bör ta bort inaktuella konton från prenumerationen|Inaktuella konton bör tas bort från dina prenumerationer. Inaktuella konton har blockerats från att logga in.|
|\[Förhandsversion]: Externa konton med ägarbehörigheter som ska tas bort från prenumerationen|Externa konton som har ägarbehörighet ska tas bort från din prenumeration för att förhindra åtkomst av behörigheter.|
|\[Förhandsversion]: Externa konton med write behörigheter ska tas bort från prenumerationen|Externa konton som har Skriv behörigheter ska tas bort från din prenumeration för att förhindra oövervakad åtkomst.|
|\[Förhandsversion]: Bör ta bort externa konton med läsbehörigheter från prenumerationen|Externa konton som har läsbehörighet ska tas bort från din prenumeration för att förhindra oövervakad åtkomst.|




## <a name="next-steps"></a>Nästa steg
I den här artikeln har vi berättat hur du ställer in säkerhetsprinciper i Security Center. Mer information om Security Center finns i följande artiklar.

* [Azure Security Center planerings- och bruksanvisning](security-center-planning-and-operations-guide.md): Lär dig hur du planerar och förstå designöverväganden i Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Vanliga frågor om Azure Security Center](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azures Säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

Mer information om Azure Policy finns [vad är Azure Policy?](../governance/policy/overview.md).
