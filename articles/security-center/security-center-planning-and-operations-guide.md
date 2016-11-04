---
title: Planering- och bruksanvisning för Security Center | Microsoft Docs
description: I det här avsnittet får du hjälp med att planera inför införandet av Azure Security Center och med vad du bör tänka på i det dagliga arbetet.
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: ''

ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2016
ms.author: yurid

---
# Planerings- och användningsguide för Azure Security Center
Den här guiden riktar sig till IT-tekniker, IT-arkitekter, informationssäkerhetsanalytiker och molnadministratörer i organisationer där man planerar att börja använda Azure Security Center.

## Planeringsanvisningar
Nedan följer ett antal anvisningar för hur du optimerar användningen av Security Center utifrån din organisations säkerhetskrav och molnhanteringsmodell. För att få ut mesta möjliga av Security Center är det viktigt att veta hur olika medarbetare och avdelningar i organisationen kommer att använda tjänsten så att kraven på säkerhet vid utvecklingsarbete, drift, övervakning, styrning och incidenthantering uppfylls. Tänk på följande när du planerar integreringen av Security Center:

* Säkerhetsroller och åtkomstkontroll
* Säkerhetsprinciper och säkerhetsrekommendationer
* Datainsamling och lagring
* Fortlöpande säkerhetsövervakning
* Incidenthantering

I nästa avsnitt lär du dig hur du planerar för vart och ett av dessa områden och hur du tillämpar rekommendationerna baserat på dina behov.

> [!NOTE]
> På vår sida med [vanliga frågor och svar om Azure Security Center](security-center-faq.md) finns en lista med ofta ställda frågor som kan vara bra att läsa i planerings- och utformningsfasen.
> 
> 

## Säkerhetsroller och åtkomstkontroll
Beroende på hur stor din organisation är och hur den är uppbyggd kan olika medarbetare och avdelningar använda Security Center för att utföra olika säkerhetsrelaterade arbetsuppgifter. Följande diagram innehåller exempel på fiktiva personer och deras olika roller och ansvarsområden:

![Roller](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-ga.png)

Med Security Center kan dessa medarbetare effektivt sköta sina respektive arbetsuppgifter. Exempel:

**Jeff (molnansvarig)**

* Hanterar en arbetsbelastning i molnet och relaterade resurser.
* Ansvarig för att implementera och underhålla säkerheten i enlighet med företagets säkerhetspolicy.

**Ellen (CISO/IT)**

* Ansvarig för alla säkerhetsaspekter på företaget.
* Behöver ha en bra överblick över företagets säkerhet i alla arbetsbelastningar i molnet.
* Måste bli informerad om större attacker och risker.

**David (IT-säkerhetsansvarig)**

* Definierar företagets säkerhetsprinciper för att se till att rätt skyddsåtgärder finns på plats.
* Övervakar principefterlevnaden.
* Genererar rapporter till chefer eller granskare.

**Judy (säkerhetsmedarbetare)**

* Övervakar och agerar på säkerhetsvarningar dygnet runt alla dagar i veckan.
* Eskalerar till molnansvarig eller IT-säkerhetsanalytiker.

**Sam (säkerhetsanalytiker)**

* Undersöker attacker.
* Åtgärdar varningar eller arbetar med företagets molnansvarige för att åtgärda problem. 

I Security Center används [rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-configure.md), vilket innebär att det finns [förinställda roller](../active-directory/role-based-access-built-in-roles.md) som kan tilldelas användare, grupper och tjänster i Azure. När en användare öppnar Security Center ser de bara information om de resurser som de har åtkomst till. Detta betyder att användaren tilldelas rollen som ägare, deltagare eller läsare för den prenumeration eller resursgrupp som en resurs hör till. Med utgångspunkt i de fiktiva personer som beskrivs i diagrammet ovan krävs följande rollbaserade åtkomst:

**Jeff (molnansvarig)**

* ägare/deltagare i resursgrupp

**David (IT-säkerhetsansvarig)**

* ägare/deltagare i prenumeration

**Judy (säkerhetsmedarbetare)**

* läsare i prenumeration för att kunna se aviseringar
* ägare/deltagare i prenumeration för att kunna avvisa aviseringar

**Sam (säkerhetsanalytiker)**

* läsare i prenumeration för att kunna se aviseringar
* ägare/deltagare i prenumeration för att kunna åtgärda och avvisa aviseringar
* kan behöva åtkomst till lagringsutrymmet

Tänk även på följande:

* Endast ägare och deltagare i prenumerationer kan ändra säkerhetsprinciper.
* Endast ägare och deltagare i prenumerationer och resursgrupper kan tillämpa säkerhetsrekommendationer på en resurs.

När du planerar åtkomstkontroll med rollbaserad åtkomst (RBAC) för Security Center är det viktigt att du vet vem i din organisation som ska använda Security Center, samt vilka typer av uppgifter som de ska utföra, innan du konfigurerar den rollbaserade åtkomsten.

> [!NOTE]
> Vi rekommenderar att du ger användarna den roll som precis ger dem den behörighet de behöver för att kunna utföra sina arbetsuppgifter. De användare som till exempel endast behöver se information om säkerhetsstatusen på resurser men inte vidta några åtgärder, som att tillämpa rekommendationer eller ändra principer, bör få rollen som läsare.
> 
> 

## Säkerhetsprinciper och säkerhetsrekommendationer
En säkerhetsprincip är ett antal kontrollfunktioner som rekommenderas för resurser i en viss prenumeration eller resursgrupp. I Security Center skapar du principer baserat på företagets säkerhetsbehov, hur informationen ska användas och hur känslig den är.

Principer som aktiveras på prenumerationsnivå gäller automatiskt för alla resursgrupper i den prenumerationen, som du ser i följande diagram:

![Säkerhetsprinciper](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig2-ga.png)

Som du ser ovan kan säkerhetsprinciperna för resursgrupper ärvas från prenumerationsnivån.

Om du har resurser i en resursgrupp som det behövs andra principer för kan du stänga av arvfunktionen och tillämpa särskilda principer för just den specifika resursgruppen.

Om du vill tillämpa särskilda principer i en viss resursgrupp inaktiverar du arvfunktionen i resursgruppen och ändrar säkerhetsprinciperna. Om du till exempel har ett antal arbetsbelastningar där principen SQL transparent datakryptering inte behövs inaktiverar du principen på prenumerationsnivå och aktiverar den bara i de resursgrupper där denna datakrypteringsprincip behövs.

När du börjar skapa särskilda principer för olika resursgrupper bör du ha i åtanke att om två principer motsäger varandra (på prenumerationsnivå och resursgruppsnivå) är det principen för resursgruppen som tillämpas. Du bör planera principtillämpningen därefter.

> [!NOTE]
> Om du vill se vilka principer som har ändrats kan du göra det i [Azure-granskningsloggarna](https://blogs.msdn.microsoft.com/cloud_solution_architect/2015/03/10/audit-logs-for-azure-events/). Alla principändringar loggas alltid i granskningsloggarna i Azure.
> 
> 

### Säkerhetsrekommendationer
Innan du börjar konfigurera säkerhetsprinciper går du igenom de olika [säkerhetsrekommendationerna](security-center-recommendations.md) och avgör om dessa principer passar dina olika prenumerationer och resursgrupper. Det är också viktigt att förstå vilka åtgärder som vidtas baserat på säkerhetsrekommendationerna.

**Endpoint Protection**: Om det inte finns någon Endpoint Protection-lösning på en virtuell dator rekommenderar Security Center att du installerar en. Om det redan finns en lokal Endpoint Protection-lösning måste du bestämma om du vill använda samma program mot skadlig kod för dina virtuella datorer i Azure. I Security Center kan du välja mellan flera alternativ för att skydda dina slutpunkter.  Du kan använda det kostnadsfria programmet Microsoft Antimalware eller välja från en lista med slutpunktsskyddslösningar från integrerade partnerleverantörer. Mer information om hur du distribuerar program mot skadlig kod med Security Center finns i [Installera Endpoint Protection i Azure Security Center](security-center-install-endpoint-protection.md).

**Systemuppdateringar**: Security Center identifierar virtuella datorer som saknar viktiga säkerhets- eller operativsystemuppdateringar för IaaS och Cloud Services (PaaS). Tänk igenom vem som ska ansvara för installationen av uppdateringar när det behövs och hur de installeras. I många organisationer används WSUS, Windows Update eller något annat verktyg.

**Baslinjekonfigurationer**: Om operativsystemkonfigurationen på virtuella datorer inte överensstämmer med rekommenderade baslinjer visas en rekommendation. Gå igenom uppsättningen med baslinjer [här](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) och fundera över hur operativsystemkonfigurationerna används.

**Diskkryptering**: Om du har virtuella diskar som inte är krypterade rekommenderar Security Center att du använder Azure Disk Encryption. Den här funktionen tillhandahåller volymkryptering av operativsystem- och datadiskar med hjälp av BitLocker för Windows och DM-Crypt för Linux. Den här rekommendationen omdirigerar dig till [en stegvis guide](security-center-disk-encryption.md) som beskriver hur du utför krypteringen.

Observera att det finns olika krypteringssituationer som du måste hantera. Nedanstående olika situationer innebär olika krav som du måste planera efter:

* kryptering av nya virtuella datorer i Azure som kommer från virtuella hårddiskar som är krypterade med era egna krypteringsnycklar
* kryptering av nya virtuella datorer i Azure som skapats i Azures galleri
* kryptering av virtuella datorer i Azure som redan körs i Azure

Olika planering måste göras för var och en av de olika situationerna. Utförligare information om de här situationerna finns i [informationsdokumentet om Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).

**Brandvägg för webbaserade program**: Security Center identifierar virtuella datorer som kör webbprogram och rekommenderar att du installerar en brandvägg för webbaserade program. Gå igenom utbudet av partnerlösningar och avgör vilken som är bäst för din organisation och bestäm hur lösningen ska licensieras (partnerleverantörerna kan erbjuda bring your own license och/eller användningsbaserad betalning). Mer information om hur du distribuerar brandväggar för webbaserade program på virtuella datorer i Azure med Security Center finns i [Lägga till en brandvägg för webbaserade program i Azure Security Center](security-center-add-web-application-firewall.md).

**Nästa generations brandvägg**: Gör att du kan etablera en virtuell enhet från ledande leverantörer, inklusive Check Point och snart därefter Cisco och Fortinet. Nätverksskyddet utökas utöver nätverkssäkerhetsgrupperna, som är inbyggda i Azure. Distributioner som nästa generations brandvägg rekommenderas för upptäcks av Security Center och du får möjlighet att göra en virtuell installation.

**Virtuella nätverk**: Security Center utvärderar infrastrukturen och konfigurationen i ditt [Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) för att kontrollera att [nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md) används och är korrekt konfigurerade med regler för inkommande trafik. Du bör fundera över vilka trafikregler som ska ställas in och meddela beslutet till de som ska tillämpa de aktuella säkerhetsrekommendationerna.

Security Center rekommenderar att du uppger kontaktuppgifter för säkerhetsrelaterade frågor relaterade till din Azure-prenumeration. Den här informationen används av Microsoft för att kontakta dig om Microsoft Security Response Center (MRSC) upptäcker att en obehörig part har kommit åt dina kunddata. Mer information om hur du aktiverar den här rekommendationen finns i [Lägga till kontaktuppgifter i Azure Security Center](security-center-provide-security-contact-details.md).

## Datainsamling och datalagring
Vi rekommenderar starkt att du aktiverar datainsamling för alla prenumerationer, eftersom du då kan vara säker på att alla virtuella datorer är säkerhetsövervakade. Datainsamling aktiveras via övervakningsagenten i Azure (ASMAgentLauncher.exe) och Azures säkerhetsövervakningstillägg (ASMMonitoringAgent.exe).

Med Azures säkerhetsövervakningstillägg söks olika säkerhetsrelevanta konfigurationer igenom och säkerhetsloggar från virtuella datorer samlas in. Insamlade data skickas till det lagringskonto som du anger. Även genomsökningshanteringsprogrammet (ASMSoftwareScanner.exe) installeras på den virtuella datorn och används sedan för kontroll av korrigeringar.

När datainsamlingsfunktionen är aktiverad i säkerhetsprincipen installeras övervakningsagenten och tilläggen automatiskt på alla befintliga och nya kompatibla virtuella datorer som skapas i  Azure.  Agenten har ingen effekt på de virtuella datorernas prestanda.

> [!NOTE]
> Information om hur du felsöker problem relaterade till Azure Security Monitoring Agent finns i [felsökningsguiden för Azure Security Center](security-center-troubleshooting-guide.md).
> 
> 

Om du vid ett senare tillfälle vill inaktivera datainsamlingen kan du göra det i säkerhetsprincipen. Om du vill ta bort övervakningsagenterna som distribuerats väljer du menyalternativet Ta bort agenter.

> [!NOTE]
> Om du vill veta vilka virtuella datorer som stöds finns en lista bland våra [vanliga frågor och svar om Azure Security Center](security-center-faq.md).
> 
> 

För var och en av de regioner där det finns virtuella datorer väljer du på vilket lagringskonto data som samlas in från de datorerna ska lagras. Om du inte väljer något lagringskonto för en region skapas ett åt dig. Du kan välja att ha olika lagringsutrymmen för olika regioner eller lagra all information centralt på en plats. Säkerhetsprinciper kan ställas in på prenumerations- och resursgruppsnivå, men regionen för lagringskontot kan bara ställas in på prenumerationsnivå.

Om du har ett och samma lagringskonto för olika Azure-resurser bör du läsa artikeln om [skalbarhets- och prestandamål för Azure Storage](../storage/storage-scalability-targets.md) för mer information om storleksgränser och begränsningar. Din prenumeration har även gränser för lagringskonton. Mer information om dessa begränsningar finns i [Gränser, kvoter och begränsningar för Azure-prenumerationer och Azure-tjänster](../azure-subscription-service-limits.md).

> [!NOTE]
> Kostnaderna för den här lagringen ingår inte i priset för Security Center-tjänsten och debiteras separat enligt [Azures vanliga priser för lagring](https://azure.microsoft.com/pricing/details/storage/).
> 
> 

Du bör även fundera på olika prestanda- och skalbarhetskrav utifrån hur mycket resurser du har i Azure och vilka resurser som tar upp mycket utrymme på lagringskontot. Se [checklistan för prestanda och skalbarhet i Microsoft Azure Storage](../storage/storage-performance-checklist.md) för mer information.

## Fortlöpande säkerhetsövervakning
När rekommendationerna i Security Center har ställts in och tillämpats är det dags att fundera över driftrutinerna i Security Center.

Du kan komma åt Security Center från Azure-portalen genom att klicka på **Bläddra** och skriva **Security Center** i fältet **Filter**. Vyerna som användaren ser baseras på dessa filter.

Security Center påverkar inte de normala driftrutinerna. Alla distributioner övervakas passivt och rekommendationer går ut baserat på de säkerhetsprinciper som du har aktiverat.

Instrumentpanelen i Security Center är uppdelad i två huvuddelar:

* Prevention (Skydd)
* Detection (Identifiering)

Första gången du aktiverar datainsamling för din befintliga Azure-miljö i Security Center är det viktigt att du läser igenom alla rekommendationer. Det kan du göra på bladet **Rekommendationer** eller för varje resurs (**virtuell dator**, **nätverk**, **SQL** och **program**).

När du har genomfört alla rekommendationer bör **skyddsdelen** vara grön för alla resurser som du har åtgärdat. Den fortlöpande övervakningen blir enklare efter det här eftersom du hädanefter bara behöver vidta åtgärder om det sker förändringar i resursernas säkerhetshälsa och i rekommendationsrutorna.

**Identifieringsdelen** är mer reaktiv. Här visas varningar om problem som antingen precis uppstått eller som uppkommit tidigare men som nyss upptäckts genom kontrollerna i Security Center och i tredjepartssystem. I rutan med säkerhetsaviseringar visas diagram med antalet hotidentifieringsaviseringar som uppkommit per dag uppdelat efter olika allvarlighetsgrader (låg, medelhög och hög). Mer information om säkerhetsaviseringar finns i [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md).

> [!NOTE]
> Du kan även använda Microsoft Power BI för att få en tydligare bild av Security Center-data. Läs [Kunskap genom statistik från Azure Security Center med Power BI](security-center-powerbi.md).
> 
> 

### Övervakning av nya och ändrade resurser
De flesta miljöer i Azure är dynamiska, med nya resurser som skapas och försvinner, nya konfigurationer och ändringar och så vidare. Med Security Center har du bra insyn i de nya objektens säkerhetsstatus.

När du lägger till nya resurser (virtuella datorer, SQL-databaser osv.) i Azure-miljön identifierar Security Center de här resurserna automatiskt och börjar övervaka deras säkerhet. Detta omfattar även arbetarroller och webbroller i PaaS. Om datainsamling har aktiverats i [säkerhetsprincipen](security-center-policies.md) aktiveras ytterligare övervakningsfunktioner för alla virtuella datorer automatiskt.

![Huvuddelar](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-ga.png)

1. För virtuella datorer går du till rutan **Resource security health (Resurssäkerhetshälsa)** och klickar på **Virtual Machines (Virtuella datorer)**. Eventuella problem med att aktivera datainsamling eller tillhörande rekommendationer visas under rubriken **Monitoring Recommendations (Övervakningsrekommendationer)**.
2. Under **Recommendations (Rekommendationer)** kan du se om några säkerhetsrisker har upptäckts i den nya resursen.
3. När nya virtuella datorer läggs till i miljön brukar det först bara vara operativsystemet som är installerat. Resursägaren kan behöva lite tid för att distribuera andra program som ska användas på de här datorerna.  Helst bör du veta vilka planerna för arbetsbelastningen är. Ska det vara en programserver? Beroende på vad den nya arbetsbelastningen ska bli kan du aktivera en **säkerhetsprincip** som passar, vilket är det tredje steget i det här arbetsflödet.
4. När nya resurser läggs in i Azuremiljön kan det hända att nya aviseringar visas i rutan **Security Alerts (Säkerhetsaviseringar)**. Kontrollera alltid om det finns nya aviseringar i den här rutan och vidta åtgärder enligt rekommendationerna i Security Center.

Du bör även regelbundet övervaka befintliga resurser för att se om det görs några konfigurationsändringar som ger upphov till säkerhetsrisker eller som inte följer rekommenderade baslinjer eller för att se om det kommer upp några säkerhetsaviseringar. Starta på instrumentpanelen för Security Center. Där finns tre större delar som du bör kontrollera regelbundet.

![Åtgärder](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4.png)

1. I rutan **Resource security health (Resurssäkerhetshälsa)** kan du snabbt gå till dina viktigaste resurser. Härifrån kan du lätt övervaka virtuella datorer, nätverk, SQL och program.
2. I rutan **Rekommendationer** ser du rekommendationerna från Security Center. Under den löpande övervakningen kanske du inte får rekommendationer varje dag, vilket är normalt eftersom du utförde alla rekommendationer när du konfigurerade Security Center. Så det finns kanske inte ny information här varje dag och du behöver bara gå hit ibland.
3. Hur ofta innehållet i **identifieringsrutan** ändras kan variera ganska mycket. Du bör alltid kontrollera säkerhetsaviseringarna och vidta åtgärder enligt rekommendationerna i Security Center.

## Incidenthantering
Security Center identifierar och varnar dig om hot så fort de uppstår. Organisationen bör övervaka om det kommer nya säkerhetsaviseringar och vidta de åtgärder som behövs för att undersöka vidare eller stoppa angreppet. Mer information om hur hotidentifieringen i Security Center fungerar finns i [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md).

Avsikten med den här artikeln är inte att hjälpa dig att skapa en egen incidenthanteringsplan, men vi ska använda Microsoft Azure Security Response i molnets livscykel för att beskriva de grundläggande incidenthanteringsfaserna. Stegen visas i följande diagram:

![Misstänkt aktivitet](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> National Institute of Standards and Technology (NIST) har en [handbok för hantering av datasäkerhetsrelaterade incidenter](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) som kan vara till hjälp när du vill skapa en egen plan.
> 
> 

Du kan använda Security Center-aviseringar i följande faser:

* **Identifiera**: Identifiera misstänkt aktivitet i en eller flera resurser. 
* **Utvärdera**: Utför en första utvärdering för att få mer information om den misstänkta aktiviteten.
* **Diagnostisera**: Gå igenom de tekniska rutinerna genom att utföra åtgärdsstegen för att åtgärda problemet.

I säkerhetsaviseringarna finns information som gör att du kan förstå vilken typ av angrepp det rör sig om och vad du kan göra för att åtgärda angreppet. I vissa aviseringar finns länkar antingen till mer information eller till andra informationskällor inom Azure. Du kan använda informationen som utgångspunkt för vidare utredning och för att påbörja arbetet med att åtgärda problemet.

Följande exempel visar en misstänkt RDP-aktivitet:

![Misstänkt aktivitet](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

På det här bladet kan du se när angreppet upptäcktes, varifrån det kommer och vilken virtuell dator som är drabbad, och här finns även rekommendationer för vad du bör göra. I vissa fall finns ingen information om varifrån angreppet kommer. [Här](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) finns mer information om de fall då uppgift om källa saknas i aviseringar i Azure Security Center.

I videoklippet [How to Leverage the Azure Security Center & Microsoft Operations Management Suite for an Incident Response](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) (Använda Azure Security Center och Microsoft Operations Management Suite för incidenthantering) kan du se några demonstrationer som beskriver hur Security Center kan användas i var och en av dessa faser.

> [!NOTE]
> Läs [Använda Azure Security Center vid incidenthantering](security-center-incident-response.md) om du vill ha mer information om hur du ska använda Security Center-funktionerna för att få hjälp under incidenthanteringsprocessen. 
> 
> 

## Se även
I det här dokumentet har du lärt dig hur du planerar integreringen av Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar Azure-resursernas hälsa.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

<!--HONumber=Sep16_HO4-->


