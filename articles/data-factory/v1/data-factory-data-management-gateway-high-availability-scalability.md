---
title: Hög tillgänglighet med gateway för datahantering i Azure Data Factory | Microsoft Docs
description: Den här artikeln förklarar hur du kan skala ut en data management gateway genom att lägga till fler noder och skala upp genom att öka antalet samtidiga jobb som kan köras på en nod.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: b9ec9867e9abd188142067e593c925e3c8acdd0b
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37113348"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Data Management Gateway - hög tillgänglighet och skalbarhet (förhandsversion)
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [lokal IR i](../create-self-hosted-integration-runtime.md). 


Den här artikeln hjälper dig att konfigurera lösning med hög tillgänglighet och skalbarhet med Data Management Gateway / integrering.    

> [!NOTE]
> Den här artikeln förutsätter att du redan är bekant med grunderna för Integration Runtime (tidigare Data Management Gateway). Om du inte är Se [Data Management Gateway](data-factory-data-management-gateway.md).

>**Den här förhandsversionsfunktionen stöds officiellt på Data Management Gateway version 2.12.xxxx.x och senare**. Kontrollera att du använder version 2.12.xxxx.x eller senare. Ladda ned den senaste versionen av Data Management Gateway [här](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Översikt
Du kan associera data management gateway som är installerade på flera lokala datorer med en enda logisk gateway från portalen. Dessa datorer kallas **noder**. Du kan ha upp till **fyra noder** som är associerade med en logisk gateway. Fördelarna med att ha flera noder (lokala datorer med installerad gateway) för en logisk gateway är:  

- Förbättra prestandan för dataförflyttning mellan lokala och molnbaserade datalager.  
- Om en av noderna stängs av av någon anledning, finns andra noder kvar för att flytta data. 
- Om en av noderna måste kopplas från för underhåll, är andra noder fortfarande tillgängliga för att flytta data.

Du kan också konfigurera antalet **samtidiga data movement jobb** som kan köras på en nod kan du utöka möjligheterna för att flytta data mellan lokala och molnbaserade datalager. 

Du kan övervaka status för noderna, vilket hjälper dig att bestämma om du vill lägga till eller ta bort en nod från logisk gateway med Azure portal. 

## <a name="architecture"></a>Arkitektur 
Följande diagram visar Arkitekturöversikt för skalbarhet och tillgänglighet funktion i Data Management Gateway: 

![Data Management Gateway - hög tillgänglighet och skalbarhet](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

En **logisk gateway** är den gateway som du lägger till i en datafabrik i Azure-portalen. Tidigare kunde du koppla bara en lokal Windows-dator med Data Management Gateway installeras med en logisk gateway. Den här lokala gateway-datorn kallas en nod. Nu kan du associera upp till **fyra fysiska noder** med en logisk gateway. En logisk gateway med flera noder kallas en **flernodiga gateway**.  

Alla dessa noder är **active**. Alla kan bearbeta data movement jobb för att flytta data mellan lokala och molnbaserade datalager. En av noderna fungerar som dispatcher- och arbetsroller. Andra noder i grupperna som är arbetsnoder. En **dispatcher** noden hämtar data movement aktiviteter/jobb från Molntjänsten och skickar dem till arbetsnoder (inklusive själva). En **worker** noden kör data movement jobb för att flytta data mellan lokala och molnbaserade datalager. Alla noder är arbetare. Endast en nod kan vara både dispatch- och arbetsroller.    

Du kan vanligtvis börjar med en nod och **skala ut** att lägga till fler noder som befintliga nod(er) översvämmas med data movement belastning. Du kan också **skala upp** data movement möjligheterna för en gateway-noden genom att öka antalet samtidiga jobb som ska tillåtas att köras på noden. Den här funktionen är också tillgängliga med en enda nod gateway (även om funktionen för skalbarhet och tillgänglighet inte är aktiverad). 

En gateway med flera noder behåller data store autentiseringsuppgifter synkroniserade på alla noder. Om det finns ett nod-till-nod anslutningsproblem, kan autentiseringsuppgifterna vara osynkroniserade. När du ställer in autentiseringsuppgifterna för ett lokalt datalager som använder en gateway, sparar autentiseringsuppgifter på dispatcher-/ arbetsnoden. Dispatchern noden synkroniseras med andra arbetsnoder. Den här processen kallas **autentiseringsuppgifter synkronisering**. Kommunikationskanalen mellan noder kan vara **krypterade** av ett offentligt SSL/TLS-certifikat. 

## <a name="set-up-a-multi-node-gateway"></a>Konfigurera en gateway med flera noder
Det här avsnittet förutsätter att du har gått igenom följande två artiklar eller bekant med principerna i de här artiklarna: 

- [Data Management Gateway](data-factory-data-management-gateway.md) – innehåller en detaljerad översikt över gatewayen.
- [Flytta data mellan lokala och molnbaserade datalager](data-factory-move-data-between-onprem-and-cloud.md) – innehåller en genomgång med stegvisa instruktioner för att använda en gateway med en enda nod.  

> [!NOTE]
> Innan du installerar en gateway för datahantering på en lokal Windows-dator, se förutsättningarna som angavs i [huvudsakliga artikeln](data-factory-data-management-gateway.md#prerequisites).

1. I den [genomgången](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), när du skapar en logisk gateway måste du aktivera den **hög tillgänglighet och skalbarhet** funktionen. 

    ![Data Management Gateway - Aktivera hög tillgänglighet och skalbarhet](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. I den **konfigurera** kan du använda antingen **Expressinstallationen** eller **manuell installation** länken för att installera en gateway på den första noden (en lokal Windows-dator).

    ![Data Management Gateway - uttryckliga eller manuell installation](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Om du använder alternativet Expressinstallationen görs nod till nod-kommunikation utan kryptering. Namnet på noden är samma som namnet på datorn. Använda manuell installation om nod till nod-kommunikation måste krypteras eller om du vill ange ett nodnamn valfri. Nodnamn kan inte redigeras senare.
3. Om du väljer **Expressinstallation**
    1. Du ser följande meddelande när gatewayen har installerats:

        ![Data Management Gateway - Expressinstallationen lyckades](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Starta Konfigurationshanteraren för Data Management för gatewayen genom att följa [instruktionerna](data-factory-data-management-gateway.md#configuration-manager). Du ser gatewaynamn, nodnamnet, status, osv.

        ![Data Management Gateway - installationen är klar](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Om du väljer **manuell installation**:
    1. Hämta installationspaketet från Microsoft Download Center, installera gatewayen på datorn köra.
    2. Använd den **autentiseringsnyckeln** från den **konfigurera** sidan för att registrera gatewayen.
    
        ![Data Management Gateway - installationen är klar](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. I den **nya gateway-noden** kan du ange ett eget **namn** till gateway-noden. Som standard är nodnamn samma som namnet på datorn.    

        ![Data Management Gateway - ange namn](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. På nästa sida kan du välja om du vill **Aktivera kryptering för kommunikation från nod till nod**. Klicka på **hoppa över** att inaktivera kryptering (standard).

        ![Data Management Gateway – Aktivera kryptering](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Ändringar av Krypteringsläge stöds bara när du har en enda gateway-noden i logisk gateway. Om du vill ändra läget för kryptering vid en gateway har flera noder, gör du följande: ta bort alla noder utom en nod, ändra Krypteringsläge och Lägg sedan till noderna.
        > 
        > Se [TLS/SSL-certifikatskrav](#tlsssl-certificate-requirements) avsnittet för en lista över kraven för att använda ett TLS/SSL-certifikat. 
    5. När gatewayen har installerats klickar du på Starta Configuration Manager:
    
        ![Manuell installation – starta configuration manager](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. Du ser Data Management Gateway Configuration Manager på noden (den lokala Windows-dator), som visar anslutningsstatus för, **gatewaynamn**, och **nodnamnet**.  

        ![Data Management Gateway - installationen är klar](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Om du etablerar en gateway på en Azure-dator, kan du använda [Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Det här skriptet skapar en logisk gateway, ställer in virtuella datorer med Data Management Gateway-program installerat och registrerar dem med logiska gatewayen. 
6. I Azure-portalen, starta den **Gateway** sidan: 
    1. I data factory startsidan i portalen klickar du på **länkade tjänster**.
    
        ![Datafabrikens startsida](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Välj den **gateway** att se den **Gateway** sidan:
    
        ![Datafabrikens startsida](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Du ser den **Gateway** sidan:   

        ![Gateway med nod-vy](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Klicka på **Lägg till nod** i verktygsfältet för att lägga till en nod logisk gateway. Om du planerar att använda Expressinstallationen, gör du det här steget från den lokala datorn som ska läggas till som en nod till gatewayen. 

    ![Data Management Gateway - noden menyn Lägg till](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Stegen är liknande för att skapa den första noden. Configuration Manager UI kan du ange namnet på noden om du väljer alternativet för manuell installation: 

    ![Configuration Manager - installation andra gateway](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. När gatewayen har installerats på noden, visas verktyget Configuration Manager följande skärm:  

    ![Configuration Manager - installation andra gateway lyckades](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Om du öppnar den **Gateway** sidan i portalen visas två gateway-noder nu: 

    ![Gateway med två noder i portalen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Ta bort en gateway-noden, klicka på **ta bort noden** verktygsfältet och välj den nod som du vill ta bort och klicka sedan på **ta bort** från verktygsfältet. Den här åtgärden tar bort den markerade noden från gruppen. Observera att den här åtgärden inte avinstallera data management gateway-programvaran från en nod (den lokala Windows-dator). Använd **Lägg till eller ta bort program** i Kontrollpanelen på lokalt avinstallation av gateway. När du avinstallerar gateway från noden tas den automatiskt bort i portalen.   

## <a name="upgrade-an-existing-gateway"></a>Uppgradera en befintlig gateway
Du kan uppgradera en befintlig gateway om du vill använda funktionen för hög tillgänglighet och skalbarhet. Den här funktionen fungerar bara med noder som har data management gateway version > = 2.12.xxxx. Du kan se versionen av data management gateway installeras på en dator i den **hjälpa** fliken av Data Management Gateway Configuration Manager. 

1. Uppdatering av gateway på den lokala datorn till den senaste versionen genom att följa genom att ladda ned och köra en MSI-installationspaketet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Se [installation](data-factory-data-management-gateway.md#installation) information.  
2. Gå till Azure-portalen. Starta den **Data Factory sidan** för din datafabrik. Klicka på panelen för länkade tjänster att starta den **länkade tjänster sidan**. Välj en gateway för att starta den **gateway sidan**. Klicka på och aktivera **förhandsgranskningsfunktion** enligt följande bild: 

    ![Data Management Gateway - aktivera förhandsversionsfunktionen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. När förhandsgranskningen är aktiverad i portalen, stänger du alla sidor. Öppna den **gateway sidan** att se det nya preview-användargränssnittet (UI).
 
    ![Data Management Gateway - aktivera förhandsversion av funktionen lyckades](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Data Management Gateway - förhandsversion UI](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Under uppgraderingen är namnet på den första noden namnet på datorn. 
3. Lägg nu till en nod. I den **Gateway** klickar du på **Lägg till nod**.  

    ![Data Management Gateway - noden menyn Lägg till](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Följ anvisningarna i föregående avsnitt för att ställa in noden. 

### <a name="installation-best-practices"></a>Metodtips för installation

- Konfigurera energischema på värddatorn för gatewayen så att datorn inte försättas i viloläge. Om värddatorn i viloläge, svarar gatewayen inte på databegäranden.
- Säkerhetskopiera certifikatet som är associerade med gatewayen.
- Se till att alla noder är av liknande konfiguration (rekommenderas) för bästa prestanda. 
- Lägg till minst två noder för att säkerställa hög tillgänglighet.  

### <a name="tlsssl-certificate-requirements"></a>Krav för TLS/SSL-certifikat
Här följer kraven för TLS/SSL-certifikatet som används för att säkra kommunikationen mellan integration runtime-noder:

- Certifikatet måste vara ett offentligt betrott X509 v3-certifikat. Vi rekommenderar att du använder certifikat som utfärdas av en offentlig (tredje parts) certifikatutfärdare (CA).
- Varje integration runtime-noden måste lita på det här certifikatet, samt den klientdator som kör Autentiseringshanteraren. 
> [!NOTE]
> Autentiseringshanteraren används när du anger autentiseringsuppgifter på ett säkert sätt från Kopieringsguiden / Azure-portalen. Och det kan vara aktiverade från valfri dator i samma nätverk som lokalt / privat datalager.
- Certifikat med jokertecken stöds. Om din FQDN-namn är **node1.domain.contoso.com**, du kan använda ***. domain.contoso.com** som ämnesnamnet för certifikatet.
- SAN-certifikat rekommenderas inte eftersom det sista objektet i Alternativt ämnesnamn används och alla andra kommer att ignoreras på grund av aktuell begränsning. T.ex. du har ett SAN-certifikat vars SAN är **node1.domain.contoso.com** och **node2.domain.contoso.com**, du kan bara använda det här certifikatet på datorn vars FQDN är **node2.domain.contoso.com**.
- Stöder alla nyckelstorlek som stöds av Windows Server 2012 R2 för SSL-certifikat.
- Certifikat med CNG-nycklar stöds inte. Doesrted DoesDoes har inte stöd för certifikat med CNG-nycklar.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Vanliga frågor och svar: När ska jag inte aktivera den här kryptering?
Aktivera kryptering kan lägga till vissa kostnad för din infrastruktur (ägande offentliga certifikat) därför kan du hoppa över aktivering av kryptering i den nedan fall:
- När integration runtime körs på ett betrott nätverk eller ett nätverk med transparent kryptering som IP/sek. Eftersom den här kanalen kommunikationen är endast begränsad i det betrodda nätverket måste du kanske inte behöver ytterligare kryptering.
- När integration runtime körs inte i en produktionsmiljö. Detta kan hjälpa att minska kostnaden för TLS/SSL-certifikat.


## <a name="monitor-a-multi-node-gateway"></a>Övervaka en gateway med flera noder
### <a name="multi-node-gateway-monitoring"></a>Övervakning av flera noder gateway
Du kan visa nästan i realtid ögonblicksbild av Resursanvändning (CPU, minne, network(in/out) osv.) på varje nod tillsammans med status för gateway-noder i Azure-portalen. 

![Data Management Gateway - övervakning av flera noder](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Du kan aktivera **avancerade inställningar** i den **Gateway** sidan för att se avancerade mått som **nätverk**(in/ut), **roll & autentiseringsstatus**, vilket är användbart vid felsökning av problem med gateway och **samtidiga jobb** (körs / begränsa) som kan vara ändrade / ändrade därefter under prestandajustering. Följande tabell innehåller beskrivningar av kolumner i den **Gateway-noder** lista:  

Övervakning av egenskap | Beskrivning
:------------------ | :---------- 
Namn | Namnet på logisk gateway och noder som är associerade med gatewayen.  
Status | Status för logisk gateway och gateway-noderna. Exempel: Online/Offline/Limited/osv. Läs om hur dessa statusar [gatewaystatus](#gateway-status) avsnittet. 
Version | Visar vilken version av logiska gatewayen och varje gateway-noden. Version av den logiska gatewayen bestäms baserat på version av merparten av noder i gruppen. Om det finns noder med olika versioner i logisk gateway-installationen endast noder med samma versionsnummer som funktionen logisk gateway korrekt. Andra är i begränsat läge och måste uppdateras manuellt (endast om automatisk uppdatering misslyckas). 
Ledigt minne | Tillgängligt minne på en gateway-noden. Det här värdet är en nästan i realtid ögonblicksbild. 
Processoranvändning | CPU-utnyttjande på en gateway-noden. Det här värdet är en nästan i realtid ögonblicksbild. 
Nätverk (In/ut) | Nätverksanvändningen för en gateway-noden. Det här värdet är en nästan i realtid ögonblicksbild. 
Samtidiga jobb (körs / begränsa) | Antal jobb eller aktiviteter som körs på varje nod. Det här värdet är en nästan i realtid ögonblicksbild. Gränsen innebär det att maximalt antal samtidiga jobb för varje nod. Det här värdet definieras baserat på storleken på datorn. Du kan höja gränsen att skala upp samtidiga jobbkörning i avancerade scenarier där CPU / minne / nätverk är underutnyttjade, men Tidsgränsen nåddes för aktiviteter. Den här funktionen är också tillgängliga med en enda nod gateway (även om funktionen för skalbarhet och tillgänglighet inte är aktiverad). Mer information finns i [skala överväganden](#scale-considerations) avsnittet. 
Roll | Det finns två typer av roller – Dispatcher- och arbetsroller. Alla noder är arbetare, vilket innebär att de kan användas för att köra jobb. Det finns bara en dispatcher-noden som används för att hämta uppgifter/jobb från cloud services och skicka dem till olika arbetsnoder (inklusive själva). 

![Data Management Gateway - avancerad övervakning av flera noder](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Status för gateway

Följande tabell innehåller olika statusar av en **gateway-noden**: 

Status  | Kommentarer/scenarier
:------- | :------------------
Online | Noden är ansluten till Data Factory-tjänsten.
Offline | Noden är offline.
Uppgraderar | Noden uppdateras automatiskt.
Begränsad | På grund av anslutningsproblem. Kanske på grund av HTTP-port 8050 problemet, service bus-anslutningsproblem eller synkroniseringsproblem för autentiseringsuppgifter. 
Inaktiv | Noden är i en konfiguration som skiljer sig från konfigurationen av andra majoritet noder.<br/><br/> En nod kan vara inaktiv när den inte kan ansluta till andra noder. 


Följande tabell innehåller olika statusar av en **logisk gateway**. Status för gateway beror på status för gateway-noderna. 

Status | Kommentarer
:----- | :-------
Måste registreras | Någon nod är ännu registrerad på den här logiska gatewayen
Online | Gateway-noderna är online
Offline | Någon nod i onlinestatus.
Begränsad | Inte alla noder i den här gatewayen är i felfritt tillstånd. Denna status är en varning om att en eller flera noder kanske inte är tillgänglig! <br/><br/>Kan bero på credential synkroniseringsproblem på dispatcher-/ arbetsnoden. 

### <a name="pipeline-activities-monitoring"></a>Pipeline / aktiviteter övervakning
Azure-portalen ger en pipeline övervakningsupplevelse med detaljerade noden information. Den visar till exempel vilka aktiviteter som kördes på vilken nod. Den här informationen kan hjälpa dig att förstå prestandaproblem på en viss nod, säger på grund av nätverksbegränsning. 

![Data Management Gateway - flera nodövervakning för pipelines](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Data Management Gateway - pipeline-information](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Skala överväganden

### <a name="scale-out"></a>Skala ut
När den **tillgängligt minne är lågt** och **processoranvändningen är hög**, lägga till en ny nod hjälper till att skala ut belastningen över datorer. Om aktiviteter misslyckas på grund av timeout eller gateway-noden är offline kan det vara bra om du lägger till en nod till gatewayen.
 
### <a name="scale-up"></a>Skala upp
När tillgängligt minne och CPU används inte bra, men outnyttjad kapacitet är 0, bör du skala upp genom att öka antalet samtidiga jobb som kan köras på en nod. Du kanske också vill skala upp när aktiviteter Tidsgränsen nåddes för eftersom gatewayen är överbelastad. I följande bild visas kan du öka maximal kapacitet för en nod. Vi rekommenderar att du fördubblar möjligheten att börja med.  

![Data Management Gateway - skala överväganden](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Kända problem/större ändringar

- Du kan för närvarande kan ha upp till fyra fysiska gateway-noder för en enskild logisk gateway. Om du behöver fler än fyra noder av prestandaskäl kan du skicka ett e- [ DMGHelp@microsoft.com ](mailto:DMGHelp@microsoft.com).
- En gateway-noden kan inte registrera med nyckeln från en annan logisk gateway att växla från den aktuella logisk gatewayen. Om du vill Omregistrera, avinstallation av gateway från noden, installera om gatewayen och registrera den med autentiseringsnyckeln för andra logiska gatewayen. 
- Om HTTP-proxyn krävs för alla gateway-noder, som proxyn i diahost.exe.config och diawp.exe.config och använda server manager för att kontrollera att alla noder har samma diahost.exe.config och diawip.exe.config. Se [konfigurera proxyinställningar](data-factory-data-management-gateway.md#configure-proxy-server-settings) information. 
- Ta bort alla noder i portalen utom ett om du vill ändra Krypteringsläge för kommunikation från nod till nod i Gateway Configuration Manager. Sedan kan lägga till noder tillbaka när du har ändrat Krypteringsläge.
- Använd ett officiella SSL-certifikat om du vill kryptera kommunikation från nod till nod-kanalen. Självsignerat certifikat kan orsaka problem med nätverksanslutningen när samma certifikat inte får vara betrodd i certifiering lista över på andra datorer. 
- Du kan inte registrera en gateway-noden till en logisk gateway när noden versionen är lägre än versionen som logisk gateway. Ta bort alla noder i en logisk gateway från portalen så att du kan registrera en lägre version node(downgrade) den. Om du tar bort alla noder i en logisk gateway manuellt installera och registrera nya noder till den logiska gatewayen. Snabbinstallation av stöds inte i det här fallet.
- Du kan inte använda Snabbinstallation för att installera noder till en befintlig logisk gateway som fortfarande använder molnautentiseringsuppgifter. Du kan kontrollera var autentiseringsuppgifterna lagras från Gateway Configuration Manager på fliken Inställningar.
- Du kan inte använda Snabbinstallation för att installera noder till en befintlig logisk gateway som har nod till nod-kryptering är aktiverat. Eftersom inställningen Krypteringsläge måste du manuellt lägga till certifikat, är Snabbinstallation inga fler ett alternativ. 
- För en filkopia från en lokal miljö, bör du inte använda \\localhost eller C:\files längre sedan localhost eller den lokala enheten kanske inte är tillgängliga via alla noder. Använd i stället \\ServerName\files att ange filernas plats.


## <a name="rolling-back-from-the-preview"></a>Återställa från förhandsversionen 
Ta bort alla noder men en nod om du vill återställa från förhandsversionen. Det spelar vilka noder som du ta bort, men se till att du har minst en nod i en logisk gateway. Du kan ta bort en nod genom att avinstallera gateway på datorn eller med hjälp av Azure portal. I Azure-portalen i den **Data Factory** klickar du på länkade tjänster för att starta den **länkade tjänster** sidan. Välj en gateway för att starta den **Gateway** sidan. Du kan se de noder som är associerade med gatewayen i Gateway-sidan. Sidan kan du ta bort en nod från gatewayen.
 
När du tar bort, klicka på **förhandsversionsfunktioner** i samma Azure-portalsidan och inaktivera funktionen för förhandsgranskning. Du har återställt din gateway till en nod GA (allmän tillgänglighet) gateway.


## <a name="next-steps"></a>Nästa steg
Granska följande artiklar:
- [Data Management Gateway](data-factory-data-management-gateway.md) – innehåller en detaljerad översikt över gatewayen.
- [Flytta data mellan lokala och molnbaserade datalager](data-factory-move-data-between-onprem-and-cloud.md) – innehåller en genomgång med stegvisa instruktioner för att använda en gateway med en enda nod. 
