---
title: Hög tillgänglighet med datahanteringsgateway i Azure Data Factory
description: I den här artikeln beskrivs hur du kan skala ut en datahanteringsgateway genom att lägga till fler noder och skala upp genom att öka antalet samtidiga jobb som kan köras på en nod.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 4ee89f4bba70bb5e81eef21247d556f65a2a1f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065195"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Data Management Gateway - hög tillgänglighet och skalbarhet (preview)
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [självvärderade integrationskörning i](../create-self-hosted-integration-runtime.md). 


Den här artikeln hjälper dig att konfigurera lösning med hög tillgänglighet och skalbarhet med Data Management Gateway/Integration.    

> [!NOTE]
> Den här artikeln förutsätter att du redan är bekant med grunderna i Integration Runtime (Earlier Data Management Gateway). Om du inte är det läser du [Data Management Gateway](data-factory-data-management-gateway.md).
> 
> **Den här förhandsgranskningsfunktionen stöds officiellt på Data Management Gateway version 2.12.xxxx.x och högre**. Se till att du använder version 2.12.xxxx.x eller högre. Ladda ner den senaste versionen av Data Management Gateway [här](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Översikt
Du kan associera datahanteringsgateways som är installerade på flera lokala datorer med en enda logisk gateway från portalen. Dessa maskiner kallas **noder**. Du kan ha upp till **fyra noder** som är associerade med en logisk gateway. Fördelarna med att ha flera noder (lokala datorer med gateway installerad) för en logisk gateway är:  

- Förbättra prestanda för dataförflyttning mellan lokala datalager och molndatalager.  
- Om en av noderna går ned av någon anledning är andra noder fortfarande tillgängliga för att flytta data. 
- Om en av noderna måste tas offline för underhåll är andra noder fortfarande tillgängliga för att flytta data.

Du kan också konfigurera antalet **samtidiga dataförflyttningsjobb** som kan köras på en nod för att skala upp möjligheten att flytta data mellan lokala datalager och molndatalager. 

Med hjälp av Azure-portalen kan du övervaka status för dessa noder, vilket hjälper dig att bestämma om du vill lägga till eller ta bort en nod från den logiska gatewayen. 

## <a name="architecture"></a>Arkitektur 
I följande diagram visas arkitekturöversikt över skalbarhet och tillgänglighet i datahanteringsgatewayen: 

![Data Management Gateway - Hög tillgänglighet och skalbarhet](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

En **logisk gateway** är den gateway som du lägger till i en datafabrik i Azure-portalen. Tidigare kunde du bara associera en lokal Windows-dator med Data Management Gateway installerad med en logisk gateway. Den här lokala gateway-datorn kallas för en nod. Nu kan du associera upp till **fyra fysiska noder** med en logisk gateway. En logisk gateway med flera noder kallas en **multinodgateway**.  

Alla dessa noder är **aktiva**. De kan alla bearbeta dataförflyttningsjobb för att flytta data mellan lokala datalager och molndatalager. En av noderna fungerar som både avsändare och arbetare. Andra noder i grupperna är arbetarnoder. En **avsändare** nod hämtar data förflyttningsuppgifter / jobb från molntjänsten och skickar dem till arbetsnoder (inklusive sig själv). En **arbetarnod** kör dataförflyttningsjobb för att flytta data mellan lokala datalager och molndatalager. Alla noder är arbetare. Endast en nod kan vara både avsändning och arbetare.    

Du kan vanligtvis börja med en nod och **skala ut** för att lägga till fler noder eftersom de befintliga noderna är överbelastade med dataförflyttningsbelastningen. Du kan också **skala upp** dataförflyttningsfunktionen för en gateway-nod genom att öka antalet samtidiga jobb som tillåts köras på noden. Den här funktionen är också tillgänglig med en gateway med en nod (även när skalbarhets- och tillgänglighetsfunktionen inte är aktiverad). 

En gateway med flera noder håller autentiseringsuppgifterna för datalagring synkroniserade mellan alla noder. Om det finns ett anslutningsproblem mellan nod till nod kan autentiseringsuppgifterna vara osynkroniserade. När du anger autentiseringsuppgifter för ett lokalt datalager som använder en gateway sparas autentiseringsuppgifter på avsändarens/arbetarnoden. Sändarnoden synkroniseras med andra arbetsnoder. Den här processen kallas **synkronisering av autentiseringsuppgifter**. Kommunikationskanalen mellan noder kan **krypteras** av ett offentligt SSL/TLS-certifikat. 

## <a name="set-up-a-multi-node-gateway"></a>Konfigurera en gateway med flera nod
Det här avsnittet förutsätter att du har gått igenom följande två artiklar eller förtrogen med begrepp i dessa artiklar: 

- [Data Management Gateway](data-factory-data-management-gateway.md) - ger en detaljerad översikt över gatewayen.
- [Flytta data mellan lokala datalager och molndatalager](data-factory-move-data-between-onprem-and-cloud.md) – innehåller en genomgång med steg-för-steg-instruktioner för att använda en gateway med en enda nod.  

> [!NOTE]
> Innan du installerar en datahanteringsgateway på en lokal Windows-dator läser du förutsättningarna i [huvudartikeln](data-factory-data-management-gateway.md#prerequisites).

1. Aktivera [walkthrough](data-factory-move-data-between-onprem-and-cloud.md#create-gateway)funktionen **hög tillgänglighet & skalbarhet** när du skapar en logisk gateway. 

    ![Data Management Gateway - möjliggör hög tillgänglighet och skalbarhet](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. På sidan **Konfigurera** använder du länken **Express setup** eller **Manuell installation** för att installera en gateway på den första noden (en lokal Windows-dator).

    ![Data Management Gateway - express eller manuell installation](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Om du använder alternativet för expressinställningar görs nod-till-nodkommunikationen utan kryptering. Nodnamnet är samma som datornamnet. Använd manuell installation om nodnodskommunikationen måste krypteras eller om du vill ange ett nodnamn som du väljer. Nodnamn kan inte redigeras senare.
3. Om du väljer **expressinställning**
    1. Följande meddelande visas när gatewayen har installerats:

        ![Data Management Gateway - snabb installation framgång](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Starta Konfigurationshanteraren för datahantering för gatewayen genom att följa [dessa instruktioner](data-factory-data-management-gateway.md#configuration-manager). Du ser gatewaynamn, nodnamn, status, etc.

        ![Data Management Gateway - installationen lyckades](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Om du väljer **manuell installation:**
    1. Hämta installationspaketet från Microsoft Download Center och kör det för att installera gatewayen på datorn.
    2. Använd **autentiseringsnyckeln** från sidan **Konfigurera** för att registrera gatewayen.
    
        ![Data Management Gateway - installationen lyckades](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. På sidan **Ny gateway-nod** kan du ange ett anpassat **namn** till gatewaynoden. Som standard är nodnamnet samma som datornamnet.    

        ![Data Management Gateway - ange namn](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. På nästa sida kan du välja om du vill **aktivera kryptering för nod-till-nodkommunikation**. Klicka på **Hoppa över** om du vill inaktivera kryptering (standard).

        ![Data Management Gateway - aktivera kryptering](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Det går bara att ändra krypteringsläget när du har en enda gatewaynod i den logiska gatewayen. Om du vill ändra krypteringsläget när en gateway har flera noder gör du följande steg: ta bort alla noder utom en nod, ändra krypteringsläget och lägg sedan till noderna igen.
        > 
        > Se [avsnittet TLS/SSL-certifikatkrav](#tlsssl-certificate-requirements) för en lista över krav för att använda ett TLS/SSL-certifikat. 
    5. När gatewayen har installerats klickar du på Starta Configuration Manager:
    
        ![Manuell installation - starta konfigurationshanterare](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. Konfigurationshanteraren för datahantering gateway på noden (lokal Windows-dator), som visar anslutningsstatus, **gatewaynamn**och **nodnamn**.  

        ![Data Management Gateway - installationen lyckades](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Om du etablerar gatewayen på en virtuell Azure-dator kan du använda [den här Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Det här skriptet skapar en logisk gateway, ställer in virtuella datorer med datahanteringsgateway-programvara installerad och registrerar dem med den logiska gatewayen. 
6. Starta sidan **Gateway** i Azure-portalen: 
    1. Klicka på **Länkade tjänster**på startsidan för datafabriken i portalen .
    
        ![Datafabrikens startsida](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. välj **gateway** för att se sidan **Gateway:**
    
        ![Datafabrikens startsida](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Du ser **sidan Gateway:**   

        ![Gateway med enkel nodvy](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Klicka på **Lägg till nod i** verktygsfältet om du vill lägga till en nod i den logiska gatewayen. Om du planerar att använda expressinställningar gör du det här steget från den lokala datorn som läggs till som en nod i gatewayen. 

    ![Data Management Gateway - lägg till nod-menyn](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Steg liknar att ställa in den första noden. Med configuration manager-användargränssnittet kan du ange nodnamnet om du väljer alternativet för manuell installation: 

    ![Configuration Manager - installera andra gateway](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. När gatewayen har installerats på noden visas följande skärm på Configuration Manager-verktyget:  

    ![Configuration Manager - installera andra gatewayen lyckades](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Om du öppnar sidan **Gateway** i portalen visas två gatewaynoder nu: 

    ![Gateway med två noder i portalen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Om du vill ta bort en gateway-nod klickar du på **Ta bort nod i** verktygsfältet, markerar den nod som du vill ta bort och klickar sedan på Ta **bort** från verktygsfältet. Den här åtgärden tar bort den markerade noden från gruppen. Observera att den här åtgärden inte avinstallerar datahanteringsgatewayprogramvaran från noden (lokal Windows-dator). Använd **Lägg till eller ta bort program** på Kontrollpanelen på den lokala för att avinstallera gatewayen. När du avinstallerar gatewayen från noden tas den bort automatiskt i portalen.   

## <a name="upgrade-an-existing-gateway"></a>Uppgradera en befintlig gateway
Du kan uppgradera en befintlig gateway för att använda funktionen hög tillgänglighet och skalbarhet. Den här funktionen fungerar bara med noder som har datahanteringsgateway för version >= 2.12.xxxx. Du kan se den version av datahanteringsgatewayen som är installerad på en dator på **fliken Hjälp** i Configuration Manager för Data Management Gateway. 

1. Uppdatera gatewayen på den lokala datorn till den senaste versionen genom att följa med hjälp av att hämta och köra ett MSI-installationspaket från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Mer [information](data-factory-data-management-gateway.md#installation) finns i installationsavsnittet.  
2. Navigera till Azure Portal. Starta **sidan Data Factory** för din datafabrik. Klicka på panelen Länkade tjänster om du vill starta **sidan länkade tjänster**. Välj den gateway som ska starta **gatewaysidan**. Klicka på och aktivera **förhandsgranskningsfunktionen** enligt följande bild: 

    ![Data Management Gateway - aktivera förhandsgranskningsfunktion](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. När förhandsgranskningsfunktionen är aktiverad i portalen stänger du alla sidor. Öppna **gatewaysidan** igen för att se det nya användargränssnittet för förhandsgranskning.
 
    ![Data Management Gateway - aktivera framgång för förhandsgranskningsfunktionen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Data Management Gateway - förhandsgranska användargränssnittet](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Under uppgraderingen är namnet på den första noden namnet på datorn. 
3. Lägg nu till en nod. Klicka på Lägg **till nod**på sidan **Gateway** .  

    ![Data Management Gateway - lägg till nod-menyn](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Följ instruktionerna från föregående avsnitt för att ställa in noden. 

### <a name="installation-best-practices"></a>Metodtips för installation

- Konfigurera energischema på värddatorn för gatewayen så att datorn inte försvinerar. Om värddatorn vilolägen, svarar gatewayen inte på databegäranden.
- Säkerhetskopiera certifikatet som är associerat med gatewayen.
- Se till att alla noder är av liknande konfiguration (rekommenderas) för perfekt prestanda. 
- Lägg till minst två noder för att säkerställa hög tillgänglighet.  

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL-certifikatkrav
Här är kraven för TLS/SSL-certifikatet som används för att skydda kommunikationen mellan integrationskörningsnoder:

- Certifikatet måste vara ett offentligt betrott X509 v3-certifikat. Vi rekommenderar att du använder certifikat som utfärdas av en offentlig certifikatutfärdare (tredje part).
- Varje nod för integrationkörning måste lita på certifikatet, liksom klientdatorn som kör programmet för autentiseringshanteraren. 
  > [!NOTE]
  > Autentiseringshanteraren programmet används samtidigt som säkert ange autentiseringsuppgifter från Kopiera Guide / Azure Portal. Och detta kan avfyras från vilken maskin som helst inom samma nätverk som det lokala/ privata datalagret.
- Wild card-certifikat stöds. Om FQDN-namnet **är node1.domain.contoso.com**kan du använda ***.domain.contoso.com** som ämnesnamn för certifikatet.
- SAN-certifikat rekommenderas inte eftersom endast det sista objektet i ämnesalternativnamnen kommer att användas och alla andra ignoreras på grund av aktuell begränsning. T.ex. du har ett SAN-certifikat vars SAN **är node1.domain.contoso.com** och **node2.domain.contoso.com**kan du bara använda det här certifikatet på en dator vars FQDN **är node2.domain.contoso.com**.
- Stöder alla nyckelstorlekar som stöds av Windows Server 2012 R2 för TLS/SSL-certifikat.
- Certifikat med CNG-nycklar stöds inte.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Vanliga frågor: När skulle jag inte aktivera denna kryptering?
Om du aktiverar kryptering kan du lägga till viss kostnad i infrastrukturen (att äga offentligt certifikat) och därför kan du hoppa över att aktivera kryptering i nedanstående fall:
- När integrationskörningen körs i ett betrott nätverk eller ett nätverk med transparent kryptering som IP/SEC. Eftersom den här kanalkommunikationen endast är begränsad i det betrodda nätverket kanske du inte behöver ytterligare kryptering.
- När integrationskörningen inte körs i en produktionsmiljö. Detta kan bidra till att minska TLS/SSL-certifikatkostnaden.


## <a name="monitor-a-multi-node-gateway"></a>Övervaka en gateway med flera nod
### <a name="multi-node-gateway-monitoring"></a>Övervakning av gateway för flera nod
I Azure-portalen kan du visa ögonblicksbilder i nästan realtid av resursutnyttjande (CPU, minne, nätverk(in/ut), etc.) på varje nod tillsammans med status för gateway-noder. 

![Data Management Gateway - flera nod övervakning](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Du kan aktivera **avancerade inställningar på** sidan **Gateway** för att se avancerade mått som **Nätverk**(in/ut), roll **& autentiseringsuppgiftersstatus**, vilket är användbart vid felsökning av gatewayproblem och **samtidiga jobb** (Löpning/ Gräns) som kan ändras/ ändras i enlighet med detta under prestandajustering. I följande tabell finns beskrivningar av kolumner i listan **Gateway-noder:**  

Egenskap för övervakning | Beskrivning
:------------------ | :---------- 
Namn | Namn på den logiska gatewayen och noder som är associerade med gatewayen.  
Status | Status för den logiska gatewayen och gatewaynoderna. Exempel: Online/Offline/Limited/etc. Information om dessa statusar finns i [statusavsnittet För gateway.](#gateway-status) 
Version | Visar versionen av den logiska gatewayen och varje gatewaynod. Versionen av den logiska gatewayen bestäms baserat på version av majoriteten av noderna i gruppen. Om det finns noder med olika versioner i den logiska gateway-inställningen är det bara noderna med samma versionsnummer som den logiska gatewayfunktionen korrekt. Andra är i begränsat läge och måste uppdateras manuellt (endast om automatisk uppdatering misslyckas). 
Tillgängligt minne | Tillgängligt minne på en gateway-nod. Det här värdet är en ögonblicksbild i nära realtid. 
CPU-användning | CPU-användning av en gateway-nod. Det här värdet är en ögonblicksbild i nära realtid. 
Nätverk (in/ut) | Nätverksanvändning av en gatewaynod. Det här värdet är en ögonblicksbild i nära realtid. 
Samtidiga jobb (löpning/gräns) | Antal jobb eller aktiviteter som körs på varje nod. Det här värdet är en ögonblicksbild i nära realtid. Limit betyder maximalt antal samtidiga jobb för varje nod. Det här värdet definieras baserat på maskinens storlek. Du kan öka gränsen för att skala upp samtidig jobbkörning i avancerade scenarier, där CPU/ minne/ nätverk är underutnyttjat, men aktiviteter är time out. Den här funktionen är också tillgänglig med en gateway med en nod (även när skalbarhets- och tillgänglighetsfunktionen inte är aktiverad). Mer information finns i avsnittet [skala överväganden.](#scale-considerations) 
Roll | Det finns två typer av roller – Dispatcher och worker. Alla noder är arbetare, vilket innebär att de alla kan användas för att utföra jobb. Det finns bara en dispatcher-nod, som används för att hämta uppgifter/jobb från molntjänster och skicka dem till olika arbetsnoder (inklusive sig själv). 

![Data Management Gateway - avancerad övervakning av flera nodar](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Gateway-status

Följande tabell innehåller möjliga status för en **gateway-nod:** 

Status  | Kommentarer/scenarier
:------- | :------------------
Online | Nod ansluten till datafabrikstjänsten.
Offline | Noden är offline.
Uppgradering | Noden uppdateras automatiskt.
Begränsad | På grund av anslutningsproblem. Kan bero på problem med HTTP-port 8050, problem med servicebussanslutning eller synkroniseringsproblem för autentiseringsuppgifter. 
Inaktiv | Noden är i en annan konfiguration än konfigurationen av andra majoritetsnoder.<br/><br/> En nod kan vara inaktiv när den inte kan ansluta till andra noder. 


Följande tabell innehåller möjliga status för en **logisk gateway**. Gateway-statusen beror på status för gatewaynoderna. 

Status | Kommentarer
:----- | :-------
Behöver registrering | Ingen nod har ännu registrerats i den här logiska gatewayen
Online | Gateway-noder är online
Offline | Ingen nod i onlinestatus.
Begränsad | Alla noder i den här gatewayen är inte i felfritt tillstånd. Denna status är en varning om att vissa nod kan vara nere! <br/><br/>Det kan bero på problemet med synkronisering av autentiseringsuppgifter för avsändaren/arbetarnoden. 

### <a name="pipeline-activities-monitoring"></a>Övervakning av rörledningar/aktiviteter
Azure-portalen ger en pipeline övervakningsupplevelse med detaljerad information om nodnivå. Den visar till exempel vilka aktiviteter som kördes på vilken nod. Den här informationen kan vara till hjälp för att förstå prestandaproblem på en viss nod, till exempel på grund av nätverksbegränsning. 

![Data Management Gateway - flera nod övervakning för rörledningar](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Data Management Gateway - pipeline-information](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Skala överväganden

### <a name="scale-out"></a>Skala ut
När det **tillgängliga minnet är lågt** och **CPU-användningen är hög**hjälper du att skala ut belastningen mellan datorer genom att lägga till en ny nod. Om aktiviteter misslyckas på grund av att time-out- eller gateway-noden är offline hjälper det om du lägger till en nod i gatewayen.
 
### <a name="scale-up"></a>Skala upp
När det tillgängliga minnet och processorn inte används väl, men inaktiv kapacitet är 0, bör du skala upp genom att öka antalet samtidiga jobb som kan köras på en nod. Du kanske också vill skala upp när aktiviteter är time out eftersom gatewayen är överbelastad. Som visas i följande bild kan du öka den maximala kapaciteten för en nod. Vi föreslår en fördubbling av den till att börja med.  

![Data Management Gateway - skala överväganden](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Kända problem/bryta ändringar

- För närvarande kan du ha upp till fyra fysiska gatewaynoder för en enda logisk gateway. Om du behöver fler än fyra noder av [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com)prestandaskäl skickar du ett e-postmeddelande till .
- Du kan inte registrera om en gateway-nod med autentiseringsnyckeln från en annan logisk gateway för att växla från den aktuella logiska gatewayen. Om du vill registrera om avinstallerar du gatewayen från noden, installerar om gatewayen och registrerar den med autentiseringsnyckeln för den andra logiska gatewayen. 
- Om HTTP-proxy krävs för alla gateway-noder anger du proxyn i diahost.exe.config och diawp.exe.config och använder serverhanteraren för att se till att alla noder har samma diahost.exe.config och diawip.exe.config. Mer information finns i avsnittet [konfigurera proxyinställningar.](data-factory-data-management-gateway.md#configure-proxy-server-settings) 
- Om du vill ändra krypteringsläget för nod-till-nodkommunikation i Configuration Manager för gateway tar du bort alla noder i portalen utom en. Lägg sedan till noder tillbaka när du har ändrat krypteringsläget.
- Använd ett officiellt TLS-certifikat om du väljer att kryptera nod-till-nodkommunikationskanalen. Självsignerat certifikat kan orsaka anslutningsproblem eftersom samma certifikat kanske inte är betrott i att certifiera auktoritetslista på andra datorer. 
- Du kan inte registrera en gateway-nod till en logisk gateway när nodversionen är lägre än den logiska gateway-versionen. Ta bort alla noder i den logiska gatewayen från portalen så att du kan registrera en nedre versionsnod(nedgradera) den. Om du tar bort alla noder i en logisk gateway installerar och registrerar du nya noder manuellt till den logiska gatewayen. Express-inställningar stöds inte i det här fallet.
- Du kan inte använda expressinställningar för att installera noder till en befintlig logisk gateway, som fortfarande använder molnautentiseringsuppgifter. Du kan kontrollera var autentiseringsuppgifterna lagras från Konfigurationshanteraren för gateway på fliken Inställningar.
- Du kan inte använda expressinställningar för att installera noder till en befintlig logisk gateway, som har nod-till-nodkryptering aktiverad. Eftersom inställningen av krypteringsläget innebär att du manuellt lägger till certifikat är expressinstallation inte längre ett alternativ. 
- För en filkopia från lokal miljö \\bör du inte längre använda localhost- eller C:\-filer eftersom localhost eller lokal enhet kanske inte är tillgängliga via alla noder. Använd i \\stället ServerName\filer för att ange filernas plats.


## <a name="rolling-back-from-the-preview"></a>Rulla tillbaka från förhandsgranskningen 
Om du vill återställa från förhandsgranskningen tar du bort alla noder utom en nod. Det spelar ingen roll vilka noder du tar bort, men se till att du har minst en nod i den logiska gatewayen. Du kan ta bort en nod antingen genom att avinstallera gateway på datorn eller med hjälp av Azure-portalen. Klicka på Länkade tjänster på sidan **Data Factory** i **Linked services** Azure-portalen. Välj den gateway som du vill starta sidan **Gateway.** På sidan Gateway kan du se de noder som är associerade med gatewayen. På sidan kan du ta bort en nod från gatewayen.
 
När du har tagit bort klickar du på **förhandsgranskningsfunktioner** på samma Azure-portalsida och inaktiverar förhandsgranskningsfunktionen. Du har återställt gatewayen till en nod GA-gateway (allmän tillgänglighet).


## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:
- [Data Management Gateway](data-factory-data-management-gateway.md) - ger en detaljerad översikt över gatewayen.
- [Flytta data mellan lokala datalager och molndatalager](data-factory-move-data-between-onprem-and-cloud.md) – innehåller en genomgång med steg-för-steg-instruktioner för att använda en gateway med en enda nod. 
