---
title: Skydda dina datorer och program i Azure Security Center | Microsoft Docs
description: Det här dokumentet belyser rekommendationer i Security Center som hjälper dig att skydda dina virtuella datorer och datorer och webbappar och App Service-miljöer.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: v-mohabe
ms.openlocfilehash: c8f381a3440d742cca880f44b73cbc22bde92ecc
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910567"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Skydda dina datorer och program i Azure Security Center
Azure Security Center analyserar säkerhets status för dina Azure-resurser, icke-Azure-servrar och virtuella datorer. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som guidar dig genom processen med att konfigurera kontrollfunktioner som behövs. Rekommendationer gäller för Azure-resurstyper: virtuella datorer (VM) och datorer, program, nätverk, SQL, och identitet och åtkomst.

Den här artikeln belyser rekommendationer som gäller för datorer och program.

## <a name="monitoring-security-health"></a>Övervakning av säkerhetshälsa
Du kan övervaka dina resursers säkerhetstillstånd på den **Security Center – översikt** instrumentpanelen. Den **resurser** avsnittet innehåller antalet problem som har identifierats och säkerhetsstatus för varje resurstyp.

Du kan visa en lista över alla problem genom att välja **rekommendationer**. Mer information om hur du utför rekommendationerna finns i [utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

En fullständig lista över beräknings- och App services-rekommendationer finns i [rekommendationer](security-center-virtual-machine-protection.md#compute-and-app-recommendations).

Om du vill fortsätta, Välj **Compute och appar** under **resurser** eller huvudmenyn i Security Center.
![Instrumentpanelen för Security Center](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Övervaka Compute-och app-tjänster
Under **compute &-appar**finns följande flikar:

- **Översikt**: Övervakning och rekommendationer som identifierats av Security Center.
- **Virtuella datorer och datorer**: Listar alla dina virtuella datorer, datorer och det aktuella säkerhetstillståndet för var och en.
- **Molntjänster**: Listar alla dina webb- och arbetarroller som övervakas av Security Center.
- **App Services**: lista över dina App Service-miljöer och aktuella säkerhets tillstånd.
- **Behållare (för hands version)** : lista över dina behållare som finns på IaaS Linux-datorer och säkerhets utvärdering av sina Docker-konfigurationer.
- **Beräknings resurser (förhands granskning)** : lista över rekommendationer för dina beräknings resurser, till exempel Service Fabric kluster och händelse nav.

Fortsätt genom att välja **compute &-appar** under **resurs säkerhets hygien**.

![Compute](./media/security-center-virtual-machine-recommendations/compute.png)

På varje flik kan det finnas olika alternativ, och i de olika avsnitten kan du välja ett individuellt alternativ och visa mer information om de åtgärder som rekommenderas för att åtgärda problemet.

### Oövervakade virtuella datorer och datorer <a name="unmonitored-vms-and-computers"></a>
En virtuell dator eller en dator övervakas av Security Center om datorn inte kör tillägget Microsoft Monitoring Agent. En dator kan ha en lokal agent som redan är installerad, till exempel OMS Direct-agenten eller System Center Operations Manager agenten. Datorer med dessa agenter identifieras som oövervakade eftersom dessa agenter inte stöds fullt ut i Security Center. För att kunna utnyttja Security Center fullt ut krävs tillägget Microsoft Monitoring Agent.

Du kan installera tillägget på den oövervakade virtuella datorn eller datorn förutom redan installerade lokal agent. Konfigurera båda agenterna på samma sätt och anslut dem till samma arbetsyta. Det gör att Security Center kan interagera med tillägget Microsoft Monitoring Agent och samla in data. I [Aktivera tillägget för virtuella datorer](../azure-monitor/learn/quick-collect-azurevm.md) finns anvisningar om hur du installerar tillägget Microsoft Monitoring Agent.

Om du vill veta mer om varför Security Center inte kan övervaka virtuella datorer och datorer som initierats för automatisk försörjning läser du i [Övervaka problem med hälsotillstånd](security-center-troubleshooting-guide.md#mon-agent).

### <a name="recommendations"></a>Rekommendationer
Det här avsnittet innehåller en uppsättning rekommendationer för varje virtuell dator och datorn, webb-och arbetsroller, Azure App Service Web Apps och Azure App Service Environment som övervakas via Security Center. Den första kolumnen visar rekommendationen. Den andra kolumnen visar det totala antalet resurser som påverkas av den här rekommendationen. Den tredje kolumnen visar problemets allvarlighets grad.

Varje rekommendation har en uppsättning åtgärder som du kan utföra när du har valt. Om du till exempel väljer **saknade system uppdateringar**, så visas antalet virtuella datorer och datorer som saknar uppdateringar och allvarlighets graden för den saknade uppdateringen.

**Tillämpa systemuppdateringar** innehåller en sammanfattning av viktiga uppdateringar i diagramformat, en för Windows och en för Linux. Den andra delen har en tabell med följande information:

- **NAMN**: Namnet på den saknade uppdateringen.
- **ANTAL Virtuella datorer & datorer**: Totalt antal virtuella datorer och datorer som saknar uppdateringen.
- **UPPDATERA ALLVARLIGHETS GRAD**: Beskriver allvarlighets graden för den specifika rekommendationen:

    - **Kritisk**: Det finns ett säkerhets problem med en meningsfull resurs (program, virtuell dator eller nätverks säkerhets grupp) och kräver åtgärd.
    - **Viktigt**: Icke-kritiska eller ytterligare steg krävs för att slutföra en process eller eliminera en sårbarhet.
    - **Måttlig**: Ett säkerhets problem bör åtgärdas men kräver ingen omedelbar åtgärd. (Rekommendationer med låg allvarlighetsgrad visas normalt inte, men du kan filtrera fram dem om du vill se dem.)


- **TILLSTÅND**: Rekommendationens aktuella tillstånd:

    - **Öppna**: Rekommendationen har ännu inte åtgärd ATS.
    - **Pågår**: Rekommendationen används för närvarande på dessa resurser och ingen åtgärd krävs av dig.
    - **Löst**: Rekommendationen har redan slutförts. (När problemet har lösts, inaktiveras posten).

Om du vill visa information om rekommendationen klickar du på namnet på den uppdatering som saknas.


> [!NOTE]
> Säkerhetsrekommendationerna här är samma som de under den **rekommendationer** panelen. Se [utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md) för mer information om hur du utför rekommendationerna.
>
>

### <a name="vms-and-computers"></a>Virtuella datorer och vanliga datorer
Avsnittet virtuella datorer och datorer får du en överblick över alla rekommendationer för virtuell dator och datorn. I varje kolumn finns en typ av rekommendationer.

![Rekommendationer för datorer och virtuella datorer](./media/security-center-virtual-machine-recommendations/vm-computers.png)

Det finns fyra typer av ikoner i den här listan:

![Datorer som inte är Azure-datorer](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Icke-Azure-dator.

![Azure Resource Manager-VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager-VM.

![Klassiska virtuella Azure-datorer](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure klassisk virtuell dator.


![Virtuella datorer identifieras från arbetsytan](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuella datorer som endast identifieras från arbetsytan som är en del av den visade prenumerationen. Detta inkluderar virtuella datorer från andra prenumerationer som rapporterar till arbets ytan i den här prenumerationen och virtuella datorer som har installerats med Operations Manager Direct agent och inte har något resurs-ID.

Genom ikonerna under de olika rekommendationerna hjälper dig att snabbt identifiera den virtuella datorn och datorn som behöver åtgärdas och typ av rekommendation. Du kan också använda filtren för att söka i listan efter **resurs typ** och efter **allvarlighets grad**.

Om du vill öka detalj nivån för säkerhets rekommendationerna för varje virtuell dator klickar du på den virtuella datorn.
![Molntjänster](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Molntjänster
För Cloud Services skapas en rekommendation när operativ systemets version är inaktuell.

![Molntjänster](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

I ett scenario där du har en rekommendation (vilket inte är fallet i föregående exempel), måste du följa anvisningarna i rekommendationen för att uppdatera versionen av operativsystemet. När en uppdatering är tillgänglig får du en avisering (röd eller orange beroende på hur allvarligt problemet är). När du väljer den här aviseringen i WebRole1 (kör Windows Server med din webbapp automatiskt distribuerad till IIS) eller WorkerRole1 (kör Windows Server med din webbapp automatiskt distribuerad till IIS) rader, visas mer information om den här rekommendationen.

Om du vill få en mer ingående förklaring av den här rekommendationen klickar du på **Uppdatera OS-version** under kolumnen **BESKRIVNING**.



![Uppdatera OS-versionen](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>App Services
Om du vill visa App Service information måste du aktivera App Service i din prenumeration. Anvisningar om hur du aktiverar den här funktionen finns i [skydda app service med Azure Security Center](security-center-app-services.md).
[!NOTE]
> Övervakning av App Service finns i förhandsversion och bara tillgängliga på standardnivån i Security Center.


Under **apptjänster**, du hittar en lista över App service-miljöer och hälsoöversikt baserat på utvärderingen Security Center utförs.

![App Services](./media/security-center-virtual-machine-recommendations/app-services.png)

Det finns tre typer av ikoner i den här listan:

![App services-miljö](./media/security-center-virtual-machine-recommendations/ase.png) App services-miljö.

![Webbprogram](./media/security-center-virtual-machine-recommendations/web-app.png) Webbprogram.

![Funktionsprogram](./media/security-center-virtual-machine-recommendations/function-app.png) Funktionsprogram.

1. Välj ett webbprogram. En sammanfattningsvy öppnas med tre flikar:

   - **Rekommendationer**: baserat på har utförts av Security Center som misslyckades.
   - **Skickas utvärderingar**: lista över har utförts av Security Center som skickas.
   - **Ej tillgängliga utvärderingar**: lista över utvärderingar som inte kunde köras på grund av ett fel eller rekommendationen gäller inte för den specifika App service

   Under **rekommendationer** är en lista över rekommendationer för det valda webbprogrammet och allvarlighetsgraden för varje rekommendation.

   ![App Services rekommendationer](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Välj en rekommendation om du vill se en beskrivning av rekommendationen och en lista över felaktiga resurser, felfria resurser och ej genomsökta resurser.

   - I kolumnen **skickade utvärderingar** finns en lista över godkända utvärderingar.  Allvarlighetsgraden för de här utvärderingar är alltid grönt.

   - Välj en skickade utvärdering från listan för en beskrivning av utvärderingen, en lista över feltillstånd och felfria resurser och en lista över ej genomsökta resurser. Det finns en flik för skadade resurser men listan är alltid tomt eftersom utvärderingen skickades.

     ![App Service reparation](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets
Security Center identifierar automatiskt om du har skalnings uppsättningar och rekommenderar att du installerar Microsoft Monitoring Agent på dessa skalnings uppsättningar. 

Installera Microsoft Monitoring Agent: 

1. Välj rekommendationen **Installera övervaknings agenten på den virtuella datorns skal uppsättning**. Du får en lista över oövervakade skalnings uppsättningar.
2. Välj en skalnings uppsättning som inte är felfri. Följ anvisningarna för att installera övervaknings agenten med hjälp av en befintlig ifylld arbets yta eller skapa en ny. Se till att ange [pris nivån](security-center-pricing.md) för arbets ytan om den inte är inställd.

   ![Installera MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Ange nya skalnings uppsättningar för att automatiskt installera Microsoft Monitoring Agent:
1. Gå till Azure Policy och klicka på **definitioner**.
2. Sök efter principen **distribuera Log Analytics agent för skalnings uppsättningar för virtuella Windows-datorer** och klicka på den.
3. Klicka på **Tilldela**.
4. Ange **området** och **Log Analytics arbets ytan** och klicka på **tilldela**.

Om du vill ange alla befintliga skalnings uppsättningar för att installera Microsoft Monitoring Agent går du till Azure Policy och går till **reparation** och tillämpar den befintliga principen i befintliga skalnings uppsättningar.


## <a name="compute-and-app-recommendations"></a>Rekommendationer för beräknings- och app
|Resurstyp|Säkerhetspoäng|Rekommendation|Beskrivning|
|----|----|----|----|
|Apptjänst|20|Webbprogram bör enbart vara åtkomliga via HTTPS|Begränsa åtkomst till webbprogram via HTTPS endast.|
|Apptjänst|20|Funktionen App bör enbart vara åtkomliga via HTTPS|Begränsa åtkomst till Funktionsappar via HTTPS endast.|
|Apptjänst|5|Diagnostikloggar i App Services ska vara aktive rad|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Apptjänst|10|Fjärrfelsökning bör stängas av för webbprogram|Inaktivera felsökning för webbprogram, om du inte längre behöver använda den. Fjärrfelsökning kräver att inkommande portar öppnas på en Funktionsapp.|
|Apptjänst|10|Fjärrfelsökning bör stängas av för Funktionsprogram|Inaktivera felsökning för Funktionsappen om du inte längre behöver använda den. Fjärrfelsökning kräver att inkommande portar öppnas på en Funktionsapp.|
|Apptjänst|10|Tillåt inte alla ('* ') resurser för att komma åt ditt program| Tillåt inte parametern website_load_certificates. den uppsättning ””. Ställa in parametern ”innebär att alla certifikat läses in till din web program personliga certifikatarkiv. Detta kan leda till missbruk av principen om lägsta behörighet eftersom det är osannolikt att webbplatsen behöver åtkomst till alla certifikat vid körning.|
|Apptjänst|20|CORS bör inte bevilja alla resurser att komma åt dina webbprogram|Tillåt endast nödvändiga domäner att interagera med ditt webbprogram. Mellan ursprung ska sharing (CORS) inte tillåta alla domäner får åtkomst till ditt webbprogram.|
|Apptjänst|20|CORS bör inte bevilja alla resurser att få åtkomst till din Funktionsapp| Tillåt endast nödvändiga domäner att interagera med ditt funktionsprogram. Mellan ursprung ska sharing (CORS) inte tillåta alla domäner åtkomst till ditt funktionsprogram.|
|Beräkningsresurser (batch)|1|Mått varnings regler ska konfigureras för batch-konton|Konfigurera måttaviseringsregler på Batch-konto och aktivera mätvärden Pool ta bort klar händelser och poolen ta bort starta händelser|
|Beräkningsresurser (service fabric)|10|Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering|Utför klientautentisering endast via Azure Active Directory i Service Fabric.|
|Beräkningsresurser (automation-konto)|5|Variabler för Automation-konton ska vara krypterade|Aktivera kryptering av Automation-konto variabler för tillgångar vid lagring av känsliga data.|
|Beräkningsresurser (Sök)|5|Granska aktivering av diagnostikloggar för Sök tjänster|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (service bus)|5|Diagnostikloggar i Service Bus ska vara aktive rad|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (stream analytics)|5|Diagnostikloggar i Azure Stream Analytics ska vara aktive rad|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (batch)|5|Aktivera diagnostikloggar i Batch-konton|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (händelsehubb)|5|Diagnostikloggar i Händelsehubben måste vara aktive rad|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (logikappar)|5|Aktivera diagnostikloggar i Logic Apps|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (service fabric)|15|Ange egenskapen ClusterProtectionLevel till EncryptAndSign i Service Fabric|Service Fabric tillhandahåller tre skyddsnivåer (ingen, logga in och EncryptAndSign) för nod-till-nod-kommunikation med hjälp av en primär klustercertifikat.  Ställa in skydd så att alla nod-till-nod-meddelanden är krypterad och har signerats digitalt. |
|Beräkningsresurser (service bus)|1|Ta bort alla regler förutom RootManageSharedAccessKey från Service Bus-namnområde |Service Bus-klienter ska inte använda en namnområdesprincip administratörsnivå som ger åtkomst till alla köer och ämnen i ett namnområde. Om du vill justera med minsta behörighets säkerhets modell bör du skapa åtkomst principer på enhets nivå för köer och ämnen för att ge åtkomst till endast den specifika entiteten.|
|Beräkningsresurser (händelsehubb)|1|Alla auktoriseringsregler utom RootManageSharedAccessKey ska tas bort från Event Hub-namnområdet|Event Hub-klienter ska inte använda en namnområdesprincip administratörsnivå som ger åtkomst till alla köer och ämnen i ett namnområde. Om du vill justera med minsta behörighets säkerhets modell bör du skapa åtkomst principer på enhets nivå för köer och ämnen för att ge åtkomst till endast den specifika entiteten.|
|Beräkningsresurser (händelsehubb)|5|Auktoriseringsregler i Event Hub-entiteten måste definieras|Granska auktoriseringsregler på entiteten Event Hub att bevilja lägsta åtkomstbehörighet.|
|Machine|50|Installera övervakningsagenten på dina datorer|Installera agenten för övervakning om du vill aktivera insamling av data, uppdateringar som genomsökning, baslinjegenomsökning och endpoint protection på varje dator.|
|Dator|50|Aktivera automatisk etablering och insamling av data för dina prenumerationer |Aktivera automatisk etablering och insamling av data för datorer i dina prenumerationer för att aktivera insamling av data, uppdateringar som genomsökning, baslinjegenomsökning och endpoint protection på varje dator som har lagts till i dina prenumerationer.|
|Dator|40|Åtgärda hälsoproblem för övervakningsagenten på dina datorer|För fullständig Security Center skydd Lös övervakningsproblem agenten på dina datorer genom att följa anvisningarna i guiden för felsökning| 
|Dator|40|Åtgärda hälsoproblem för slutpunktsskydd på dina datorer|Lös övervakningsproblem agenten på dina datorer genom att följa anvisningarna i guiden för felsökning för fullständig Security Center-skydd.|
|Dator|40|Felsök saknade skanningsdata på dina datorer|Felsöka genomsökningsdata som saknas på virtuella datorer och datorer. Skanningsdata saknas på dina datorer blir saknas säkerhetsutvärderingar som uppdatera genomsökning, baslinje genomsökning och saknas genomsökning för endpoint protection lösning.|
|Dator|40|System uppdateringar bör installeras på dina datorer|Installera saknas systemsäkerhet och viktiga uppdateringar för att skydda dina Windows- och Linux-datorer och datorer
|Machine|15|Lägg till en brandvägg för webbappar| Distribuera en lösning web application firewall (WAF) för att skydda dina webbprogram. |
|Machine|40|Uppdatera operativsystemsversionen för dina molntjänstroller|Uppdatera operativsystemets version för dina molntjänstroller till den senaste tillgängliga versionen för din OS-familj.|
|Dator|35|Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas|Åtgärda sårbarheter i Säkerhetskonfiguration på dina datorer för att skydda dem från attacker.|
|Dator|35|Åtgärda sårbarheter i Säkerhetskonfiguration på dina behållare|Åtgärda sårbarheter i säkerhetskonfigurationen på datorer med Docker installerat för att skydda dem mot angrepp.|
|Dator|25|Aktivera anpassningsbara programkontroller|Aktivera programmet kontroller som program kan köras på din virtuella dator i Azure. Detta hjälper att skydda dina virtuella datorer mot skadlig kod. Security Center använder maskininlärning att analysera programmen som körs på varje virtuell dator och hjälper till att du tillämpar tillåter regler med den här intelligensen. Den här funktionen förenklar processen med att konfigurera och underhålla program tillåter regler.|
|Dator|20|Installera endpoint protection-lösningen på dina datorer|Installera en endpoint protection-lösning på dina virtuella datorer, att skydda dem mot hot och sårbarheter.|
|Dator|20|Starta om dina datorer för att att tillämpa systemuppdateringarna|Starta om dina datorer för att tillämpa systemuppdateringarna och skydda datorn från säkerhetsrisker.|
|Machine|15|Disk kryptering bör tillämpas på virtuella datorer|Kryptera virtuella diskar med Azure Disk Encryption både för Windows och Linux-datorer. Azure Disk Encryption (ADE) använder branschens standard BitLocker-funktion i Windows och DM-crypt i Linux för att ange operativsystem- och diskkryptering för att skydda och skydda dina data och att uppfylla din organisations säkerhet och efterlevnad åtaganden i kundens Azure-nyckelvalv. När dina krav på efterlevnad och säkerhet måste du kryptera data från slutpunkt till slutpunkt med hjälp av dina krypteringsnycklar, inklusive kryptering av tillfälliga (lokalt anslutna tillfälliga) disk, Använd Azure-diskkryptering. Du kan också som standard är Managed Disks krypterade i vila som standard med hjälp av Azure Storage Service Encryption där krypteringsnycklarna är hanteras av Microsoft-nycklar i Azure. Om det uppfyller din efterlevnad och säkerhetskrav, kan du använda standard Managed diskkryptering för att uppfylla dina krav.|
|Machine|30|Installera en sårbarhetsbedömningslösning på dina virtuella datorer|Installera en sårbarhetsbedömningslösning på dina virtuella datorer|
|Dator|15|Lägg till en brandvägg för webbappar| Distribuera en lösning web application firewall (WAF) för att skydda dina webbprogram. |
|Dator|30|Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning|Virtuella datorer som distribueras en 3 part lösning för sårbarhetsbedömning som kontinuerligt utvärderas mot program- och OS-säkerhetsproblem. När sådana sårbarheter finns finns dessa mer information som en del av rekommendationen.|
|Machine|30|Installera en sårbarhetsbedömningslösning på dina virtuella datorer|Installera en sårbarhetsbedömningslösning på dina virtuella datorer|
|Machine|1|Virtuella datorer ska migreras till nya AzureRM-resurser|Använd Azure Resource Manager för dina virtuella datorer för att ge säkerhets förbättringar som: starkare åtkomst kontroll (RBAC), bättre granskning, Resource Manager-baserad distribution och styrning, åtkomst till hanterade identiteter, åtkomst till nyckel valv för hemligheter, Azure AD-baserad autentisering och stöd för taggar och resurs grupper för enklare säkerhets hantering. |
|Machine|30|Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning|Virtuella datorer som distribueras en 3 part lösning för sårbarhetsbedömning som kontinuerligt utvärderas mot program- och OS-säkerhetsproblem. När sådana sårbarheter finns finns dessa mer information som en del av rekommendationen.|
|Skaluppsättning för den virtuella datorn |4|Diagnostikloggar i Virtual Machine Scale Sets ska vara aktive rad|Aktivera loggar och behåll dem i upp till ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte. Detta är användbart när en säkerhets incident inträffar eller nätverket komprometteras.|
|Skaluppsättning för den virtuella datorn|35|Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas|Åtgärda sårbarheter i säkerhets konfiguration på dina virtuella datorers skalnings uppsättningar för att skydda dem mot angrepp. |
|Skaluppsättning för den virtuella datorn|5|Åtgärda problem med hälso tillstånd för Endpoint Protection på virtuella datorers skalnings uppsättningar|Åtgärda problem med slut punkts skydd på den virtuella datorns skalnings uppsättningar för att skydda dem mot hot och sårbarheter. |
|Skaluppsättning för den virtuella datorn|10|Endpoint Protection bör installeras på virtuella datorer|Installera en Endpoint Protection-lösning på den virtuella datorns skalnings uppsättningar för att skydda dem mot hot och sårbarheter. |
|Skaluppsättning för den virtuella datorn|40|System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras|Installera system säkerhet och viktiga uppdateringar som saknas för att skydda dina skalnings uppsättningar för virtuella Windows-och Linux-datorer. |
 





## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:


* [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
* [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.

