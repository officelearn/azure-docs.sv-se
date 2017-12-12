---
title: Introduktion till Microsoft Azure | Microsoft Docs
description: "Ny till Microsoft Azure? Få en översikt av tjänster som den erbjuder med exempel på hur de är användbara."
services: " "
documentationcenter: .net
author: rboucher
manager: carolz
editor: 
ms.assetid: 6f47f711-2208-4c21-8c1d-826a54c05c29
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2015
ms.author: robb
ms.openlocfilehash: 2fb989d1fd9ee76ee1d21323574971438d607238
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="introducing-microsoft-azure"></a>Introduktion till Microsoft Azure
Microsoft Azure är Microsofts programplattform för det offentliga molnet.  Syftet med den här artikeln är att ge dig en grund för att förstå grunderna i Azure, även om du inte känner till något om cloud computing.

**Hur du läser den här artikeln**

Azure växer hela tiden så att det är enkelt att överbelastas.  Börja med de grundläggande tjänster, som visas först i den här artikeln och fortsätter sedan till ytterligare tjänster. Det betyder inte att inte du kan bara de ytterligare tjänsterna ensamt, men de grundläggande tjänsterna utgör kärnan i ett program som körs i Azure.

**Ge feedback**

Din feedback är viktig. Den här artikeln bör du få en effektiv översikt över Azure. Om det inte berätta i kommentaravsnittet längst ned på sidan. Ge vissa detaljer på vad du förväntade dig att se och hur vi kan förbättra artikeln.  

## <a name="the-components-of-azure"></a>Komponenterna i Azure
Azure grupperar tjänster i kategorier i hanteringsportalen och på olika visuella hjälpmedel som den [vad är Azure Infographic](https://azure.microsoft.com/documentation/infographics/azure/) . Hanteringsportalen är det du använder för att hantera de flesta (men inte alla) tjänster i Azure.

Den här artikeln använder en **annan organisation** pratar om tjänster baserat på liknande funktion och identifiera viktiga underordnade tjänster som ingår i större de.  

![Azure-komponenter](./media/fundamentals-introduction-to-azure/AzureComponentsIntroNew780.png)   
 *Bild: Azure tillhandahåller Internet-tillgängliga programtjänster körs i Azure-datacenter.*

## <a name="management-portal"></a>Hanteringsportal
Azure har ett webbgränssnitt som kallas den [hanteringsportalen](http://manage.windowsazure.com) som gör det möjligt för administratörer att få åtkomst till och administrera de flesta, men inte alla Azure-funktioner.  Microsoft släpper vanligtvis nyare UI-portal i beta innan den dras tillbaka en tidigare version. Den nya som kallas den [”Azure Portal”](https://portal.azure.com/).

Det är vanligtvis en lång överlappning när båda portaler är aktiva. Även om kärntjänster visas i båda portalerna kanske inte alla funktioner tillgängliga i båda. Nyare tjänster kan visas i nyare portal första och äldre tjänster och funktioner kan endast finnas i den äldre.  Det här meddelandet är om du inte hittar något i den äldre portalen kontrollerar den nyare och vice versa.

## <a name="compute"></a>Compute
En av de mest grundläggande sakerna som har en molnplattform är att köra program. Varje Azure compute-modeller har sin egen roll.

Du kan använda dessa tekniker separat eller kombinera dem efter behov för att skapa rätt grunden för ditt program. Den metod du väljer beror på vilka problem som du försöker lösa.

### <a name="azure-virtual-machines"></a>Azure Virtual Machines
![Virtuella Azure-datorer ROBBCSIART_TEST](./media/fundamentals-introduction-to-azure/mscsiart_VirtualMachinesIntroNew_12345.png)   
*Bild: Virtuella datorer i Azure ger dig fullständig kontroll över instanser för virtuella datorer i molnet.*

Möjligheten att skapa en virtuell dator på begäran, om du anger från en standardiserad avbildning eller från en, kan vara väldigt användbar. Den här metoden, ofta kallade infrastruktur som en tjänst (IaaS), är den Azure Virtual Machines erbjuder. Bild 2 visar en kombination av hur en virtuell dator (VM) körs och hur du skapar ett från en virtuell Hårddisk.  

Om du vill skapa en virtuell dator kan du ange vilken VHD ska användas och storleken på den virtuella datorn.  Sedan betalar för den tid som den virtuella datorn körs. Du betalar per minut och endast när den körs, men det finns en minimal lagring kostnad för att behålla den virtuella Hårddisken som är tillgängliga. Azure erbjuder ett galleri med börs virtuella hårddiskar (kallas ”bilder”) som innehåller ett startbart operativsystem för att starta från. Dessa inkluderar Microsoft och partner alternativ, till exempel Windows Server och Linux, SQL Server, Oracle och mycket mer. Du har möjlighet att skapa virtuella hårddiskar och bilder och överför dem själv. Du kan även överföra virtuella hårddiskar som innehåller endast data och komma åt dem från din virtuella datorer som körs.

Oavsett var den virtuella Hårddisken kommer från, kan du lagra ändringar som görs när en virtuell dator körs beständigt. Nästa gång du skapar en virtuell dator från den virtuella Hårddisken, ta saker vid där du slutade. De virtuella hårddiskarna som stöder virtuella datorer lagras i Azure Storage blob som vi pratar om senare.  Det innebär att du får redundans för att säkerställa att dina virtuella datorer inte försvinner på grund av maskin- och fel. Det är också möjligt att kopiera den ändrade VHD av Azure och sedan köra det lokalt.

Programmet körs inom en eller flera virtuella datorer, beroende på hur du har skapat det innan eller välja att skapa från grunden nu.

Den här allmän metod för molntjänster kan användas för att lösa många olika problem.

**Scenarier för virtuell dator**

1. **Utveckling och testning** -du kan använda dem för att skapa en billig utveckling och testning plattform som du kan stänga av när du är klar. Du kan också skapa och köra program som använder oavsett språk och bibliotek som du vill. Dessa program kan använda något av alternativen för hantering av data som Azure tillhandahåller och du kan också välja att använda SQL Server eller en annan DBMS som körs i en eller flera virtuella datorer.
2. **Flytta program till Azure (Lift och SKIFT)** -”Lift och SKIFT” refererar till mycket flytta programmet som du skulle använda en gaffeltruck för att flytta ett stort objekt.  Du ”lyfter” på den virtuella Hårddisken från ditt lokala datacenter och ”växlas” den till Azure och kör det det.  Du behöver normalt krävs en del arbete för att ta bort beroenden i andra system. Om det finns för många, kan du välja alternativ 3 i stället.  
3. **Utöka ditt Datacenter** -Använd Azure virtuella datorer som ett tillägg till ditt lokala datacenter kör SharePoint eller andra program. Stöd för detta är det möjligt att skapa Windows-domäner i molnet genom att köra Active Directory i virtuella Azure-datorer. Du kan använda Azure Virtual Network (beskrivs senare) för att koppla det lokala nätverket och nätverket i Azure tillsammans.

### <a name="web-apps"></a>Web Apps
![Azure Web Apps ROBBCSIART_TEST](./media/fundamentals-introduction-to-azure/mscsiart_AzureWebsitesIntroNew_12345.png)   
 *Bild: Azure Web Apps körs en webbplatsprogrammet i molnet utan att behöva hantera underliggande webbservern.*

En av de vanligaste sakerna som människor gör i molnet körs webbplatser och webbprogram. Virtuella Azure-datorer kan detta, men den lämnar du fortfarande med ansvar för att administrera en eller flera virtuella datorer och de underliggande operativsystemen. Cloud services web-roller kan göra detta, men distribuera och underhålla dem. fortfarande tar administrativt arbete.  Vad händer om du bara vill ha en webbplats där någon annan tar hand om det administrativa arbetet åt dig?

Detta är exakt vad Web Apps erbjuder. Den här modellen för beräkning erbjuder en hanterad webbmiljö med hjälp av Azure Management portal samt API: er. Du kan flytta ett befintligt program för webbplatsen i Web Apps oförändrade eller skapa en ny direkt i molnet. När en webbplats körs kan du lägga till eller ta bort instanser dynamiskt, förlita dig på Azure Web Apps att läsa in Utjämna begäranden om över dem. Appar i Azure erbjuder både en delad, där webbplatsen körs i en virtuell dator med andra platser, och en standard som gör att en plats ska köras på egen virtuell dator. Standard alternativet kan du öka storleken (prestanda) för dina instanser om det behövs.

För utveckling stöder Web Apps .NET, PHP, Node.js, Java och Python tillsammans med SQL Database och Azure-databas för MySQL för relationslagret. Den innehåller också inbyggda stöd för flera populära program, inklusive WordPress, Joomla och Drupal. Målet är att tillhandahålla en billig, skalbara och fritt och praktiskt plattform för att skapa webbplatser och webbprogram i det offentliga molnet.

**Web Apps scenarier**

Web Apps är avsedd att vara användbara för företag, utvecklare och web design myndigheter. För företag är det ett enkelt att hantera, skalbar, mycket säkert och hög tillgänglighet lösning för att köra förekomst webbplatser. När du behöver konfigurera en webbplats, är det bäst att börja med Azure-Webbappar och fortsätta till molntjänster när du behöver en funktion som inte är tillgänglig. Se slutet av avsnittet ”beräkning” för flera länkar som kan hjälpa dig att välja mellan alternativen.

### <a name="cloud-services"></a>Molntjänster
![Azure-molntjänst](./media/fundamentals-introduction-to-azure/CloudServicesIntroNew.png)   
*Bild: Azure Cloud Services tillhandahåller en mycket skalbar anpassad kod körs på en plattform som en tjänst (PaaS)-miljö*

Anta att du vill skapa en moln-program som har stöd för många samtidiga användare, inte kräver mycket administration och aldrig kraschar. Du kanske en upprättad programvaruleverantör, till exempel som har beslutat att omfatta programvara som en tjänst (SaaS) genom att skapa en version av ett program i molnet. Eller kanske är en Startup som skapar en konsument-program som du förväntar dig ökar snabbt. Om du bygger på Azure, vilka körningsmodell bör du använda?

Azure Web Apps gör den här typen av webbprogram, men det finns vissa begränsningar. Du har inte administratörsbehörighet, till exempel vilket innebär att du inte kan installera skadlig programvara. Virtuella datorer i Azure ger dig stor flexibilitet, inklusive administrativ åtkomst och du har använda den för att skapa en mycket skalbar program, men måste du hantera många aspekter av tillförlitlighet och administration av dig själv. Vad som är ett alternativ som ger dig kontroll du behöver, men hanterar också mesta av arbetet som krävs för tillförlitlighet och administration.

Detta är exakt vad som tillhandahålls av Azure Cloud Services. Den här tekniken är utformad att stödja skalbar, tillförlitlig och låg-admin-program, och den är ett exempel på hur ofta kallas plattform som en tjänst (PaaS). För att använda den, kan du skapa ett program med hjälp av den teknik som du väljer, till exempel C#, Java, PHP, Python, Node.js eller något annat. Koden utför på virtuella datorer (kallas instanser) kör en version av Windows Server.

Men dessa virtuella datorer skiljer sig från de som du skapar med Azure Virtual Machines. För en sak, Azure själva hanterar dem göra sådant som installerar korrigeringsfiler för operativsystem och automatiskt lansera nya korrigeras bilder. Detta innebär att ditt program inte bör underhåller tillstånd i webb- eller arbetarroll rollinstanser; Det bör i stället hållas i ett Azure data management alternativen som beskrivs i nästa avsnitt. Azure övervakar även dessa virtuella datorer starta om alla att misslyckas. Du kan ange molntjänster att automatiskt skapa fler eller färre instanser som svar på begäran. På så sätt kan du hantera ökad användning och skala tillbaka så att du inte betalar så mycket om det finns mindre användning.

Du har två roller för att välja mellan när du skapar en instans måste båda baseras på Windows Server. Den största skillnaden mellan två är att en instans av en webbroll körs IIS, men inte av en instans av en arbetsroll. Både hanteras på samma sätt men och det är vanligt att ett program att använda både. Till exempel en rollinstans web kan godkänna begäranden från användare och sedan skicka dem till en worker rollinstans för bearbetning. Om du vill skala upp eller ned ditt program kan du begära att Azure skapar flera instanser av antingen rollen eller stänga av befintliga instanser. Och liknande till virtuella datorer i Azure, du är debiteras endast för tiden att varje webb eller arbetare roll-instansen körs.

**Cloud Services scenarier**

Molntjänster är perfekt att stödja massiv skala ut när du behöver mer kontroll över plattformen än Azure Web Apps men behöver inte kontroll över det underliggande operativsystemet.

#### <a name="choosing-a-compute-model"></a>Om du väljer en beräknings-modell
Sidan [jämförelse mellan Azure Web Apps, molntjänster och virtuella datorer](app-service/choose-web-site-cloud-service-vm.md) ger mer detaljerad information om hur du väljer en beräknings-modell.

## <a name="data-management"></a>Databearbetning
Program behöver data och olika typer av program behöver olika typer av data. Därför tillhandahåller Azure flera olika sätt att lagra och hantera data. Azure tillhandahåller många lagringsalternativ, men alla är utformade för mycket beständig lagring.  Med någon av dessa alternativ finns det alltid 3 kopior av dina data synkroniseras mellan ett Azure-datacenter – 6 om du tillåter Azure för att säkerhetskopiera till en annan datacenter minst 300 miles direkt med geo-redundans.     

### <a name="in-virtual-machines"></a>I virtuella datorer
Redan har tagits ovannämnda möjlighet att köra SQL Server eller en annan DBMS på en virtuell dator som skapats med virtuella datorer i Azure. Tänk på att det här alternativet inte är begränsad till relationella system. Du kan också möjlighet att köra NoSQL teknologier som MongoDB och Cassandra. Kör en egen databas är enkla it replikerar vad vi kan användas för att i våra eget Datacenter- men det krävs också hantering av den DBMS administration.  Andra alternativ hanterar Azure flera eller alla administration för dig.

Tillståndet för den virtuella datorn och eventuella ytterligare datadisk du skapar eller ladda upp igen och backas upp av blob-lagring (som vi pratar om senare).  

### <a name="azure-sql-database"></a>Azure SQL Database
![Azure Storage SQL-databas](./media/fundamentals-introduction-to-azure/StorageAzureSQLDatabaseIntroNew.png)   

*Bild: Azure SQL Database är en hanterad relationsdatabas-tjänst i molnet.*

Azure tillhandahåller för relationella lagring funktionen SQL-databas. Låt inte namngivningen lura dig. Detta skiljer sig en typisk SQL-databas som tillhandahålls av SQL Server som körs på Windows Server.  

Kallades SQL Azure, tillhandahåller Azure SQL Database alla viktiga funktioner i en relationsdatabas management-systemet, inklusive atomiska transaktioner samtidig dataåtkomst av flera användare med dataintegritet, ANSI SQL-frågor och välbekanta Programmeringsmiljön. Öppna tekniker som SQL Server, SQL-databasen kan öppnas med hjälp av Entity Framework ADO.NET, JDBC och andra välkända data. Det stöder också de flesta av T-SQL-språket tillsammans med SQL Server-verktyg som SQL Server Management Studio. Vem som helst bekant med SQL Server (eller en annan relationsdatabas) med hjälp av SQL-databasen är för enkelt.

Men SQL-databas är inte bara ett DBMS i it-moln är en PaaS-tjänst. Du ändå ha kontroll över dina data och som har åtkomst till den, men SQL-databas som tar hand om administrativa grunt arbetet, till exempel hantering av infrastrukturen för maskinvara och automatiskt uppdatera databasen och operativsystemet programvara kontinuerligt. SQL-databasen dessutom ger hög tillgänglighet, automatisk säkerhetskopiering i tidpunkt återställa funktioner och kan replikeras geografiska regioner kopior.  

**Scenarier för SQL-databas**

Om du skapar ett Azure-program (med någon av beräkning) som behöver relationslagret, kan SQL-databasen vara ett bra alternativ. Program som körs utanför molnet kan också använda den här tjänsten, så att det finns tillräckligt med andra scenarier. Data som lagras i SQL-databas kan exempelvis nås från annan klientsystem, inklusive stationära datorer, bärbara datorer, surfplattor och telefoner. Och eftersom den innehåller inbyggd hög tillgänglighet via replikering med hjälp av SQL-databas kan du minimera driftstopp.

### <a name="tables"></a>Tabeller
![Azure Storage-tabeller](./media/fundamentals-introduction-to-azure/StorageTablesIntroNew.png)  

*Bild: Azure-tabeller innehåller en platt NoSQL sätt att lagra data.*

Den här funktionen kallas ibland olika villkor som det är en del av en större funktion som kallas ”Azure Storage”. Om du ser ”tabeller”, ”Azure-tabeller” eller ”storage-tabeller”, är det samma sak.  

Och inte förväxlas med namnet: den här tekniken inte ger relationslagret. Faktum är att det är ett exempel på en NoSQL-metod som kallas för en nyckel/värde-lagring. Azure-tabeller kan ett program som lagrar egenskaper av olika typer, till exempel strängar, heltal och datum. Ett program kan sedan hämta en grupp egenskaper genom att tillhandahålla en unik nyckel för den gruppen. När avancerade åtgärder som kopplingar inte stöds, tabeller ger snabb åtkomst till skrivna data. Det är också mycket skalbar, med en enda tabell kan innehålla så mycket som en terabyte data. Och matchar deras enkelhet tabeller är oftast billigare att använda än relationslagret för SQL-databas.

**Scenarier för register**

Anta att du vill skapa ett Azure-program som behöver snabb åtkomst till typangivna data, kan vara mycket av det, men behöver inte utföra SQL-frågor på dessa data. Anta att du skapar en konsument-program som behöver lagra kundinformation profil för varje användare. Appen kommer att vara mycket populär, så du måste tillåta för stora mängder data, men du inte göra mycket med dessa data förutom lagra, hämta den på ett enkelt sätt. Detta är exakt vilken typ av scenariot där klokt att Azure-tabeller.

### <a name="blobs"></a>Blobar
![Azure Storage-Blobbar](./media/fundamentals-introduction-to-azure/StorageBlobsIntroNew.png)    
*Bild: Azure-Blobbar ger Ostrukturerade binära data.*  

Azure BLOB (igen ”Blob Storage” och bara ”Storage-Blobbar” är det samma sak) är utformad för att lagra Ostrukturerade binära data. Blobbar ger kostnadseffektiv lagring som tabeller, och en enda blob kan vara så stor som 1TB (en terabyte). Azure-program kan också använda Azure enheter, som gör att BLOB tillhandahålla beständiga lagring för ett Windows-filsystem som är monterat i en Azure-instans. Programmet ser vanliga Windows-filer, men innehållet lagras i en blob.

BLOB-lagring som används av andra Azure-funktioner (inklusive virtuella datorer), så den kan fungera utmärkt hantera dina arbetsbelastningar för.

**Scenarier för Blobbar**

Ett program som lagrar video, massiv filer eller andra binär information kan använda BLOB för enkel och billig lagring. Blobbar används också ofta tillsammans med andra tjänster som Content Delivery Network, som vi ska tala om senare.  

### <a name="import--export"></a>Import/Export
![Azure-importen Export Service](./media/fundamentals-introduction-to-azure/ImportExportIntroNew.png)  

*Bild: Azure Import / Export ger möjlighet att leverera en fysisk hårddisk eller från Azure och snabbare och billigare stora mängder data Importera eller exportera.*  

Ibland vill du att flytta stora mängder data till Azure. Som skulle ta lång tid, kanske dagar och använder mycket bandbredd. I dessa fall kan använda du Azure Import/Export, där du kan leverera Bitlocker-krypterad 3,5-tums SATA-hårddiskar direkt till Azure-datacenter, där Microsoft överför data i blob-lagring för dig.  När överföringen är klar levereras enheter tillbaka till Microsoft.  Du kan också begära att stora mängder data från Blob Storage exporteras till hårddiskar och skickas tillbaka till dig via e-post.

**Scenarier för Import / Export**

* **Stora datamigrering** -när du har stora mängder data (terabyte) som du vill överföra till Azure Import/Export-tjänsten är ofta mycket snabbare och kanske billigare än att överföra den via internet. När data är i blobbar, kan du bearbeta det till andra former, till exempel tabellagring eller en SQL-databas.
* **Arkiverade dataåterställning** -du kan använda Import/Export för att Microsoft överför stora mängder data lagras i Azure Blob Storage till en lagringsenhet som du skickar och har enheten levereras till en plats som du önskar. Eftersom detta kan ta lite tid, men det är inte ett bra alternativ för katastrofåterställning. Det är bäst för arkiverade data som du inte behöver snabb åtkomst till.

### <a name="file-service"></a>Tjänsten File
![Tjänsten Azure File](./media/fundamentals-introduction-to-azure/FileServiceIntroNew.png)    
*Bild: Azure Filtjänster tillhandahåller SMB \\ \\server\share sökvägar för program som körs i molnet.*

Lokalt, är det vanligt att stora mängder lagring av filer som är tillgängligt via Server Message Block (SMB) protokollet använder en \\ \\Server\share format. Azure har nu en tjänst som gör att du kan använda det här protokollet i molnet. Program som körs i Azure kan använda den för att dela filer mellan virtuella datorer med hjälp av välbekanta filsystem API: er som ReadFile och WriteFile. Filerna kan dessutom också nås på samma gång via ett REST-gränssnitt som ger dig åtkomst till resurser från lokala när du också konfigurera ett virtuellt nätverk. Azure Files är byggt på blob-tjänsten så att det ärver den samma tillgänglighet, hållbarhet, skalbarhet och geo-redundans är inbyggda i Azure Storage.

**Scenarier för Azure-filer**

* **Migrera befintliga appar till molnet** -dess enklare att migrera lokala program till molnet som använder filresurser för att dela data mellan delar av programmet. Varje virtuell dator ansluter till filresursen och sedan den kan läsa och skriva filer precis som det skulle mot en lokal fil dela.
* **Delade programinställningar** -vanliga mönstret för distribuerade program är att ha configuration-filer på en central plats där de kan nås från många olika virtuella datorer. Dessa konfigurationsfiler kan lagras i en filresurs på Azure och läses av alla instanser av programmet. Inställningarna kan också hanteras via REST-gränssnitt, vilket ger globalt åtkomst till konfigurationsfilerna.
* **Diagnostiska resursen** – du kan spara och dela filer av diagnostiska loggar, mått, och kraschdumpar. Med de här filerna kan tillgängliga via SMB- och REST-gränssnittet program som används av en mängd olika analysverktyg för för bearbetning och analysera diagnostiska data.
* **Dev/Test/Debug** – när utvecklare och administratörer arbetar med virtuella datorer i molnet, ofta måste en uppsättning verktyg. Installera och distribuera dessa verktyg på varje virtuell dator är tidskrävande. En utvecklare eller administratören kan lagra sina favoritverktyg på en filresurs och ansluta till dem från en virtuell dator med Azure-filer.

## <a name="networking"></a>Nätverk
Azure körs idag i många datacenter som är utspridda över hela världen. Du kan välja en eller flera av dessa Datacenter ska användas när du kör ett program eller lagra data. Du kan också ansluta till dessa Datacenter på olika sätt med hjälp av tjänster som anges nedan.

### <a name="virtual-network"></a>Virtual Network
![VirtualNetwork](./media/fundamentals-introduction-to-azure/VirtualNetworkIntroNew.png)   

*Bild: Virtuella nätverk tillhandahåller ett privat nätverk i molnet så att olika tjänster kan kommunicera med varandra och till lokala resurser om du konfigurerar en VPN-anslutning mellan lokala anslutningen.*  

Ett bra sätt att använda ett offentligt moln är att behandla det som ett tillägg till ditt eget datacenter.

Eftersom du kan skapa virtuella datorer på begäran, sedan ta bort dem (och stoppa betalar) när de inte längre behövs, du kan ha datorkraft bara när du vill. Och eftersom Azure virtuella datorer kan du skapa virtuella datorer som kör SharePoint, Active Directory och andra välkända lokal programvara, den här metoden kan arbeta med program som du redan har.

Om du vill göra detta användbart, men bör användarna kunna behandla programmen som om de kördes i ditt eget datacenter. Detta är exakt vad Azure Virtual Network. Med hjälp av en VPN-gateway-enhet, kan en administratör konfigurera ett virtuellt privat nätverk (VPN) mellan ditt lokala nätverk och dina virtuella datorer som distribueras till ett virtuellt nätverk i Azure. Eftersom du tilldela egna IP v4-adresser till molnet virtuella datorer som de visas på ditt eget nätverk. Användare i din organisation kan komma åt program som dessa virtuella datorer innehåller som om de kördes lokalt.

Mer information om planering och skapa ett virtuellt nätverk som passar dig finns [virtuellt nätverk](virtual-network/virtual-networks-overview.md).

### <a name="express-route"></a>Express Route
![ExpressRoute](./media/fundamentals-introduction-to-azure/ExpressRouteIntroNew.png)   

*Bild: ExpressRoute använder ett virtuellt Azure-nätverk men dirigerar anslutningar via snabbare dedikerade rader i stället för det offentliga Internet.*  

Om du behöver mer bandbredd eller säkerhet än ett Azure Virtual Network anslutning kan ge, kan du söka i ExpressRoute. I vissa fall kan ExpressRoute också spara pengar. Du måste fortfarande ett virtuellt nätverk i Azure, men länken mellan Azure och webbplatsen använder en dedikerad anslutning som inte går via det offentliga Internet. För att kunna använda den här tjänsten måste ha ett avtal med en nätverkstjänstleverantören eller en exchange-provider.

Konfigurera en ExpressRoute anslutningen kräver mer tid och planering, så du kanske vill börja med en plats-till-plats-VPN och sedan migrera till en ExpressRoute-anslutning.

Mer information om ExpressRoute finns [teknisk översikt för ExpressRoute](expressroute/expressroute-introduction.md).

### <a name="traffic-manager"></a>Traffic Manager
![TrafficManager](./media/fundamentals-introduction-to-azure/TrafficManagerIntroNew.png)   

*Bild: Azure Traffic Manager kan du dirigera globala trafik till tjänsten baserat på intelligent regler.*

Om din Azure-program körs i flera datacenter, kan du använda Azure Traffic Manager för att skicka begäranden från användare som använder det Intelligent mellan instanser av programmet. Du kan också dirigera trafik till tjänster inte körs i Azure så länge de är tillgängliga från internet.  

Ett Azure-program med användare i en enda del av världen kan köras i endast en Azure-datacenter. Ett program med användare som spridda runtom i världen är dock troligt att köras i flera Datacenter kanske även alla. Står inför ett problem i den här andra situationen: hur du använder det Intelligent dirigera användare till programinstanser? I de flesta fall, vill du förmodligen varje användare att komma åt datacenter som är närmast, eftersom du förmodligen får henne bästa svarstid. Men vad händer om instansen av programmet är överbelastad eller inte är tillgängligt? I det här fallet, skulle det vara praktiskt att dirigera sin begäran automatiskt till en annan datacenter. Detta är exakt vad görs av Azure Traffic Manager.

Ägaren till ett program definierar regler som anger hur begäranden från användare som ska dirigeras till datacenter och förlitar sig på Traffic Manager att genomföra de här reglerna. Användare kan normalt dirigeras till närmaste Azure-datacentret men skickas till en annan när svarstid från sina datacenter standard överskrider svarstid från andra datacenter. Globalt distribuerade program med många användare är har en inbyggd tjänst för att hantera problem som dessa användbart.

Traffic manager använder Directory Name Service (DNS) för vägen användarna att slutpunkter, men ytterligare trafik går inte via Traffic Manager när anslutningen görs. Detta håller Traffic Manager från att vara en flaskhals kan sakta ned service-kommunikation.

## <a name="developer-services"></a>Utvecklartjänster
Azure erbjuder ett antal verktyg som hjälper utvecklare och IT-proffs att skapa och hantera program i molnet.  

### <a name="azure-sdk"></a>Azure SDK
Första förhandsversionen av Azure stöds endast .NET development tillbaka i 2008. Idag, men kan du skapa Azure-program i nästan alla språk. Microsoft tillhandahåller för närvarande språkspecifika SDK för .NET, Java, PHP, Node.js, Ruby och Python. Det finns också en allmän Azure SDK som tillhandahåller grundläggande stöd för alla språk, till exempel C++.  

Dessa SDK hjälpa dig att skapa, distribuera och hantera Azure-program. De är tillgängliga antingen från [www.microsoftazure.com](https://azure.microsoft.com/downloads/) eller GitHub och de kan användas med Visual Studio och Eclipse. Azure erbjuder också kommandoradsverktyg som utvecklare kan använda med alla editor eller utvecklingsmiljö, inklusive verktyg för att distribuera program till Azure från Linux- och Macintosh-system.

Tillsammans med hjälper dig att skapa Azure-program, dessa SDK: er ger också klientbibliotek som hjälper dig att skapa program som använder Azure-tjänster. Du kan till exempel skapa ett program som läser och skriver Azure BLOB eller skapa ett verktyg som distribuerar Azure program via Azure hanteringsgränssnitt.

### <a name="visual-studio-team-services"></a>Visual Studio Team Services
Visual Studio Team Services är ett marknadsföring namn som omfattar ett antal tjänster som bidrar till att utveckla program i Azure.

För att undvika förvirring - finns det inte en värdbaserad eller webbaserade version av Visual Studio. Du måste fortfarande din lokala körande kopia av Visual Studio. Men den innehåller många andra verktyg som kan vara användbar.

Det omfattar en värdbaserad källkontrollsystem kallas Team Foundation-tjänst som erbjuder versionskontroll och arbete objektet spårning.  Du kan även använda Git för versionskontroll om du föredrar som. Och du kan variera källkontrollsystem som du använder av projektet. Du kan skapa obegränsade privata grupprojekt nås från var som helst i världen.  

Visual Studio Team Services innehåller en belastningen tester. Du kan köra belastningstester som skapats i Visual Studio på virtuella datorer i molnet. Du anger det totala antalet användare som du vill testa med att läsa in och Visual Studio Team Services avgör automatiskt hur många agenter krävs få igång de nödvändiga virtuella datorerna och kör din belastningstester. Om du är en MSDN-prenumerant kan hämta du tusentals ledigt användarminuter belastningen Testa varje månad.

Visual Studio Team Services har också stöd för flexibel utveckling med funktioner som kontinuerlig integration bygger, Kanban: er och virtuella team lokaler.

**Visual Studio Team Services scenarier**

Visual Studio Team Services är ett bra alternativ för företag som behöver samarbeta över hela världen och inte redan har infrastrukturen för att göra detta. Du kan hämta installationen i minuter, välja ett system för källa och börja skriva kod och skapa den dagen.  Team verktyg är en plats för samordning och samarbete och ytterligare verktyg tillhandahåller en analys krävs för att testa och finjustera tillämpningsprogrammet snabbt.

Men organisationer som redan har ett lokalt system kan testa nya projekt i Visual Studio Team Services för att se om det är mer effektivt.   

### <a name="application-insights"></a>Application Insights
![Application Insights](./media/fundamentals-introduction-to-azure/ApplicationInsights.png)  

*Bild: Application Insights övervakar prestanda och användning av appen live webb- eller enhet.*

När du har publicerat din app – om den körs på mobila enheter, datorer eller webbläsare - anger Application Insights hur den fungerar och vad användarna gör med den. Kommer att antalet krascher och lång svarstid, varning om siffrorna mellan oacceptabel tröskelvärden och hjälpa dig att diagnostisera problemen.

När du utvecklar en ny funktion planera dess framgångarna mäts med användare. Genom att analysera användningsmönster, förstå vad som fungerar bäst för dina kunder och förbättra din app i varje utvecklingscykeln.

Även om den finns i Azure, fungerar Application Insights för ett växande antal appar, både på och från Azure. J2EE- och ASP.NET web apps omfattas, samt iOS, Android, OS x och Windows-program. Telemetri skickas från en SDK som skapats med appen, som analyseras och visas i Application Insights-tjänsten i Azure.

Om du vill ha mer specialiserad analytics exportera telemetri dataströmmen till en databas eller till Power BI eller andra verktyg.

**Application Insights-scenarier**

Du utvecklar en app. Det kan vara ett webbprogram eller en enhetsapp eller en enhetsapp med en webb-serverdel.

* Finjustera prestanda för din app när den har publicerats, eller när den är i belastningstester.  Application Insights aggregerar telemetri från alla installerade instanser och ger dig diagram över svarstider, begäran och antalet undantag, beroende svarstider och andra nyckeltal. Dessa hjälpa dig att justera prestandan för din app. Du kan infoga kod för att rapportera mer specifik information om det behövs.
* Identifiera och diagnostisera problem i din aktiva app. Du kan få aviseringar via e-post om nyckeltal mellan godkända tröskelvärden. Du kan undersöka specifika användarsessioner, till exempel för att se den begäran som orsakade ett undantag.
* Spåra användning att utvärdera framgången för varje ny funktion. När du skapar en ny artikel användare kan planera att mäta hur mycket den används, och om användarna få sina målen. Application Insights ger grundläggande användningsdata, till exempel Webbsidevyer, och du kan infoga kod för att spåra användarupplevelsen i detalj.

### <a name="automation"></a>Automation
Ingen gillar slösar tid göra samma manuella processer flera gånger. Azure Automation kan du skapa, övervaka, hantera och distribuera resurser i Azure-miljön.  

Automation använder ”runbooks”, som använder Windows PowerShell-arbetsflöden (jämfört med vanliga PowerShell) under försättsbladen. Runbooks är avsedda att köras utan användaråtgärder. PowerShell-arbetsflöden kan tillståndet för ett skript som ska sparas på kontrollpunkter på vägen. Sedan om ett fel inträffar kan behöver du inte starta ett skript från början. Du kan starta om den senaste kontrollpunkten. Detta sparar mycket arbete att skapa skriptet hantera alla möjliga fel.

**Automatiseringsscenarier**

Azure Automation är bra att automatisera manuella, långvariga, felbenägna och ofta återkommande uppgifter i Azure.

### <a name="api-management"></a>API Management
Skapa och publicera programmerare programgränssnitt (API) på internet är ett vanligt sätt att tillhandahålla tjänster till program. Om dessa tjänster resellable (till exempel väder data), kan en organisation någon tredje part att komma åt tjänsterna samma mot en avgift. När du skalar till flera partners, behöver du vanligtvis optimera och styr åtkomsten.  Vissa partner behöva även data i ett annat format.

Azure API Management gör det lättare för organisationer att publicera API: er till partners, anställda och andra leverantörer på ett säkert sätt och i större skala. Det ger en annan API-slutpunkt och fungerar som proxy för att anropa faktisk slutpunkt när tillhandahåller tjänster som cachelagring, transformation, begränsning, åtkomstkontroll och analytics aggregering.

**Scenarier för API-hantering**

Anta att ditt företag har en uppsättning enheter som alla måste motringning till en central tjänst för att hämta data, till exempel ett leverans företag som har enheter i varje lastbil på vägen.  Har ska företaget du konfigurera ett system för att spåra eget lastbilar så att den kan förutsäga och uppdatera leveranstider på ett tillförlitligt sätt. Den kan vet hur många lastbilar har och planera på lämpligt sätt.  Varje lastbil måste en enhet som anropar tillbaka till en central plats med dess placering och hastighet data och kanske.

En kund leverans företagets skulle förmodligen också dra nytta av komma informationen placering.  Kunden kan använda för att vet hur långt produkter har överföras, där de fastna, hur mycket de betalar längs vissa vägar (om det kombineras med vad de betalt att leverera). Om företaget leverans sammanställer informationen redan, kan många kunder betalar för den.  Men sedan leverans företaget måste kunna erbjuda ett sätt att ge kunderna data. När de ger åtkomst till kunder kanske de inte har kontroll över hur ofta data efterfrågas. De måste ange regler om vem som kan komma åt vilka data. Alla dessa regler måste vara inbyggt i sina externt API. Detta är där API-hantering kan hjälpa.  

## <a name="identity-and-access"></a>Identitet och åtkomst
Arbeta med identity ingår i de flesta program. Att veta vem en användare är kan ett program bestämma hur den ska interagera med användaren. Azure tillhandahåller tjänster för att spåra identitet samt integrera den med identitetslagringar kanske du redan använder.

### <a name="active-directory"></a>Active Directory
Precis som de flesta katalogtjänster lagrar Azure Active Directory information om användare och organisationer som de tillhör. Användarna kan logga in därefter tillhandahåller dem med token presentera för begäran om att bevisa sin identitet. Här kan också synkronisera användarinformation med Windows Server Active Directory körs lokalt i det lokala nätverket. Metoder och dataformat som används av Azure Active Directory inte är identiska med de som används i Windows Server Active Directory, är de funktioner som den utför ganska lika.

Det är viktigt att förstå att Azure Active Directory har utformats för användning av molnprogram. Den kan användas av program som körs på Azure, till exempel eller för andra molnplattformar. Det används också av Microsofts egna molnprogram, till exempel de som finns i Office 365. Om du vill utöka ditt datacenter till molnet genom att använda Azure virtuella datorer och Azure Virtual Network dock Azure Active Directory är inte rätt val. I stället ska du vill köra Windows Server Active Directory på virtuella datorer.

Azure Active Directory innehåller en RESTful-API kallas Azure Active Directory Graph så att program som har åtkomst till information som den innehåller. Detta API kan program som körs på någon plattform åtkomst directory objekt och förhållanden mellan dem.  Ett auktoriserade program kan till exempel använda den här API för att lära dig om en användare och grupper som han hör till annan information. Program kan också visa relationer mellan användare deras sociala graph-låta de fungerar bättre med anslutningar mellan personer.

En funktion i den här tjänsten Azure Active Directory-åtkomstkontroll, gör det enklare för ett program att acceptera identitetsinformation från Facebook, Google, Windows Live ID och andra populära identitetsleverantörer. I stället för att kräva programmet för att förstå de olika dataformat och protokoll som används av var och en av dessa providers översätter åtkomstkontroll dem till ett enda vanliga format. Du kan också ett program som accepterar inloggningar från en eller flera Active Directory-domäner. En leverantör som tillhandahåller ett SaaS-program kan använda Azure Active Directory-åtkomstkontroll för att ge användare i var och en av dess kunder enkel inloggning till programmet.

Katalogtjänster är en core gäller lokala datorer. Det får inte vara konstigt att de är också viktigt i molnet.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
![Azure Multi-Factor Authentication](./media/fundamentals-introduction-to-azure/MFAIntroNew.png)   

*Bild: Multi-Factor Authentication tillhandahåller funktioner för ditt program att verifiera mer än en identifieringsmetod*

Säkerhet är alltid viktigt. Multifaktorautentisering (MFA) hjälper till att säkerställa att användarna själva åtkomst till sina konton. MFA (även kallat tvåfaktorsautentisering eller ”2FA”) kräver att användare anger två av dessa tre metoder för att verifiera identiteten för användarinloggningar och transaktioner.

* Något du vet (normalt ett lösenord)
* Något du har (betrodda enheter som inte enkelt dubbleras, t.ex. en telefon)
* Något du är (biometrik)

När en användare loggar in, kan du så kräver dem också verifiera sin identitet med en mobilapp, ett telefonsamtal eller ett textmeddelande i kombination med sitt lösenord. Som standard stöder användning av lösenord som enda verifieringsmetod för användarinloggningar i Azure Active Directory. Du kan använda MFA tillsammans med Azure AD eller med anpassade program och kataloger med hjälp av MFA-SDK. Du kan också använda den tillsammans med lokala program med hjälp av Multi-Factor Authentication-servern.

**MFA-scenarier**

Logga in skydd på känsliga konton, till exempel bank inloggningar och koden åtkomst till datakällan där obehörig transaktion kan ha en hög finansiella eller immateriella egenskap kostnad.   

## <a name="mobile"></a>Mobil
Om du skapar en app för en mobil enhet kan det hjälpa Azure lagrar data i molnet, autentisera användare och skicka push-meddelanden utan att skriva en massa anpassad kod.

När du har skapa serverdelen för en mobil app med molntjänster, virtuella datorer eller Web Apps, kan du lägga mycket mindre tid som skriver underliggande tjänstkomponenter med Azures tjänster.

### <a name="mobile-apps"></a>Mobile Apps
![Mobile Apps](./media/fundamentals-introduction-to-azure/MobileServicesIntroNew.png)

*Bild: Mobilappar ger funktioner som krävs vanligtvis av program som har gränssnitt med mobila enheter.*

Azure Mobile Apps innehåller många användbara funktioner som kan du spara tid när du skapar en serverdel för mobila program. Det kan du göra enkla etablering och hantering av data som lagras i en SQL-databas. Du kan enkelt använda ytterligare data lagringsalternativ som blob-lagring eller MongoDB med serverkod. Mobilappar har stöd för meddelanden, men i vissa fall du i stället använda Notification Hubs som beskrivs nedan.  Tjänsten har också en REST-API som mobilappen kan anropa för att få jobbet gjort. Mobilappar ger också möjlighet att autentisera användare via Microsoft och Active Directory och andra välkända identitetsleverantörer som Facebook, Twitter och Google.   

Du kan använda andra Azure-tjänster som Service Bus- och arbetsroller och ansluta till lokalt system. Du kan även använda 3 part tillägg i Azure Store (till exempel SendGrid för e-post) för att ge ytterligare funktioner.

Intern klientbibliotek för Android, iOS, HTML/JavaScript, Windows Phone och Windows Store gör det enklare att utveckla för appar på alla större mobila plattformar. En REST-API kan du använda funktionerna för Mobile Services data och autentisering med appar på olika plattformar. En enda Mobiltjänst kan säkerhetskopiera flera klientappar så att du får en konsekvent användarupplevelse över enheter.

Eftersom Azure stöder omfattande utskalning redan, kan du hantera trafiken då appen blir mer populära.  Övervakning och loggning kan användas för att felsöka problem och hantera prestanda.

### <a name="notification-hubs"></a>Notification Hubs
![NotificationHubs](./media/fundamentals-introduction-to-azure/NotificationHubsIntroNew.png)  

*Bild: Notification Hubs ger funktioner som krävs vanligtvis av program som har gränssnitt med mobila enheter.*

Medan du kan skriva kod för att göra meddelanden i Azure Mobile Apps optimeras Notification Hubs för att sända miljoner personanpassat push-meddelanden i minuter.  Du behöver inte bry dig om detaljer som mobiloperatör eller enhetstillverkaren. Du kan rikta person eller miljontals användare med ett enda API-anrop.

Notification Hubs är avsedd att fungera med alla serverdelar. Du kan använda Azure Mobile Apps en anpassad serverdel i molnet som körs på valfri provider eller en lokal serverdel.

**Notification Hub scenarier** om du skriver ett mobila spel där spelare tog aktiverar kan du behöva meddela spelare 2 som player 1 slutfört sin tur. Du kan bara använda Mobile Apps om det är allt du behöver göra. Men om du har 100 000 användare spela spel och du vill skicka känslig kostnadsfria erbjudandet för alla Meddelandehubbar är ett bättre val.

Du kan skicka de senaste nyheterna, sporting händelser och produkten meddelande meddelanden till miljontals användare med låg latens. Företag kan meddela sina anställda om ny tid känsliga kommunikationer, till exempel säljleads, så att anställda inte behöver ständigt kontrollera e-post eller andra program för att hålla dig informerad. Du kan också skicka en-gång-lösenord som krävs för multi-Factor authentication.

## <a name="back-up"></a>Säkerhetskopiera
Alla företag behöver säkerhetskopiera och återställa data. Du kan använda Azure för att säkerhetskopiera och återställa ditt program i molnet eller lokalt. Azure erbjuder olika alternativ för att hjälpa beroende på vilken typ av säkerhetskopia.

### <a name="site-recovery"></a>Webbplatsåterställning
Med hjälp av Azure Site Recovery (tidigare Hyper-V Recovery Manager) kan du skydda viktiga program genom att samordna replikering och återställning mellan platser. Site Recovery tillhandahåller möjligheten att skydda baserat på Hyper-v, VMWare eller SAN-nätverk till dina egna sekundär plats, till en värd plats eller till Azure-program och undvika den kostnad och komplexitet för att skapa och hantera egna sekundär plats. Azure krypteras data och kommunikation och du har rätt att aktivera kryptering för data i vila för.

Den övervakar kontinuerligt hälsotillståndet för dina tjänster och hjälper till att automatisera rätt återställningen av tjänster vid ett avbrott för platsen på det primära datacentrat. Virtuella datorer kan göras tillgängliga med hjälp av dirigering för att en tjänst snabbt ska kunna återställas, till och med för avancerade flerskiktade arbetsbelastningar.

Site Recovery fungerar med befintlig teknik som Hyper-V-replikering, System Center och SQL Server alltid aktiverad. Checka ut [översikt över Azure Site Recovery](site-recovery/site-recovery-overview.md) för mer information.

### <a name="azure-backup"></a>Azure Backup
![Azure Backup](./media/fundamentals-introduction-to-azure/AzureBackupIntroNew.png)  

*Bild: Azure-säkerhetskopiering säkerhetskopierar data från lokala Windows-servrar till molnet.*  

Azure-säkerhetskopiering säkerhetskopierar data från lokala servrar som kör Windows Server till molnet. Du kan hantera dina säkerhetskopieringar direkt från säkerhetskopieringsverktyg i Windows Server 2012, Windows Server 2012 Essentials eller System Center 2012 – Data Protection Manager. Du kan också använda en särskild backup-agenten.

Data är säkrare eftersom krypteras säkerhetskopior före överföringen och lagras krypterade i Azure och skyddas av ett certifikat som du överför. Tjänsten använder samma skydd för redundant och hög tillgänglighet finns i Azure Storage.  Du kan säkerhetskopiera filer och mappar enligt ett schema eller direkt, med fullständig eller inkrementell säkerhetskopiering. När data har säkerhetskopierats till molnet kan behöriga användare enkelt återställa säkerhetskopior till valfri server. Den erbjuder konfigurerbara datakvarhållningsprinciper, komprimering och data transfer begränsning så att du kan hantera kostnaden för att lagra och överföra data.

**Scenarier för Azure-säkerhetskopiering**

Om du redan använder Windows Server eller System Center, är Azure-säkerhetskopiering en fysisk lösning för att säkerhetskopiera dina servrar filsystem, virtuella datorer och SQL Server-databaser.  Den fungerar med krypterade, sparse och komprimerade filer. Det finns vissa begränsningar, så du bör [Kontrollera kraven för Azure Backup](http://technet.microsoft.com/library/dn296608.aspx) första.

## <a name="messaging-and-integration"></a>Meddelandefunktioner och integration
Oavsett vad det gör, måste kod ofta interagera med annan kod.  I vissa fall är allt som behövs grundläggande köade meddelanden. I annat fall krävs mer komplexa interaktioner. Azure tillhandahåller ett antal olika sätt att lösa dessa problem. Bild 5 visar alternativen.

### <a name="queues"></a>Köer
![Azure Service Bus Relay](./media/fundamentals-introduction-to-azure/QueuesIntroNew.png)

*Bild: Köer kan förlora koppling mellan delar av ett program och underlätta skalning.*  

Queuing är en enkel idé: ett program visas ett meddelande i en kö och meddelandet så småningom läsa av ett annat program. Om ditt program behöver bara den här enkla tjänsten, kan Azure köer vara det bästa valet.

Ange liknande queuing tjänster på grund av hur Azure vuxen över tid, Azure Storage-köer och Service Bus-köer. Beskrivs skälen varför du vill använda en via en annan i ganska tekniska dokumentet [Azure köer och Service Bus-köer - skillnad från och med](http://msdn.microsoft.com/library/azure/hh767287.aspx).  I många fall är antingen kommer att fungera.

**Scenarier för kön**

Ett vanligt användningsområde för köer är idag så att en webb-rollinstans som kommunicerar med en worker rollinstans inom samma Cloud Services-program.

Anta att du skapar ett Azure-program för delning av video. Programmet består av PHP-kod som körs i en webbroll som gör att användare överför och titta på videor, tillsammans med en arbetsroll som implementerats i C# som översätter överförda video till olika format.

När en web rollinstans hämtar en ny video från en användare, kan den lagra videon i en blob och skicka ett meddelande till en arbetsroll via en kö om den var du hittar den här nya videon. En worker-rollen instans-den inte roll som en kommer sedan läsa meddelandet från kön och göra nödvändiga video översättningar i bakgrunden.

Ett program i det här sättet att strukturera kan asynkron bearbetning och gör det också programmet enklare att skala, eftersom antalet rollinstanser för webb och worker rollinstanser varieras oberoende av varandra. Du kan också använda köstorleken som en utlösare för att skala antalet arbetsroller uppåt och nedåt. För hög och du lägga till fler roller. Du kan minska antalet roller för att spara pengar som körs när det hämtar lägre.  

Du kan använda den här samma mönster mellan många olika delar av programmet även om de inte använder webb-och arbetsroller.  Du kan skala delar på endera sidan av kön uppåt och nedåt som begäran och bearbetningstid kräver.

### <a name="service-bus"></a>Service Bus
Om de körs i molnet, i ditt datacenter på en mobil enhet eller någon annanstans, behöver program interagera. Målet med Azure Service Bus är så att program som körs nästan var som helst utbyta data.

Förutom de köer (1) ovan, innehåller Service Bus även andra kommunikationsmetoder.

#### <a name="service-bus-relay"></a>Service Bus Relay
![Azure Service Bus Relay](./media/fundamentals-introduction-to-azure/ServiceBusRelayIntroNew.png)

*Bild: Service Bus Relay tillåter kommunikation mellan program på olika sidor av en brandvägg.*

Service Bus kan direkt kommunikation via dess vidarebefordringstjänst, vilket ger ett säkert sätt att interagera via brandväggar. Service Bus reläer Aktivera program för att kommunicera genom att utbyta meddelanden via en slutpunkt som finns i molnet i stället för lokalt.

**Service Bus Relay-scenarier**

Program som kommunicerar via Service Bus kan vara Azure-program eller program som körs på vissa andra molnplattform. De kan även vara program som körs utanför moln, men. Se exempelvis ett flygbolag som implementerar reservation tjänster på datorer i ett eget datacenter. Flygbolagen måste exponera dessa tjänster för många klienter, inklusive incheckningskiosker i flygplatser reservation agent terminaler och kanske även kunders telefoner. Kan den använda Service Bus gör detta genom att skapa löst kopplade samverkan mellan olika program.

#### <a name="service-bus-topics-and-subscriptions"></a>Service Bus-ämnen och prenumerationer
![Azure Service Bus-ämnen](./media/fundamentals-introduction-to-azure/ServiceBusTopicsSubsIntroNew.png)   
 *Bild: Service Bus-ämnen kan flera appar anslå meddelanden och andra program för att prenumerera på att ta emot meddelanden som uppfyller ett visst villkor.*

Service Bus innehåller en publicera och prenumerera-funktion som kallas ämnen och prenumerationer. Med publish-subscribe skicka ett program meddelanden till ett ämne, medan andra program kan skapa prenumerationer till det här avsnittet. Detta kan en-till-många-kommunikationen mellan en uppsättning program, så att samma meddelande läsas av flera mottagare.

**Service Bus-ämnen och prenumerationer scenarier**

När du ställer in där det finns många meddelanden som är alla viktig, men olika underordnade system behöver bara lyssna på olika delar av kommunikationen, Service Bus-ämne och -prenumerationer är ett bra alternativ.

### <a name="biztalk-services"></a>BizTalk Services
![BizTalk-tjänst](./media/fundamentals-introduction-to-azure/BizTalkServicesIntroNew.png)   
 *Bild: BizTalk-tjänst ger möjlighet att omvandla XML-meddelanden format i molnet.*

Ibland behöver ansluta datorer som kommunicerar med olika meddelanden format. Det är vanligt för företag att ha olika databasen scheman och XML-meddelanden format, även om en gemensam standard är tillgänglig. Du kan använda BizTalk Server lokalt för att integrera olika system i stället för att skriva mycket anpassade kod.  Azure BizTalk-tjänster innehåller samma typ av tjänst, men i molnet. Du kan betalar bara vad du använder och oroa dig inte om skala som du skulle behöva lokalt.

**BizTalk Services-scenarier**

Interaktioner Business-to-Business (B2B) kräver ofta den här typen av översättning.  Till exempel måste ett företag skapa flygplan ordning delar från dess leverantörer för olika delar. Har många delar leverantörer.  Dessa order ska automatiseras för att gå direkt från flygplan builders system i leverantörer system.  Varken företag vill ändra core system och meddelandeformat och det är troligt att dessa format är samma. BizTalk-tjänst kan ta meddelanden och översätta mellan de nya format båda hållen. Flygplan leverantören kan göra arbetet för att översätta eller olika leverantörer kan, beroende på vem vill mer kontroll och mängden översättning som behövs.     

## <a name="compute-assistance"></a>Compute-hjälp
Azure tillhandahåller stöd för tjänster som inte behöver köras hela tiden.  

### <a name="scheduler"></a>Scheduler
![Azure Scheduler](./media/fundamentals-introduction-to-azure/SchedulerIntroNew.png)   
*Bild: Azure Schemaläggaren kan du schemalägga vid en viss tid för en viss tid.*

Ibland behöver bara program som körs vid en viss tid. I Azure, kan du spara pengar med den här typen av app i stället för att låta ett program bara hålla körs 24 x 7 väntar på att data att bearbeta. Azure Schemaläggaren kan du schemalägga när ett program ska köras baserat på ett intervall eller en kalender. Det är tillförlitlig och verifierar att en process körs även om det förekommer fel för nätverk, datorer och data center. Du kan använda Schemaläggaren REST API för att hantera dessa åtgärder.

När en schemalagd larm Scheduler HTTP eller HTTPS-meddelanden skickas till en viss slutpunkt eller placera ett meddelande i en kö för lagring.  Så behöver du ha tillämpningsprogrammet har en tillgänglig slutpunkt eller det övervaka kön för lagring. Den kan sedan utföra de åtgärder som den är programmerad att när det hämtar meddelandet.

**Scenarier för Schemaläggaren**

* Återkommande programmet åtgärder: exempelvis kan en tjänst med jämna mellanrum hämta data från twitter och samla in data i en vanlig feed.
* Dagligt underhåll: loggen bearbetning eller rensas, säkerhetskopiera och andra periodvis schemalägga aktiviteter.
* Aktiviteter som körs på natten.
* Web applications uppgifter som dagliga rensning av loggar, göra säkerhetskopior och andra underhållsaktiviteter. En administratör kan välja att säkerhetskopiera sin databas kl 1 varje dag för nästa nio månader, till exempel.

Scheduler API kan du skapa, uppdatera, ta bort, visa och hantera jobbsamlingar och schemalagda jobb programmässigt.

## <a name="performance"></a>Prestanda
Prestanda är alltid viktigt för ett program. Program tenderar att få åtkomst till samma data flera gånger. Ett sätt att förbättra prestanda är att behålla en kopia av data närmare till programmet, vilket minimerar den tid som behövs för att hämta den. Azure tillhandahåller olika tjänster för att göra detta.

### <a name="azure-caching"></a>Azures cachelagring
![Azures cachelagring](./media/fundamentals-introduction-to-azure/AzureCacheIntroNew.png)   
 **Bild: Ett Azure-program kan cachelagra data i minnet och även dela den på många arbetsroller**

Åtkomst till data som lagras i något av hantering av Azures tjänster SQL-databas, tabeller eller BLOB-är ganska snabbt. Har åtkomst till data som lagras i minnet är snabbare. Därför förbättra att behålla en kopia i minnet av data som används ofta programmets prestanda. Du kan använda Azures InMemory-cachelagring för att göra detta.

Ett Cloud Services-program kan lagra data i det här cacheminnet och sedan hämta direkt utan att behöva komma åt beständig lagring. Cachen kan underhållas i programmets virtuella datorer eller anges av virtuella datorer som är dedikerad enbart för cachelagring. I båda fallen cachen kan distribueras, med data som den innehåller sprids över flera virtuella datorer i ett Azure-datacenter.

Azure har ett antal olika cache-tekniker som har flyttat över tid. I den ordning som de har introducerats finns en delad i rollen hanteras och Redis-cache. Delade cachelagring är en äldre teknik och du bör inte skapa nya implementeringar med den. Hanterade cacheminnet har samma funktioner för cachelagring i Rollinstanser, men som hanterad tjänst utanför Azure-hanteringsportalen. Redis-Cache är i förhandsgranskningen. Redis-implementering har det största antalet funktioner och rekommenderas när du skriver nya cachelagring kod.

**Scenarier för Azure Cache**

Ett program som läser en produktkatalogen upprepade gånger kan dra nytta av den här typen av cachelagring, till exempel sedan data måste blir tillgängliga snabbare. Tekniken stöder också låsa, så att den användas med läsning och skrivning som skrivskyddade data. Och ASP.NET-program kan använda tjänsten för att lagra sessionsdata med bara en konfigurationsändring.

### <a name="content-delivery-network"></a>Content Delivery Network
![Azure CDN](./media/fundamentals-introduction-to-azure/CDNIntroNew.png)   
 **Bild: Kopior av en blob kan cachelagras på platser över hela världen.**

Anta att du behöver lagra blob-data som ska användas av användare i världen. Kanske är det en video senaste VM matchningen, till exempel eller drivrutinsuppdateringar populära e-bok. Spara en kopia av data i Azure-datacenter hjälper, men om det finns många användare kan det förmodligen inte är tillräckligt. Du kan använda Azure CDN för ännu bättre prestanda.

CDN har dussintals platser över hela världen, alla kan lagra kopior av Azure-blobbar. Första gången en användare i vissa delar av världen kommer åt en viss blob kopieras av informationen från ett Azure-datacenter till lokal CDN lagring i den geografiska. Senare, har åtkomst från någon del av världen att använda blob-kopia som cachelagrats i CDN-de behöver inte gå till det närmaste Azure-datacentret. Resultatet är snabbare åtkomst till data som ofta används av användare var som helst i världen.

**CDN-scenarier**

Det är vanligt att använda CDN med Media Services för att leverera video över hela världen. Video är vanligtvis stor och kräver en stor mängd bandbredd.  Media Services är talade vi om någon annanstans på den här sidan.

## <a name="big-data-and-big-compute"></a>Big Data och stor beräkning
### <a name="hdinsight-hadoop"></a>HDInsight (Hadoop)
![HDInsight](./media/fundamentals-introduction-to-azure/HDInsightIntroNew.png)   
 **Bild: Kan hjälpa dig med bulk-bearbetning av stora mängder data med HDInsight**

Flesta dataanalys har gjorts på relationella data som lagras i ett datalager som skapats med en relationella DBMS för många år. Den här typen av Företagsanalys fortfarande är viktigt och blir för lång tid att starta. Men vad händer om data som du vill analysera är så stort att relationsdatabaser bara inte kan hantera? Och anta att data inte är relationell? Det kan vara serverloggen i ett datacenter, till exempel historiska händelsedata från sensorer eller något annat. I detta fall har du som kallas stordata problem. Du måste en annan metod.

Företag tekniken idag för analys av stordata är Hadoop. En Apache öppna projekt, den här tekniken lagrar data med Hadoop Distributed File System (HDFS) och sedan kan utvecklare skapa MapReduce-jobb för att analysera data. HDFS sprider ut data över flera servrar och sedan kör mängder MapReduce-jobb på var och en, så att big data bearbetas parallellt.

HDInsight är namnet på den Azure Apache Hadoop-baserad tjänst. HDInsight kan HDFS lagra data på klustret och distribuera den över flera virtuella datorer. Det sprids också logik på ett MapReduce-jobb över de virtuella datorerna. Precis som med lokala Hadoop data är bearbetade lokalt logiken och data det fungerar på finns i samma virtuella dator- och parallellt för bättre prestanda. HDInsight kan också lagra data i Azure Storage valvet (ASV), som använder blobbar.  Med hjälp av ASV kan du spara pengar eftersom du kan ta bort ditt HDInsight-kluster som, utan att dina data i molnet.

HDinsight stöder andra komponenter i Hadoop-ekosystemet, inklusive Hive och Pig. Microsoft har också skapat komponenter som gör det lättare att arbeta med data som produceras av HDInsight med hjälp av traditionella BI-verktyg, till exempel HiveODBC nätverkskort och Data Explorer som fungerar med Excel.

### <a name="high-performance-computing-big-compute"></a>Högpresterande datorbearbetning (stort beräkning)
En av de mest bra sätten att använda en molnplattform är att köra högpresterande datorbearbetning (HPC) och andra ”stor Compute” program. Exempel inkluderar specialiserade engineering program som skapats för att använda branschstandardiserad Message Passing Interface (MPI) samt s.k. embarrassingly parallella program, till exempel finansiella risk modeller.

Huvudsakligen stort Compute är kod körs på många datorer samtidigt. I Azure datorer det innebär kör många virtuella samtidigt, alla arbetar parallellt för att lösa problem. Detta kräver något sätt till resurser och att schemalägga program, d.v.s., distribuerar sitt arbete över dessa instanser. Microsofts ledigt HPC Pack och andra lösningar för beräkning kan fungera bra i Azure, dra fördel av Azure beräkning och infrastruktur-tjänster att lägga till kapacitet på begäran till en lokal beräkningskluster eller köra ett stort Compute program helt i molnet.

Azure tillhandahåller ett intervall för VM instans storlekar med olika konfigurationer för CPU kärnor, minne, diskutrymme och andra egenskaper att uppfylla kraven i olika program. Den nyligen lanserade A8- och A9 instanser fungerar bra för många compute-intensiv arbetsbelastning och parallella MPI program särskilt eftersom de har hög hastighet, flera kärnor processorer och stora mängder minne. I vissa konfigurationer dra instanserna nytta av ett nätverk med låg latens och hög genomströmning program i molnet som innehåller remote direct memory access (RDMA)-teknik för maximal effektivitet parallella MPI applikationer.

Azure erbjuder också ett stort Compute programutvecklare och partners en fullständig uppsättning beräkningskapacitet, tjänster, arkitektur val och utvecklingsverktyg. Azure stöder anpassade stort Compute-arbetsflöden som rör särskilda data arbetsflöden och jobbet och schemaläggningstjänsten mönster som kan skalas till tusentals compute kärnor.

## <a name="media"></a>Media
![Azure Media Services](./media/fundamentals-introduction-to-azure/MediaServicesIntroNew.png)   
 **Bild: Media Services är en plattform för program som tillhandahåller media video- och andra klienter i världen.**

Video utgör en stor del av Internettrafik idag och så stor procentandel blir ännu större i morgon. Men att tillhandahålla en video på webben är inte enkelt. Det finns många variabler, till exempel kodning algoritmen och skärmupplösning på användarens skärm. Video tenderar också att ha belastning i begäran som en lördag natt insamling när många bestämmer de vill titta på en online-film.

Dess popularitet är, det ett säkert val att många nya program kommer att skapas att använda bilden. Men alla behöver lösa en del av samma problem och gör dem lösa dessa problem på sin egen gör meningslös. En bättre metod är att skapa en plattform som innehåller vanliga lösningar för många program att använda. Och skapa den här plattformen i molnet har vissa Rensa fördelar. Det kan vara tillgänglig på grundval av betalning per användning och den kan också hantera variationer i begäran video program mot ofta.

Azure Media Services löser problemet. Det ger en uppsättning komponenter i molnet som gör liv enklare för användare att skapa och köra program med hjälp av video och andra.

Media Services tillhandahåller en uppsättning komponenter för program som fungerar med video och andra media som du ser i bilden. Innehåller till exempel ett medium infognings-komponent som du vill överföra video till Media Services (där den lagras i Azure BLOB), en kodning komponenten som har stöd för olika ljud och video-format, en innehållsskydd-komponent som ger digital rights management, en komponent för att infoga annonser i en video-ström, komponenter för strömning och mer. Microsoft-partner kan också ange komponenter för plattformen och har Microsoft distribuera dessa komponenter och debiterar åt.

Program som använder den här plattformen kan köras på Azure eller någon annanstans. Till exempel ett skrivbordsprogram för en video produktion house kan användarna ladda upp video till Media Services sedan behandlas på olika sätt. Alternativt kan en molnbaserad innehållshantering-tjänsten körs på Azure förlitar sig på Media Services för att bearbeta och distribuera video. När den körs och vad som har väljer varje program vilka komponenter som ska användas, komma åt dem via RESTful-gränssnitt.

Om du vill distribuera den genererar kan ett program använda Azure CDN, en annan CDN eller bara skicka bits direkt till användare. Men när det hämtar det kan video som skapats med hjälp av Media Services användas av olika klientsystem, inklusive Windows, Macintosh, HTML 5, iOS, Android, Windows Phone, Flash och Silverlight. Målet är att göra det enklare att skapa media för moderna program.

**Referenser**

En mer visuell översikt över hur Media Services fungerar kan du hämta den [Azure Media Services affisch][Azure Media Services Poster].

## <a name="commerce"></a>Handel
Ökningen av programvara som en tjänst förändrar hur vi skapa program. Det även Omforma hur vi säljer program. Eftersom SaaS-program finns i molnet är logiskt att dess potentiella kunder ska söka efter lösningar online. Och den här ändringen gäller för data samt vad gäller program. Varför bör inte personer ut till molnet för kommersiellt tillgängliga datauppsättningar? Microsoft adresser båda dessa problem med den [Azure Marketplace](https://azure.microsoft.com/marketplace/).

![Azure handel](./media/fundamentals-introduction-to-azure/CommerceIntroNew.png)   
 **Bild: Azure Marketplace och Azure Store kan du söka efter och köpa Azure-program och kommersiella datauppsättningar och använda dem som en del av din Azure-program.**

Skillnaden mellan dem är att Marketplace ligger utanför Azure-hanteringsportalen, men Store kan nås från i portalen. Potentiella kunder kan söka efter Azure-program som uppfyller deras behov. Kunder kan söka efter kommersiell datauppsättningar, inklusive demografisk data, ekonomiska data, geografiska data och mycket mer. När de hittar något de som kommer de åt den från leverantören, direkt via Marketplace eller Store-webbplatser eller i vissa fall från hanteringsportalen. Program kan också använda API: et för Bing Search via Marketplace ge dem åtkomst till web sökresultaten.

**Commerce-scenarier**

SendGrid är ett program i Azure Store där du kan skicka e-post. Den erbjuder ytterligare funktioner som tillförlitliga leverans och statistik.  Du kan köpa programmet och relaterade tjänster i stället försök att skapa denna infrastruktur själv.  

## <a name="getting-started"></a>Komma igång
Nu när du har den stora bilden är nästa steg att skriva ditt första Azure-program. Välj önskat språk och [hämta lämplig SDK](/downloads/), och gå för den. Cloud computing är den nya standarden--Kom igång nu.

[Azure Media Services Poster]: http://azure.microsoft.com/documentation/infographics/media-services/
