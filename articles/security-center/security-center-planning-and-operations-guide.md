---
title: "Planering- och bruksanvisning för Security Center | Microsoft Docs"
description: "I det här avsnittet får du hjälp med att planera inför införandet av Azure Security Center och med vad du bör tänka på i det dagliga arbetet."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: f984e4a2-ac97-40bf-b281-2f7f473494c4
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: yurid
ms.openlocfilehash: 52e421a62fa24a56a077bc030e03c0fed34305fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-planning-and-operations-guide"></a>Planerings- och användningsguide för Azure Security Center
Den här guiden riktar sig till IT-tekniker, IT-arkitekter, informationssäkerhetsanalytiker och molnadministratörer i organisationer där man planerar att börja använda Azure Security Center.

    
## <a name="planning-guide"></a>Planeringsanvisningar
Nedan följer ett antal anvisningar för hur du optimerar användningen av Security Center utifrån din organisations säkerhetskrav och molnhanteringsmodell. För att få ut mesta möjliga av Security Center är det viktigt att veta hur olika medarbetare och avdelningar i organisationen kommer att använda tjänsten så att kraven på säkerhet vid utvecklingsarbete, drift, övervakning, styrning och incidenthantering uppfylls. Tänk på följande när du planerar integreringen av Security Center:

* Säkerhetsroller och åtkomstkontroll
* Säkerhetsprinciper och säkerhetsrekommendationer
* Datainsamling och lagring
* Pågående icke-Azure-resurser
* Fortlöpande säkerhetsövervakning
* Incidenthantering

I nästa avsnitt lär du dig hur du planerar för vart och ett av dessa områden och hur du tillämpar rekommendationerna baserat på dina behov.


> [!NOTE]
> På vår sida med [vanliga frågor och svar om Azure Security Center](security-center-faq.md) finns en lista med ofta ställda frågor som kan vara bra att läsa i planerings- och utformningsfasen.
> 

## <a name="security-roles-and-access-controls"></a>Säkerhetsroller och åtkomstkontroll
Beroende på hur stor din organisation är och hur den är uppbyggd kan olika medarbetare och avdelningar använda Security Center för att utföra olika säkerhetsrelaterade arbetsuppgifter. Följande diagram innehåller exempel på fiktiva personer och deras olika roller och ansvarsområden:

![Roller](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Med Security Center kan dessa medarbetare effektivt sköta sina respektive arbetsuppgifter. Exempel:

**Jens (arbetsbelastningsägare)**

* Hanterar en arbetsbelastning i molnet och relaterade resurser.
* Ansvarig för att implementera och underhålla säkerheten i enlighet med företagets säkerhetspolicy.

**Elisabeth (IT-chef)**

* Ansvarig för alla säkerhetsaspekter på företaget.
* Behöver ha en bra överblick över företagets säkerhet i alla arbetsbelastningar i molnet.
* Måste bli informerad om större attacker och risker.

**Daniel (IT-säkerhetsansvarig)**

* Definierar företagets säkerhetsprinciper för att se till att rätt skyddsåtgärder finns på plats.
* Övervakar principefterlevnaden.
* Genererar rapporter till chefer eller granskare.

**Selma (säkerhetsmedarbetare)**

* Övervakar och agerar på säkerhetsvarningar dygnet runt alla dagar i veckan.
* Eskalerar till molnansvarig eller IT-säkerhetsanalytiker.

**Sami (säkerhetsanalytiker)**

* Undersöker attacker.
* Arbetar med företagets molnansvarige för att åtgärda problemet 

I Security Center används [rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-configure.md), vilket innebär att det finns [förinställda roller](../active-directory/role-based-access-built-in-roles.md) som kan tilldelas användare, grupper och tjänster i Azure. När en användare öppnar Security Center ser de bara information om de resurser som de har åtkomst till. Detta betyder att användaren tilldelas rollen som ägare, deltagare eller läsare för den prenumeration eller resursgrupp som en resurs hör till. Förutom dessa roller finns två specifika roller i Security Center:

- **Security-läsare**: en användare som tillhör den här rollen kan bara visa Security Center-konfigurationer, vilket innehåller rekommendationer, aviseringar, principer och hälsa, men kan inte ändra.
- **Security-admin**: samma som security-läsare, men kan också uppdatera säkerhetsprinciper, stänga rekommendationer och aviseringar.

Security Center-rollerna som beskrivs ovan har inte åtkomst till andra delar av Azure, till exempel lagring, webb och mobil eller IoT (sakernas internet).  

> [!NOTE]
> En användare måste vara minst en prenumerant, resursgruppägare eller deltagare för att kunna se Säkerhetscenter i Azure. 
> 
> 

Med utgångspunkt i de fiktiva personer som beskrivs i diagrammet ovan krävs följande rollbaserade åtkomst:

**Jens (arbetsbelastningsägare)**

* ägare/deltagare i resursgrupp

**Daniel (IT-säkerhetsansvarig)**

* Agare/deltagare i prenumeration eller Security Admin

**Selma (säkerhetsmedarbetare)**

* Läsare i prenumeration eller Security-läsare för att kunna se aviseringar
* Agare/deltagare i prenumeration eller Security-admin för att kunna avvisa aviseringar

**Sami (säkerhetsanalytiker)**

* Läsare i prenumeration för att kunna se aviseringar
* Ägare/deltagare i prenumeration för att kunna avvisa aviseringar
* Åtkomst till arbetsytan kan krävas

Tänk även på följande:

* Endast ägare och deltagare i prenumerationer samt Security-administratörer kan ändra säkerhetsprinciper.
* Endast ägare och deltagare i prenumerationer och resursgrupper kan tillämpa säkerhetsrekommendationer på en resurs.

När du planerar åtkomstkontroll med rollbaserad åtkomst (RBAC) för Security Center är det viktigt att du vet vem i din organisation som ska använda Security Center, samt vilka typer av uppgifter som de ska utföra, innan du konfigurerar den rollbaserade åtkomsten.

> [!NOTE]
> Vi rekommenderar att du ger användarna den roll som precis ger dem den behörighet de behöver för att kunna utföra sina arbetsuppgifter. De användare som till exempel endast behöver se information om säkerhetsstatusen på resurser men inte vidta några åtgärder, som att tillämpa rekommendationer eller ändra principer, bör få rollen som läsare.
> 
> 

## <a name="security-policies-and-recommendations"></a>Säkerhetsprinciper och säkerhetsrekommendationer
En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav. I Security Center kan du definiera principer för dina Azure-prenumerationer, som kan skräddarsys efter arbetsbelastningstyp eller datakänslighet.

Security Center-principer innehåller följande komponenter:
- [Datainsamling](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection): agentetablering och datainsamlingsinställningar.
- [Säkerhetsprincip](https://docs.microsoft.com/azure/security-center/security-center-policies): en [Azure-princip](http://docs.microsoft.com/en-us/azure/azure-policy/azure-policy-introduction) som bestämmer vilka kontroller som övervakas och rekommenderas av Security Center, eller använd Azure-principen till att skapa nya definitioner, definiera fler principer och tilldela principer i flera hanteringsgrupper.
- [E-postmeddelanden](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details): säkerhetskontakter och inställningar för meddelanden.
- [Prisnivå](https://docs.microsoft.com/azure/security-center/security-center-pricing): val av kostnadsfritt eller standardpris, som bestämmer vilka Security Center-funktioner som är tillgängliga för resurser i omfattningen (kan anges för prenumerationer, resursgrupper och arbetsytor).

> [!NOTE]
> Om du anger ett säkerhetskontrakt säkerställer du att Azure kan nå rätt person i organisationen om en säkerhetsincident inträffar. Mer information om hur du aktiverar den här rekommendationen finns i [Lägga till kontaktuppgifter i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).

### <a name="security-policies-definitions-and-recommendations"></a>Säkerhetsprincipdefinitioner och rekommendationer
Security Center skapar automatiskt en standardsäkerhetsprincip för var och en av dina Azure-prenumerationer. Du kan redigera principen i Security Center eller använda Azure Policy för att skapa nya definitioner, definiera ytterligare principer och tilldela principer i hanteringsgrupper (som kan motsvara hela organisationen, en affärsenhet osv.) och övervaka efterlevnad av dessa principer i dessa omfattningar.

Innan du börjar konfigurera säkerhetsprinciper går du igenom de olika [säkerhetsrekommendationerna](https://docs.microsoft.com/azure/security-center/security-center-recommendations) och avgör om dessa principer passar dina olika prenumerationer och resursgrupper. Det är också viktigt att förstå vilka åtgärder som vidtas för att hantera säkerhetsrekommendationer och vem i din organisation är ansvarig för att övervaka nya rekommendationer och vidta nödvändiga åtgärder.

## <a name="data-collection-and-storage"></a>Datainsamling och datalagring
Azure Security Center använder Microsoft Monitoring Agent (samma agent används av Operations Management Suite och Log Analytics-tjänsten) för att samla in säkerhetsdata från dina virtuella datorer. [Data som samlas in](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) från den här agenten kommer att lagras i Log Analytics-arbetsytor.

### <a name="agent"></a>Agent

När automatisk etablering är aktiverat i säkerhetsprincipen installeras Microsoft Monitoring Agent (för [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) eller [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)) på alla virtuella Azure-datorer som stöds och alla nya som skapas. Om den virtuella datorn eller datorn redan har Microsoft Monitoring Agent installerad, kommer Azure Security Center att utnyttja den befintliga installerade agenten. Agentens process är avsedd att vara icke-inkräktande och har mycket minimal påverkan på den virtuella datorns prestanda.

Microsoft Monitoring Agent för Windows kräver TCP-port 443. Se [Felsökningsartikeln](security-center-troubleshooting-guide.md) för ytterligare information.

Om du vid ett senare tillfälle vill inaktivera datainsamlingen kan du göra det i säkerhetsprincipen. Men eftersom Microsoft Monitoring Agent kan användas av andra Azure-hanterings. och övervakningstjänster avinstalleras agenten inte automatiskt när du stänger av datainsamling i Security Center. Du kan avinstallera agenten manuellt om det behövs.

> [!NOTE]
> Om du vill veta vilka virtuella datorer som stöds finns en lista bland våra [vanliga frågor och svar om Azure Security Center](security-center-faq.md).
> 

### <a name="workspace"></a>Arbetsyta

En arbetsyta är en Azure-resurs som fungerar som en databehållare. Du eller andra medlemmar i din organisation kan använda flera arbetsytor för att hantera olika uppsättningar av data som samlas in från alla eller delar av din IT-infrastruktur.

Data som samlas in från Microsoft Monitoring Agent (för Azure Security Center) lagras i befintliga logganalysarbetsytor som är associerade med din Azure-prenumeration eller nya arbetsytor med hänsyn till den virtuella datorns geografiska plats. 

Du kan bläddra om du vill se en lista över dina logganalysarbetsytor, inklusive alla som skapats av Azure Security Center i Azure-portalen. En relaterad resursgrupp skapas för nya arbetsytor. Både följer namnkonventionen: 

* Arbetsyta: *DefaultWorkspace-[prenumerations-ID]-[geo]*
* Resursgrupp: *DefaultResourceGroup-[geo]*

För arbetsytor som skapats av Azure Security Center sparas data i 30 dagar. För befintliga arbetsytor baseras kvarhållningen på arbetsytans prisnivå. Om du vill kan du även använda en befintlig arbetsyta.

> [!NOTE]
> Microsoft arbetar hårt för att skydda sekretessen och säkerheten för dessa data. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst. Mer information om datahantering och sekretess finns i [Datasäkerhet i Azure Security Center](security-center-data-security.md).
> 

## <a name="onboarding-non-azure-resources"></a>Publicera icke-Azure-resurser

Security Center kan övervaka säkerhetsstatusen för icke-Azure-datorer men du måste först publicera dessa resurser. Mer information om hur du publicerar icke-Azure-resurser finns i [Publicera i Azure Security Center Standard för förbättrad säkerhet](https://docs.microsoft.com/azure/security-center/security-center-onboarding#onboard-non-azure-computers).

## <a name="ongoing-security-monitoring"></a>Fortlöpande säkerhetsövervakning
När rekommendationerna i Security Center har ställts in och tillämpats är det dags att fundera över driftrutinerna i Security Center.

Översikten över Security Center ger en enhetlig vy över säkerheten i alla dina Azure-resurser och eventuella icke-Azure-resurser som du har anslutit. Exemplet nedan visar en miljö med många problem att hantera:

![instrumentpanel](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig10.png)

> [!NOTE]
> Security Center påverkar inte de normala driftrutinerna. Alla distributioner övervakas passivt och rekommendationer går ut baserat på de säkerhetsprinciper som du har aktiverat.

Första gången du väljer att använda Security Center för din befintliga Azure-miljö är det viktigt att du läser igenom alla rekommendationer. Det kan du göra på bladet **Rekommendationer** eller för varje resurs (**Compute**, **Networking**, **Storage & data** och **Application**).

När du har genomfört alla rekommendationer bör **skyddsdelen** vara grön för alla resurser som du har åtgärdat. Den fortlöpande övervakningen blir enklare efter det här eftersom du hädanefter bara behöver vidta åtgärder om det sker förändringar i resursernas säkerhetshälsa och i rekommendationsrutorna.

**Identifieringsdelen** är mer reaktiv. Här visas varningar om problem som antingen precis uppstått eller som uppkommit tidigare men som nyss upptäckts genom kontrollerna i Security Center och i tredjepartssystem. I rutan med säkerhetsaviseringar visas diagram med antalet hotidentifieringsaviseringar som uppkommit per dag uppdelat efter olika allvarlighetsgrader (låg, medelhög och hög). Mer information om säkerhetsaviseringar finns i [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md).

Planera in att använda [Hotinformation](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) som en del av dina dagliga säkerhetsåtgärder. Där kan du identifiera säkerhetshot mot miljön, till exempel identifiera om en viss dator är en del av ett botnät.

### <a name="monitoring-for-new-or-changed-resources"></a>Övervakning av nya och ändrade resurser
De flesta miljöer i Azure är dynamiska, med nya resurser som skapas och försvinner, nya konfigurationer och ändringar och så vidare. Med Security Center har du bra insyn i de nya objektens säkerhetsstatus.

När du lägger till nya resurser (virtuella datorer, SQL-databaser osv.) i Azure-miljön identifierar Security Center de här resurserna automatiskt och börjar övervaka deras säkerhet. Detta omfattar även arbetarroller och webbroller i PaaS. Om datainsamling har aktiverats i [säkerhetsprincipen](security-center-policies.md) aktiveras ytterligare övervakningsfunktioner för alla virtuella datorer automatiskt.

![Huvuddelar](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. För virtuella datorer, klickar du på **Compute** under området **Förebyggande**. Eventuella problem med att aktivera datainsamling eller tillhörande rekommendationer visas i fliken **Översikt** och **Monitoring Recommendations** (Övervakningsrekommendationer).
2. Under **Recommendations (Rekommendationer)** kan du se om några säkerhetsrisker har upptäckts i den nya resursen.
3. När nya virtuella datorer läggs till i miljön brukar det först bara vara operativsystemet som är installerat. Resursägaren kan behöva lite tid för att distribuera andra program som ska användas på de här datorerna.  Helst bör du veta vilka planerna för arbetsbelastningen är. Ska det vara en programserver? Beroende på vad den nya arbetsbelastningen ska bli kan du aktivera en **säkerhetsprincip** som passar, vilket är det tredje steget i det här arbetsflödet.
4. När nya resurser läggs in i Azuremiljön kan det hända att nya aviseringar visas i rutan **Security Alerts (Säkerhetsaviseringar)**. Kontrollera alltid om det finns nya aviseringar i den här rutan och vidta åtgärder enligt rekommendationerna i Security Center.

Du bör även regelbundet övervaka befintliga resurser för att se om det görs några konfigurationsändringar som ger upphov till säkerhetsrisker eller som inte följer rekommenderade baslinjer eller för att se om det kommer upp några säkerhetsaviseringar. Starta på instrumentpanelen för Security Center. Där finns tre större delar som du bör kontrollera regelbundet.

![Åtgärder](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. I rutan **Förebyggande** kan du snabbt gå till dina viktigaste resurser. Använd det här alternativet om du vill övervaka beräkning, nätverk, lagring och data samt program.
2. I rutan **Rekommendationer** ser du rekommendationerna från Security Center. Under den löpande övervakningen kanske du inte får rekommendationer varje dag, vilket är normalt eftersom du utförde alla rekommendationer när du konfigurerade Security Center. Så det finns kanske inte ny information här varje dag och du behöver bara gå hit ibland.
3. Hur ofta innehållet i **identifieringspanelen** ändras kan variera ganska mycket. Du bör alltid kontrollera säkerhetsaviseringarna och vidta åtgärder enligt rekommendationerna i Security Center.

### <a name="hardening-access-and-applications"></a>Härdning av åtkomst och program

Som en del av dina säkerhetsåtgärder bör du även vidta förebyggande åtgärder för att begränsa åtkomsten till virtuella datorer och kontrollera programmen som körs på virtuella datorer. Genom att låsa inkommande trafik till dina virtuella Azure-datorer minskar du exponeringen för attacker och samtidigt ger du enkel anslutningsåtkomst till virtuella datorer när det behövs. Använd funktionen [Just-in-time-åtkomst till virtuell dator](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) för att härda åtkomsten till dina virtuella datorer. 

Du kan använda [anpassningsbara programkontroller](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) till att kontrollera vilka program som kan köras på din virtuella dator i Azure, vilket bland annat hjälper dig skydda dina virtuella datorer mot skadlig kod. Security Center använder Machine Learning för att analysera processerna som körs i den virtuella datorn och hjälper dig att tillämpa vitlisteregler med den här intelligensen.


## <a name="incident-response"></a>Incidenthantering
Security Center identifierar och varnar dig om hot så fort de uppstår. Organisationen bör övervaka om det kommer nya säkerhetsaviseringar och vidta de åtgärder som behövs för att undersöka vidare eller stoppa angreppet. Mer information om hur hotidentifieringen i Security Center fungerar finns i [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md).

Avsikten med den här artikeln är inte att hjälpa dig att skapa en egen incidenthanteringsplan, men vi ska använda Microsoft Azure Security Response i molnets livscykel för att beskriva de grundläggande incidenthanteringsfaserna. Stegen visas i följande diagram:

![Misstänkt aktivitet](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> National Institute of Standards and Technology (NIST) har en [handbok för hantering av datasäkerhetsrelaterade incidenter](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) som kan vara till hjälp när du vill skapa en egen plan.
> 

Du kan använda Security Center-aviseringar i följande faser:

* **Identifiera**: Identifiera misstänkt aktivitet i en eller flera resurser. 
* **Utvärdera**: Utför en första utvärdering för att få mer information om den misstänkta aktiviteten.
* **Diagnostisera**: Gå igenom de tekniska rutinerna genom att utföra åtgärdsstegen för att åtgärda problemet.

I säkerhetsaviseringarna finns information som gör att du kan förstå vilken typ av angrepp det rör sig om och vad du kan göra för att åtgärda angreppet. I vissa aviseringar finns länkar antingen till mer information eller till andra informationskällor inom Azure. Du kan använda informationen för ytterligare forskning och för att påbörja åtgärdsarbetet. Du kan också söka säkerhetsrelaterade data som lagras på din arbetsyta.

Följande exempel visar en misstänkt RDP-aktivitet:

![Misstänkt aktivitet](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Den här sidan visar information om när angreppet upptäcktes, varifrån det kommer och vilken virtuell dator som är drabbad, och här finns även rekommendationer för vad du bör göra. I vissa fall finns ingen information om varifrån angreppet kommer. [Här](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) finns mer information om de fall då uppgift om källa saknas i aviseringar i Azure Security Center.

På den här sidan kan du även starta en [undersökning](https://docs.microsoft.com/azure/security-center/security-center-investigation) för att bättre förstå attackens tidslinje, hur attacken skedde, vilka system som möjligen har drabbats, vilka autentiseringsuppgifter som användes samt visa en grafisk representation av hela attackkedjan.

När du har identifierat det drabbade systemet kan du köra [strategiböcker för säkerhet](https://docs.microsoft.com/azure/security-center/security-center-playbooks) som har skapats tidigare. Säkerhetsspelbok är en samling processer som kan köras från Security Center när en viss spelbok löses ut av en vald avisering.

I videoklippet [How to Leverage the Azure Security Center & Microsoft Operations Management Suite for an Incident Response](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) (Använda Azure Security Center och Microsoft Operations Management Suite för incidenthantering) kan du se några demonstrationer som beskriver hur Security Center kan användas i var och en av dessa faser.

> [!NOTE]
> Läs [Använda Azure Security Center vid incidenthantering](security-center-incident-response.md) om du vill ha mer information om hur du ska använda Security Center-funktionerna för att få hjälp under incidenthanteringsprocessen. 
> 
> 

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du planerar integreringen av Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar Azure-resursernas hälsa.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

