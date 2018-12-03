---
title: Azure principdefinitioner som övervakas i Azure Security Center | Microsoft Docs
description: Azure principdefinitioner som övervakas i Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: rkarlin
ms.openlocfilehash: a4f9fc31f411d36e63775a3665b6dfe27eec7710
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319381"
---
# <a name="azure-security-policies-monitored-by-azure-security-center"></a>Azure säkerhetsprinciper som övervakas av Azure Security Center
Den här artikeln innehåller en lista över Azure principdefinitioner som kan övervakas i Security Center.

## <a name="available-security-policies"></a>Tillgängliga säkerhetsprinciper

För att förstå de inbyggda principer som övervakas av Security Center finns i följande tabell:

| Princip | Vad principen gör |
| --- | --- |
|Granska aktivering av diagnostikloggar i Service Fabric och skalningsuppsättningar för virtuell dator|Du rekommenderas att aktivera loggar så att aktivitetsspår kan återskapas för närmare undersökning vid en incident eller obehörigt intrång.|
|Granska auktoriseringsregler för namnområden för händelsehubb|Event Hub-klienter ska inte använda en namnområdesprincip administratörsnivå som ger åtkomst till alla köer och ämnen i ett namnområde. För att anpassas till det lägsta privilegiet säkerhetsmodell, bör du skapa principer för åtkomst på enhetsnivå för köer och ämnen för att ge åtkomst till endast den specifika enheten.|
|Granska förekomst av auktoriseringsregler för händelsehubbentiteter|Granska förekomst av auktoriseringsregler på Händelsehubb entiteter för att ge lägsta åtkomstbehörighet.|
|Granska obegränsad nätverksåtkomst till lagringskonton|Granska obegränsad nätverksåtkomst i brandväggsinställningarna för storage-konto. Konfigurera i stället Nätverksregler så att endast program från tillåtna nätverk har åtkomst till lagringskontot. Om du vill tillåta anslutningar från specifika Internet eller en lokal klienter beviljas åtkomst till trafik från specifika Azure-nätverk eller till offentliga Internet-IP-adressintervall.|
|Granska användning av anpassade RBAC-regler|Granska inbyggda roller som ägare, deltagarbehörighet, läsare i stället för anpassad RBAC-roller som är felbenägna. Med hjälp av anpassade roller behandlas som ett undantag och kräver en omfattande granskning och hotmodellering.|
|Granska aktivering av diagnostikloggar i Azure Stream Analytics|Granska aktivering av loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Granska säker överföring till lagringskonton|Granska krav för säker överföring i ditt lagringskonto. Säker överföring är ett alternativ som tvingar ditt storage-konto för att acceptera begäranden endast från säkra anslutningar (HTTPS). Använda HTTPS säkerställer autentisering mellan servern och tjänsten och skyddar data under överföring från network layer attacker, till exempel man-in-the-middle, avlyssning och sessionskapning.|
|Granska tillhandahållande av Azure Active Directory-administratör för SQL Server|Granska etablering av en Azure Active Directory-administratör för SQL-servern att aktivera Azure AD-autentisering. Azure AD-autentisering möjliggör förenklad behörighetshantering och centraliserad Identitetshantering för databasanvändare och andra Microsoft-tjänster.|
|Granska auktoriseringsregler för Service Bus-namnområden|Service Bus-klienter ska inte använda en namnområdesprincip administratörsnivå som ger åtkomst till alla köer och ämnen i ett namnområde. För att anpassas till det lägsta privilegiet säkerhetsmodell, bör du skapa principer för åtkomst på enhetsnivå för köer och ämnen för att ge åtkomst till endast den specifika enheten.|
|Granska aktivering av diagnostikloggar i Service Bus|Granska aktivering av loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Granska att inställningen för egenskapen ClusterProtectionLevel är inställd på EncryptAndSign i Service Fabric|Service Fabric tillhandahåller tre skyddsnivåer (ingen, logga in och EncryptAndSign) för nod-till-nod-kommunikation med hjälp av en primär klustercertifikat. Ställa in skydd så att alla nod-till-nod-meddelanden är krypterad och har signerats digitalt.| 
|Granska användning av Azure Active Directory för klientautentisering i Service Fabric|Granska användning av klientautentisering endast via Azure Active Directory i Service Fabric| 
|Granska aktivering av diagnostikloggar i söktjänsten|Granska aktivering av loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.| 
|Granska aktivering av enbart säkra anslutningar till Redis Cache|Granska aktiverar endast anslutningar via SSL till Redis Cache. Användning av säkra anslutningar säkerställer autentisering mellan servern och tjänsten och skyddar data under överföring från network layer attacker, till exempel man-in-the-middle, avlyssning och sessionskapning| 
|Granska aktivering av diagnostikloggar i Logic Apps|Granska aktivering av loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.| 
|Granska aktivering av diagnostikloggar i Key Vault|Granska aktivering av loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Granska aktivering av diagnostikloggar i Event Hubs|Granska aktivering av loggar och behålla upp till ett år. På så sätt kan återskapning av aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.| 
|Granska aktivering av diagnostikloggar i Azure Data Lake Store|Granska aktivering av loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.| 
|Granska aktivering av diagnostikloggar i Data Lake Analytics|Granska aktivering av loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.| 
|Granska användningen av klassiska lagringskonton|Använd Azure Resource Manager för storage-konton för att tillhandahålla förbättringar av säkerhet som: starkare åtkomstkontroll (RBAC), bättre granskning, Azure Resource Manager-baserade distribution och styrning åtkomst till hanterade identiteter, åtkomst till nyckelvalvet för hemligheter , Azure AD-baserad autentisering och stöd för taggar och resursgrupper för enklare säkerhetshantering.| 
|Granska användningen av klassiska virtuella datorer|Använda Azure Resource Manager för dina virtuella datorer för att ange säkerhetsförbättringar t.ex: starkare åtkomstkontroll (RBAC), bättre granskning, Azure Resource Manager-baserade distribution och styrning åtkomst till hanterade identiteter, åtkomst till nyckelvalvet för hemligheter , Azure AD-baserad autentisering och stöd för taggar och resursgrupper för enklare säkerhetshantering.| 
|Granska konfiguration av måttaviseringsregler i Batch-konton|Granska konfigurationen av måttaviseringsregler på Batch-konto för att möjliggöra nödvändiga måttet.| 
|Granska konfiguration av måttaviseringsregler i Batch-konton|Granska konfigurationen av måttaviseringsregler på Batch-konto för att möjliggöra nödvändiga måttet.| 
|Granska aktivering av diagnostikloggar i Batch-konton|Granska aktivering av loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.| 
|Granska aktivering av kryptering av Automation-kontovariabler|Det är viktigt att aktivera kryptering av Automation-kontovariabeltillgångar vid lagring av känsliga data.| 
|Granska aktivering av diagnostikloggar i App Services|Granska aktiverar diagnostikloggar i appen. På så sätt kan du återskapa aktivitet spår undersökning om en säkerhetsincident inträffar eller nätverket komprometteras.| 
|Granska status för transparent datakryptering|Granska status för transparent datakryptering för SQL-databaser.| 
|Granska granskningsinställningar på SQL-servernivå|Granskar förekomsten av SQL-granskning på servernivå.| 
|[Förhandsversion]: Övervaka okrypterad SQL-databas i Azure Security Center|Okrypterade SQL-servrar eller databaser kommer att övervakas av Azure Security Center som rekommendationer.| 
|[Förhandsversion]: Övervaka ogranskad SQL-databas i Azure Security Center|SQL-servrar och databaser som inte har SQL-granskning aktiverat kommer att övervakas av Azure Security Center som rekommendationer.| 
|[Förhandsversion]: Övervaka systemuppdateringar som saknas i Azure Security Center|Säkerhetssystemuppdateringar på dina servrar kommer att övervakas av Azure Security Center som rekommendationer.| 
|[Förhandsversion]: Granska blobkryptering som saknas för lagringskonton|Den här principen granskar lagringskonton utan blobbkryptering. Det gäller bara Microsoft.Storage-resurstyper, inte andra lagringsprovidrar. Nätverket Just In Time-åtkomst kommer att övervakas av Azure Security Center som rekommendationer.| 
|[Förhandsversion]: Övervaka möjlig just-in-time-nätverksåtkomst (JIT) i Azure Security Center|Nätverket bara i tid JIT-åtkomst kommer att övervakas av Azure Security Center som rekommendationer.| 
|[Förhandsversion]: Övervaka möjlig lista över tillåtna program i Azure Security Center|Lista över tillåtna program konfigurationer kommer att övervakas av Azure Security Center.| 
|[Förhandsversion]: Övervaka tillåten åtkomst till nätverk i Azure Security Center|Nätverkssäkerhetsgrupper med alltför Tillåtande regler kommer att övervakas av Azure Security Center som rekommendationer.| 
|[Förhandsversion]: Övervaka säkerhetsrisker i operativsystem i Azure Security Center|Servrar som inte uppfyller den inställda baslinjen kommer att övervakas av Azure Security Center som rekommendationer.| 
|[Förhandsversion]: Övervaka nätverksslutpunkter som saknas i Azure Security Center|Servrar utan en installerad Endpoint Protection agent kommer att övervakas av Azure Security Center som rekommendationer.| 
|[Förhandsversion]: Övervaka okrypterade virtuella datordiskar i Azure Security Center|Datorer utan diskkryptering övervakas av Azure Security Center som rekommendationer.| 
|[Förhandsversion]: Övervaka säkerhetsrisker i virtuell dator i Azure Security Center|Övervakar säkerhetsrisker som upptäcks av en lösning för sårbarhetsbedömning och virtuella datorer utan lösning för sårbarhetsbedömning i Azure Security Center som rekommendationer.| 
|[Förhandsversion]: Övervaka oskyddat webbprogram i Azure Security Center|Webbprogram som saknar en brandvägg för webbaserade program kommer att övervakas av Azure Security Center som rekommendationer.| 
|[Förhandsversion]: Övervaka oskyddade nätverksslutpunkter i Azure Security Center|Nätverksslutpunkter som saknar en nästa generations brandväggsskydd kommer att övervakas av Azure Security Center som rekommendationer.| 
|[Förhandsversion]: Övervaka SQL-sårbarhetsbedömningsresultat i Azure Security Center|Övervaka sårbarhetsbedömningsresultat och rekommendationer för att reparera säkerhetsrisker i databaser.| 
|[Förhandsversion]: Granska högsta antal ägare av en prenumeration|Du rekommenderas att utse upp till 3 prenumerationsägare för att minska risken för brott av obehörig ägare.| 
|[Förhandsversion]: Granska lägsta antal ägare av en prenumeration|Vi rekommenderar att du anger mer än en prenumerationsägare, så att flera har administratörsåtkomst.| 
|[Förhandsversion]: Granska konton med ägarbehörighet som inte är MFA-aktiverade i en prenumeration|Multifaktorautentisering (MFA) måste vara aktiverat för alla prenumerationskonton med ägarbehörighet för att undvika obehörig användning av konton eller resurser.| 
|[Förhandsversion]: Granska konton med skrivbehörighet som inte är MFA-aktiverade i en prenumeration|Multifaktorautentisering (MFA) måste vara aktiverat för alla prenumerationskonton med skrivbehörighet för att undvika obehörig användning av konton eller resurser.| 
|[Förhandsversion]: Granska konton med läsbehörighet som inte är MFA-aktiverade i en prenumeration|Multifaktorautentisering (MFA) måste vara aktiverat för alla prenumerationskonton med läsbehörighet för att undvika obehörig användning av konton eller resurser.| 
|[Förhandsversion]: Granska inaktuella konton med skrivbehörighet i en prenumeration|Tas bör bort inaktuella konton med ägarbehörigheter från prenumerationen. Inaktuella konton är konton som har blockerats från att logga in.| 
|[Förhandsversion]: Granska inaktuella konton i en prenumeration|Du bör ta bort inaktuella konton från dina prenumerationer. Inaktuella konton är konton som har blockerats från att logga in.| 
|[Förhandsversion]: Granska externa konton med ägarbehörighet i en prenumeration|Externa konton med ägarbehörighet bör tas bort från prenumerationen för att undvika oövervakad åtkomst.| 
|[Förhandsversion]: Granska externa konton med skrivbehörighet i en prenumeration|Du måste ta bort externa konton med skrivprivilegier från din prenumeration så att du kan förhindra oövervakad åtkomst.| 
|[Förhandsversion]: Granska externa konton med läsbehörighet i en prenumeration|Du måste ta bort externa konton med läsprivilegier från din prenumerationen så att du kan förhindra oövervakad åtkomst.| 




## <a name="next-steps"></a>Nästa steg
I den här artikeln har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md): Här får du lära dig att planera och vad du behöver tänka på när det gäller design när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

Mer information om Azure Policy finns i [Vad är Azure Policy?](../azure-policy/azure-policy-introduction.md)
