---
title: "Hög tillgänglighet med data management gateway i Azure Data Factory | Microsoft Docs"
description: "Den här artikeln förklarar hur du kan skala ut en data management gateway genom att lägga till fler noder och skala upp genom att öka antalet samtidiga jobb som kan köras på en nod."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: fda3c7a9a369eec1b9033ee7077a5f3770647c9f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Data Management Gateway - hög tillgänglighet och skalbarhet (förhandsgranskning)
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [egenvärdbaserat integrering körning i version 2](../create-self-hosted-integration-runtime.md). 


Den här artikeln hjälper dig att konfigurera lösning för hög tillgänglighet och skalbarhet med Data Management Gateway / integrering.    

> [!NOTE]
> Den här artikeln förutsätter att du känner till grunderna för integrering körningen (tidigare Data Management Gateway). Om du inte är finns [Data Management Gateway](data-factory-data-management-gateway.md).

>**Den här förhandsgranskningsfunktionen stöds officiellt i Data Management Gateway version 2.12.xxxx.x och senare**. Kontrollera att du använder version 2.12.xxxx.x eller senare. Hämta den senaste versionen av Data Management Gateway [här](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Översikt
Du kan associera data management gateway som är installerade på flera lokala datorer med en enda logiska gateway från portalen. Dessa datorer kallas **noder**. Du kan ha upp till **fyra noder** som är associerade med en logisk gateway. Fördelarna med flera noder (lokala datorer med installerade-gateway) för en logisk gateway är:  

- Förbättra prestanda vid flytt av data mellan lokala och moln datalager.  
- Andra noder är tillgängliga för att flytta data om en av noderna kraschar av någon anledning. 
- Om en av noderna måste vara offline för underhåll, är andra noder tillgängliga för att flytta data.

Du kan också konfigurera antalet **samtidiga data movement jobb** som körs på en nod kan du utöka möjligheterna för att flytta data mellan lokalt och i molnet datalager. 

Du kan övervaka statusen för dessa noder som hjälper dig att bestämma om du vill lägga till eller ta bort en nod från den logiska gatewayen med Azure-portalen. 

## <a name="architecture"></a>Arkitektur 
Följande diagram ger Arkitekturöversikt för skalbarhet och tillgänglighet funktion i Data Management Gateway: 

![Data Management Gateway - hög tillgänglighet och skalbarhet](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

En **logiska gateway** är den gateway som du lägger till en datafabrik i Azure-portalen. Tidigare, kan du koppla bara en lokal Windows-dator med Data Management Gateway installeras med en logisk gateway. Det lokala gateway-datorn kallas en nod. Nu kan du koppla upp till **fyra fysiska noder** med en logisk gateway. En logisk gateway med flera noder kallas en **med flera noder gateway**.  

Alla noder är **active**. Alla kan bearbeta data movement jobb för att flytta data mellan lokalt och i molnet datalager. En av noderna fungerar som dispatcher- och arbetsroller. Andra noder i grupperna är arbetsnoderna. En **dispatcher** hämtar data movement uppgifter/jobb från Molntjänsten och skickar dem till arbetsnoder (inklusive sig själv). En **worker** noden kör data movement jobb för att flytta data mellan lokalt och i molnet datalager. Alla noder är arbetare. Endast en nod kan vara både sändnings- och arbetsroller.    

Normalt börjar med en nod och **skala ut** att lägga till fler noder som de befintliga noderna är för många med flytt datainläsning. Du kan också **skala upp** data movement möjligheterna för en gateway-noden genom att öka antalet samtidiga jobb som tillåts att köras på noden. Den här funktionen finns också med en enda nod gateway (även om funktionen skalbarhet och tillgänglighet inte är aktiverad). 

En gateway med flera noder behåller autentiseringsuppgifterna för datalager synkroniserade på alla noder. Om en nod till nod anslutningsproblem kanske autentiseringsuppgifterna inte synkroniserade. När du ställer in autentiseringsuppgifterna för ett lokalt datalager som använder en gateway sparar autentiseringsuppgifter på dispatcher/arbetsnoden. Dispatcher nod synkroniseras med andra arbetsnoderna. Den här processen kallas **autentiseringsuppgifter sync**. Kommunikationskanalen mellan noder kan vara **krypterade** av ett offentligt SSL/TLS-certifikat. 

## <a name="set-up-a-multi-node-gateway"></a>Konfigurera en gateway med flera noder
Det här avsnittet förutsätter att du har gått igenom följande två artiklar eller bekant med principerna i de här artiklarna: 

- [Data Management Gateway](data-factory-data-management-gateway.md) -ger en detaljerad översikt över gatewayen.
- [Flytta data mellan lokalt och i molnet datalager](data-factory-move-data-between-onprem-and-cloud.md) -innehåller en genomgång med stegvisa instruktioner för att använda en gateway med en enda nod.  

> [!NOTE]
> Innan du installerar en data management gateway på en lokal Windows-dator, se förutsättningarna som angavs i [huvudsakliga artikeln](data-factory-data-management-gateway.md#prerequisites).

1. I den [genomgången](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), när du skapar en logisk gateway måste aktivera den **hög tillgänglighet och skalbarhet** funktion. 

    ![Data Management Gateway - Aktivera hög tillgänglighet och skalbarhet](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. I den **konfigurera** använder antingen **snabbinstallationen** eller **manuell installation** länken för att installera en gateway på den första noden (ett lokalt Windows-dator).

    ![Data Management Gateway - uttrycklig eller manuell installation](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Om du använder alternativet Expressinstallationen görs kommunikationen nod till nod utan kryptering. Nodnamnet är samma som namnet på datorn. Använda manuell installation om nod nod-kommunikation måste krypteras eller om du vill ange ett nodnamn du väljer. Nod-namn kan inte ändras senare.
3. Om du väljer **Expressinstallation**
    1. Följande meddelande visas när gatewayen har installerats:

        ![Data Management Gateway - Expressinstallationen lyckades](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Starta Data Management Configuration Manager för en gateway genom att följa [instruktionerna](data-factory-data-management-gateway.md#configuration-manager). Du kan se gateway-namnet, namn, status, osv.

        ![Data Management Gateway - installationen är klar](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Om du väljer **manuell installation**:
    1. Hämta installationspaketet från Microsoft Download Center, kör den för installation av gateway på din dator.
    2. Använd den **autentiseringsnyckel** från den **konfigurera** att registrera gatewayen.
    
        ![Data Management Gateway - installationen är klar](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. I den **ny gateway-noden** kan du ange ett eget **namn** till gateway-noden. Nodnamnet är samma som namnet på datorn.    

        ![Data Management Gateway - ange namn](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. På nästa sida kan du välja om du vill **Aktivera kryptering för kommunikation från nod till nod**. Klicka på **hoppa över** att inaktivera kryptering (standard).

        ![Data Management Gateway - Aktivera kryptering](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Ändra läget för kryptering stöds endast när du har en enda gateway-nod i den logiska gatewayen. Gör följande för att ändra Krypteringsläge när en gateway har flera noder: ta bort alla noder utom en nod, ändra läget för kryptering och Lägg sedan till noderna.
        > 
        > Se [kraven för TLS/SSL-certifikat](#tlsssl-certificate-requirements) avsnittet för en lista över kraven för att använda TLS/SSL-certifikat. 
    5. När gatewayen har installerats klickar du på Starta Configuration Manager:
    
        ![Manuell installation – starta configuration manager](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. Data Management Gateway Configuration Manager hittar du på noden (lokala Windows-dator), som visar anslutningsstatus, **gatewaynamnet**, och **nodnamnet**.  

        ![Data Management Gateway - installationen är klar](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Om du etablerar en gateway på en Azure VM, kan du använda [Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Det här skriptet skapar en logisk gateway, ställer in virtuella datorer med Data Management Gateway-programvaran och registrerar dem med logiska gatewayen. 
6. I Azure-portalen, starta den **Gateway** sidan: 
    1. Klicka på data factory startsidan i portalen, **länkade tjänster**.
    
        ![Datafabrikens startsida](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Välj den **gateway** att se den **Gateway** sidan:
    
        ![Datafabrikens startsida](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Du ser den **Gateway** sidan:   

        ![Gateway med enskild nod vy](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Klicka på **Lägg till nod** i verktygsfältet för att lägga till en nod till den logiska gatewayen. Om du planerar att använda Expressinstallation, gör du det här steget från den lokala datorn som ska läggas till som en nod till gateway. 

    ![Data Management Gateway - noden menyn Lägg till](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Stegen är liknande för att skapa den första noden. Configuration Manager UI kan du ange namnet på noden om du väljer alternativet för manuell installation: 

    ![Configuration Manager - installationen andra gateway](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. När gatewayen har installerats på noden, visas följande skärm verktyget Configuration Manager:  

    ![Configuration Manager - installationen andra gateway lyckades](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Om du öppnar den **Gateway** sida i portalen visas två gateway-noder nu: 

    ![Gateway med två noder i portalen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Ta bort en gateway-noden, klicka på **ta bort nod** i verktygsfältet, markerar du den nod som du vill ta bort och klicka sedan på **ta bort** från verktygsfältet. Den här åtgärden tar bort den markerade noden från gruppen. Observera att den här åtgärden inte avinstallerar data management gateway-programvara från noden (lokala Windows-dator). Använd **Lägg till eller ta bort program** på Kontrollpanelen på lokalt avinstallera gateway. När du avinstallerar gateway från noden bort den automatiskt i portalen.   

## <a name="upgrade-an-existing-gateway"></a>Uppgradera en befintlig gateway
Du kan uppgradera en befintlig gateway för att använda funktionen för hög tillgänglighet och skalbarhet. Den här funktionen fungerar bara med noder som har data management gateway version > = 2.12.xxxx. Du kan se versionen av data management gateway installeras på en dator i den **hjälp** fliken av Konfigurationshanteraren för Data Management Gateway. 

1. Uppdatera gatewayen på den lokala datorn till den senaste versionen genom att följa genom att hämta och kör en MSI-installationspaketet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Se [installation](data-factory-data-management-gateway.md#installation) information.  
2. Gå till Azure-portalen. Starta den **Data Factory sidan** för din data factory. Klicka på rutan för länkade tjänster att starta den **länkade tjänster**. Välj en gateway för att starta den **gateway sidan**. Klicka på och aktivera **förhandsgranskningsfunktion** som visas i följande bild: 

    ![Data Management Gateway - aktivera funktionen för förhandsgranskning](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. När förhandsgranskningen är aktiverad i portalen, stänger du alla sidor. Öppna den **gateway sidan** att se det nya preview-användargränssnittet (UI).
 
    ![Data Management Gateway - aktivera preview funktionen lyckades](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Data Management Gateway - Förhandsgranska UI](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Under uppgraderingen är namnet på den första noden namnet på datorn. 
3. Lägg till en nod. I den **Gateway** klickar du på **Lägg till nod**.  

    ![Data Management Gateway - noden menyn Lägg till](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Följ instruktionerna i det föregående avsnittet för att konfigurera noden. 

### <a name="installation-best-practices"></a>Metodtips för installation

- Konfigurera energischema på värddatorn för gateway så att datorn inte försättas i viloläge. Om värddatorn i viloläge, svarar gatewayen inte på databegäranden.
- Säkerhetskopiera certifikatet som är kopplade till gatewayen.
- Se till att alla noder har liknande konfiguration (rekommenderas) för bästa prestanda. 
- Lägg till minst två noder för att säkerställa hög tillgänglighet.  

### <a name="tlsssl-certificate-requirements"></a>Krav för TLS/SSL-certifikat
Här följer kraven för TLS/SSL-certifikatet som används för att skydda kommunikationen mellan integration runtime noder:

- Certifikatet måste vara offentligt betrodda X509 v3-certifikat. Vi rekommenderar att du använder certifikat som utfärdas av en offentlig (tredje parts) certifikatutfärdare (CA).
- Varje integration runtime-nod måste ha förtroende för certifikatet, samt den klientdator som kör autentiseringsuppgifter manager-program. 
> [!NOTE]
> Autentiseringsuppgifter manager används vid på ett säkert sätt ställa in autentiseringsuppgifter från guiden Kopiera / Azure-portalen. Och det kan vara Eldad från en dator i samma nätverk som lokal / privat datalagret.
- Certifikat med jokertecken stöds. Om din FQDN-namn är **node1.domain.contoso.com**, du kan använda ***. domain.contoso.com** som ämnesnamnet för certifikatet.
- SAN-certifikat rekommenderas inte eftersom det sista objektet i Alternativt ämnesnamn används och alla andra kommer att ignoreras på grund av aktuell begränsning. T.ex. du har ett SAN-certifikat vars SAN är **node1.domain.contoso.com** och **node2.domain.contoso.com**, du kan bara använda det här certifikatet på datorn vars FQDN är **node2.domain.contoso.com**.
- Stöder alla nyckelstorlek som stöds av Windows Server 2012 R2 för SSL-certifikat.
- Certifikat med CNG nycklar stöds inte. Doesrted DoesDoes har inte stöd för certifikat med CNG-nycklar.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Vanliga frågor och svar: När kan jag inte aktiverar krypteringsnycklar?
Aktivera kryptering kan lägga till vissa kostnaden för infrastrukturen (ägande offentliga certifikat) därför kan du hoppa över att aktivera kryptering i den nedan fall:
- När integration runtime körs på ett betrott nätverk eller ett nätverk med transparent kryptering som IP/sek. Eftersom den här kanalen kommunikationen är endast begränsad i det betrodda nätverket, kanske du inte behöver ytterligare kryptering.
- När integration runtime inte körs i en produktionsmiljö. Detta kan hjälpa att minska kostnaden för TLS/SSL-certifikat.


## <a name="monitor-a-multi-node-gateway"></a>Övervaka en gateway med flera noder
### <a name="multi-node-gateway-monitoring"></a>Övervakning av flera noder gateway
Du kan visa nära realtid ögonblicksbild av resursutnyttjande (processor, minne, network(in/out), etc.) på varje nod tillsammans med statusen för gateway-noder i Azure-portalen. 

![Data Management Gateway - övervakning av flera nod](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Du kan aktivera **avancerade inställningar** i den **Gateway** sidan för att visa avancerade mått som **nätverk**(in/ut), **roll & autentiseringsuppgifter Status**, vilket är användbart vid felsökning av problem med gateway och **samtidiga jobb** (kör / begränsa) som kan vara ändrade / ändrade därefter under prestandajustering. Följande tabell innehåller beskrivningar av kolumner i den **Gateway-noder** lista:  

Övervakning av egenskapen | Beskrivning
:------------------ | :---------- 
Namn | Namnet på den logiska gateway och noder som är kopplade till gatewayen.  
Status | Status för logiska gatewayen och gateway-noder. Exempel: Online/Offline/begränsad/etc. Information om dessa statusar finns [gatewaystatusen](#gateway-status) avsnitt. 
Version | Visar vilken version av den logiska gatewayen och varje gateway-noden. Versionen av den logiska gatewayen bestäms baserat på version av merparten av noder i gruppen. Om det finns noder med olika versioner i logiska gateway-inställningarna endast noder med samma versionsnummer som funktionen logiska gateway korrekt. Andra är i begränsat läge och måste uppdateras manuellt (bara om automatisk uppdatering misslyckas). 
Tillgängligt minne | Tillgängligt minne på en gateway-noden. Det här värdet är en nära realtid ögonblicksbild. 
CPU-användning | Processoranvändningen för en gateway-nod. Det här värdet är en nära realtid ögonblicksbild. 
Nätverk (In/ut) | Nätverksanvändning för en gateway-nod. Det här värdet är en nära realtid ögonblicksbild. 
Samtidiga jobb (kör / begränsa) | Antal jobb eller aktiviteter som körs på varje nod. Det här värdet är en nära realtid ögonblicksbild. Gränsen innebär högsta samtidiga jobb för varje nod. Det här värdet definieras baserat på storleken på datorn. Du kan höja gränsen att skala upp samtidiga jobbkörningen i avancerade scenarier där CPU / minne / nätverket är outnyttjad men aktiviteter uppnådd tidsgräns. Den här funktionen finns också med en enda nod gateway (även om funktionen skalbarhet och tillgänglighet inte är aktiverad). Mer information finns i [skala överväganden](#scale-considerations) avsnitt. 
Roll | Det finns två typer av roller – Dispatcher- och arbetsroller. Alla noder är arbetare, vilket innebär att de kan användas för att köra jobb. Det finns bara en dispatcher nod som används för att hämta uppgifter/jobb från molntjänster och skicka dem till olika arbetsnoder (inklusive sig själv). 

![Data Management Gateway - avancerad övervakning av flera nod](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Gateway-status

Följande tabell innehåller olika statusar av en **gateway-noden**: 

Status  | Kommentarer-scenarier
:------- | :------------------
Online | Noden är ansluten till Data Factory-tjänsten.
Offline | Noden är offline.
Uppgradera | Noden håller på att uppdateras automatiskt.
Begränsad | På grund av anslutningsproblem. Kan bero på http-porten 8050 problemet, service bus-anslutningsproblem eller autentiseringsuppgifter synkroniseringsproblem. 
Inaktiva | Noden är i en konfiguration som skiljer sig från konfigurationen av andra majoritet noder.<br/><br/> En nod kan vara inaktiv när den inte kan ansluta till andra noder. 


Följande tabell innehåller olika statusar av en **logiska gateway**. Gatewayens status beror på statusen för gateway-noder. 

Status | Kommentarer
:----- | :-------
Måste registreras | Ingen nod är ännu registrerad på den här logiska gatewayen
Online | Gateway-noder är online
Offline | Ingen nod i online-status.
Begränsad | Inte alla noder i den här gatewayen är i felfritt tillstånd. Denna status är en varning om att vissa noden kanske inte igång! <br/><br/>Kan bero på autentiseringsuppgifter synkroniseringsproblem på dispatcher/arbetsnoden. 

### <a name="pipeline-activities-monitoring"></a>Pipeline / aktiviteter övervakning
Azure-portalen innehåller en pipeline övervakning erfarenhet av noden detaljerad information. Till exempel visar vilka aktiviteter som kördes på vilken nod. Den här informationen kan vara lättare att förstå prestandaproblem på en viss nod, säg på grund av nätverksbegränsning. 

![Data Management Gateway - flera nodövervakning för pipelines](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Data Management Gateway - pipeline-information](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Skala överväganden

### <a name="scale-out"></a>Skala ut
När den **finns lite tillgängligt minne** och **processoranvändningen är hög**, lägga till en ny nod hjälper till att skala ut belastningen över datorer. Om aktiviteter misslyckas på grund av timeout eller gateway-noden som är offline kan det vara bra om du lägger till en nod till gateway.
 
### <a name="scale-up"></a>Skala upp
När tillgängligt minne och processor används inte väl, men ledig kapacitet är 0, bör du skalar upp genom att öka antalet samtidiga jobb som kan köras på en nod. Du kanske också vill skala upp när aktiviteter timeout inträffar eftersom gatewayen är överbelastad. Du kan öka den maximala kapaciteten för en nod som visas i följande bild. Vi rekommenderar att du vilket fördubblar börja med.  

![Data Management Gateway - skala överväganden](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Kända problem/senaste ändringar

- Du kan för närvarande kan ha upp till fyra fysiska gateway-noder för en enskild logisk gateway. Om du behöver fler än fyra noder av prestandaskäl kan du skicka ett e-postmeddelande till [ DMGHelp@microsoft.com ](mailto:DMGHelp@microsoft.com).
- Du kan inte registrera en gateway-noden med autentiseringsnyckeln från en annan logisk gateway växla från aktuell logiska gateway. Om du vill registrera, avinstallera gateway från noden, installerar du om gatewayen och registrera den med autentiseringsnyckeln för andra logiska gateway. 
- Om HTTP-proxyn krävs för alla gateway-noder, som proxyn i diahost.exe.config och diawp.exe.config och använda server manager för att säkerställa att alla noder som har samma diahost.exe.config och diawip.exe.config. Se [konfigurera proxyinställningar](data-factory-data-management-gateway.md#configure-proxy-server-settings) information. 
- Ändra Krypteringsläge för kommunikation från nod till nod i Gateway Configuration Manager genom att ta bort alla noder i portalen utom ett. Sedan kan lägga till noder tillbaka när du har ändrat Krypteringsläge.
- Använd ett officiella SSL-certifikat om du vill kryptera nod till nod kommunikationskanalen. Självsignerat certifikat kan orsaka problem med nätverksanslutningen när samma certifikat inte kan vara betrodd i certifiera lista över på andra datorer. 
- Du kan inte registrera en gateway-noden till en logisk gateway när noden-versionen är lägre än den logiska gatewayversionen. Ta bort alla noder i logiska gatewayen från portalen så att du kan registrera en lägre version node(downgrade) den. Om du tar bort alla noder i en logisk gateway manuellt installera och registrera nya noder till den logiska gatewayen. Expressinstallationen stöds inte i det här fallet.
- Du kan inte använda Expressinstallation installera noder för en befintlig logisk gateway som fortfarande använder autentiseringsuppgifter i molnet. Du kan kontrollera där autentiseringsuppgifterna som lagras i Gateway Configuration Manager på fliken Inställningar.
- Du kan inte använda Expressinstallation installera noder för en befintlig logisk gateway som har en nod till nod kryptering aktiverat. Ange läget encryption måste du manuellt lägga till certifikat, är Snabbinstallation ingen mer ett alternativ. 
- För en filkopia från lokala miljö bör du inte använda \\localhost eller C:\files längre sedan localhost eller en lokal enhet kanske inte är tillgängligt via alla noder. Använd i stället \\ServerName\files att ange filernas plats.


## <a name="rolling-back-from-the-preview"></a>Återställer från förhandsversionen 
Ta bort alla noder, men en nod om du vill återställa från förhandsversionen. Det spelar ingen roll vilken noder du ta bort, men se till att du har minst en nod i logiska gatewayen. Du kan ta bort en nod genom att avinstallera gateway på datorn eller med hjälp av Azure portal. I Azure-portalen i den **Datafabriken** klickar du på länkade tjänster att starta den **länkade tjänster** sidan. Välj en gateway för att starta den **Gateway** sidan. På sidan Gateway ser du de noder som är kopplade till gatewayen. Sidan kan du ta bort en nod från gateway.
 
När du tar bort, klickar du på **förhandsgranskningsfunktioner** i samma Azure Portalsida och inaktivera funktionen för förhandsgranskning. Du har återställt din gateway till en nod GA (allmän tillgänglighet) gateway.


## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:
- [Data Management Gateway](data-factory-data-management-gateway.md) -ger en detaljerad översikt över gatewayen.
- [Flytta data mellan lokalt och i molnet datalager](data-factory-move-data-between-onprem-and-cloud.md) -innehåller en genomgång med stegvisa instruktioner för att använda en gateway med en enda nod. 
