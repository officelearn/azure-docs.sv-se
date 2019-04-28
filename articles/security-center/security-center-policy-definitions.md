---
title: Azure principdefinitioner som övervakas i Azure Security Center | Microsoft Docs
description: Azure principdefinitioner som övervakas i Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 9d9369afd36f64c27cd2222cab0de5912aa913de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60909204"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Azure säkerhetsprinciper som övervakas av Security Center
Den här artikeln innehåller en lista över Azure Policy-definitioner som du kan övervaka i Azure Security Center. Mer information om säkerhetsprinciper finns [arbeta med säkerhetsprinciper](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Tillgängliga säkerhetsprinciper

Mer om de inbyggda principer som övervakas av Security Center finns i följande tabell:

| Princip | Vad principen gör |
| --- | --- |
|Granska aktivering av diagnostik-loggarna i Azure Service Fabric och virtual machine scale sets|Vi rekommenderar att du aktiverar loggar så att en aktivitet spårning är tillgänglig för undersökning efter en incident eller kompromisser.|
|Granska auktoriseringsregler för namnområden för Event Hubs|Azure Event Hubs-klienter bör inte använda en åtkomstprincip för namnområdesnivån som ger åtkomst till alla köer och ämnen i ett namnområde. Du bör skapa åtkomstprinciper på enhetsnivå för köer och ämnen för att ge åtkomst till endast den specifika enheten så att den överensstämmer med den lägsta behörighet säkerhetsmodellen.|
|Granska förekomst av auktoriseringsregler på Händelsehubbar entiteter|Granska förekomst av auktoriseringsregler på Händelsehubbar entiteter för att bevilja åtkomst med lägsta behörighet.|
|Granska obegränsad nätverksåtkomst till lagringskonton|Granska obegränsad nätverksåtkomst i brandväggsinställningarna för storage-konto. Konfigurera regler så att endast program från tillåtna nätverk har åtkomst till lagringskontot. Bevilja åtkomst till trafik från specifika Azure-nätverk eller till offentliga internet, IP-adress-intervall för att tillåta anslutningar från specifika internet eller lokala klienter.|
|Granska användning av anpassade RBAC-regler|Granska inbyggda roller, som ”ägare, deltagare, läsare” i stället för anpassade rollbaserad åtkomstkontroll (RBAC) roller, som är felbenägna. Användning av anpassade roller behandlas som ett undantag och kräver omfattande granskning och hotmodellering.|
|Granska aktivering av diagnostikloggar i Azure Stream Analytics|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Granska säker överföring till lagringskonton|Granska kraven för säker överföring i ditt lagringskonto. Säker överföring är ett alternativ som tvingar ditt storage-konto för att acceptera begäranden endast från säkra anslutningar (HTTPS). Använda HTTPS säkerställer autentisering mellan servern och tjänsten. Det skyddar också data under överföring från nätverksattacker lager, till exempel man-in-the-middle, avlyssning och sessionskapning.|
|Granska etablering av en Azure Active Directory-administratör för SQL Server|Granska etablering av en administratör för Azure Active Directory (Azure AD) för SQL Server för att aktivera Azure AD-autentisering. Azure AD-autentisering har stöd för förenklad behörighetshantering och centraliserad Identitetshantering för databasanvändare och andra Microsoft-tjänster.|
|Granska auktoriseringsregler för Service Bus-namnområden|Azure Service Bus-klienter bör inte använda en åtkomstprincip för namnområdesnivån som ger åtkomst till alla köer och ämnen i ett namnområde. Skapa principer för åtkomst på enhetsnivå för köer och ämnen för att ge åtkomst till endast den specifika enheten så att den överensstämmer med den lägsta behörighet säkerhetsmodellen.|
|Granska aktivering av diagnostikloggar i Service Bus|Granska aktivering av loggar och låt dem dig för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Granska att inställningen för egenskapen ClusterProtectionLevel är inställd på EncryptAndSign i Service Fabric|Service Fabric tillhandahåller tre skyddsnivåer för nod-till-nod-kommunikation som använder ett klustercertifikat för primär: Ingen, logga in och EncryptAndSign. Ställa in skydd så att alla nod-till-nod-meddelanden är krypterad och har signerats digitalt.|
|Granska användning av Azure Active Directory för klientautentisering i Service Fabric|Granska användning av klientautentisering endast via Azure AD i Service Fabric.|
|Granska aktivering av diagnostikloggar i söktjänsten|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Granska aktiverar endast säkra anslutningar till Azure Cache för Redis|Granska aktiverar endast anslutningar via SSL till Azure Cache för Redis. Användning av säkra anslutningar säkerställer autentisering mellan servern och tjänsten. Det skyddar också data under överföring från nätverksattacker lager, till exempel man-in-the-middle, avlyssning och sessionskapning.|
|Granska aktivering av diagnostikloggar i Logic Apps|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Granska aktivering av diagnostikloggar i Key Vault|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Granska aktiverar diagnostikloggar i Event Hubs|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Granska aktivering av diagnostikloggar i Azure Data Lake Store|Granska aktivering av loggar och låt dem upp till ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Granska aktivering av diagnostikloggar i Data Lake Analytics|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Granska användningen av klassiska lagringskonton|Använda Azure Resource Manager för storage-konton för att tillhandahålla förbättringar av säkerhet. Exempel på dessa är: <br>-Starkare åtkomstkontroll (RBAC)<br>– Granskning bättre<br>– Azure Resource Manager-baserade distribution och styrning<br>-Åtkomst till hanterade identiteter<br>-Åtkomst till Azure Key Vault för hemligheter<br>– Azure AD-baserad autentisering<br>-Stöd för taggar och resursgrupper för enklare säkerhetshantering|
|Granska användningen av klassiska virtuella datorer|Använda Azure Resource Manager för dina virtuella datorer för att tillhandahålla förbättringar av säkerhet.  Exempel på dessa är: <br>-Starkare åtkomstkontroll (RBAC)<br>– Granskning bättre<br>– Azure Resource Manager-baserade distribution och styrning<br>-Åtkomst till hanterade identiteter<br>-Åtkomst till Azure Key Vault för hemligheter<br>– Azure AD-baserad autentisering<br>-Stöd för taggar och resursgrupper för enklare säkerhetshantering|
|Granska konfiguration av måttaviseringsregler i Batch-konton|Granska konfigurationen av måttaviseringsregler på Azure Batch-konton gör nödvändiga måttet.|
|Granska aktivering av diagnostikloggar i Batch-konton|Granska aktivering av loggar och hålla dem för ett år. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Granska aktivering av kryptering av variabler för Automation-konto|Det är viktigt att aktivera kryptering av Azure Automation-konto variabler för tillgångar när du lagrar känsliga data.|
|Granska aktiverar diagnostikloggar i App Service|Granska aktiverar diagnostikloggar i appen. Detta skapar aktivitet spår för undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Granska status för transparent datakryptering|Granska status för transparent datakryptering för SQL-databaser.|
|Granska granskningsinställningar på SQL-servernivå|Granska förekomst av SQL-granskning på servernivå.|
|\[Förhandsversion]: Övervaka okrypterade SQL-databas i Azure Security Center|Azure Security Center övervakar okrypterade SQL-servrar eller databaser som rekommenderas.|
|\[Förhandsversion]: Övervaka ogranskad SQL-databas i Azure Security Center|Azure Security Center övervakar SQL-servrar och databaser som inte har SQL-granskning aktiverat vilket rekommenderas.|
|\[Förhandsversion]: Övervaka avsaknad av systemuppdateringar i Azure Security Center|Azure Security Center övervakar system säkerhetsuppdateringar som saknas på dina servrar, vilket rekommenderas.|
|\[Förhandsversion]: Granska avsaknad av blobkryptering för lagringskonton|Granska lagringskonton som inte använder blobbkryptering. Detta gäller endast Microsoft storage resurstyper, inte storage från andra leverantörer. Azure Security Center övervakar möjlig just-in-time-nätverksåtkomst som rekommenderas.|
|\[Förhandsversion]: Övervaka nätverket just-in-time-åtkomst i Azure Security Center|Azure Security Center övervakar möjlig just-in-time-nätverksåtkomst som rekommenderas.|
|\[Förhandsversion]: Övervaka möjlig lista över tillåtna program i Azure Security Center|Azure Security Center övervakar möjliga godkända programkonfiguration.|
|\[Förhandsversion]: Övervaka tillåten åtkomst till nätverk i Azure Security Center|Azure Security Center övervakar nätverkssäkerhetsgrupper med alltför Tillåtande regler som rekommenderas.|
|\[Förhandsversion]: Övervaka säkerhetsrisker i operativsystem i Azure Security Center|Azure Security Center övervakar servrar som inte uppfyller grundkonfigurationen enligt rekommendationer.| 
|\[Förhandsversion]: Övervaka avsaknad av slutpunktsskydd i Azure Security Center|Azure Security Center övervakar servrar som inte har en installerad agent för Microsoft System Center Endpoint Protection, vilket rekommenderas.|
|\[Förhandsversion]: Övervaka okrypterade VM-diskar i Azure Security Center|Azure Security Center övervakar virtuella datorer som inte har aktiverat enligt rekommendationer för diskkryptering.|
|\[Förhandsversion]: Övervaka säkerhetsrisker i virtuell dator i Azure Security Center|Övervaka sårbarheter som identifieras av lösning för sårbarhetsbedömning och virtuella datorer som inte har en lösning för sårbarhetsbedömning i Azure Security Center enligt rekommendationer.|
|\[Förhandsversion]: Övervaka oskyddat webbprogram i Azure Security Center|Azure Security Center ska övervaka webbprogram som saknar Brandvägg för web application enligt rekommendationer.|
|\[Förhandsversion]: Övervaka oskyddade nätverksslutpunkter i Azure Security Center|Azure Security Center övervakar nätverks-slutpunkter som saknar nästa generations brandväggsskydd enligt rekommendationer.|
|\[Förhandsversion]: Övervaka resultaten av SQL-sårbarhetsbedömning i Azure Security Center|Övervaka sårbarhetsbedömning skanningsresultat och rekommenderar så åtgärda sårbarheter i databasen.|
|\[Förhandsversion]: Granska högsta antal ägare av en prenumeration|Vi rekommenderar att du anger upp till tre prenumerationsägare att minska risken för intrång genom en komprometterad ägare.|
|\[Förhandsversion]: Granska lägsta antal ägare av en prenumeration|Vi rekommenderar att du anger mer än en prenumerationsägare så administratörsåtkomst redundans.|
|\[Förhandsversion]: Granska konton med ägarbehörighet som inte är MFA-aktiverade i en prenumeration|Multifaktorautentisering (MFA) bör aktiveras för alla prenumerationskonton som har ägarbehörighet för att förhindra att överträdelser görs avseende konton och resurser.|
|\[Förhandsversion]: Granska konton med skrivbehörighet som inte är MFA-aktiverade i en prenumeration|Multifaktorautentisering ska aktiveras för alla prenumerationskonton med skrivbehörighet för att förhindra att överträdelser görs avseende konton och resurser.|
|\[Förhandsversion]: Granska konton med läsbehörighet som inte är MFA-aktiverade i en prenumeration|Multifaktorautentisering ska aktiveras för alla prenumerationskonton som har läsbehörighet till att förhindra att överträdelser görs avseende konton och resurser.|
|\[Förhandsversion]: Granska föråldrade konton med skrivbehörighet i en prenumeration|Inaktuella konton som har ägarbehörighet bör tas bort från prenumerationen. Inaktuella konton har blockerats från att logga in.|
|\[Förhandsversion]: Granska föråldrade konton i en prenumeration|Du bör ta bort inaktuella konton från dina prenumerationer. Inaktuella konton har blockerats från att logga in.|
|\[Förhandsversion]: Granska externa konton med ägarbehörighet i en prenumeration|Externa konton som har ägarbehörighet ska tas bort från din prenumeration för att förhindra åtkomst av behörigheter.|
|\[Förhandsversion]: Granska externa konton med skrivbehörighet i en prenumeration|Externa konton som har Skriv behörigheter ska tas bort från din prenumeration för att förhindra oövervakad åtkomst.|
|\[Förhandsversion]: Granska externa konton med läsbehörighet i en prenumeration|Externa konton som har läsbehörighet ska tas bort från din prenumeration för att förhindra oövervakad åtkomst.|




## <a name="next-steps"></a>Nästa steg
I den här artikeln har vi berättat hur du ställer in säkerhetsprinciper i Security Center. Mer information om Security Center finns i följande artiklar.

* [Azure Security Center planerings- och bruksanvisning](security-center-planning-and-operations-guide.md): Lär dig hur du planerar och förstå designöverväganden i Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Vanliga frågor om Azure Security Center](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azures Säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

Mer information om Azure Policy finns [vad är Azure Policy?](../governance/policy/overview.md).
