---
title: Säker poäng i Azure Security Center
description: Beskrivning av Azure Security Centers säkra poäng och dess säkerhetskontroller
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
ms.openlocfilehash: d7eea9cd83e72b6ffeaae319a8e87c065015e6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415783"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Förbättrad säker poäng (förhandsversion) i Azure Security Center

## <a name="introduction-to-secure-score"></a>Introduktion till säkra poäng

Azure Security Center har två huvudmål: att hjälpa dig att förstå din nuvarande säkerhetssituation och för att hjälpa dig att effektivt och effektivt förbättra din säkerhet. Den centrala aspekten av Security Center som gör att du kan uppnå dessa mål är säker poäng.

Security Center utvärderar kontinuerligt dina resurser, prenumerationer och din organisation för säkerhetsproblem. Det sammanställer sedan alla resultat i en enda poäng så att du kan berätta, i korthet, din nuvarande säkerhetssituation: ju högre poäng, desto lägre den identifierade risknivån. Använd poängen för att spåra säkerhetsinsatser och projekt i din organisation. 

Den säkra poängsidan i Security Center innehåller:

- **Poängen** - Den säkra poängen visas som ett procentvärde, men de underliggande värdena är också tydliga:

    [![Säker poäng som visas som ett procentvärde med de underliggande talen tydliga också](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Säkerhetskontroller** – Varje kontroll är en logisk grupp med relaterade säkerhetsrekommendationer och återspeglar dina sårbara angreppsytor. En kontroll är en uppsättning säkerhetsrekommendationer med instruktioner som hjälper dig att implementera dessa rekommendationer. Din poäng förbättras bara när du åtgärdar *alla* rekommendationer för en enskild resurs i en kontroll.

    Om du omedelbart vill se hur väl din organisation skyddar varje enskild attackyta granskar du poängen för varje säkerhetskontroll.

    Mer information finns i [Hur den säkra poängen beräknas](secure-score-security-controls.md#how-the-secure-score-is-calculated) nedan. 


>[!TIP]
> Tidigare versioner av Security Center gav poäng på rekommendationsnivå: när du åtgärdade en rekommendation för en enskild resurs förbättrades din säkra poäng. Idag förbättras din poäng bara om du åtgärdar *alla* rekommendationer för en enskild resurs i en kontroll. Så din poäng förbättras bara när du har förbättrat säkerheten för en resurs.
> Den här förbättrade versionen är fortfarande i förhandsversion, men den tidigare säkra poängupplevelsen är tillgänglig som ett alternativ från Azure Portal. 


## <a name="locating-your-secure-score"></a>Hitta din säkra poäng

Security Center visar dina poäng på en framträdande plats: det är det första som visas på översiktssidan. Om du klickar dig vidare till den dedikerade sidan för säkra poäng visas poängen uppdelade efter prenumeration. Klicka på en enda prenumeration om du vill se en detaljerad lista över prioriterade rekommendationer och den potentiella inverkan som åtgärdade dem kommer att ha på prenumerationens poäng.

## <a name="how-the-secure-score-is-calculated"></a>Hur den säkra poängen beräknas 

Bidraget från varje säkerhetskontroll mot den övergripande säkra poängen visas tydligt på rekommendationssidan.

[![Den förbättrade säkra poängen introducerar säkerhetskontroller](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

För att få alla möjliga poäng för en säkerhetskontroll måste alla dina resurser följa alla säkerhetsrekommendationer inom säkerhetskontrollen. Security Center har till exempel flera rekommendationer om hur du skyddar hanteringsportarna. Tidigare kan du åtgärda några av dessa relaterade och ömsesidigt beroende rekommendationer samtidigt som andra olösta, och din säkra poäng skulle förbättras. När man tittar på objektivt, är det lätt att hävda att din säkerhet inte hade förbättrats förrän du hade löst dem alla. Nu måste du åtgärda dem alla för att göra en skillnad för din säkra poäng.

Säkerhetskontrollen "Använd systemuppdateringar" har till exempel en maximal poäng på sex poäng, som du kan se i verktygstipset på kontrollens potentiella ökningsvärde:

[![Säkerhetskontrollen "Tillämpa systemuppdateringar"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Potentialen för säkerhetskontrollen "Tillämpa systemuppdateringar" i skärmbilden ovan visar "2% (1 Point)". Det innebär att om du åtgärda alla rekommendationer i denna kontroll, kommer din poäng öka med 2% (i detta fall, en punkt). För enkelhetens skull avrundas värdena i rekommendationslistans kolumn "Potentiell ökning" till heltal. Verktygstipsen visar de exakta värdena:

* **Maxpoäng** - Det maximala antalet poäng du kan få genom att fylla i alla rekommendationer inom en kontroll. Den maximala poängen för en kontroll anger den relativa betydelsen av denna kontroll. Använd maxpoängvärdena för att triage vilka problem som ska fungera först. 
* **Potentiell ökning** - De återstående punkterna som är tillgängliga för dig inom kontrollen. Om du vill lägga till dessa poäng i din säkra poäng, åtgärda alla kontrollens rekommendationer. I exemplet ovan är den punkt som visas för kontrollen faktiskt 0,96 poäng.
* **Aktuell poäng** - Den aktuella poängen för den här kontrollen. Varje kontroll bidrar till den totala poängen. I det här exemplet bidrar kontrollen med 5,04 poäng till totalsumman. 

### <a name="calculations---understanding-your-score"></a>Beräkningar - förstå din poäng

|Mått|Formel och exempel|
|-|-|
|**Säkerhetskontrollens aktuella poäng**|<br>![Ekvation för beräkning av en säkerhetskontrolls aktuella poäng](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Varje enskild säkerhetskontroll bidrar till säkerhetspoängen. Varje resurs som påverkas av en rekommendation inom kontrollen bidrar till kontrollens aktuella poäng. Den aktuella poängen för varje kontroll är ett mått på status för resurserna *i* kontrollen.<br>![Verktygstips som visar de värden som används vid beräkning av säkerhetskontrollens aktuella poäng](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>I det här exemplet skulle maxpoängen på 6 divideras med 78 eftersom det är summan av de felfria och ohälsosamma resurserna.<br>6 / 78 = 0,0769<br>Multiplicera det med antalet felfria resurser (4) resulterar i den aktuella poängen:<br>0,0769 * 4 = **0,31**<br><br>|
|**Säkerhetspoäng**<br>Enstaka prenumeration|<br>![Ekvation för beräkning av aktuell säker poäng](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Säker poäng för en prenumeration med alla kontroller aktiverade](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>I det här exemplet finns det en enda prenumeration med alla tillgängliga säkerhetskontroller (en potentiell maximal poäng på 60 poäng). Poängen visar 28 poäng av en möjlig 60 och de återstående 32 poäng återspeglas i "Potentiella poängökning" siffror för säkerhetskontroller.<br>![Lista över kontroller och den potentiella poängökningen](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Säkerhetspoäng**<br>Flera prenumerationer|<br>Den aktuella poängen för alla resurser i alla prenumerationer läggs till och beräkningen är då densamma som för en enda prenumeration<br><br>När du visar flera prenumerationer utvärderar säker poäng alla resurser inom alla aktiverade principer och grupperar deras kombinerade inverkan på varje säkerhetskontrolls högsta poäng.<br>![Säker poäng för flera prenumerationer med alla kontroller aktiverade](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Den kombinerade poängen är **inte** ett genomsnitt; Snarare är det den utvärderade hållningen för status för alla resurser i alla prenumerationer.<br>Även här, om du går till rekommendationer sidan och lägga upp de potentiella tillgängliga poäng, kommer du att upptäcka att det är skillnaden mellan den aktuella poängen (24) och den högsta tillgängliga poängen (60).|
||||

## <a name="improving-your-secure-score"></a>Förbättra din säkra poäng

Om du vill förbättra din säkra poäng kan du åtgärda säkerhetsrekommendationer från rekommendationslistan. Du kan åtgärda varje rekommendation manuellt för varje resurs, eller genom att använda **Quick Fix!** (när det är tillgängligt) för att snabbt kunna åtgärda en rekommendation för en resursgrupp. Mer information finns i [Åtgärda rekommendationer](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Endast inbyggda rekommendationer påverkar den säkra poängen.

## <a name="security-controls-and-their-recommendations"></a>Säkerhetskontroller och deras rekommendationer

I tabellen nedan visas säkerhetskontrollerna i Azure Security Center. För varje kontroll kan du se det maximala antalet poäng som du kan lägga till i din säkra poäng om du åtgärdar *alla* rekommendationer som anges i kontrollen, för *alla* dina resurser. 

> [!TIP]
> Om du vill filtrera eller sortera listan på ett annat sätt kopierar och klistrar du in den i Excel.

|Säkerhetskontroll|Högsta säkra poäng|Rekommendationer|
|----------------|:-------------------:|---------------|
|**Aktivera MFA**|10|- MFA bör aktiveras på konton med ägarbehörighet för din prenumeration<br>- MFA bör aktiveras på konton med läsbehörighet för din prenumeration<br>- MFA ska aktiveras konton med skrivbehörighet på din prenumeration|
|**Säkra hanteringsportar**|8|- Just-In-Time nätverksåtkomstkontroll bör tillämpas på virtuella datorer<br>- Virtuella datorer bör associeras med en nätverkssäkerhetsgrupp<br>- Hanteringsportar bör stängas på dina virtuella datorer|
|**Tillämpa systemuppdateringar**|6|- Övervakning agent hälsofrågor bör lösas på dina maskiner<br>- Övervakningsagenten bör installeras på skalningsuppsättningar för virtuella datorer<br>- Övervakningsagenten ska installeras på dina maskiner<br>- OS-versionen bör uppdateras för dina molntjänstroller<br>- Systemuppdateringar på skalningsuppsättningar för virtuella datorer bör installeras<br>- Systemuppdateringar bör installeras på dina maskiner<br>- Dina maskiner bör startas om för att tillämpa systemuppdateringar<br>- Kubernetes Services bör uppgraderas till en icke-sårbar Kubernetes-version<br>- Övervakningsagent bör installeras på dina virtuella datorer|
|**Åtgärda sårbarheter**|6|- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar<br>- Säkerhetsproblem i Azure Container Registry avbildningar bör åtgärdas<br>- Sårbarheter i dina SQL-databaser bör åtgärdas<br>- Sårbarheter bör åtgärdas genom en sårbarhetsbedömningslösning<br>- Sårbarhetsbedömning bör aktiveras på dina SQL-hanterade instanser<br>- Sårbarhetsbedömning bör aktiveras på dina SQL-servrar<br>- Sårbarhetsbedömningslösning bör installeras på dina virtuella datorer|
|**Aktivera kryptering i vila**|4|- Diskkryptering bör tillämpas på virtuella datorer<br>- Transparent datakryptering på SQL-databaser bör aktiveras<br>- Automationskontovariabler ska krypteras<br>- Service Fabric kluster bör ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign<br>- SQL-server TDE-skydd bör krypteras med din egen nyckel|
|**Kryptera data under transport**|4|- API App bör endast vara tillgänglig via HTTPS<br>- Funktionsappen ska endast vara tillgänglig via HTTPS<br>- Endast säkra anslutningar till Redis-cachen ska aktiveras<br>- Säker överföring till lagringskonton bör aktiveras<br>- Webbprogrammet bör endast vara tillgängligt via HTTPS|
|**Hantera åtkomst och behörigheter**|4|- Högst 3 ägare bör utses för din prenumeration<br>- Föråldrade konton ska tas bort från din prenumeration (Preview)<br>- Föråldrade konton med ägarbehörigheter ska tas bort från din prenumeration (Förhandsversion)<br>- Externa konton med ägarbehörigheter ska tas bort från din prenumeration (Förhandsversion)<br>- Externa konton med läsbehörighet bör tas bort från din prenumeration<br>- Externa konton med skrivbehörighet ska tas bort från din prenumeration (Förhandsversion)<br>- Det bör finnas mer än en ägare som tilldelats din prenumeration<br>- Rollbaserad åtkomstkontroll (RBAC) ska användas på Kubernetes Services (förhandsversion)<br>- Service Fabric-kluster bör endast använda Azure Active Directory för klientautentisering|
|**Åtgärda säkerhetskonfigurationer**|4|- Pod säkerhetspolicyer bör definieras på Kubernetes Services<br>- Sårbarheter i behållarens säkerhetskonfigurationer bör åtgärdas<br>- Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas<br>- Sårbarheter i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas<br>- Övervakningsagent bör installeras på dina virtuella datorer<br>- Övervakningsagenten ska installeras på dina maskiner<br>- Övervakningsagenten bör installeras på skalningsuppsättningar för virtuella datorer<br>- Övervakning agent hälsofrågor bör lösas på dina maskiner|
|**Begränsa obehörig nätverksåtkomst**|4|- IP-vidarebefordran på din virtuella dator bör inaktiveras<br>- Auktoriserade IP-intervall bör definieras på Kubernetes Services (Preview)<br>- (FÖRÅLDRAD) Åtkomst till App Services bör begränsas (Preview)<br>- (FÖRÅLDRAD) Reglerna för webbapplikationer på IaaS NSG bör skärpas<br>- Virtuella datorer bör associeras med en nätverkssäkerhetsgrupp<br>- CORS bör inte tillåta alla resurser att komma åt din API-app<br>- CORS bör inte tillåta alla resurser att komma åt din funktionsapp<br>- CORS bör inte tillåta alla resurser att komma åt din webbapplikation<br>- Fjärrfelsökning bör inaktiveras för API App<br>- Fjärrfelsökning bör stängas av för Funktionsapp<br>- Fjärrfelsökning bör inaktiveras för webbprogram<br>- Åtkomsten bör begränsas för tillåtande nätverkssäkerhetsgrupper med virtuella datorer som är vända mot Internet<br>- Regler för nätverkssäkerhetsgruppen för internetvända virtuella datorer bör skärpas|
|**Tillämpa adaptiv programkontroll**|3|- Adaptiva programkontroller bör aktiveras på virtuella datorer<br>- Övervakningsagent bör installeras på dina virtuella datorer<br>- Övervakningsagenten ska installeras på dina maskiner<br>- Övervakning agent hälsofrågor bör lösas på dina maskiner|
|**Tillämpa dataklassificering**|2|- Känsliga data i dina SQL-databaser bör klassificeras (Preview)|
|**Skydda program mot DDoS-attacker**|2|- DDoS Protection Standard bör aktiveras|
|**Aktivera slutpunktsskydd**|2|- Hälsofel för slutpunktsskydd bör åtgärdas i skalningsuppsättningar för virtuella datorer<br>- Hälsofrågor för slutpunktsskydd bör lösas på dina maskiner<br>- Slutpunktsskyddslösning bör installeras på skalningsuppsättningar för virtuella datorer<br>- Installera slutpunktsskyddslösning på virtuella datorer<br>- Övervakning agent hälsofrågor bör lösas på dina maskiner<br>- Övervakningsagenten bör installeras på skalningsuppsättningar för virtuella datorer<br>- Övervakningsagenten ska installeras på dina maskiner<br>- Övervakningsagent bör installeras på dina virtuella datorer<br>- Installera slutpunktsskyddslösning på dina maskiner|
|**Aktivera granskning och loggning**|1|- Granskning på SQL-server bör aktiveras<br>- Diagnostikloggar i App Services bör aktiveras<br>- Diagnostikloggar i Azure Data Lake Store ska aktiveras<br>- Diagnostikloggar i Azure Stream Analytics bör aktiveras<br>- Diagnostikloggar i Batch-konton bör aktiveras<br>- Diagnostikloggar i Data Lake Analytics bör aktiveras<br>- Diagnostikloggar i Event Hub ska aktiveras<br>- Diagnostiska loggar i IoT Hub bör aktiveras<br>- Diagnostikloggar i Key Vault bör aktiveras<br>- Diagnostikloggar i Logic Apps bör aktiveras<br>- Diagnostikloggar i söktjänsten bör aktiveras<br>- Diagnosloggar i Service Bus ska aktiveras<br>- Diagnostikloggar i skalningsuppsättningar för virtuella datorer bör aktiveras<br>- Måttvarningsregler bör konfigureras på batchkonton<br>- SQL-granskningsinställningar bör ha åtgärdsgrupper konfigurerade för att fånga kritiska aktiviteter<br>- SQL-servrar bör konfigureras med granskning kvarhållningsdagar som är större än 90 dagar.|
|**Implementera metodtips för säkerhet**|0|- Tillgången till lagringskonton med brandvägg och virtuella nätverkskonfigurationer bör begränsas<br>- Alla auktoriseringsregler utom RootManageSharedAccessKey bör tas bort från event hub-namnområdet<br>- En Azure Active Directory-administratör bör etableras för SQL-servrar<br>- Auktoriseringsregler för Event Hub-instansen bör definieras<br>- Lagringskonton ska migreras till nya Azure Resource Manager-resurser<br>- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser<br>- Avancerade datasäkerhetsinställningar för SQL-servern bör innehålla en e-postadress för att ta emot säkerhetsvarningar<br>- Avancerad datasäkerhet bör aktiveras på dina hanterade instanser<br>- Alla avancerade typer av skydd mot hot bör aktiveras i avancerade avancerade datasäkerhetsinställningar för SQL-hanterade instanser<br>- E-postmeddelanden till administratörer och prenumerationsägare bör aktiveras i avancerade datasäkerhetsinställningar för SQL-servern<br>- Avancerade typer av skydd mot hot bör ställas in på "Alla" i SQL-servern Avancerade datasäkerhetsinställningar<br>- Undernät bör associeras med en nätverkssäkerhetsgrupp<br>- Alla avancerade typer av skydd mot hot bör aktiveras i avancerade datasäkerhetsinställningar för SQL-servern|
||||

## <a name="secure-score-faq"></a>Vanliga frågor om säker poäng

### <a name="why-has-my-secure-score-gone-down"></a>Varför har min säkra poäng gått ner?
Security Center har växlat till en förbättrad säker poäng (för närvarande i förhandsstatus) som innehåller ändringar i hur poängen beräknas. Nu måste du lösa alla rekommendationer för en resurs för att få poäng. Poängen ändrades också till en skala från 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Om jag bara tar upp tre av fyra rekommendationer i en säkerhetskontroll, ändras min säkra poäng?
Nej. Det ändras inte förrän du åtgärdar alla rekommendationer för en enskild resurs. Om du vill ha den maximala poängen för en kontroll måste du åtgärda alla rekommendationer för alla resurser.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>Är den tidigare erfarenheten av den säkra poängen fortfarande tillgänglig? 
Ja. Ett tag kommer de att köra sida vid sida för att underlätta övergången. Räkna med att den tidigare modellen ska fasas ut i tid. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Om en rekommendation inte är tillämplig på mig, och jag inaktiverar den i principen, kommer min säkerhetskontroll att uppfyllas och min säkra poäng uppdateras?
Ja. Vi rekommenderar att du inaktiverar rekommendationer när de inte kan tillämpas i din miljö. Instruktioner om hur du inaktiverar en viss rekommendation finns i [Inaktivera säkerhetsprinciper](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Om en säkerhetskontroll ger mig noll poäng mot min säkra poäng, ska jag ignorera det?
I vissa fall ser du en kontroll maxpoäng större än noll, men effekten är noll. När den inkrementella poängen för att åtgärda resurser är försumbar avrundas den till noll. Ignorera inte dessa rekommendationer eftersom de fortfarande ger säkerhetsförbättringar. Det enda undantaget är kontrollen "Ytterligare bästa praxis". Åtgärda dessa rekommendationer kommer inte att öka din poäng, men det kommer att förbättra din totala säkerhet.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs den säkra poängen och de säkerhetskontroller som den introducerar. För relaterat material, se följande artiklar:

- [Läs mer om de olika elementen i en rekommendation](security-center-recommendations.md)
- [Läs om hur du åtgärdar rekommendationer](security-center-remediate-recommendations.md)