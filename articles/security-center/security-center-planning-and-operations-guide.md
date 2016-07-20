<properties
   pageTitle="Planering- och bruksanvisning för Security Center | Microsoft Azure"
   description="I det här avsnittet får du hjälp med att planera inför införandet av Azure Security Center och med vad du bör tänka på i det dagliga arbetet."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/25/2016"
   ms.author="yurid"/>
 
# Planering- och bruksanvisning för Azure Security Center
De här anvisningarna riktar sig till de som arbetar med IT, IT-arkitekter, informationssäkerhetsanalytiker och molnadministratörer i organisationer där man planerar att börja använda Azure Security Center.

> [AZURE.NOTE] Informationen i det här dokumentet rör förhandsversionen av Azure Security Center.

## Översikt över Azure Security Center
Med hjälp av Azure Security Center kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar. 

På vår sida med [vanliga frågor och svar om Azure Security Center](security-center-faq.md) finns en lista med ofta ställda frågor som kan vara bra att läsa i planerings- och utformningsfasen.

## Planeringsanvisningar
Nedan följer ett antal anvisningar för hur du optimerar användningen av Azure Security Center utifrån din organisations säkerhetskrav och molnhanteringsmodell. För att få ut mesta möjliga av Azure Security Center är det viktigt att veta hur olika medarbetare och avdelningar i organisationen kommer att använda tjänsten för att kunna uppfylla kraven på säkerhet i utvecklingsarbetet, driften, övervakningen, styrningen och incidenthanteringen. Du bör tänka på följande när du planerar inför införandet av Azure Security Center:

- Säkerhetsroller och åtkomstkontroll
- Säkerhetsprinciper och säkerhetsrekommendationer
- Datainsamling och lagring
- Fortlöpande säkerhetsövervakning 
- Incidenthantering

Nedan berättar vi hur du planerar för vart och ett av ovanstående områden och tillämpar rekommendationerna enligt aktuella behov. 

## Säkerhetsroller och åtkomstkontroll 

Beroende på hur stor din organisation är och hur den är uppbyggd kan olika medarbetare och avdelningar använda Security Center för att utföra olika säkerhetsrelaterade arbetsuppgifter. Nedan hittar du exempel på fiktiva personer och deras olika roller och ansvarsområden:

![Roller](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01.png)

De här olika medarbetarna kan ha de här olika ansvarsområdena i Azure Security Center. Exempel:

**Jeff (molnansvarig)**

- kan se och utföra Security Center-rekommendationerna i Azure Portal 
- kan även ha ett ärendesystem för att spåra ändringar (rekommendationer kommer upp via API)

**Rex (informationssäkerhetschef)**

- kan se Security Center-rapporter i Power BI eller Excel

**David (IT-säkerhetsansvarig)**

- bestämmer säkerhetsprinciper och ser säkerhetshälsa i Azure Portal
- analyserar data och tar fram rapporter i Power BI 

**Sam (säkerhetsmedarbetare)**

- ser och prioriterar Security Center-aviseringar i Azure Portal 
- kan använda en befintlig instrumentpanel (aviseringar kommer upp via API)

**Sherlock (säkerhetsanalytiker)**

- ser Security Center-aviseringar i Azure Portal 
- kan använda en befintlig instrumentpanel (aviseringar kommer upp via API)
- analyserar aviseringstrender i Power BI 
- går igenom händelseloggarna från lagringsutrymmet

I Azure Security Center tillämpas [rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-configure.md), vilket innebär att det finns [förinställda roller](../active-directory/role-based-access-built-in-roles.md) som kan ges till användare, grupper och tjänster i Azure. När användarna öppnar Azure Security Center ser de bara information om resurser som de har åtkomst till. De har åtkomst till resurserna i prenumerationer eller resursgrupper där de har någon av rollerna ägare, deltagare eller läsare. Personerna ovan bör ha följande roller:

**Jeff (molnansvarig)**

- ägare/deltagare i resursgrupp

**David (IT-säkerhetsansvarig)**

- ägare/deltagare i prenumeration

**Sam (säkerhetsmedarbetare)**

- läsare i prenumeration för att kunna se aviseringar
- ägare/deltagare i prenumeration för att kunna avvisa aviseringar

**Sherlock (säkerhetsanalytiker)**

- läsare i prenumeration för att kunna se aviseringar
- ägare/deltagare i prenumeration för att kunna åtgärda och avvisa aviseringar
- kan behöva åtkomst till lagringsutrymmet

Tänk även på följande:

- Endast ägare och deltagare i prenumerationer kan ändra säkerhetsprinciper.
- Endast ägare och deltagare i prenumerationer och resursgrupper kan tillämpa säkerhetsrekommendationer på en resurs.

När du planerar åtkomstkontrollen med rollbaserad åtkomstkontroll i Azure Security Center bör du alltså veta vilka i organisationen som kommer att använda Azure Security Center och vilken typ av arbetsuppgifter de kommer att utföra. Sedan ställer du in den rollbaserade åtkomstkontrollen därefter. 

> [AZURE.NOTE] Vi rekommenderar att du ger användarna den roll som precis ger dem den behörighet de behöver för att kunna utföra sina arbetsuppgifter. De användare som till exempel endast behöver se information om säkerhetsstatusen på resurser men inte vidta några åtgärder, som att tillämpa rekommendationer eller ändra principer, bör få rollen som läsare.

## Säkerhetsprinciper och säkerhetsrekommendationer
En säkerhetsprincip är ett antal kontrollfunktioner som rekommenderas för resurser inom en viss prenumeration eller resursgrupp. I Azure Security Center sätter du upp principer utifrån företagets säkerhetskrav och typ av program eller datakänslighetsnivå. 

Principer som ställs in på prenumerationsnivå gäller automatiskt för alla resursgrupper i den prenumerationen så som visas i bilden nedan: 

![Säkerhetsprinciper](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig2.png) 

Som du ser ovan kan säkerhetsprinciperna för resursgrupper ärvas från prenumerationsnivån. 

Om du har resurser i en resursgrupp som det behövs andra principer för kan du stänga av arvfunktionen och tillämpa särskilda principer för just den specifika resursgruppen. 

Om du vill tillämpa särskilda principer i en viss resursgrupp inaktiverar du arvfunktionen i resursgruppen och ändrar säkerhetsprinciperna. Om du till exempel har ett antal arbetsbelastningar där principen SQL transparent datakryptering inte behövs inaktiverar du principen på prenumerationsnivå och aktiverar den bara i de resursgrupper där denna datakrypteringsprincip behövs.
 
När du börjar skapa särskilda principer för olika resursgrupper bör du ha i åtanke att om två principer motsäger varandra (på prenumerationsnivå och resursgruppsnivå) är det principen för resursgruppen som tillämpas. Du bör planera principtillämpningen därefter. 

> [AZURE.NOTE] Om du vill se vilka principer som har ändrats kan du göra det i [Azure-granskningsloggarna](https://blogs.msdn.microsoft.com/cloud_solution_architect/2015/03/10/audit-logs-for-azure-events/). Alla principändringar loggas alltid i granskningsloggarna i Azure.

### Säkerhetsrekommendationer

Innan du börjar ställa in säkerhetsprinciper bör du gå igenom de olika [säkerhetsrekommendationerna](security-center-recommendations.md) och bestämma vilka som är lämpliga för vilka prenumerationer och resursgrupper. Det är även viktigt att veta vilka åtgärder som kommer att vidtas enligt säkerhetsrekommendationerna.

**Endpoint Protection (Slutpunktsskydd)**: Om det inte finns något slutpunktsskydd aktiverat på en virtuell dator rekommenderar Azure Security Center att sådant skydd installeras. Om ni redan har en slutpunktsskyddslösning lokalt som ni använder måste du avgöra om samma program mot skadlig kod ska användas för de virtuella datorerna i Azure. I Azure Security Center finns flera alternativ för slutpunktsskydd.  Du kan använda det kostnadsfria programmet Microsoft Antimalware eller välja från en lista med slutpunktsskyddslösningar från integrerade partnerleverantörer. Mer information om hur du distribuerar program mot skadlig kod i Azure Security Center finns i dokumentet [Aktivera program mot skadlig kod i Azure Security Center](security-center-enable-antimalware.md).

**System Updates (Systemuppdateringar)**: Kontroll görs om det finns virtuella datorer som saknar säkerhetsuppdateringar eller viktiga operativsystemuppdateringar. Fundera över vem som ska vara ansvarig för att installera uppdateringarna när det behövs och hur de ska installeras. I många organisationer används WSUS, Windows Update eller något annat verktyg.

**Baseline Configurations (Baslinjekonfigurationer)**: Om operativsystemkonfigurationen på virtuella datorer inte stämmer överens med rekommenderade baslinjer visas en rekommendation. Du bör titta igenom baslinjerna [här](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) och fundera över hur operativsystemkonfigurationerna ska tillämpas. 

**Disk Encryption (Diskkryptering)**: Om det finns diskar på virtuella datorer som inte är krypterade går en rekommendation ut om att tillämpa Azure Disk Encryption, där BitLocker används i Windows, och DM-Crypt i Linux, för volymkryptering av operativsystemet och datadiskarna. Genom rekommendationen omdirigeras du till [anvisningar](security-center-disk-encryption.md) om hur den här krypteringen ska genomföras. 

Observera att det finns olika krypteringssituationer som du måste hantera. Nedanstående olika situationer innebär olika krav som du måste planera efter:

- kryptering av nya virtuella datorer i Azure som kommer från virtuella hårddiskar som är krypterade med era egna krypteringsnycklar
- kryptering av nya virtuella datorer i Azure som skapats i Azures galleri
- kryptering av virtuella datorer i Azure som redan körs i Azure

Olika planering måste göras för var och en av de olika situationerna. Utförligare information om de här situationerna finns i [informationsdokumentet om Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).

**Web Application Firewall (Brandvägg för webbaserade program)**: Kontroll görs efter virtuella datorer där webbaserade program körs och det går ut en rekommendation om att en brandvägg för webbaserade program bör installeras. Gå igenom utbudet av partnerlösningar och avgör vilken som är bäst för din organisation och bestäm hur lösningen ska licensieras (partnerleverantörerna kan erbjuda bring your own license och/eller användningsbaserad betalning). Mer information om hur du distribuerar brandväggar för webbaserade program på virtuella datorer i Azure med Azure Security Center finns i artikeln om att [lägga till en brandvägg för webbaserade program i Azure Security Center](security-center-add-web-application-firewall.md).

**Virtual Networking (Virtuella nätverk)**: Infrastrukturen och konfigurationen i de [virtuella nätverken i Azure](https://azure.microsoft.com/documentation/services/virtual-network/) utvärderas och det kontrolleras att en [nätverkssäkerhetsgrupp](../virtual-network/virtual-networks-nsg.md) tillämpas och att den är rätt konfigurerad med regler för inkommande trafik. Du bör fundera över vilka trafikregler som ska ställas in och meddela beslutet till de som ska tillämpa de aktuella säkerhetsrekommendationerna.

## Datainsamling och lagring

Vi rekommenderar starkt att du aktiverar datainsamling för alla prenumerationer, eftersom du då kan vara säker på att alla virtuella datorer är säkerhetsövervakade. Datainsamling aktiveras via övervakningsagenten i Azure (ASMAgentLauncher.exe) och Azures säkerhetsövervakningstillägg (ASMMonitoringAgent.exe). 

Med Azures säkerhetsövervakningstillägg söks olika säkerhetsrelevanta konfigurationer igenom och säkerhetsloggar från virtuella datorer samlas in. Insamlade data skickas till det lagringskonto som du anger. Även genomsökningshanteringsprogrammet (ASMSoftwareScanner.exe) installeras på den virtuella datorn och används sedan för kontroll av korrigeringar.

När datainsamlingsfunktionen är aktiverad i säkerhetsprincipen installeras övervakningsagenten och tilläggen automatiskt på alla befintliga och nya kompatibla virtuella datorer som skapas i  Azure.  Agenten har ingen effekt på de virtuella datorernas prestanda.
 
Om du vid ett senare tillfälle vill inaktivera datainsamlingen kan du göra det i säkerhetsprincipen. Om du vill ta bort övervakningsagenterna som distribuerats väljer du menyalternativet Ta bort agenter.

> [AZURE.NOTE] Om du vill veta vilka virtuella datorer som stöds finns en lista bland våra [vanliga frågor och svar om Azure Security Center](security-center-faq.md).

För var och en av de regioner där det finns virtuella datorer väljer du på vilket lagringskonto data som samlas in från de datorerna ska lagras. Om du inte väljer något lagringskonto för en region skapas ett åt dig. Du kan välja att ha olika lagringsutrymmen för olika regioner eller lagra all information centralt på en plats. Säkerhetsprinciper kan ställas in på prenumerations- och resursgruppsnivå, men regionen för lagringskontot kan bara ställas in på prenumerationsnivå.

Om du har ett och samma lagringskonto för olika Azure-resurser bör du läsa artikeln om [skalbarhets- och prestandamål för Azure Storage](../storage/storage-scalability-targets.md) för mer information om storleksgränser och begränsningar. Det finns även lagringskontogränser i prenumerationer. I artikeln om [gränser, kvoter och begränsningar i prenumerationer och tjänster i Azure](../azure-subscription-service-limits) finns mer information om begränsningar.

> [AZURE.NOTE] Kostnader för det här lagringsutrymmet ingår inte i priset för Azure Security Center-tjänsten och debiteras separat enligt [Azures vanliga lagringspriser](https://azure.microsoft.com/pricing/details/storage/). 

Du bör även fundera på olika prestanda- och skalbarhetskrav utifrån hur mycket resurser du har i Azure och vilka resurser som tar upp mycket utrymme på lagringskontot. Se [checklistan för prestanda och skalbarhet i Microsoft Azure Storage](../storage/storage-performance-checklist.md) för mer information.

## Fortlöpande säkerhetsövervakning

När rekommendationerna i Azure Security Center har ställts in och tillämpats är det dags att fundera över driftrutinerna i Azure Security Center. 

Du kan gå till Azure Security Center från Azure Portal genom att klicka på **Browse (Bläddra)** och skriva in **Security Center** i **filterfältet**. Vad användarna ser beror på de här tillämpade filtren.

Azure Security Center påverkar inte era vanliga driftrutiner. Alla distributioner övervakas passivt och rekommendationer går ut utifrån de säkerhetsprinciper som du har aktiverat.

Instrumentpanelen i Azure Security Center är uppdelad i två huvuddelar: 

- Prevention (Skydd) 
- Detection (Identifiering) 

När du aktiverar datainsamling för din befintliga Azuremiljö i Azure Security Center första gången bör du läsa igenom alla rekommendationer. Det kan du göra på bladet **Recommendations (Rekommendationer)** eller per resurs (**virtuell dator**, **nätverk**, **SQL** och **program**). 

När du har genomfört alla rekommendationer bör **skyddsdelen** vara grön för alla resurser som du har åtgärdat. Den fortlöpande övervakningen blir enklare efter det här eftersom du hädanefter bara behöver vidta åtgärder om det sker förändringar i resursernas säkerhetshälsa och i rekommendationsrutorna.

**Identifieringsdelen** är mer reaktiv. Här visas aviseringar om problem som antingen just uppstått eller som uppstått tidigare men som just upptäckts genom kontrollerna i Azure Security Center och i tredjepartssystem. I rutan med säkerhetsaviseringar visas diagram med antalet hotidentifieringsaviseringar som uppkommit per dag uppdelat efter olika allvarlighetsgrader (låg, medelhög och hög). Mer information om säkerhetsaviseringar finns i [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md). 

> [AZURE.NOTE] Du kan även använda Microsoft Power BI för att få en tydligare bild av Azure Security Center-data. Läs [Kunskap genom statistik från Azure Security Center med Power BI](security-center-powerbi.md).

### Övervakning av nya och ändrade resurser

De flesta miljöer i Azure är dynamiska, med nya resurser som skapas och försvinner, nya konfigurationer och ändringar och så vidare. Med Azure Security Center har du bra insyn i säkerhetsläget för de här nya resurserna.

När du lägger till nya resurser (virtuella datorer, SQL-databaser) i miljön i Azure upptäcks de här resurserna i Azure Security Center och de börjar övervakas direkt. Om datainsamling har aktiverats i säkerhetsprincipen aktiveras ytterligare övervakningsfunktioner för alla virtuella datorer automatiskt. 

![Huvuddelar](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3.png) 

1.  För virtuella datorer går du till rutan **Resource security health (Resurssäkerhetshälsa)** och klickar på **Virtual Machines (Virtuella datorer)**. Eventuella problem med att aktivera datainsamling eller tillhörande rekommendationer visas under rubriken **Monitoring Recommendations (Övervakningsrekommendationer)**.
2.  Under **Recommendations (Rekommendationer)** kan du se om några säkerhetsrisker har upptäckts i den nya resursen.
3.  När nya virtuella datorer läggs till i miljön brukar det först bara vara operativsystemet som är installerat. Resursägaren kan behöva lite tid för att distribuera andra program som ska användas på de här datorerna.  Helst bör du veta vilka planerna för arbetsbelastningen är. Ska det vara en programserver? Beroende på vad den nya arbetsbelastningen ska bli kan du aktivera en **säkerhetsprincip** som passar, vilket är det tredje steget i det här arbetsflödet.
4.  När nya resurser läggs in i Azuremiljön kan det hända att nya aviseringar visas i rutan **Security Alerts (Säkerhetsaviseringar)**. Kontrollera alltid om det finns nya aviseringar i den här rutan och vidta åtgärder enligt rekommendationerna i Azure Security Center.

Du bör även regelbundet övervaka befintliga resurser för att se om det görs några konfigurationsändringar som ger upphov till säkerhetsrisker eller som inte följer rekommenderade baslinjer eller för att se om det kommer upp några säkerhetsaviseringar. Du börjar från instrumentpanelen i Azure Security Center. Där finns tre större delar som du bör kontrollera regelbundet.

![Åtgärder](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4.png) 

1.  I rutan **Resource security health (Resurssäkerhetshälsa)** kan du snabbt gå till dina viktigaste resurser. Härifrån kan du lätt övervaka virtuella datorer, nätverk, SQL och program. 
2.  I rutan **Recommendations (Rekommendationer)** ser du rekommendationerna från Azure Security Center. Under den fortlöpande övervakningen kommer det inte att finnas rekommendationer varje dag, vilket är normalt eftersom du utförde alla rekommendationer när du första konfigurerade Azure Security Center. Så det finns kanske inte ny information här varje dag och du behöver bara gå hit ibland.
3.  Hur ofta innehållet i **identifieringsrutan** ändras kan variera ganska mycket. Du bör alltid kontrollera säkerhetsaviseringarna och vidta åtgärder enligt rekommendationerna i Azure Security Center. 

## Incidenthantering

Med Azure Security Center upptäcks hot samtidigt som de uppkommer och du meddelas om det. Organisationen bör övervaka om det kommer nya säkerhetsaviseringar och vidta de åtgärder som behövs för att undersöka vidare eller stoppa angreppet.

> [AZURE.NOTE] Syftet med den här artikeln är inte att hjälpa dig att skapa en egen incidenthanteringsplan. National Institute of Standards and Technology (NIST) har en [handbok för hantering av datasäkerhetsrelaterade incidenter](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) som kan vara till hjälp när du vill skapa en egen plan.

I säkerhetsaviseringarna finns information som gör att du kan förstå vilken typ av angrepp det rör sig om och vad du kan göra för att åtgärda angreppet. I vissa aviseringar finns länkar antingen till mer information eller till andra informationskällor inom Azure. Du kan använda informationen som grund för ytterligare undersökningar och för att påbörja åtgärdsarbetet.

I exemplet nedan har misstänkt RDP-aktivitet upptäckts.

![Misstänkt aktivitet](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5.png)

På det här bladet kan du se när angreppet upptäcktes, varifrån det kommer och vilken virtuell dator som är drabbad, och här finns även rekommendationer för vad du bör göra. I vissa fall finns ingen information om varifrån angreppet kommer. [Här](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) finns mer information om de fall då uppgift om källa saknas i aviseringar i Azure Security Center.

| **Nivå**        | **Angreppsidentifiering**                     | **Föreslagen åtgärd**                                                                       |
|------------------|------------------------------------------|-----------------------------------------------------------------------------------------------|
| Nätverk          | Nyckelsökningsangrepp i RDP upptäckta                | Minska angreppsytan genom att ta bort öppna slutpunkter som inte behövs, konfigurera ACL:er och använda starka lösenord |
| Virtuella datorer | SVCHOST körs i fel katalog | Flytta den virtuella datorn till ett annat undernät (isolerat), genomsök den och återskapa den                        |
| Program      | SQL-inmatning i Azures databas          | Förstärk databaskonfigurationerna                                                                |



## Nästa steg
I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

- [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.
- [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
- [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.



<!--HONumber=Jun16_HO2-->


