---
title: Skydda dina datorer och program i Azure Security Center | Microsoft Docs
description: Det här dokumentet belyser rekommendationer i Security Center som hjälper dig att skydda dina virtuella datorer och datorer och webbappar och App Service-miljöer.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 0bd93e0732b0e822fae68b4ac2cc5e28fb246808
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016972"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Skydda dina datorer och program i Azure Security Center
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som guidar dig genom processen med att konfigurera kontrollfunktioner som behövs. Rekommendationer gäller för Azure-resurstyper: virtuella datorer (VM) och datorer, program, nätverk, SQL, och identitet och åtkomst.

Den här artikeln belyser rekommendationer som gäller för datorer och program.

## <a name="monitoring-security-health"></a>Övervakning av säkerhetshälsa
Du kan övervaka dina resursers säkerhetstillstånd på den **Security Center – översikt** instrumentpanelen. Den **resurser** avsnittet innehåller antalet problem som har identifierats och säkerhetsstatus för varje resurstyp.

Du kan visa en lista över alla problem genom att välja **rekommendationer**. Mer information om hur du utför rekommendationerna finns i [utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

En fullständig lista över beräknings- och App services-rekommendationer finns i [rekommendationer](security-center-virtual-machine-recommendations.md).

Om du vill fortsätta, Välj **Compute och appar** under **resurser** eller huvudmenyn i Security Center.
![Instrumentpanelen för Security Center][1]

## <a name="monitor-compute-and-app-services"></a>Övervaka beräkning och App services
Under **Beräkning** finns det fyra flikar:

- **Översikt**: Övervakning och rekommendationer som identifierats av Security Center.
- **Virtuella datorer och datorer**: Listar alla dina virtuella datorer, datorer och det aktuella säkerhetstillståndet för var och en.
- **Molntjänster**: Listar alla dina webb- och arbetarroller som övervakas av Security Center.
- **App services (förhandsversion)**: lista över dina App service-miljöer och aktuella säkerhetstillståndet för var och en.
Om du vill fortsätta, Välj **Compute och appar** under **resurser** eller huvudmenyn i Security Center.

![Compute][2]

På varje flik kan det finnas olika alternativ, och i de olika avsnitten kan du välja ett individuellt alternativ och visa mer information om de åtgärder som rekommenderas för att åtgärda problemet.

### <a name="monitoring-recommendations"></a>Rekommendationer för övervakning
Det här avsnittet visas det totala antalet virtuella datorer och datorer som initierats för automatisk etablering och deras aktuella status. I det här exemplet finns det en rekommendation, **Övervaka problem med hälsotillstånd**. Välj den rekommendationen.

![Övervaka problem med hälsotillstånd][3]

**Övervaka problem med hälsotillstånd** öppnas. Virtuella datorer och vanliga datorer som Security Center inte kan övervaka visas i en lista. Välj en virtuell eller vanlig dator för detaljerad information. I **ÖVERVAKNINGSTILLSTÅND** finns en orsak till att det inte går att övervaka Security Center. Läs [felsökningsguiden för Security Center](security-center-troubleshooting-guide.md) och visa en lista över steg för värden, beskrivningar och lösningar för **ÖVERVAKNINGSTILLSTÅND**.

### Oövervakade virtuella datorer och datorer <a name="unmonitored-vms-and-computers"></a>
En virtuell dator eller en dator övervakas av Security Center om datorn inte kör tillägget Microsoft Monitoring Agent. En dator kan ha en lokal agent installerad, exempelvis OMS direktagent eller SCOM-agenten. Datorer med dessa agenter identifieras som oövervakade eftersom dessa agenter inte stöds fullt ut i Security Center. För att kunna utnyttja Security Center fullt ut krävs tillägget Microsoft Monitoring Agent.

Du kan installera tillägget på den oövervakade virtuella datorn eller datorn förutom redan installerade lokal agent. Konfigurera båda agenterna på samma sätt och anslut dem till samma arbetsyta. Det gör att Security Center kan interagera med tillägget Microsoft Monitoring Agent och samla in data. I [Aktivera tillägget för virtuella datorer](../log-analytics/log-analytics-quick-collect-azurevm.md) finns anvisningar om hur du installerar tillägget Microsoft Monitoring Agent.

Om du vill veta mer om varför Security Center inte kan övervaka virtuella datorer och datorer som initierats för automatisk försörjning läser du i [Övervaka problem med hälsotillstånd](security-center-troubleshooting-guide.md#mon-agent).

### <a name="recommendations"></a>Rekommendationer
Det här avsnittet innehåller en uppsättning rekommendationer för varje virtuell dator och datorn, webb-och arbetsroller, Azure App Service Web Apps och Azure App Service Environment som övervakas via Security Center. Den första kolumnen visar rekommendationen. Den andra kolumnen visar det totala antalet resurser som påverkas av den här rekommendationen. Den tredje kolumnen visar hur allvarligt problemet är, såsom illustreras på följande skärmbild:

![Rekommendationer][4]

Varje rekommendation har en uppsättning åtgärder som du kan utföra när du har valt. Exempel: Om du väljer **systemuppdateringar**, hur många virtuella datorer och datorer som saknar korrigeringsfiler och allvarlighetsgraden för saknad uppdatering visas som i följande skärmbild:

![Tillämpa systemuppdateringar][5]

**Tillämpa systemuppdateringar** innehåller en sammanfattning av viktiga uppdateringar i diagramformat, en för Windows och en för Linux. Den andra delen har en tabell med följande information:

- **NAMN**: Namnet på den uppdatering som saknas.
- **ANTAL VM:AR OCH DATORER**: Det totala antalet virtuella datorer och datorer som saknar denna uppdatering.
- **Uppdatera ALLVARLIGHETSGRAD**: visas hur viktig rekommendationen:

    - **Kritiska**: en säkerhetsrisk finns en viktig resurs (program, virtuell dator eller nätverkssäkerhetsgrupp) som måste åtgärdas.
    - **Viktiga**: icke-kritiska eller ytterligare åtgärder krävs för att slutföra en process eller åtgärda en säkerhetsrisk.
    - **Måttlig**: en säkerhetsrisk som bör åtgärdas men inte kräver omedelbar åtgärd. (Rekommendationer med låg allvarlighetsgrad visas normalt inte, men du kan filtrera fram dem om du vill se dem.)


- **STATE (STATUS)**: Här visas det aktuella tillståndet för rekommendationen:

    - **Öppen**: Rekommendationen har inte utförts än.
    - **Pågående**: Rekommendationen håller på att utföras och ingen åtgärd behövs från din sida.
    - **Löst**: Rekommendation har redan slutförts. (När problemet har lösts, inaktiveras posten).

Om du vill visa information om rekommendationen klickar du på namnet på den uppdatering som saknas.

![Information om rekommendation][6]

> [!NOTE]
> Säkerhetsrekommendationerna här är samma som de under den **rekommendationer** panelen. Se [utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md) för mer information om hur du utför rekommendationerna.
>
>

### <a name="vms-and-computers"></a>Virtuella datorer och vanliga datorer
Avsnittet virtuella datorer och datorer får du en överblick över alla rekommendationer för virtuell dator och datorn. Varje kolumn representerar en uppsättning rekommendationer såsom visas på följande skärmbild:

![Rekommendationer för datorer och virtuella datorer][7]

Det finns fyra typer av ikoner i den här listan:

![Datorer som inte är Azure-datorer][8] Icke-Azure-dator.

![Azure Resource Manager-VM][9] Azure Resource Manager-VM.

![Klassiska virtuella Azure-datorer][10] Azure klassisk virtuell dator.

![Virtuella datorer identifieras från arbetsytan][11] Virtuella datorer som endast identifieras från arbetsytan som är en del av den visade prenumerationen. Detta omfattar alla virtuella datorer från andra prenumerationer som rapporterar till arbetsytan i denna prenumeration, och virtuella datorer som installerades med SCOM-direktagent och som inte har något resurs-ID.

Genom ikonerna under de olika rekommendationerna hjälper dig att snabbt identifiera den virtuella datorn och datorn som behöver åtgärdas och typ av rekommendation. Du kan också använda alternativet Filter för att välja vilka alternativ som visas på den här skärmen.

![Filter][12]

I exemplet ovan har en virtuell dator en viktig rekommendation angående slutpunktsskydd. Välj den virtuella datorn för att få mer information om den:

![Viktig rekommendation][13]

Här kan du visa säkerhetsinformation för den virtuella datorn eller datorn. Längst ned ser du den rekommenderade åtgärden och allvarlighetsgraden på de olika problemen.

### <a name="cloud-services"></a>Molntjänster
För molntjänster skapas en rekommendation när operativsystemets version är inaktuell, såsom visas på följande skärmbild:

![Molntjänster][14]

I ett scenario där du har en rekommendation (vilket inte är fallet i föregående exempel), måste du följa anvisningarna i rekommendationen för att uppdatera versionen av operativsystemet. När en uppdatering är tillgänglig får du en avisering (röd eller orange beroende på hur allvarligt problemet är). När du väljer den här aviseringen på raden webrole1 (kör Windows Server med din webbapp automatiskt distribuerad till IIS) eller WorkerRole1 (kör Windows Server med din webbapp automatiskt distribuerad till IIS) visas mer information om den här rekommendationen enligt den följande skärmbild:

![WorkerRole1][15]

Om du vill få en mer ingående förklaring av den här rekommendationen klickar du på **Uppdatera OS-version** under kolumnen **BESKRIVNING**.

![Uppdatera OS-versionen][16]

### <a name="app-services-preview"></a>App Services (förhandsversion)

> [!NOTE]
> Övervakning av App Service finns i förhandsversion och bara tillgängliga på standardnivån i Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
>
>

Under **apptjänster**, du hittar en lista över App service-miljöer och hälsoöversikt baserat på utvärderingen Security Center utförs.

![App Services][17]

Det finns tre typer av ikoner i den här listan:

![App services-miljö][18] App services-miljö.

![Webbprogram][19] Webbprogram.

![Funktionsprogram][24] Funktionsprogram.

1. Välj ett webbprogram. En sammanfattningsvy öppnas med tre flikar:

  - **Rekommendationer**: baserat på har utförts av Security Center som misslyckades.
  - **Skickas utvärderingar**: lista över har utförts av Security Center som skickas.
  - **Ej tillgängliga utvärderingar**: lista över utvärderingar som inte kunde köras på grund av ett fel eller rekommendationen gäller inte för den specifika App service

  Under **rekommendationer** är en lista över rekommendationer för det valda webbprogrammet och allvarlighetsgraden för varje rekommendation.

  ![Sammanfattningsvy][20]

2. Välj en rekommendation för en beskrivning av rekommendationen och en lista över skadade resurser, felfria resurser och ej genomsökta resurser.

  ![Beskrivning av rekommendation][21]

  Under **skickas utvärderingar** är en lista över skickade utvärderingar.  Allvarlighetsgraden för de här utvärderingar är alltid grönt.

  ![Skickade utvärderingar][22]

3. Välj en skickade utvärdering från listan för en beskrivning av utvärderingen, en lista över feltillstånd och felfria resurser och en lista över ej genomsökta resurser. Det finns en flik för skadade resurser men listan är alltid tomt eftersom utvärderingen skickades.

    ![Felfria resurser][23]

## <a name="compute-and-app-recommendations"></a>Rekommendationer för beräknings- och app
|Resurstyp|Säkerhetspoäng|Rekommendation|Beskrivning|
|----|----|----|----|
|Dator|50|Installera övervakningsagenten på dina datorer|Installera agenten för övervakning om du vill aktivera insamling av data, uppdateringar som genomsökning, baslinjegenomsökning och endpoint protection på varje dator.|
|Dator|50|Aktivera automatisk etablering och insamling av data för dina prenumerationer |Aktivera automatisk etablering och insamling av data för datorer i dina prenumerationer för att aktivera insamling av data, uppdateringar som genomsökning, baslinjegenomsökning och endpoint protection på varje dator som har lagts till i dina prenumerationer.|
|Dator|40|Åtgärda hälsoproblem för övervakningsagenten på dina datorer|För fullständig Security Center skydd Lös övervakningsproblem agenten på dina datorer genom att följa anvisningarna i guiden för felsökning| 
|Dator|40|Åtgärda hälsoproblem för slutpunktsskydd på dina datorer|Lös övervakningsproblem agenten på dina datorer genom att följa anvisningarna i guiden för felsökning för fullständig Security Center-skydd.|
|Dator|40|Felsök saknade skanningsdata på dina datorer|Felsöka genomsökningsdata som saknas på virtuella datorer och datorer. Skanningsdata saknas på dina datorer blir saknas säkerhetsutvärderingar som uppdatera genomsökning, baslinje genomsökning och saknas genomsökning för endpoint protection lösning.|
|Dator|40|Installera systemuppdateringar på dina datorer|Installera saknas systemsäkerhet och viktiga uppdateringar för att skydda dina Windows- och Linux-datorer och datorer
|Dator|40|Uppdatera operativsystemsversionen för dina molntjänstroller|Uppdatera operativsystemets version för dina molntjänstroller till den senaste tillgängliga versionen för din OS-familj.|
|Dator|35|Åtgärda säkerhetsriskerna i säkerhetskonfigurationen på dina datorer|Åtgärda sårbarheter i Säkerhetskonfiguration på dina datorer för att skydda dem från attacker. |
|Dator|35|Åtgärda sårbarheter i Säkerhetskonfiguration på dina behållare|Åtgärda sårbarheter i säkerhetskonfigurationen på datorer med Docker installerat för att skydda dem mot angrepp.|
|Dator|25|Aktivera anpassningsbara programkontroller|Aktivera programmet kontroller som program kan köras på din virtuella dator i Azure. Detta hjälper att skydda dina virtuella datorer mot skadlig kod. Security Center använder maskininlärning att analysera programmen som körs på varje virtuell dator och hjälper till att du tillämpar tillåter regler med den här intelligensen. Den här funktionen förenklar processen med att konfigurera och underhålla program tillåter regler.|
|Dator|20|Installera endpoint protection-lösningen på dina datorer|Installera en endpoint protection-lösning på dina virtuella datorer, att skydda dem mot hot och sårbarheter.|
|Dator|20|Starta om dina datorer för att att tillämpa systemuppdateringarna|Starta om dina datorer för att tillämpa systemuppdateringarna och skydda datorn från säkerhetsrisker.|
|Apptjänst|20|Webbprogram bör enbart vara åtkomliga via HTTPS|Begränsa åtkomst till webbprogram via HTTPS endast.|
|Apptjänst|20|Funktionen App bör enbart vara åtkomliga via HTTPS|Begränsa åtkomst till Funktionsappar via HTTPS endast.|
|Dator|15|Tillämpa diskkryptering på dina virtuella datorer|Kryptera virtuella diskar med Azure Disk Encryption både för Windows och Linux-datorer. Azure Disk Encryption (ADE) använder branschens standard BitLocker-funktion i Windows och DM-crypt i Linux för att ange operativsystem- och diskkryptering för att skydda och skydda dina data och att uppfylla din organisations säkerhet och efterlevnad åtaganden i kundens Azure-nyckelvalv. När dina krav på efterlevnad och säkerhet måste du kryptera data från slutpunkt till slutpunkt med hjälp av dina krypteringsnycklar, inklusive kryptering av tillfälliga (lokalt anslutna tillfälliga) disk, Använd Azure-diskkryptering. Du kan också som standard är Managed Disks krypterade i vila som standard med hjälp av Azure Storage Service Encryption där krypteringsnycklarna är hanteras av Microsoft-nycklar i Azure. Om det uppfyller din efterlevnad och säkerhetskrav, kan du använda standard Managed diskkryptering för att uppfylla dina krav.|
|Beräkningsresurser (service fabric)|10|Använd Azure Active Directory för klientautentisering i Service Fabric|Utför klientautentisering endast via Azure Active Directory i Service Fabric.|
|Beräkningsresurser (automation-konto)|5| Aktivera kryptering av Automation-konto|Aktivera kryptering av Automation-konto variabler för tillgångar vid lagring av känsliga data.|
|Apptjänst|5|Aktivera diagnostikloggar i App service|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (belastningsutjämnare)|5|Aktivera diagnostikloggar i belastningsutjämnaren|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (Sök)|5|Aktivera diagnostikloggar i söktjänst|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (service bus)|5|Aktivera diagnostikloggar i Service Bus|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (stream analytics)|5|Aktivera diagnostikloggar i Azure Stream Analytics|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (service fabric)|5|Aktivera diagnostikloggar i Service Fabric|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (batch)|5|Aktivera diagnostikloggar i Batch-konton|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (händelsehubb)|5|Aktivera diagnostikloggar i Event Hub|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Beräkningsresurser (logikappar)|5|Aktivera diagnostikloggar i Logic Apps|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Dator|30|Installera en sårbarhetsbedömningslösning på dina virtuella datorer|Installera en sårbarhetsbedömningslösning på dina virtuella datorer|
|Dator|15|Lägg till en brandvägg för webbappar| Distribuera en lösning web application firewall (WAF) för att skydda dina webbprogram. |
|Dator|30|Åtgärda sårbarheter - genom en lösning för sårbarhetsbedömning|Virtuella datorer som distribueras en 3 part lösning för sårbarhetsbedömning som kontinuerligt utvärderas mot program- och OS-säkerhetsproblem. När sådana sårbarheter finns finns dessa mer information som en del av rekommendationen.|
|Beräkningsresurser (service fabric)|15|Ange egenskapen ClusterProtectionLevel till EncryptAndSign i Service Fabric|Service Fabric tillhandahåller tre skyddsnivåer (ingen, logga in och EncryptAndSign) för nod-till-nod-kommunikation med hjälp av en primär klustercertifikat.  Ställa in skydd så att alla nod-till-nod-meddelanden är krypterad och har signerats digitalt. |
|Apptjänst|10|Fjärrfelsökning bör stängas av för webbprogram|Inaktivera felsökning för webbprogram, om du inte längre behöver använda den. Fjärrfelsökning kräver att inkommande portar öppnas på en Funktionsapp.|
|Apptjänst|10|Fjärrfelsökning bör stängas av för Funktionsprogram|Inaktivera felsökning för Funktionsappen om du inte längre behöver använda den. Fjärrfelsökning kräver att inkommande portar öppnas på en Funktionsapp.|
|Apptjänst|10|Konfigurera IP-begränsningar för webbprogram|Definiera en lista över IP-adresser som ska kunna komma åt ditt program. Användning av IP-restriktioner skyddar du webbappar mot vanliga attacker.|
|Apptjänst|10|Konfigurera IP-begränsningar för Funktionsappen| Definiera en lista över IP-adresser som ska kunna komma åt ditt program. Användning av IP-restriktioner skyddar en funktionsapp från vanliga attacker.|
|Apptjänst|10|Tillåt inte alla ('* ') resurser för att komma åt ditt program| Tillåt inte parametern website_load_certificates. den uppsättning ””. Ställa in parametern ”innebär att alla certifikat läses in till din web program personliga certifikatarkiv. Detta kan leda till missbruk av principen om lägsta behörighet eftersom det är osannolikt att webbplatsen behöver åtkomst till alla certifikat vid körning.|
|Apptjänst|5|Webbsockets bör inaktiveras för webbprogram|Granska användningen av Web Sockets i webbappar. Web Sockets-protokollet är utsatt för olika typer av säkerhetshot.|
|Apptjänst|5|Webbsockets bör inaktiveras för Funktionsappen|Granska användningen av Web Sockets i Funktionsappar. Web Sockets-protokollet är utsatt för olika typer av säkerhetshot.|
|Apptjänst|5|Använd anpassade domäner för ditt webbprogram|Använd anpassade domäner för att skydda ett webbprogram mot vanliga attacker som nätfiske och andra DNS-relaterade attacker.|
|Apptjänst|5|Använd anpassade domäner för Funktionsappen|Använd anpassade domäner för att skydda en funktionsapp från vanliga attacker som nätfiske och andra DNS-relaterade attacker.|
|Beräkningsresurser (batch)|1|Konfigurera måttaviseringsregler på Batch-konto|Konfigurera måttaviseringsregler på Batch-konto och aktivera mätvärden Pool ta bort klar händelser och poolen ta bort starta händelser|
|Beräkningsresurser (service bus)|1|Ta bort alla regler förutom RootManageSharedAccessKey från Service Bus-namnområde |Service Bus-klienter ska inte använda en namnområdesprincip administratörsnivå som ger åtkomst till alla köer och ämnen i ett namnområde. För att anpassas till den minsta säkerhet behörighet modell, ska du skapa principer för åtkomst på enhetsnivå för köer och ämnen för att ge åtkomst till endast den specifika enheten.|
|Beräkningsresurser (händelsehubb)|1|Ta bort alla regler förutom RootManageSharedAccessKey från namnområdet för Händelsehubben |Event Hub-klienter ska inte använda en namnområdesprincip administratörsnivå som ger åtkomst till alla köer och ämnen i ett namnområde. För att anpassas till den minsta säkerhet behörighet modell, ska du skapa principer för åtkomst på enhetsnivå för köer och ämnen för att ge åtkomst till endast den specifika enheten.|
|Beräkningsresurser (händelsehubb)|5|Definiera regler för Event Hub-entitet|Granska auktoriseringsregler på entiteten Event Hub att bevilja lägsta åtkomstbehörighet.|
|Dator|30|Installera en sårbarhetsbedömningslösning på dina virtuella datorer|Installera en sårbarhetsbedömningslösning på dina virtuella datorer|
|Apptjänst|20|CORS bör inte bevilja alla resurser att komma åt dina webbprogram|Tillåt endast nödvändiga domäner att interagera med ditt webbprogram. Mellan ursprung ska sharing (CORS) inte tillåta alla domäner får åtkomst till ditt webbprogram.|
|Apptjänst|20|CORS bör inte bevilja alla resurser att få åtkomst till din Funktionsapp| Tillåt endast nödvändiga domäner att interagera med ditt funktionsprogram. Mellan ursprung ska sharing (CORS) inte tillåta alla domäner åtkomst till ditt funktionsprogram.|
|Dator|15|Lägg till en brandvägg för webbappar| Distribuera en lösning web application firewall (WAF) för att skydda dina webbprogram. |
|Apptjänst|10|Använd senast stöds .NET Framework för webbprogram|Använd den senaste versionen av .NET Framework för de senaste säkerhetsklasserna. Använder äldre klasser och typer kan du göra ditt program sårbart.|
|Apptjänst|10|Använd den senaste Java versionen för webbprogram|Använd den senaste Java-versionen för de senaste säkerhetsklasserna. Använder äldre klasser och typer kan du göra ditt program sårbart.|
|Apptjänst|10|Använd den senaste PHP versionen för webbprogram|Använd den senaste PHP-versionen för de senaste säkerhetsklasserna. Använder äldre klasser och typer kan du göra ditt program sårbart.|
|Apptjänst|10|Använd den senaste Node.js versionen för webbprogram|Använd den senaste versionen av Node.js för de senaste säkerhetsklasserna. Använder äldre klasser och typer kan du göra ditt program sårbart.|
|Apptjänst|10|Använd den senaste Python versionen för webbprogram|Använd den senaste Python-versionen för de senaste säkerhetsklasserna. Använder äldre klasser och typer kan du göra ditt program sårbart.|
|Virtuella datorer och vanliga datorer|1|Migrera virtuella datorer till nya AzureRM-resurser|Använda den nya Azure Resource Manager v2 för dina virtuella datorer för att tillhandahålla förbättringar av säkerhet som: starkare åtkomstkontroll (RBAC), bättre granskning, ARM-baserade distributionen och styrning åtkomst till hanterade identiteter, åtkomst till nyckelvalvet för hemligheter, Azure AD-baserad autentisering och stöd för taggar och resursgrupper för enklare säkerhetshantering. |
|Dator|30|Åtgärda sårbarheter - genom en lösning för sårbarhetsbedömning|Virtuella datorer som distribueras en 3 part lösning för sårbarhetsbedömning som kontinuerligt utvärderas mot program- och OS-säkerhetsproblem. När sådana sårbarheter finns finns dessa mer information som en del av rekommendationen.|




## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:


* [Förstå Azure Security Center-rekommendationer för virtuella datorer](security-center-virtual-machine-recommendations.md)
* [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
