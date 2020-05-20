---
title: Säkra poäng i Azure Security Center
description: Beskrivning av Azure Security Center säkra poäng och säkerhets kontroller
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2020
ms.author: memildin
ms.openlocfilehash: 1476e00386f95e1c86630a0aa0ea7e4bb4180307
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684690"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Förbättrade säkra poäng (för hands version) i Azure Security Center

## <a name="introduction-to-secure-score"></a>Introduktion till säkra Poäng

Azure Security Center har två huvudsakliga mål: för att hjälpa dig att förstå den aktuella säkerhets situationen och för att hjälpa dig att effektivt och effektivt förbättra säkerheten. Den centrala aspekten av Security Center som gör att du kan uppnå dessa mål är säkra poäng.

Security Center utvärderar kontinuerligt dina resurser, prenumerationer och din organisation efter säkerhets problem. Den sammanställer sedan alla resultat i en enda poäng så att du snabbt kan tala om din aktuella säkerhets situation: ju högre poäng, desto lägre är den identifierade risk nivån. Använd poängen för att spåra säkerhets aktiviteter och projekt i din organisation. 

På sidan säker Poäng för Security Center ingår:

- **Poängen** – den säkra poängen visas som ett procent värde, men de underliggande värdena är också tydliga:

    [![Säkra poäng som visas som ett procent värde med underliggande tal rensa för](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Säkerhets kontroller** – varje kontroll är en logisk grupp relaterade säkerhets rekommendationer och återspeglar dina sårbara angrepps ytor. En kontroll är en uppsättning säkerhets rekommendationer med anvisningar som hjälper dig att implementera dessa rekommendationer. Poängen ökar bara när du reparerar *alla* rekommendationer för en enskild resurs i en kontroll.

    Om du vill se hur väl din organisation skyddar varje enskild attack yta granskar du poängen för varje säkerhets kontroll.

    Mer information finns i [så här beräknas de säkra poängen](secure-score-security-controls.md#how-the-secure-score-is-calculated) nedan. 


>[!TIP]
> Tidigare versioner av Security Center tilldelade punkter på rekommendations nivå: när du har reparerat en rekommendation för en enskild resurs, förbättras dina säkra poäng. Idag ökar poängen bara om du reparerar *alla* rekommendationer för en enskild resurs i en kontroll. Det innebär att poängen bara ökar när du har förbättrat säkerheten för en resurs.
> Även om den här förbättrade versionen fortfarande är i för hands version är den tidigare säkra poängen tillgänglig som ett alternativ från Azure Portal. 


## <a name="locating-your-secure-score"></a>Hitta dina säkra Poäng

Security Center visar ditt resultat på ett framträdande sätt: det är det första som visas på sidan Översikt. Om du klickar till sidan för dedikerade säkra poäng visas poängen uppdelad efter prenumeration. Klicka på en enskild prenumeration om du vill se en detaljerad lista över prioriterade rekommendationer och den potentiella påverkan som åtgärdas i prenumerationens resultat.

## <a name="how-the-secure-score-is-calculated"></a>Så här beräknas den säkra poängen 

Bidraget för varje säkerhets kontroll till den övergripande säkra poängen visas tydligt på sidan rekommendationer.

[![Den förbättrade säkra poängen introducerar säkerhets kontroller](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

För att få alla möjliga punkter för en säkerhets kontroll måste alla dina resurser följa alla säkerhets rekommendationer i säkerhets kontrollen. Security Center har till exempel flera rekommendationer om hur du skyddar dina hanterings portar. Tidigare kunde du åtgärda några av de relaterade och beroende rekommendationerna samtidigt som du lämnar andra olösta och dina säkra poäng skulle öka. När du har tittat på målet är det enkelt att att att din säkerhetsinte hade förbättras tills du har löst alla. Nu måste du reparera dem för att göra en skillnad på dina säkra poäng.

Säkerhets kontrollen "tillämpa system uppdateringar" har till exempel en högsta poäng på sex punkter, som du kan se i knapp beskrivningen för kontrollens potentiella öknings värde:

[![Säkerhets kontrollen tillämpa system uppdateringar](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Risken för säkerhets kontrollen "Använd System uppdateringar" i skärm bilden ovan visar "2% (1 punkt)". Det innebär att om du reparerar alla rekommendationer i den här kontrollen kommer poängen att öka med 2% (i det här fallet en punkt). För enkelhetens skull avrundas värdena i kolumnen "potentiell ökning" till heltal. Knapp beskrivningarna visar de exakta värdena:

* **Högsta poäng** – det maximala antalet poäng som du kan få genom att slutföra alla rekommendationer inom en kontroll. Den maximala poängen för en kontroll anger den relativa betydelsen av kontrollen. Använd Max poäng värden för att prioritering vilka problem som ska fungera först. 
* **Eventuell ökning** – de återstående punkterna är tillgängliga i kontrollen. Om du vill få de här punkterna tillagda på dina säkra Poäng kan du åtgärda alla kontrollers rekommendationer. I exemplet ovan är den ena punkten som visas för kontrollen faktiskt 0,96 punkter.
* **Aktuell Poäng** – den aktuella poängen för den här kontrollen. Varje kontroll bidrar till den totala poängen. I det här exemplet bidrar kontrollen 5,04 punkter till summan. 

### <a name="calculations---understanding-your-score"></a>Beräkningar – förstå dina Poäng

|Metric|Formel och exempel|
|-|-|
|**Säkerhets kontrollens aktuella Poäng**|<br>![Formel för att beräkna en säkerhets kontrolls aktuella Poäng](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Varje enskild säkerhets kontroll bidrar till säkerhets poängen. Varje resurs som påverkas av en rekommendation inom kontrollen bidrar till kontrollens aktuella resultat. Den aktuella poängen för varje kontroll är ett mått på statusen för resurserna *i* kontrollen.<br>![Knapp beskrivningar som visar de värden som används när du beräknar säkerhets kontrollens aktuella Poäng](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>I det här exemplet skulle max poängen på 6 divideras med 78 eftersom det är summan av de felfria och felaktiga resurserna.<br>6/78 = 0,0769<br>Om du multiplicerar det med antalet felfria resurser (4) resulterar det i den aktuella poängen:<br>0,0769 * 4 = **0,31**<br><br>|
|**Säkerhetspoäng**<br>Enstaka prenumeration|<br>![Ekvation för att beräkna nuvarande säkra Poäng](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Säker Poäng för enskild prenumeration med alla kontroller aktiverade](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>I det här exemplet finns det en enda prenumeration med alla säkerhets kontroller som är tillgängliga (en potentiell högsta poäng på 60 punkter). Poängen visar 28 punkter av en möjlig 60 och de återstående 32 punkterna visas i siffrorna "potentiella Poäng ökning" i säkerhets kontrollerna.<br>![Lista över kontroller och potentiella Poäng ökningar](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Säkerhetspoäng**<br>Flera prenumerationer|<br>Den aktuella poängen för alla resurser i alla prenumerationer läggs till och beräkningen är sedan samma som för en enskild prenumeration<br><br>När du visar flera prenumerationer utvärderar säkra poäng alla resurser i alla aktiverade principer och grupperar deras kombinerade påverkan på varje säkerhets kontrolls maximala poäng.<br>![Säkra Poäng för flera prenumerationer med aktiverade kontroller](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Den kombinerade poängen är **inte** ett medelvärde. i stället är det utvärderat position av status för alla resurser i alla prenumerationer.<br>Om du går till sidan rekommendationer och lägger till tillgängliga tillgängliga punkter, kommer du att se att det är skillnaden mellan den aktuella poängen (24) och den maximala poängen som är tillgänglig (60).|
||||

## <a name="improving-your-secure-score"></a>Förbättra dina säkra Poäng

Du kan förbättra dina säkra poäng genom att åtgärda säkerhets rekommendationerna från listan över rekommendationer. Du kan åtgärda varje rekommendation manuellt för varje resurs, eller genom att använda **snabb korrigering!** alternativ (när det är tillgängligt) för att tillämpa en reparation för en rekommendation till en grupp med resurser snabbt. Mer information finns i [åtgärda rekommendationer](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Det är bara inbyggda rekommendationer som påverkar de säkra poängen.

## <a name="security-controls-and-their-recommendations"></a>Säkerhets kontroller och deras rekommendationer

I tabellen nedan visas säkerhets kontrollerna i Azure Security Center. För varje kontroll kan du se det maximala antalet punkter som du kan lägga till i dina säkra poäng om du reparerar *alla* rekommendationer som anges i kontrollen för *alla* dina resurser. 

> [!TIP]
> Om du vill filtrera eller sortera listan på olika sätt, kopiera och klistra in den i Excel.

|Säkerhets kontroll|Högsta antal säkra Poäng Poäng|Rekommendationer|
|----------------|:-------------------:|---------------|
|**Aktivera MFA**|10|-MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration<br>-MFA ska vara aktiverade konton med Skriv behörighet för din prenumeration|
|**Säkra hanterings portar**|8|-Just-in-Time-Network Access Control ska tillämpas på virtuella datorer<br>– Virtuella datorer ska associeras med en nätverks säkerhets grupp<br>– Hanterings portar bör stängas på dina virtuella datorer|
|**Tillämpa systemuppdateringar**|6|– Övervaknings agentens hälso problem bör lösas på dina datorer<br>– Övervaknings agenten ska installeras på virtuella datorers skalnings uppsättningar<br>– Övervaknings agenten ska installeras på dina datorer<br>-OS-versionen ska uppdateras för dina moln tjänst roller<br>– System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras<br>– System uppdateringar bör installeras på datorerna<br>-Dina datorer måste startas om för att tillämpa system uppdateringar<br>– Kubernetes Services bör uppgraderas till en icke-sårbar Kubernetes-version<br>– Övervaknings agenten ska installeras på dina virtuella datorer|
|**Åtgärda sårbarheter**|6|– Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar<br>-Säkerhets risker i Azure Container Registry avbildningar bör åtgärdas<br>– Säkerhets risker i SQL-databaser bör åtgärdas<br>-Sårbarheter bör åtgärdas av en lösning för sårbarhets bedömning<br>– En sårbarhets bedömning ska vara aktive rad på SQL-hanterade instanser<br>– En sårbarhets bedömning ska vara aktive rad på dina SQL-servrar<br>– Lösningen för sårbarhets bedömning bör installeras på dina virtuella datorer|
|**Aktivera kryptering i vila**|4|– Disk kryptering bör tillämpas på virtuella datorer<br>-Transparent datakryptering på SQL-databaser ska aktive ras<br>– Variabler för Automation-konton ska vara krypterade<br>-Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign<br>-SQL Server TDE-skyddskomponenten bör vara krypterat med din egen nyckel|
|**Kryptera data under överföring**|4|– API-appen bör bara vara tillgänglig via HTTPS<br>-Funktionsapp bör endast vara tillgängligt via HTTPS<br>– Endast säkra anslutningar till din Redis Cache ska vara aktiverade<br>– Säker överföring till lagrings konton ska vara aktiverat<br>– Webb programmet bör endast vara tillgängligt via HTTPS|
|**Hantera åtkomst och behörigheter**|4|-Inaktuella konton bör tas bort från din prenumeration (för hands version)<br>-Inaktuella konton med ägar behörigheter bör tas bort från din prenumeration (för hands version)<br>-Externa konton med ägar behörigheter bör tas bort från din prenumeration (för hands version)<br>-Externa konton med Skriv behörighet bör tas bort från din prenumeration (för hands version)<br>-Det måste finnas fler än en ägare som har tilldelats din prenumeration<br>-Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services (för hands version)<br>-Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering|
|**Åtgärda säkerhetskonfigurationer**|4|-Pod säkerhets principer bör definieras på Kubernetes-tjänster<br>-Sårbarheter i säkerhets konfigurationerna för behållaren bör åtgärdas<br>-Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas<br>– Säkerhets risker i säkerhets konfiguration på dina virtuella datorers skalnings uppsättningar bör åtgärdas<br>– Övervaknings agenten ska installeras på dina virtuella datorer<br>– Övervaknings agenten ska installeras på dina datorer<br>– Övervaknings agenten ska installeras på virtuella datorers skalnings uppsättningar<br>– Övervaknings agentens hälso problem bör lösas på dina datorer|
|**Begränsa obehörig nätverks åtkomst**|4|-IP-vidarebefordran på den virtuella datorn måste inaktive ras<br>-Auktoriserade IP-intervall ska definieras för Kubernetes Services (för hands version)<br>-(Föråldrad) åtkomst till App Services ska vara begränsad (för hands version)<br>-(Inaktuell) reglerna för webb program på IaaS NSG: er bör vara härdade<br>– Virtuella datorer ska associeras med en nätverks säkerhets grupp<br>-CORS bör inte tillåta alla resurser åtkomst till din API-app<br>-CORS bör inte tillåta alla resurser att komma åt din Funktionsapp<br>-CORS bör inte tillåta alla resurser åtkomst till ditt webb program<br>– Fjärrfelsökning bör inaktive ras för API-appen<br>– Fjärrfelsökning bör inaktive ras för Funktionsapp<br>– Fjärrfelsökning bör inaktive ras för webb program<br>– Åtkomsten ska begränsas för tillåtna nätverks säkerhets grupper med virtuella datorer som riktas mot Internet<br>– Regler för nätverks säkerhets grupper för virtuella datorer som riktas mot Internet bör vara skärpta|
|**Använd adaptiv program kontroll**|3|– Adaptiva program kontroller ska vara aktiverade på virtuella datorer<br>– Övervaknings agenten ska installeras på dina virtuella datorer<br>– Övervaknings agenten ska installeras på dina datorer<br>– Övervaknings agentens hälso problem bör lösas på dina datorer|
|**Använd data klassificering**|2|-Känsliga data i dina SQL-databaser ska klassificeras (för hands version)|
|**Skydda program mot DDoS-attacker**|2|-DDoS Protection standard ska vara aktive rad|
|**Aktivera Endpoint Protection**|2|-Hälso fel för Endpoint Protection bör åtgärdas på virtuella datorers skalnings uppsättningar<br>– Hälso problem med slut punkts skydd bör lösas på dina datorer<br>– Slut punkts skydds lösningen bör installeras på virtuella datorers skalnings uppsättningar<br>– Installera Endpoint Protection-lösning på virtuella datorer<br>– Övervaknings agentens hälso problem bör lösas på dina datorer<br>– Övervaknings agenten ska installeras på virtuella datorers skalnings uppsättningar<br>– Övervaknings agenten ska installeras på dina datorer<br>– Övervaknings agenten ska installeras på dina virtuella datorer<br>– Installera Endpoint Protection-lösning på dina datorer|
|**Aktivera granskning och loggning**|1|– Granskningen av SQL Server måste vara aktive rad<br>-Diagnostikloggar i App Services ska vara aktive rad<br>-Diagnostikloggar i Azure Data Lake Store ska vara aktive rad<br>-Diagnostikloggar i Azure Stream Analytics ska vara aktive rad<br>-Diagnostikloggar i batch-konton måste vara aktiverade<br>-Diagnostikloggar i Data Lake Analytics ska vara aktive rad<br>-Diagnostikloggar i Händelsehubben ska vara aktive rad<br>-Diagnostikloggar i IoT Hub ska vara aktive rad<br>-Diagnostikloggar i Key Vault ska vara aktive rad<br>-Diagnostikloggar i Logic Apps ska vara aktive rad<br>-Diagnostikloggar i Search-tjänsten måste vara aktive rad<br>-Diagnostikloggar i Service Bus ska vara aktive rad<br>-Diagnostikloggar i Virtual Machine Scale Sets ska vara aktive rad<br>– Mått för mått regler ska konfigureras för batch-konton<br>-Inställningarna för SQL-granskning bör ha åtgärds grupper konfigurerade för att fånga kritiska aktiviteter<br>– SQL-servrar bör konfigureras med granskning av antalet dagar som är större än 90 dagar.|
|**Implementera rekommenderade säkerhets metoder**|0|-Högst 3 ägare bör anges för din prenumeration<br>-Externa konton med Läs behörighet bör tas bort från din prenumeration<br>-MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration<br>-Åtkomst till lagrings konton med brand väggar och virtuella nätverkskonfigurationer bör begränsas<br>-Alla auktoriseringsregler utom RootManageSharedAccessKey ska tas bort från Event Hub-namnområdet<br>-En Azure Active Directory administratör bör tillhandahållas för SQL-servrar<br>– Auktoriseringsregler för Event Hub-instansen måste definieras<br>-Lagrings konton ska migreras till nya Azure Resource Manager resurser<br>– Virtuella datorer ska migreras till nya Azure Resource Manager resurser<br>– Avancerade data säkerhets inställningar för SQL Server ska innehålla en e-postadress för att ta emot säkerhets aviseringar<br>– Avancerad data säkerhet ska vara aktiverat på dina hanterade instanser<br>-Alla avancerade hot skydds typer ska vara aktiverade i avancerade data säkerhets inställningar för SQL-hanterad instans<br>– E-postaviseringar till administratörer och prenumerations ägare måste vara aktiverade i avancerade data säkerhets inställningar i SQL Server<br>– Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL Server<br>-Undernät ska associeras med en nätverks säkerhets grupp<br>-Alla avancerade skydds typer bör vara aktiverade i avancerade data säkerhets inställningar i SQL Server<br>-[För hands version] Windows sårbarhets skydd måste vara aktiverat <br>-[För hands version] konfigurations agenten för gäster bör installeras|
||||

## <a name="secure-score-faq"></a>Vanliga frågor och svar om säker Poäng

### <a name="why-has-my-secure-score-gone-down"></a>Varför är mina säkra Poäng borta?
Security Center har bytt till en förbättrad säker Poäng (för närvarande i för hands version) som innehåller ändringar i hur poängen beräknas. Nu måste du lösa alla rekommendationer för en resurs för att ta emot punkter. Poängen har också ändrats till en skala på 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Om jag bara har tre av fyra rekommendationer i en säkerhets kontroll, kommer mina säkra poäng att ändras?
Nej. Den ändras inte förrän du reparerar alla rekommendationer för en enskild resurs. För att få den högsta poängen för en kontroll måste du åtgärda alla rekommendationer för alla resurser.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>Är tidigare erfarenhet av de säkra poängen fortfarande tillgängliga? 
Ja. För en stund kommer de att köras sida vid sida för att under lätta över gången. Förväntar att föregående modell ska gå ut i tid. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Om en rekommendation inte gäller mig och jag inaktiverar den i principen, kommer min säkerhets kontroll att uppfyllas och mina säkra resultat uppdateras?
Ja. Vi rekommenderar att du inaktiverar rekommendationer när de inte är tillämpliga i din miljö. Instruktioner för hur du inaktiverar en speciell rekommendation finns i [inaktivera säkerhets principer](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Om en säkerhets kontroll ger mig noll punkter mot mina säkra poäng, ska jag ignorera den?
I vissa fall visas en kontroll över max poängen som är större än noll, men effekten är noll. När den stegvisa poängen för att lösa resurserna är försumbar, avrundas den till noll. Ignorera inte de här rekommendationerna eftersom de fortfarande ger säkerhets förbättringar. Det enda undantaget är kontrollen "ytterligare bästa praxis". Att åtgärda dessa rekommendationer ökar inte dina poäng, men det förbättrar den övergripande säkerheten.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs säkra poäng och de säkerhets kontroller som det introducerar. Information om relaterade material finns i följande artiklar:

- [Lär dig mer om de olika elementen i en rekommendation](security-center-recommendations.md)
- [Lär dig hur du åtgärdar rekommendationer](security-center-remediate-recommendations.md)