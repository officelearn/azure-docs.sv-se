---
title: Azure policy definitionerna övervakas i Azure Security Center | Microsoft Docs
description: Den här artikeln innehåller en lista över Azure Policy definitioner som du kan övervaka i Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 8/22/2019
ms.author: memildin
ms.openlocfilehash: 07d45f7cafa09ef6c5341acd5ffd0914cc56e4fd
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559266"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Azure säkerhets principer övervakas av Security Center
Den här artikeln innehåller en lista över Azure Policy definitioner som du kan övervaka i Azure Security Center. Mer information om säkerhets principer finns i [arbeta med säkerhets principer](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Tillgängliga säkerhets principer

Mer information om de inbyggda principer som övervakas av Security Center finns i följande tabell:

| Princip | Vad principen gör |
| --- | --- |
|Diagnostikloggar i Virtual Machine Scale Sets ska vara aktive rad|Vi rekommenderar att du aktiverar loggar så att en aktivitets spårning är tillgänglig för undersökning efter en incident eller komprometterande.|
|Alla auktoriseringsregler utom RootManageSharedAccessKey ska tas bort från Event Hub-namnområdet|Azure Event Hubs-klienter bör inte använda en åtkomst princip på namn områdes nivå som ger åtkomst till alla köer och ämnen i ett namn område. Om du vill justera med säkerhets modellen med minst privilegium bör du skapa åtkomst principer på enhets nivå för köer och ämnen för att ge åtkomst till endast den specifika entiteten.|
|Auktoriseringsregler i Event Hub-entiteten måste definieras|Granska förekomsten av auktoriseringsregler på Event Hubs entiteter för att bevilja åtkomst med lägsta behörighet.|
|Åtkomst till lagrings konton med brand väggar och virtuella nätverkskonfigurationer bör begränsas|Granska obegränsad nätverks åtkomst i brand Väggs inställningarna för ditt lagrings konto. Konfigurera nätverks regler så att endast program från tillåtna nätverk kan komma åt lagrings kontot. Om du vill tillåta anslutningar från vissa Internet-eller lokala klienter ger du åtkomst till trafik från vissa virtuella Azure-nätverk eller offentliga IP-adressintervall för Internet.|
|Granska användningen av anpassade RBAC-regler|Granska inbyggda roller, till exempel "ägare, deltagare, läsare" i stället för anpassade rollbaserade roller för rollbaserad åtkomst kontroll (RBAC) som är fel känsliga. Användning av anpassade roller behandlas som ett undantag och kräver rigorös granskning och hot modellering.|
|Diagnostikloggar i Azure Stream Analytics ska vara aktive rad|Granska aktivering av loggar och behåll dem i upp till ett år. Detta skapar aktivitets spårningar för undersökning när en säkerhets incident inträffar eller nätverket komprometteras.|
|Säker överföring till lagrings konton ska vara aktiverat|Granska kraven för säker överföring i ditt lagrings konto. Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten. Den skyddar också data i överföring från angrepp på nätverks nivå, till exempel man-in-the-Middle, avlyssning och session-kapning.|
|Azure AD-administratören för SQL Server bör tillhandahållas|Granska etablering av en Azure Active Directory (Azure AD)-administratör för SQL Server för att aktivera Azure AD-autentisering. Azure AD-autentisering stöder förenklad behörighets hantering och centraliserad identitets hantering för databas användare och andra Microsoft-tjänster.|
|Alla auktoriseringsregler utom RootManageSharedAccessKey bör tas bort från Service Bus namnrymd|Azure Service Bus-klienter bör inte använda en åtkomst princip på namn områdes nivå som ger åtkomst till alla köer och ämnen i ett namn område. Om du vill justera med säkerhets modellen med lägsta behörighet skapar du åtkomst principer på enhets nivå för köer och ämnen för att ge åtkomst till endast den specifika entiteten.|
|Diagnostikloggar i Service Bus ska vara aktive rad|Granska aktivering av loggar och behåll dem i ett år. Detta skapar aktivitets spårningar för undersökning när en säkerhets incident inträffar eller nätverket komprometteras.|
|Egenskapen ClusterProtectionLevel för EncryptAndSign i Service Fabric ska anges|Service Fabric tillhandahåller tre skydds nivåer för nod-till-nod-kommunikation som använder ett primärt kluster certifikat: ingen, sign och EncryptAndSign. Ange skydds nivå för att säkerställa att alla nod-till-nod-meddelanden är krypterade och digitalt signerade.|
|Klientautentisering bör använda Azure Active Directory|Granska användningen av klientautentisering enbart via Azure AD i Service Fabric.|
|Diagnostikloggar i search Services ska vara aktive rad|Granska aktivering av loggar och behåll dem i upp till ett år. Detta skapar aktivitets spårningar för undersökning när en säkerhets incident inträffar eller nätverket komprometteras.|
|Endast säkra anslutningar till din Redis Cache ska vara aktiverade|Granskning aktiverar endast anslutningar via SSL till Azure cache för Redis. Användningen av säkra anslutningar säkerställer autentisering mellan servern och tjänsten. Den skyddar också data i överföring från angrepp på nätverks nivå, till exempel man-in-the-Middle, avlyssning och session-kapning.|
|Diagnostikloggar i Logic Apps ska vara aktive rad|Granska aktivering av loggar och behåll dem i upp till ett år. Detta skapar aktivitets spårningar för undersökning när en säkerhets incident inträffar eller nätverket komprometteras.|
|Diagnostikloggar i Key Vault ska vara aktive rad|Granska aktivering av loggar och behåll dem i upp till ett år. Detta skapar aktivitets spårningar för undersökning när en säkerhets incident inträffar eller nätverket komprometteras.|
|Diagnostikloggar i Händelsehubben måste vara aktive rad|Granska aktivering av loggar och behåll dem i upp till ett år. Detta skapar aktivitets spårningar för undersökning när en säkerhets incident inträffar eller nätverket komprometteras.|
|Diagnostikloggar i Azure Data Lake Store ska vara aktive rad|Granska aktivering av loggar och se till att de är upp till ett år. Detta skapar aktivitets spårningar för undersökning när en säkerhets incident inträffar eller nätverket komprometteras.|
|Diagnostikloggar i Data Lake Analytics ska vara aktive rad|Granska aktivering av loggar och behåll dem i upp till ett år. Detta skapar aktivitets spårningar för undersökning när en säkerhets incident inträffar eller nätverket komprometteras.|
|Lagrings konton ska migreras till nya AzureRM-resurser|Använd Azure Resource Manager för dina lagrings konton för att tillhandahålla säkerhets förbättringar. Dessa är: <br>– Starkare åtkomst kontroll (RBAC)<br>– Bättre granskning<br>– Azure Resource Manager-baserad distribution och styrning<br>-Åtkomst till hanterade identiteter<br>– Åtkomst till Azure Key Vault för hemligheter<br>– Azure AD-baserad autentisering<br>– Stöd för taggar och resurs grupper för enklare säkerhets hantering|
|Virtuella datorer ska migreras till nya AzureRM-resurser|Använd Azure Resource Manager för dina virtuella datorer för att förbättra säkerheten. Dessa är: <br>– Starkare åtkomst kontroll (RBAC)<br>– Bättre granskning<br>– Azure Resource Manager-baserad distribution och styrning<br>-Åtkomst till hanterade identiteter<br>– Åtkomst till Azure Key Vault för hemligheter<br>– Azure AD-baserad autentisering<br>– Stöd för taggar och resurs grupper för enklare säkerhets hantering|
|Mått varnings regler ska konfigureras för batch-konton|Granska konfigurationen av mått varnings regler på Azure Batch konton för att aktivera det nödvändiga måttet.|
|Diagnostikloggar i batch-konton måste vara aktiverade|Granska aktivering av loggar och behåll dem i upp till ett år. Detta skapar aktivitets spårningar för undersökning när en säkerhets incident inträffar eller nätverket komprometteras.|
|Kryptering måste vara aktiverat på variabler för Automation-konto|Det är viktigt att aktivera kryptering av Azure Automation konto variabel till gångar när du lagrar känsliga data.|
|Diagnostikloggar i App Services ska vara aktive rad|Granska aktivering av diagnostikloggar i appen. Detta skapar aktivitets spårningar för undersökning när en säkerhets incident inträffar eller nätverket komprometteras.|
|transparent datakryptering på SQL-databaser ska aktive ras|Granska transparent data krypterings status för SQL-databaser.|
|SQL Server Auditing måste vara aktiverat|Granska förekomsten av SQL-granskning på server nivå.|
|\[för hands version]: övervaka okrypterad SQL-databas i Azure Security Center|Azure Security Center övervakar okrypterade SQL-servrar eller databaser som rekommenderade.|
|\[för hands version]: övervaka en SQL-databas som inte har granskats i Azure Security Center|Azure Security Center övervakar SQL-servrar och databaser som inte har SQL-granskning aktiverat som rekommenderat.|
|\[för hands version]: system uppdateringar bör installeras på dina datorer|Azure Security Center övervakar säkerhets system uppdateringar som saknas på dina servrar enligt rekommendationer.|
|\[för hands version]: granska saknad BLOB-kryptering för lagrings konton|Granska lagrings konton som inte använder BLOB-kryptering. Detta gäller endast för Microsofts lagrings resurs typer, inte lagring från andra leverantörer. Azure Security Center övervakar möjliga nätverks åtkomst för just-in-Time som rekommenderat.|
|\[för hands version]: just-in-Time-kontrollen för nätverks åtkomst ska tillämpas på virtuella datorer|Azure Security Center övervakar möjliga nätverks åtkomst för just-in-Time som rekommenderat.|
|\[för hands version]: adaptiva program kontroller ska aktive ras på virtuella datorer|Azure Security Center övervakar möjliga program vitlista-konfigurationer.|
|\[för hands version]: nätverks säkerhets grupper som saknas för virtuella datorer måste konfigureras|Azure Security Center övervakar nätverks säkerhets grupper som har för få regler, enligt rekommendationer.|
|\[för hands version]: säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas|Azure Security Center övervakar servrar som inte uppfyller den konfigurerade bas linjen enligt rekommendationer.| 
|\[för hands version]: Endpoint Protection bör installeras på virtuella datorer|Azure Security Center övervakar servrar som inte har installerat Microsoft System Center Endpoint Protection Agent som rekommenderat.|
|\[för hands version]: disk kryptering bör tillämpas på virtuella datorer|Azure Security Center övervakar virtuella datorer som inte har disk kryptering aktiverat som rekommenderat.|
|\[för hands version]: sårbarheter bör åtgärdas av en lösning för sårbarhets bedömning|Övervaka säkerhets risker som identifieras av lösningen för sårbarhets bedömning och virtuella datorer som inte har någon lösning för sårbarhets bedömning i Azure Security Center som rekommenderas.|
|\[för hands version]: övervaka oskyddat webb program i Azure Security Center|Azure Security Center övervakar webb program som saknar brand Väggs skydd för webb program som rekommenderat.|
|\[för hands version]: Endpoint Protection-lösningen bör installeras på virtuella datorer|Azure Security Center övervakar nätverks slut punkter som inte har nästa generations brand Väggs skydd som rekommenderat.|
|\[för hands version]: sårbarheter på SQL-databaser bör åtgärdas|Övervaka genomsöknings resultat för sårbarhets bedömning och rekommendera hur du åtgärdar databas sårbarheter.|
|\[för hands version]: högst tre ägare bör anges för din prenumeration|Vi rekommenderar att du anger upp till tre prenumerations ägare för att minska risken för intrång av en komprometterad ägare.|
|\[för hands version]: fler än en ägare måste tilldelas till din prenumeration|Vi rekommenderar att du anger fler än en prenumerations ägare för att säkerställa att administratören har åtkomst till redundans.|
|\[för hands version]: MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton som har ägar behörigheter för att förhindra överträdelser av konton eller resurser.|
|\[för hands version]: MFA ska vara aktiverat på dina prenumerations konton med Skriv behörighet|Multi-Factor Authentication ska vara aktiverat för alla prenumerations konton som har Skriv behörighet för att förhindra överträdelser av konton eller resurser.|
|\[för hands version]: MFA ska vara aktiverat på dina prenumerations konton med Läs behörighet|Multi-Factor Authentication ska vara aktiverat för alla prenumerations konton som har Läs behörighet för att förhindra överträdelser av konton eller resurser.|
|\[för hands version]: föråldrade konton med ägar behörigheter bör tas bort från din prenumeration|Föråldrade konton som har ägar behörigheter bör tas bort från din prenumeration. Inaktuella konton har blockerats från att logga in.|
|\[för hands version]: föråldrade konton bör tas bort från din prenumeration|Föråldrade konton bör tas bort från dina prenumerationer. Inaktuella konton har blockerats från att logga in.|
|\[för hands version]: externa konton med ägar behörigheter bör tas bort från din prenumeration|Externa konton som har ägar behörigheter bör tas bort från din prenumeration för att förhindra åtkomst till behörigheter.|
|\[för hands version]: externa konton med Skriv behörighet bör tas bort från din prenumeration|Externa konton som har Skriv behörighet bör tas bort från din prenumeration för att förhindra oövervakad åtkomst.|
|\[för hands version]: externa konton med Läs behörighet bör tas bort från din prenumeration|Externa konton som har Läs behörighet bör tas bort från din prenumeration för att förhindra oövervakad åtkomst.|




## <a name="next-steps"></a>Nästa steg
I den här artikeln har vi berättat hur du ställer in säkerhetsprinciper i Security Center. Mer information om Security Center finns i följande artiklar.

* [Azure Security Center planering och drifts guide](security-center-planning-and-operations-guide.md): Lär dig hur du planerar och förstår design överväganden i Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

Mer information om Azure Policy finns i [Vad är Azure policy?](../governance/policy/overview.md).
