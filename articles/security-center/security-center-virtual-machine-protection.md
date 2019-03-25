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
ms.date: 3/20/2019
ms.author: monhaber
ms.openlocfilehash: fa664952f3eb7d6f9e611fb87a9e484e97f388a2
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403841"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Skydda dina datorer och program i Azure Security Center
Azure Security Center analyserar säkerhetstillståndet för dina Azure-resurser, icke-Azure-servrar och virtuella datorer. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som guidar dig genom processen med att konfigurera kontrollfunktioner som behövs. Rekommendationer gäller för Azure-resurstyper: virtuella datorer (VM) och datorer, program, nätverk, SQL, och identitet och åtkomst.

Den här artikeln belyser rekommendationer som gäller för datorer och program.

## <a name="monitoring-security-health"></a>Övervakning av säkerhetshälsa
Du kan övervaka dina resursers säkerhetstillstånd på den **Security Center – översikt** instrumentpanelen. Den **resurser** avsnittet innehåller antalet problem som har identifierats och säkerhetsstatus för varje resurstyp.

Du kan visa en lista över alla problem genom att välja **rekommendationer**. Mer information om hur du utför rekommendationerna finns i [utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

En fullständig lista över beräknings- och App services-rekommendationer finns i [rekommendationer](security-center-virtual-machine-recommendations.md).

Om du vill fortsätta, Välj **Compute och appar** under **resurser** eller huvudmenyn i Security Center.
![Instrumentpanelen för Security Center](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Övervaka beräknings- och app services
Under **Compute och appar**, finns följande flikar:

- **Översikt**: Övervakning och rekommendationer som identifierats av Security Center.
- **Virtuella datorer och datorer**: Listar alla dina virtuella datorer, datorer och det aktuella säkerhetstillståndet för var och en.
- **Molntjänster**: Listar alla dina webb- och arbetarroller som övervakas av Security Center.
- **Apptjänster**: lista över dina App service-miljöer och aktuella säkerhetstillståndet för var och en.
- **Behållare (förhandsgranskning)**: lista över dina behållare på IaaS Linux-datorer och säkerhetsbedömning för sina Docker-konfigurationer.
- **Beräkningsresurser (förhandsversion)**: lista över rekommendationer för dina beräkningsresurser, till exempel Service Fabric-kluster och Event hubs.

Om du vill fortsätta, Välj **Compute och appar** under **Resource security hygien**.

![Compute](./media/security-center-virtual-machine-recommendations/compute.png)

På varje flik kan det finnas olika alternativ, och i de olika avsnitten kan du välja ett individuellt alternativ och visa mer information om de åtgärder som rekommenderas för att åtgärda problemet.

### Oövervakade virtuella datorer och datorer <a name="unmonitored-vms-and-computers"></a>
En virtuell dator eller en dator övervakas av Security Center om datorn inte kör tillägget Microsoft Monitoring Agent. En dator kan ha en lokal agent installerad, exempelvis OMS direktagent eller System Center Operations Manager-agenten. Datorer med dessa agenter identifieras som oövervakade eftersom dessa agenter inte stöds fullt ut i Security Center. För att kunna utnyttja Security Center fullt ut krävs tillägget Microsoft Monitoring Agent.

Du kan installera tillägget på den oövervakade virtuella datorn eller datorn förutom redan installerade lokal agent. Konfigurera båda agenterna på samma sätt och anslut dem till samma arbetsyta. Det gör att Security Center kan interagera med tillägget Microsoft Monitoring Agent och samla in data. I [Aktivera tillägget för virtuella datorer](../azure-monitor/learn/quick-collect-azurevm.md) finns anvisningar om hur du installerar tillägget Microsoft Monitoring Agent.

Om du vill veta mer om varför Security Center inte kan övervaka virtuella datorer och datorer som initierats för automatisk försörjning läser du i [Övervaka problem med hälsotillstånd](security-center-troubleshooting-guide.md#mon-agent).

### <a name="recommendations"></a>Rekommendationer
Det här avsnittet innehåller en uppsättning rekommendationer för varje virtuell dator och datorn, webb-och arbetsroller, Azure App Service Web Apps och Azure App Service Environment som övervakas via Security Center. Den första kolumnen visar rekommendationen. Den andra kolumnen visar det totala antalet resurser som påverkas av den här rekommendationen. Den tredje kolumnen visar hur allvarligt problemet.

Varje rekommendation har en uppsättning åtgärder som du kan utföra när du har valt. Exempel: Om du väljer **systemuppdateringar**, hur många virtuella datorer och datorer som saknar korrigeringsfiler och allvarlighetsgraden för saknad uppdatering visas.

**Tillämpa systemuppdateringar** innehåller en sammanfattning av viktiga uppdateringar i diagramformat, en för Windows och en för Linux. Den andra delen har en tabell med följande information:

- **NAMN**: Namnet på uppdateringen som saknas.
- **ANTAL VM: ar och datorer**: Totalt antal virtuella datorer och datorer som saknar denna uppdatering.
- **UPPDATERA ALLVARLIGHETSGRAD**: Visas hur viktig rekommendationen:

    - **Kritiska**: En sårbarhet i finns en viktig resurs (program, virtuell dator eller nätverkssäkerhetsgrupp) som måste åtgärdas.
    - **Viktiga**: Icke-kritiska eller ytterligare åtgärder krävs för att slutföra en process eller åtgärda en säkerhetsrisk.
    - **Måttlig**: Ett problem bör åtgärdas, men kräver inte omedelbar åtgärd. (Rekommendationer med låg allvarlighetsgrad visas normalt inte, men du kan filtrera fram dem om du vill se dem.)


- **TILLSTÅND**: Det aktuella tillståndet för rekommendationen:

    - **Öppna**: Rekommendationen har inte utförts än.
    - **Pågår**: Rekommendationen håller på att utföras och ingen åtgärd krävs av dig.
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


![Virtuella datorer identifieras från arbetsytan](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuella datorer som endast identifieras från arbetsytan som är en del av den visade prenumerationen. Detta inkluderar virtuella datorer från andra prenumerationer som rapporterar till arbetsytan i den här prenumerationen och virtuella datorer som installerades med Operations Manager-direktagent och har inga resurs-ID.

Genom ikonerna under de olika rekommendationerna hjälper dig att snabbt identifiera den virtuella datorn och datorn som behöver åtgärdas och typ av rekommendation. Du kan också använda filter för att söka i listan efter **resurstyp** och av **allvarlighetsgrad**.

Om du vill granska nedåt i säkerhetsrekommendationer för varje virtuell dator, klicka på den virtuella datorn.
Här kan du visa säkerhetsinformation för den virtuella datorn eller datorn. Längst ned ser du den rekommenderade åtgärden och allvarlighetsgraden på de olika problemen.
![Molntjänster](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Molntjänster
För molntjänster skapas en rekommendation när operativsystemets version är inaktuell.

![Molntjänster](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

I ett scenario där du har en rekommendation (vilket inte är fallet i föregående exempel), måste du följa anvisningarna i rekommendationen för att uppdatera versionen av operativsystemet. När en uppdatering är tillgänglig får du en avisering (röd eller orange beroende på hur allvarligt problemet är). När du väljer den här aviseringen på raden webrole1 (kör Windows Server med din webbapp automatiskt distribuerad till IIS) eller WorkerRole1 (kör Windows Server med din webbapp automatiskt distribuerad till IIS) rader, visas mer information om den här rekommendationen.

Om du vill få en mer ingående förklaring av den här rekommendationen klickar du på **Uppdatera OS-version** under kolumnen **BESKRIVNING**.



![Uppdatera OS-versionen](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>App Services
Du måste aktivera App Service i din prenumeration för att visa information för App Service. Anvisningar om hur du aktiverar den här funktionen finns i [skydda App Service med Azure Security Center](security-center-app-services.md).
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

   ![Rekommendationer för App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Välj en rekommendation att se en beskrivning av rekommendationen och en lista över skadade resurser, felfria resurser och ej genomsökta resurser.

   - Under den **skickas utvärderingar** kolumnen är en lista med skickade utvärderingar.  Allvarlighetsgraden för de här utvärderingar är alltid grönt.

   - Välj en skickade utvärdering från listan för en beskrivning av utvärderingen, en lista över feltillstånd och felfria resurser och en lista över ej genomsökta resurser. Det finns en flik för skadade resurser men listan är alltid tomt eftersom utvärderingen skickades.

     ![App Service-reparation](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Skalningsuppsättningar för virtuella datorer
Security Center identifierar automatiskt om du har skalor uppsättningar och rekommenderar att du installerar Microsoft Monitoring Agent på de här skalningsuppsättningar. 

Installera Microsoft Monitoring Agent: 

1. Välj rekommendationen **installera övervakningsagenten på virtual machine scale Sets**. Du får en lista över oövervakade skalningsuppsättningar.
2. Välj en defekt skalningsuppsättning. Följ anvisningarna för att installera övervakningsagenten med hjälp av en befintlig fylls i automatiskt arbetsyta eller skapa en ny. Se till att ange arbetsytan [prisnivån](security-center-pricing.md) om den inte har angetts.

   ![Installera MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Om du vill ange skalningsuppsättningar nya för att automatiskt installera Microsoft Monitoring Agent:
1. Gå till Azure Policy och klicka **definitioner**.
2. Sök efter principen **distribuerar Log Analytics-agenten för Windows VM scale sets** och klicka på den.
3. Klicka på **Tilldela**.
4. Ange den **omfång** och **Log Analytics-arbetsyta** och klicka på **tilldela**.

Om du vill ange alla befintliga skalningsuppsättningar för att installera Microsoft Monitoring Agent i Azure Policy, går du till **reparation** och tillämpa den befintliga principen på befintliga skalningsuppsättningar.


## <a name="compute-and-app-recommendations"></a>Rekommendationer för beräknings- och app
|Resurstyp|Säkerhetspoäng|Rekommendation|Beskrivning|
|----|----|----|----|
|Apptjänst|20|Webbprogram bör enbart vara åtkomliga via HTTPS|Begränsa åtkomst till webbprogram via HTTPS endast.|
|Apptjänst|20|Funktionen App bör enbart vara åtkomliga via HTTPS|Begränsa åtkomst till Funktionsappar via HTTPS endast.|
|Apptjänst|5|Aktivera diagnostikloggar i App service|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Apptjänst|10|Fjärrfelsökning bör stängas av för webbprogram|Inaktivera felsökning för webbprogram, om du inte längre behöver använda den. Fjärrfelsökning kräver att inkommande portar öppnas på en Funktionsapp.|
|Apptjänst|10|Fjärrfelsökning bör stängas av för Funktionsprogram|Inaktivera felsökning för Funktionsappen om du inte längre behöver använda den. Fjärrfelsökning kräver att inkommande portar öppnas på en Funktionsapp.|
|Apptjänst|10|Konfigurera IP-begränsningar för webbprogram|Definiera en lista över IP-adresser som ska kunna komma åt ditt program. Användning av IP-restriktioner skyddar du webbappar mot vanliga attacker.|
|Apptjänst|10|Tillåt inte alla ('* ') resurser för att komma åt ditt program| Tillåt inte parametern website_load_certificates. den uppsättning ””. Ställa in parametern ”innebär att alla certifikat läses in till din web program personliga certifikatarkiv. Detta kan leda till missbruk av principen om lägsta behörighet eftersom det är osannolikt att webbplatsen behöver åtkomst till alla certifikat vid körning.|
|Apptjänst|20|CORS bör inte bevilja alla resurser att komma åt dina webbprogram|Tillåt endast nödvändiga domäner att interagera med ditt webbprogram. Mellan ursprung ska sharing (CORS) inte tillåta alla domäner får åtkomst till ditt webbprogram.|
|Apptjänst|20|CORS bör inte bevilja alla resurser att få åtkomst till din Funktionsapp| Tillåt endast nödvändiga domäner att interagera med ditt funktionsprogram. Mellan ursprung ska sharing (CORS) inte tillåta alla domäner åtkomst till ditt funktionsprogram.|
|Beräkningsresurser (batch)|1|Konfigurera måttaviseringsregler på Batch-konto|Konfigurera måttaviseringsregler på Batch-konto och aktivera mätvärden Pool ta bort klar händelser och poolen ta bort starta händelser|
|Beräkningsresurser (service fabric)|10|Använd Azure Active Directory för klientautentisering i Service Fabric|Utför klientautentisering endast via Azure Active Directory i Service Fabric.|
|Beräkningsresurser (automation-konto)|5| Aktivera kryptering av Automation-konto|Aktivera kryptering av Automation-konto variabler för tillgångar vid lagring av känsliga data.|
|Beräkningsresurser (belastningsutjämnare)|5|Aktivera diagnostikloggar i belastningsutjämnaren|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (Sök)|5|Aktivera diagnostikloggar i söktjänst|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (service bus)|5|Aktivera diagnostikloggar i Service Bus|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (stream analytics)|5|Aktivera diagnostikloggar i Azure Stream Analytics|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (service fabric)|5|Aktivera diagnostikloggar i Service Fabric|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (batch)|5|Aktivera diagnostikloggar i Batch-konton|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (händelsehubb)|5|Aktivera diagnostikloggar i Event Hub|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (logikappar)|5|Aktivera diagnostikloggar i Logic Apps|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (service fabric)|15|Ange egenskapen ClusterProtectionLevel till EncryptAndSign i Service Fabric|Service Fabric tillhandahåller tre skyddsnivåer (ingen, logga in och EncryptAndSign) för nod-till-nod-kommunikation med hjälp av en primär klustercertifikat.  Ställa in skydd så att alla nod-till-nod-meddelanden är krypterad och har signerats digitalt. |
|Beräkningsresurser (service bus)|1|Ta bort alla regler förutom RootManageSharedAccessKey från Service Bus-namnområde |Service Bus-klienter ska inte använda en namnområdesprincip administratörsnivå som ger åtkomst till alla köer och ämnen i ett namnområde. För att anpassas till det lägsta privilegiet säkerhetsmodell, bör du skapa principer för åtkomst på enhetsnivå för köer och ämnen för att ge åtkomst till endast den specifika enheten.|
|Beräkningsresurser (händelsehubb)|1|Ta bort alla regler förutom RootManageSharedAccessKey från namnområdet för Händelsehubben |Event Hub-klienter ska inte använda en namnområdesprincip administratörsnivå som ger åtkomst till alla köer och ämnen i ett namnområde. För att anpassas till det lägsta privilegiet säkerhetsmodell, bör du skapa principer för åtkomst på enhetsnivå för köer och ämnen för att ge åtkomst till endast den specifika enheten.|
|Beräkningsresurser (händelsehubb)|5|Definiera regler för Event Hub-entitet|Granska auktoriseringsregler på entiteten Event Hub att bevilja lägsta åtkomstbehörighet.|
|Dator|50|Installera övervakningsagenten på dina datorer|Installera agenten för övervakning om du vill aktivera insamling av data, uppdateringar som genomsökning, baslinjegenomsökning och endpoint protection på varje dator.|
|Dator|50|Aktivera automatisk etablering och insamling av data för dina prenumerationer |Aktivera automatisk etablering och insamling av data för datorer i dina prenumerationer för att aktivera insamling av data, uppdateringar som genomsökning, baslinjegenomsökning och endpoint protection på varje dator som har lagts till i dina prenumerationer.|
|Dator|40|Åtgärda hälsoproblem för övervakningsagenten på dina datorer|För fullständig Security Center skydd Lös övervakningsproblem agenten på dina datorer genom att följa anvisningarna i guiden för felsökning| 
|Dator|40|Åtgärda hälsoproblem för slutpunktsskydd på dina datorer|Lös övervakningsproblem agenten på dina datorer genom att följa anvisningarna i guiden för felsökning för fullständig Security Center-skydd.|
|Dator|40|Felsök saknade skanningsdata på dina datorer|Felsöka genomsökningsdata som saknas på virtuella datorer och datorer. Skanningsdata saknas på dina datorer blir saknas säkerhetsutvärderingar som uppdatera genomsökning, baslinje genomsökning och saknas genomsökning för endpoint protection lösning.|
|Dator|40|Installera systemuppdateringar på dina datorer|Installera saknas systemsäkerhet och viktiga uppdateringar för att skydda dina Windows- och Linux-datorer och datorer
|Dator|15|Lägg till en brandvägg för webbappar| Distribuera en lösning web application firewall (WAF) för att skydda dina webbprogram. |
|Dator|40|Uppdatera operativsystemsversionen för dina molntjänstroller|Uppdatera operativsystemets version för dina molntjänstroller till den senaste tillgängliga versionen för din OS-familj.|
|Dator|35|Åtgärda säkerhetsriskerna i säkerhetskonfigurationen på dina datorer|Åtgärda sårbarheter i Säkerhetskonfiguration på dina datorer för att skydda dem från attacker. |
|Dator|35|Åtgärda sårbarheter i Säkerhetskonfiguration på dina behållare|Åtgärda sårbarheter i säkerhetskonfigurationen på datorer med Docker installerat för att skydda dem mot angrepp.|
|Dator|25|Aktivera anpassningsbara programkontroller|Aktivera programmet kontroller som program kan köras på din virtuella dator i Azure. Detta hjälper att skydda dina virtuella datorer mot skadlig kod. Security Center använder maskininlärning att analysera programmen som körs på varje virtuell dator och hjälper till att du tillämpar tillåter regler med den här intelligensen. Den här funktionen förenklar processen med att konfigurera och underhålla program tillåter regler.|
|Dator|20|Installera endpoint protection-lösningen på dina datorer|Installera en endpoint protection-lösning på dina virtuella datorer, att skydda dem mot hot och sårbarheter.|
|Dator|20|Starta om dina datorer för att att tillämpa systemuppdateringarna|Starta om dina datorer för att tillämpa systemuppdateringarna och skydda datorn från säkerhetsrisker.|
|Dator|15|Tillämpa diskkryptering på dina virtuella datorer|Kryptera virtuella diskar med Azure Disk Encryption både för Windows och Linux-datorer. Azure Disk Encryption (ADE) använder branschens standard BitLocker-funktion i Windows och DM-crypt i Linux för att ange operativsystem- och diskkryptering för att skydda och skydda dina data och att uppfylla din organisations säkerhet och efterlevnad åtaganden i kundens Azure-nyckelvalv. När dina krav på efterlevnad och säkerhet måste du kryptera data från slutpunkt till slutpunkt med hjälp av dina krypteringsnycklar, inklusive kryptering av tillfälliga (lokalt anslutna tillfälliga) disk, Använd Azure-diskkryptering. Du kan också som standard är Managed Disks krypterade i vila som standard med hjälp av Azure Storage Service Encryption där krypteringsnycklarna är hanteras av Microsoft-nycklar i Azure. Om det uppfyller din efterlevnad och säkerhetskrav, kan du använda standard Managed diskkryptering för att uppfylla dina krav.|
|Dator|30|Installera en sårbarhetsbedömningslösning på dina virtuella datorer|Installera en sårbarhetsbedömningslösning på dina virtuella datorer|
|Dator|15|Lägg till en brandvägg för webbappar| Distribuera en lösning web application firewall (WAF) för att skydda dina webbprogram. |
|Dator|30|Åtgärda sårbarheter med hjälp av en lösning för sårbarhetsbedömning|Virtuella datorer som distribueras en 3 part lösning för sårbarhetsbedömning som kontinuerligt utvärderas mot program- och OS-säkerhetsproblem. När sådana sårbarheter finns finns dessa mer information som en del av rekommendationen.|
|Dator|30|Installera en sårbarhetsbedömningslösning på dina virtuella datorer|Installera en sårbarhetsbedömningslösning på dina virtuella datorer|
|Dator|1|Migrera virtuella datorer till nya Azure Resource Manager-resurser|Använda Azure Resource Manager för dina virtuella datorer för att ange säkerhetsförbättringar t.ex: starkare åtkomstkontroll (RBAC), bättre granskning, Resource Manager-baserade distributionen och styrning åtkomst till hanterade identiteter, åtkomst till nyckelvalvet för hemligheter, Azure AD-baserad autentisering och stöd för taggar och resursgrupper för enklare säkerhetshantering. |
|Dator|30|Åtgärda sårbarheter med hjälp av en lösning för sårbarhetsbedömning|Virtuella datorer som distribueras en 3 part lösning för sårbarhetsbedömning som kontinuerligt utvärderas mot program- och OS-säkerhetsproblem. När sådana sårbarheter finns finns dessa mer information som en del av rekommendationen.|
|Skaluppsättning för virtuella datorer |4|Aktivera diagnostikloggar i Virtual Machine Scale Sets|Aktivera loggar och behålla för upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning. Detta är användbart när en säkerhetsincident inträffar eller nätverket komprometteras.|
|Skaluppsättning för virtuella datorer|35|Åtgärda säkerhetsproblem i säkerhetskonfigurationen på dina skalningsuppsättningar för virtuella datorer|Åtgärda sårbarheter i säkerhetskonfigurationen för din virtuella dators skalningsuppsättningar för att skydda dem från angrepp. |
|Skaluppsättning för virtuella datorer|5|Åtgärda hälsokontrollfel för slutpunktsskydd i skalningsuppsättningar för virtuella datorer|Åtgärda Endpoint Protection-hälsofel på skalningsuppsättningar för virtuella datorer för att skydda mot hot och sårbarheter. |
|Skaluppsättning för virtuella datorer|10|Installera lösning för slutpunktsskydd i skalningsuppsättningar för virtuella datorer|Installera en endpoint protection-lösning på dina VM-skalningsuppsättningar, att skydda dem mot hot och sårbarheter. |
|Skaluppsättning för virtuella datorer|40|Installera systemuppdateringar i skalningsuppsättningar för virtuella datorer|Installera saknade systemsäkerhetsuppdateringar och viktiga uppdateringar för att skydda dina skalningsuppsättningar för virtuella Windows och Linux-dator. |
 





## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:


* [Förstå Azure Security Center-rekommendationer för virtuella datorer](security-center-virtual-machine-recommendations.md)
* [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.

