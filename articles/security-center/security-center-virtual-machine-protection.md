---
title: Skydda dina datorer och program i Azure Security Center
description: Det här dokumentet beskriver rekommendationer i Security Center som hjälper dig att skydda dina virtuella datorer och datorer och dina webbappar och App Service miljöer.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 803e64c9df1b52a33a1b50714f77b005032bf200
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686351"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Skydda dina datorer och program i Azure Security Center
Azure Security Center analyserar säkerhets status för dina Azure-resurser, icke-Azure-servrar och virtuella datorer. När Security Center identifierar potentiella säkerhets problem skapas rekommendationer som vägleder dig genom processen att konfigurera de nödvändiga kontrollerna. Rekommendationer gäller för Azure-resurs typer: virtuella datorer (VM) och datorer, program, nätverk, SQL och identitet och åtkomst.

Den här artikeln beskriver rekommendationer som gäller för datorer och program.

## <a name="monitoring-security-health"></a>Övervakning av säkerhetshälsa
Du kan övervaka säkerhets status för dina resurser på instrument panelen för **Security Center – översikt** . Avsnittet **resurser** innehåller antalet identifierade problem och säkerhets status för varje resurs typ.

Du kan visa en lista över alla problem genom att välja **rekommendationer**. Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

En fullständig lista över rekommendationer för beräkning och app Services finns i [rekommendationer](security-center-virtual-machine-protection.md#compute-and-app-recs).

Fortsätt genom att välja **compute & Apps** under **resurser** eller Security Center huvud menyn.
![Security Center instrument panel](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Övervaka Compute-och app-tjänster
Under **compute &-appar**finns följande flikar:

- **Översikt**: Övervakning och rekommendationer som identifierats av Security Center.
- **Virtuella datorer och datorer**: Listar alla dina virtuella datorer, datorer och det aktuella säkerhetstillståndet för var och en.
- **Molntjänster**: Listar alla dina webb- och arbetarroller som övervakas av Security Center.
- **App Services**: lista över dina App Service-miljöer och aktuella säkerhets tillstånd.
- **Behållare**: lista över behållare och säkerhets utvärdering av deras konfigurationer.
- **Beräknings resurser (förhands granskning)** : lista över rekommendationer för dina beräknings resurser, till exempel Service Fabric kluster och händelse nav.

Fortsätt genom att välja **compute &-appar** under **resurs säkerhets hygien**.

![Compute](./media/security-center-virtual-machine-recommendations/compute.png)

På varje flik kan det finnas olika alternativ, och i de olika avsnitten kan du välja ett individuellt alternativ och visa mer information om de åtgärder som rekommenderas för att åtgärda problemet.

### Oövervakade virtuella datorer och datorer<a name="unmonitored-vms-and-computers"></a>
En virtuell dator eller dator är inte övervakad av Security Center om datorn inte kör tillägget Microsoft Monitoring Agent. En dator kan ha en lokal agent som redan är installerad, till exempel OMS Direct-agenten eller System Center Operations Manager agenten. Datorer med dessa agenter identifieras som oövervakade eftersom de här agenterna inte stöds fullt ut i Security Center. För att kunna utnyttja Security Center fullt ut krävs tillägget Microsoft Monitoring Agent.

Du kan installera tillägget på den oövervakade virtuella datorn eller datorn utöver den redan installerade lokala agenten. Konfigurera båda agenterna på samma sätt och anslut dem till samma arbetsyta. Det gör att Security Center kan interagera med tillägget Microsoft Monitoring Agent och samla in data. I [Aktivera tillägget för virtuella datorer](../azure-monitor/learn/quick-collect-azurevm.md) finns anvisningar om hur du installerar tillägget Microsoft Monitoring Agent.

Om du vill veta mer om varför Security Center inte kan övervaka virtuella datorer och datorer som initierats för automatisk försörjning läser du i [Övervaka problem med hälsotillstånd](security-center-troubleshooting-guide.md#mon-agent).

### <a name="recommendations"></a>Rekommendationer
Det här avsnittet innehåller en uppsättning rekommendationer för varje virtuell dator och dator, webb-och arbets roller, Azure App Service Web Apps och Azure App Service-miljön som Security Center övervakare. Den första kolumnen visar rekommendationen. Den andra kolumnen visar det totala antalet resurser som påverkas av den rekommendationen. Den tredje kolumnen visar problemets allvarlighets grad.

Varje rekommendation har en uppsättning åtgärder som du kan utföra när du har valt den. Om du till exempel väljer **saknade system uppdateringar**, så visas antalet virtuella datorer och datorer som saknar uppdateringar och allvarlighets graden för den saknade uppdateringen.

**Använd System uppdateringar** har en sammanfattning av kritiska uppdateringar i ett diagram format, en för Windows och en för Linux. Den andra delen har en tabell med följande information:

- **NAMN**: Namnet på den uppdatering som saknas.
- **Nej. Virtuella datorer & datorer**: det totala antalet virtuella datorer och datorer som saknar den här uppdateringen.
- **Uppdatera allvarlighets**grad: beskriver allvarlighets graden för den specifika rekommendationen:

    - **Kritisk**: det finns ett säkerhets problem med en meningsfull resurs (program, virtuell dator eller nätverks säkerhets grupp) och kräver åtgärd.
    - **Viktigt**: icke-kritiska eller ytterligare steg krävs för att slutföra en process eller eliminera en sårbarhet.
    - **Måttlig**: ett säkerhets problem bör åtgärdas men kräver ingen omedelbar åtgärd. (Rekommendationer med låg allvarlighetsgrad visas normalt inte, men du kan filtrera fram dem om du vill se dem.)


- **STATE (STATUS)** : Här visas det aktuella tillståndet för rekommendationen:

    - **Öppen**: Rekommendationen har inte utförts än.
    - **Pågående**: Rekommendationen håller på att utföras och ingen åtgärd behövs från din sida.
    - **Löst**: Rekommendation har redan slutförts. (När problemet har lösts, inaktiveras posten).

Om du vill visa information om rekommendationen klickar du på namnet på den uppdatering som saknas.


> [!NOTE]
> Säkerhets rekommendationerna här är desamma som de som visas på panelen **rekommendationer** . Mer information om hur du löser rekommendationer finns [i implementera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md).
>
>

### <a name="vms-and-computers"></a>Virtuella datorer och datorer
Avsnittet virtuella datorer och datorer ger en översikt över alla rekommendationer för virtuella datorer och datorer. I varje kolumn finns en typ av rekommendationer.

![Rekommendationer för datorer och virtuella datorer](./media/security-center-virtual-machine-recommendations/vm-computers.png)

Det finns fyra typer av ikoner som representeras i den här listan:

![Icke-Azure-dator](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Icke-Azure-dator.

![Azure Resource Manager VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM.

![Klassisk virtuell Azure-dator](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klassisk virtuell Azure-dator.


![Virtuella datorer som identifierats från arbets ytan](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuella datorer som endast identifieras från arbetsytan som är en del av den visade prenumerationen. Detta inkluderar virtuella datorer från andra prenumerationer som rapporterar till arbets ytan i den här prenumerationen och virtuella datorer som har installerats med Operations Manager Direct agent och inte har något resurs-ID.

Ikonen som visas under varje rekommendation hjälper dig att snabbt identifiera den virtuella datorn och datorn som behöver åtgärdas och typ av rekommendation. Du kan också använda filtren för att söka i listan efter **resurs typ** och efter **allvarlighets grad**.

Om du vill öka detalj nivån för säkerhets rekommendationerna för varje virtuell dator klickar du på den virtuella datorn.
Här ser du säkerhets informationen för den virtuella datorn eller datorn. Längst ned kan du se den rekommenderade åtgärden och allvarlighets graden för varje problem.
![Molntjänster](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Molntjänster
För Cloud Services skapas en rekommendation när operativ systemets version är inaktuell.

![Molntjänster](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

I ett scenario där du har en rekommendation (som inte är fallet i föregående exempel), måste du följa stegen i rekommendationen för att uppdatera operativ system versionen. När en uppdatering är tillgänglig får du en avisering (röd eller orange beroende på hur allvarligt problemet är). När du väljer den här aviseringen i WebRole1 (kör Windows Server med din webbapp automatiskt distribuerad till IIS) eller WorkerRole1 (kör Windows Server med din webbapp automatiskt distribuerad till IIS) rader, visas mer information om den här rekommendationen.

Om du vill få en mer ingående förklaring av den här rekommendationen klickar du på **Uppdatera OS-version** under kolumnen **BESKRIVNING**.



![Uppdatera OS-versionen](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>App Services
Om du vill visa App Service information måste du aktivera App Service i din prenumeration. Anvisningar om hur du aktiverar den här funktionen finns i [skydda app service med Azure Security Center](security-center-app-services.md).
[!NOTE]
> Övervaknings App Service är i för hands version och endast tillgängligt på standard nivån för Security Center.


Under **app Services**hittar du en lista över dina App Service-miljöer och hälso översikten baserat på utvärderings Security Center utförd.

![App Services](./media/security-center-virtual-machine-recommendations/app-services.png)

Det finns tre typer av ikoner som visas i den här listan:

![App Services-miljö](./media/security-center-virtual-machine-recommendations/ase.png) App Services-miljö.

![Webbprogram](./media/security-center-virtual-machine-recommendations/web-app.png) Webb program.

![Funktions program](./media/security-center-virtual-machine-recommendations/function-app.png) Function-program.

1. Välj ett webb program. En sammanfattningsvy öppnas med tre flikar:

   - **Rekommendationer**: baserat på utvärderingar som utförts av Security Center som misslyckades.
   - **Slutförda utvärderingar**: lista över utvärderingar som utförts av Security Center som skickades.
   - **Ej tillgängliga utvärderingar**: lista över utvärderingar som inte kunde köras på grund av ett fel eller att rekommendationen inte är relevant för den speciella App Service

   Under **rekommendationer** är en lista över rekommendationerna för det valda webb programmet och allvarlighets graden för varje rekommendation.

   ![App Services rekommendationer](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Välj en rekommendation om du vill se en beskrivning av rekommendationen och en lista över felaktiga resurser, felfria resurser och ej genomsökta resurser.

   - I kolumnen **skickade utvärderingar** finns en lista över godkända utvärderingar. Allvarlighets graden för de här utvärderingarna är alltid grön.

   - Välj en slutförd utvärdering i listan för en beskrivning av utvärderingen, en lista över felaktiga och felfria resurser samt en lista över ej inskannade resurser. Det finns en flik för resurser som inte är felfria, men listan är alltid tom sedan utvärderingen skickades.

     ![App Service reparation](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Skalningsuppsättningar för virtuella datorer
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


## Beräknings-och program rekommendationer<a name="compute-and-app-recs"></a>
|Resurstyp|Säkerhetspoäng|Rekommendation|Beskrivning|
|----|----|----|----|
|App Service|20|Webb program bör endast vara tillgängliga via HTTPS|Begränsa åtkomsten till webb program endast över HTTPS.|
|App Service|20|Funktionsapp bör endast vara tillgängligt via HTTPS|Begränsa åtkomsten till funktions appar endast över HTTPS.|
|App Service|5|Diagnostikloggar i App Services ska vara aktive rad|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|App Service|10|Fjärrfelsökning bör inaktive ras för webb program|Inaktivera fel sökning för webb program om du inte längre behöver använda den. Fjärrfelsökning kräver att inkommande portar öppnas på en Funktionsapp.|
|App Service|10|Fjärrfelsökning bör inaktive ras för Function-program|Inaktivera fel sökning för Funktionsapp om du inte längre behöver använda den. Fjärrfelsökning kräver att inkommande portar öppnas på en Funktionsapp.|
|App Service|10|Tillåt inte alla (' * ') resurser för att få åtkomst till ditt program| Tillåt inte set of WEBSITE_LOAD_CERTIFICATES-parametern till "". Om du anger parametern till, innebär det att alla certifikat har lästs in i dina webb programs personliga certifikat arkiv. Detta kan leda till missbruk av principen om minsta behörighet eftersom det är osannolikt att platsen behöver åtkomst till alla certifikat vid körning.|
|App Service|20|CORS bör inte tillåta alla resurser åtkomst till dina webb program|Tillåt endast domäner som krävs för att interagera med ditt webb program. Resurs delning mellan ursprung (CORS) bör inte tillåta alla domäner att komma åt ditt webb program.|
|App Service|20|CORS bör inte tillåta alla resurser att komma åt din Funktionsapp| Tillåt endast domäner som krävs för att interagera med ditt funktions program. Resurs delning mellan ursprung (CORS) bör inte tillåta alla domäner att komma åt ditt funktions program.|
|Beräknings resurser (batch)|1|Mått varnings regler ska konfigureras för batch-konton|Konfigurera mått varnings regler för batch-kontot och aktivera mått poolen ta bort Slutför händelser och ta bort start händelser för poolen|
|Beräknings resurser (Service Fabric)|10|Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering|Genomför endast klientautentisering via Azure Active Directory i Service Fabric.|
|Beräknings resurser (Automation-konto)|5|Variabler för Automation-konton ska vara krypterade|Aktivera kryptering av variabel till gångar för Automation-kontot vid lagring av känsliga data.|
|Beräknings resurser (Sök)|5|Granska aktivering av diagnostikloggar för Sök tjänster|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|Beräknings resurser (Service Bus)|5|Diagnostikloggar i Service Bus ska vara aktive rad|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|Beräknings resurser (Stream Analytics)|5|Diagnostikloggar i Azure Stream Analytics ska vara aktive rad|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|Beräknings resurser (batch)|5|Aktivera diagnostikloggar i batch-konton|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|Beräknings resurser (Event Hub)|5|Diagnostikloggar i Händelsehubben måste vara aktive rad|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|Beräknings resurser (Logic Apps)|5|Aktivera diagnostikloggar i Logic Apps|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|Beräknings resurser (Service Fabric)|15|Ange egenskapen ClusterProtectionLevel till EncryptAndSign i Service Fabric|Service Fabric tillhandahåller tre skydds nivåer (ingen, sign och EncryptAndSign) för nod-till-nod-kommunikation med ett primärt kluster certifikat. Ange skydds nivå för att säkerställa att alla nod-till-nod-meddelanden är krypterade och digitalt signerade. |
|Beräknings resurser (Service Bus)|1|Ta bort alla auktoriseringsregler utom RootManageSharedAccessKey från Service Bus namnrymd |Service Bus-klienter bör inte använda en åtkomst princip för namn områdes nivå som ger åtkomst till alla köer och ämnen i ett namn område. Om du vill justera med minsta behörighets säkerhets modell bör du skapa åtkomst principer på enhets nivå för köer och ämnen för att ge åtkomst till endast den specifika entiteten.|
|Beräknings resurser (Event Hub)|1|Alla auktoriseringsregler utom RootManageSharedAccessKey ska tas bort från Event Hub-namnområdet|Event Hub-klienter bör inte använda en åtkomst princip för namn områdes nivå som ger åtkomst till alla köer och ämnen i ett namn område. Om du vill justera med minsta behörighets säkerhets modell bör du skapa åtkomst principer på enhets nivå för köer och ämnen för att ge åtkomst till endast den specifika entiteten.|
|Beräknings resurser (Event Hub)|5|Auktoriseringsregler i Event Hub-entiteten måste definieras|Granska auktoriseringsregler i entiteten Event Hub för att bevilja åtkomst med lägsta privilegier.|
|Dator|50|Installera övervaknings agenten på dina datorer|Installera övervaknings agenten för att aktivera data insamling, uppdaterings genomsökning, bas linje genomsökning och Endpoint Protection på varje dator.|
|Dator|50|Aktivera automatisk etablering och data insamling för dina prenumerationer |Aktivera automatisk etablering och data insamling för datorer i dina prenumerationer för att aktivera insamling av data, uppdaterings genomsökning, bas linje genomsökning och Endpoint Protection på varje dator som läggs till i dina prenumerationer.|
|Dator|40|Lös problem med övervaknings agentens hälso tillstånd på dina datorer|Lös problem med övervaknings agenten på datorerna genom att följa anvisningarna i fel söknings guiden för fullständig Security Center skydd| 
|Dator|40|Lös problem med hälso tillstånd för slut punkts skydd på dina datorer|Lös problem med övervaknings agenten på datorerna genom att följa anvisningarna i fel söknings guiden för fullständig Security Center skydd.|
|Dator|40|Felsök saknade Sök data på dina datorer|Felsök saknade Sök data på virtuella datorer och datorer. Genomsöknings data som saknas på dina datorer resulterar i att säkerhets utvärderingen saknas, till exempel uppdaterings genomsökning, bas linje sökning och en slut punkts skydds lösning som saknas.|
|Dator|40|System uppdateringar bör installeras på dina datorer|Installera system säkerhet och viktiga uppdateringar som saknas för att skydda dina virtuella Windows-och Linux-datorer och datorer
|Dator|15|Lägg till en brandvägg för webbappar| Distribuera en brand vägg för webbaserade program (WAF) för att skydda dina webb program. |
|Dator|40|Uppdatera OS-versionen för dina moln tjänst roller|Uppdatera operativ system versionen för dina moln tjänst roller till den senaste versionen som är tillgänglig för din OS-familj.|
|Dator|35|Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas|Åtgärda sårbarheter i säkerhets konfiguration på dina datorer för att skydda dem från attacker.|
|Dator|35|Åtgärda sårbarheter i säkerhets konfiguration på dina behållare|Åtgärda sårbarheter i säkerhets konfiguration på datorer med Docker installerat för att skydda dem från attacker.|
|Dator|25|Aktivera anpassningsbara program kontroller|Aktivera program kontroll för att styra vilka program som kan köras på dina virtuella datorer som finns i Azure. På så sätt kan dina virtuella datorer öka mot skadlig kod. Security Center använder Machine Learning för att analysera de program som körs på varje virtuell dator och hjälper dig att tillämpa Tillåt-regler med hjälp av den här intelligensen. Den här funktionen fören klar processen att konfigurera och underhålla regler för att tillåta program.|
|Dator|20|Installera Endpoint Protection-lösning på dina datorer|Installera en Endpoint Protection-lösning på dina virtuella datorer för att skydda dem mot hot och sårbarheter.|
|Dator|20|Starta om datorerna för att tillämpa system uppdateringar|Starta om datorerna för att tillämpa system uppdateringar och skydda datorn från sårbarheter.|
|Dator|15|Disk kryptering bör tillämpas på virtuella datorer|Kryptera dina virtuella dator diskar med Azure Disk Encryption både för virtuella Windows-och Linux-datorer. Azure Disk Encryption (ADE) använder branschens standard BitLocker-funktion i Windows och funktionen DM-crypt i Linux för att tillhandahålla operativ system-och data disk kryptering för att skydda och skydda dina data och hjälpa dig att uppfylla organisationens säkerhet och efterlevnad åtaganden i kund Azure Key Vault. När ditt krav på efterlevnad och säkerhet kräver att du krypterar data slut för att sluta använda dina krypterings nycklar, inklusive kryptering av den tillfälliga (lokalt anslutna temporära) disken, använder du Azure Disk Encryption. Som standard är Managed Disks som standard krypterade i vila som standard med hjälp av Azure Storage tjänst kryptering där krypterings nycklarna är Microsoft-hanterade nycklar i Azure. Om detta uppfyller dina krav på efterlevnad och säkerhet kan du utnyttja den förvalda hanterade disk krypteringen för att uppfylla dina krav.|
|Dator|30|Installera en lösning för sårbarhets bedömning på dina virtuella datorer|Installera en lösning för sårbarhets bedömning på dina virtuella datorer|
|Dator|15|Lägg till en brandvägg för webbappar| Distribuera en brand vägg för webbaserade program (WAF) för att skydda dina webb program. |
|Dator|30|Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning|Virtuella datorer som en sårbarhets bedömning från tredje part har distribuerats till utvärderas kontinuerligt mot program-och OS-sårbarheter. När sådana sårbarheter hittas är dessa tillgängliga för mer information som en del av rekommendationen.|
|Dator|30|Installera en lösning för sårbarhets bedömning på dina virtuella datorer|Installera en lösning för sårbarhets bedömning på dina virtuella datorer|
|Dator|1|Virtuella datorer ska migreras till nya AzureRM-resurser|Använd Azure Resource Manager för dina virtuella datorer för att ge säkerhets förbättringar som: starkare åtkomst kontroll (RBAC), bättre granskning, Resource Manager-baserad distribution och styrning, åtkomst till hanterade identiteter, åtkomst till nyckel valv för hemligheter, Azure AD-baserad autentisering och stöd för taggar och resurs grupper för enklare säkerhets hantering. |
|Dator|30|Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning|Virtuella datorer som en sårbarhets bedömning från tredje part har distribuerats till utvärderas kontinuerligt mot program-och OS-sårbarheter. När sådana sårbarheter hittas är dessa tillgängliga för mer information som en del av rekommendationen.|
|Skaluppsättning för virtuella datorer |4|Diagnostikloggar i Virtual Machine Scale Sets ska vara aktive rad|Aktivera loggar och behåll dem i upp till ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte. Detta är användbart när en säkerhets incident inträffar eller nätverket komprometteras.|
|Skaluppsättning för virtuella datorer|35|Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas|Åtgärda sårbarheter i säkerhets konfiguration på dina virtuella datorers skalnings uppsättningar för att skydda dem mot angrepp. |
|Skaluppsättning för virtuella datorer|5|Åtgärda problem med hälso tillstånd för Endpoint Protection på virtuella datorers skalnings uppsättningar|Åtgärda problem med slut punkts skydd på den virtuella datorns skalnings uppsättningar för att skydda dem mot hot och sårbarheter. |
|Skaluppsättning för virtuella datorer|10|Endpoint Protection bör installeras på virtuella datorer|Installera en Endpoint Protection-lösning på den virtuella datorns skalnings uppsättningar för att skydda dem mot hot och sårbarheter. |
|Skaluppsättning för virtuella datorer|40|System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras|Installera system säkerhet och viktiga uppdateringar som saknas för att skydda dina skalnings uppsättningar för virtuella Windows-och Linux-datorer. |
 





## <a name="next-steps"></a>Nästa steg
Mer information om rekommendationer som gäller för andra typer av Azure-resurser finns i följande avsnitt:


* [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
* [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.

