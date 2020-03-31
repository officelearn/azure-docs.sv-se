---
title: Data management gateway för data factory
description: Konfigurera en datagateway för att flytta data mellan lokala och molnet. Använd Data Management Gateway i Azure Data Factory för att flytta dina data.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 1340c205477b256e3d96ff7ccacb64e575725c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065412"
---
# <a name="data-management-gateway"></a>Gateway för datahantering
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [självvärderade integrationskörning i](../create-self-hosted-integration-runtime.md).

> [!NOTE]
> Data Management Gateway har nu bytt namn till Självvärdförd Integration Runtime.

Datahanteringsgatewayen är en klientagent som du måste installera i din lokala miljö för att kopiera data mellan moln- och lokala datalager. De lokala datalager som stöds av Data Factory visas i avsnittet [Datakällor som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

Den här artikeln kompletterar genomgången i artikeln [Flytta data mellan lokala data och molnlager.](data-factory-move-data-between-onprem-and-cloud.md) I genomgången skapar du en pipeline som använder gatewayen för att flytta data från en lokal SQL Server-databas till en Azure-blob. Den här artikeln innehåller detaljerad detaljerad information om datahanteringsgatewayen.

Du kan skala ut en datahanteringsgateway genom att associera flera lokala datorer med gatewayen. Du kan skala upp genom att öka antalet dataförflyttningsjobb som kan köras samtidigt på en nod. Den här funktionen är också tillgänglig för en logisk gateway med en enda nod. Mer information finns [i Scaling data management gateway i Azure Data](data-factory-data-management-gateway-high-availability-scalability.md) Factory-artikeln.

> [!NOTE]
> För närvarande stöder gateway endast kopieringsaktivitet och lagrad proceduraktivitet i Data Factory. Det går inte att använda gatewayen från en anpassad aktivitet för att komma åt lokala datakällor.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
### <a name="capabilities-of-data-management-gateway"></a>Funktioner för datahanteringsgateway
Datahanteringsgateway ger följande funktioner:

* Modellera lokala datakällor och molndatakällor i samma datafabrik och flytta data.
* Ha en enda glasruta för övervakning och hantering med insyn i gatewaystatus från sidan Data Factory.
* Hantera åtkomsten till lokala datakällor på ett säkert sätt.
  * Inga ändringar krävs för företagets brandvägg. Gateway gör bara utgående HTTP-baserade anslutningar för att öppna internet.
  * Kryptera autentiseringsuppgifter för dina lokala datalager med ditt certifikat.
* Flytta data effektivt – data överförs parallellt, flexibelt till intermittenta nätverksproblem med logik för automatiskt återförsök.

### <a name="command-flow-and-data-flow"></a>Kommandoflöde och dataflöde
När du använder en kopieringsaktivitet för att kopiera data mellan lokala och moln använder aktiviteten en gateway för att överföra data från lokal datakälla till molnet och vice versa.

Här är dataflödet på hög nivå för och sammanfattning ![av steg för kopiering med datagateway: Dataflöde med gateway](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Datautvecklare skapar en gateway för en Azure Data Factory med antingen [Azure-portalen](https://portal.azure.com) eller [PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.datafactory/).
2. Datautvecklare skapar en länkad tjänst för ett lokalt datalager genom att ange gatewayen. Som en del av inrättandet av den länkade tjänsten använder datautvecklaren programmet Inställningsautentiseringsuppgifter för att ange autentiseringstyper och autentiseringsuppgifter. Dialogrutan Inställningsautentiseringsprogram kommunicerar med datalagret för att testa anslutningen och gatewayen för att spara autentiseringsuppgifter.
3. Gateway krypterar autentiseringsuppgifterna med certifikatet som är associerat med gatewayen (tillhandahålls av datautvecklaren) innan autentiseringsuppgifterna sparas i molnet.
4. Data Factory-tjänsten kommunicerar med gatewayen för schemaläggning & hantering av jobb via en kontrollkanal som använder en delad Azure-tjänstbusskö. När ett kopieringsaktivitetsjobb måste sparkas av köar Data Factory begäran tillsammans med information om autentiseringsuppgifter. Gateway startar jobbet efter avsökning av kön.
5. Gatewayen dekrypterar autentiseringsuppgifterna med samma certifikat och ansluter sedan till det lokala dataarkivet med rätt autentiseringstyp och autentiseringsuppgifter.
6. Gatewayen kopierar data från ett lokalt arkiv till en molnlagring, eller tvärtom beroende på hur kopieringsaktiviteten är konfigurerad i datapipelinen. I det här steget kommunicerar gatewayen direkt med molnbaserade lagringstjänster som Azure Blob Storage via en säker (HTTPS)-kanal.

### <a name="considerations-for-using-gateway"></a>Överväganden för att använda gateway
* En enda instans av datahanteringsgateway kan användas för flera lokala datakällor. En **enda gateway-instans är dock knuten till endast en Azure-datafabrik** och kan inte delas med en annan datafabrik.
* Du kan bara ha **en instans av datahanteringsgatewayen** installerad på en enda dator. Anta att du har två datafabriker som behöver komma åt lokala datakällor, du måste installera gateways på två lokala datorer. Med andra ord är en gateway knuten till en specifik datafabrik
* **Gatewayen behöver inte finnas på samma dator som datakällan**. Men om du har en gateway närmare datakällan minskar tiden för gatewayen att ansluta till datakällan. Vi rekommenderar att du installerar gatewayen på en dator som skiljer sig från den som är värd för lokal datakälla. När gatewayen och datakällan finns på olika datorer konkurrerar gatewayen inte om resurser med datakällan.
* Du kan ha **flera gateways på olika datorer som ansluter till samma lokala datakälla**. Du kan till exempel ha två gateways som betjänar två datafabriker, men samma lokala datakälla registreras med båda datafabrikerna.
* Om du redan har en gateway installerad på datorn som betjänar ett **Power BI-scenario** installerar du en **separat gateway för Azure Data Factory** på en annan dator.
* Gateway måste användas även när du använder **ExpressRoute**.
* Behandla datakällan som en lokal datakälla (som finns bakom en brandvägg) även när du använder **ExpressRoute**. Använd gatewayen för att upprätta anslutning mellan tjänsten och datakällan.
* Du måste **använda gatewayen** även om datalagret finns i molnet på en **Azure IaaS VM**.

## <a name="installation"></a>Installation
### <a name="prerequisites"></a>Krav
* **Operativsystemversionerna** som stöds är Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Installation av datahanteringsgatewayen på en domänkontrollant stöds för närvarande inte.
* .NET Framework 4.5.1 eller högre krävs. Om du installerar gateway på en Windows 7-dator installerar du .NET Framework 4.5 eller senare. Mer information finns i [.NET Framework System Requirements.](https://msdn.microsoft.com/library/8z6watww.aspx)
* Den rekommenderade **konfigurationen** för gateway-datorn är minst 2 GHz, 4 kärnor, 8 GB RAM och 80 GB-disk.
* Om värddatorn vilolägen, svarar gatewayen inte på databegäranden. Konfigurera därför ett lämpligt **energischema** på datorn innan du installerar gatewayen. Om datorn är konfigurerad för viloläge uppmanar gatewayinstallationen ett meddelande.
* Du måste vara administratör på datorn för att kunna installera och konfigurera datahanteringsgatewayen. Du kan lägga till ytterligare användare i den lokala Windows-gruppen användare för **datahanteringsgateway.** Medlemmarna i den här gruppen kan använda **konfigurationshanteraren för datahanteringsgateway** för att konfigurera gatewayen.

När kopieringsaktivitet körs på en viss frekvens följer resursanvändningen (CPU, minne) på datorn också samma mönster med topp- och inaktiva tider. Resursutnyttjandet beror också mycket på mängden data som flyttas. När flera kopieringsjobb pågår visas resursanvändningen under rusningstid.

### <a name="installation-options"></a>Installationsalternativ
Datahanteringsgateway kan installeras på följande sätt:

* Genom att hämta ett MSI-installationspaket från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). MSI kan också användas för att uppgradera befintlig datahanteringsgateway till den senaste versionen, med alla inställningar bevarade.
* Genom att klicka på **Länken Hämta och installera datagateway** under MANUELL INSTALLATION eller Installera direkt på den här **datorn** under EXPRESS SETUP. Se [Flytta data mellan lokal och molnartikel](data-factory-move-data-between-onprem-and-cloud.md) för steg-för-steg-instruktioner om hur du använder expressinställningar. Det manuella steget tar dig till nedladdningscentret. Instruktionerna för att hämta och installera gatewayen från download center finns i nästa avsnitt.

### <a name="installation-best-practices"></a>Metodtips för installation:
1. Konfigurera energischema på värddatorn för gatewayen så att datorn inte försvinerar. Om värddatorn vilolägen, svarar gatewayen inte på databegäranden.
2. Säkerhetskopiera certifikatet som är associerat med gatewayen.

### <a name="install-the-gateway-from-download-center"></a>Installera gatewayen från download center
1. Gå till [nedladdningssidan för Microsoft Data Management Gateway](https://www.microsoft.com/download/details.aspx?id=39717).
2. Klicka på **Hämta**, välj **64-bitarsversionen** (32-bitars stöds inte längre) och klicka på **Nästa**.
3. Kör **MSI** direkt eller spara den på hårddisken och kör.
4. På **välkomstsidan** väljer du ett **språk** på **Nästa**.
5. **Acceptera** licensavtalet för slutanvändare och klicka på **Nästa**.
6. Välj **mapp** för att installera gatewayen och klicka på **Nästa**.
7. Klicka på **Installera**på sidan **Klar att installeras.**
8. Klicka på **Slutför** för att slutföra installationen.
9. Hämta nyckeln från Azure-portalen. Se nästa avsnitt för steg-för-steg-instruktioner.
10. Gör följande på sidan **Registrera gateway** i Configuration Manager för **datahanteringsgateway** som körs på datorn:
    1. Klistra in nyckeln i texten.
    2. Du kan också klicka på **Visa gateway-tangenten** för att visa nyckeltexten.
    3. Klicka på **Registrera**.

### <a name="register-gateway-using-key"></a>Registrera gateway med hjälp av nyckel
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Om du inte redan har skapat en logisk gateway i portalen
Om du vill skapa en gateway i portalen och hämta nyckeln från sidan **Konfigurera** följer du steg från genomgången i artikeln [Flytta data mellan lokala och moln.](data-factory-move-data-between-onprem-and-cloud.md)

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Om du redan har skapat den logiska gatewayen i portalen
1. I Azure-portalen navigerar du till sidan **Data factory** och klickar på panelen **Länkade tjänster.**

    ![Sidan Datafabrik](media/data-factory-data-management-gateway/data-factory-blade.png)
2. På sidan **Länkade tjänster** väljer du den logiska **gateway** som du skapade i portalen.

    ![logisk gateway](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. Klicka på **Hämta och installera datagateway**på sidan **Datagateway.**

    ![Ladda ner länk i portalen](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. Klicka på Återskapa **nyckel**på sidan **Konfigurera.** Klicka på Ja på varningsmeddelandet efter att ha läst det noggrant.

    ![Återskapa nyckel](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Klicka på Knappen Kopiera bredvid knappen Kopiera. Nyckeln kopieras till Urklipp.

    ![Kopiera nyckel](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ikoner/meddelanden för systemfältet
Följande bild visar några av de fackikoner som visas.

![ikoner för systemfältet](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Om du flyttar markören över systemfältets ikon/meddelande visas information om tillståndet för gateway-/uppdateringsåtgärden i ett popup-fönster.

### <a name="ports-and-firewall"></a>Portar och brandvägg
Det finns två brandväggar som du måste tänka på: **företagsbrandvägg** som körs på organisationens centrala router och **Windows-brandväggen** konfigurerad som en demon på den lokala datorn där gatewayen är installerad.

![Brandväggar](./media/data-factory-data-management-gateway/firewalls2.png)

På företagsbrandväggsnivå måste du konfigurera följande domäner och utgående portar:

| Domännamn | Portar | Beskrivning |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Används för kommunikation med Data Movement Service backend |
| *.core.windows.net |443 |Används för mellanlagd kopia med Azure Blob (om den är konfigurerad)|
| *.frontend.clouddatahub.net |443 |Används för kommunikation med Data Movement Service backend |
| *.servicebus.windows.net |9350-9354, 5671 |Servicebussrelä som tillval över TCP som används av kopieringsguiden |

På Windows-brandväggsnivå är dessa utgående portar normalt aktiverade. Om inte, kan du konfigurera domäner och portar därefter på gateway-datorn.

> [!NOTE]
> 1. Baserat på din källa / sänkor, kan du behöva vitlista ytterligare domäner och utgående portar i företagets / Windows-brandväggen.
> 2. För vissa molndatabaser (till [exempel: Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access), etc.), kan du behöva vitlista IP-adressen för Gateway-datorn i brandväggskonfigurationen.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Kopiera data från ett källdatalager till ett sink-datalager
Kontrollera att brandväggsreglerna är korrekt aktiverade i företagets brandvägg, Windows-brandväggen på gateway-datorn och själva datalagret. Om du aktiverar dessa regler kan gatewayen ansluta till både källa och diskho. Aktivera regler för varje datalager som ingår i kopieringen.

Om du till exempel vill kopiera från **ett lokalt datalager till en Azure SQL-databasmottagare eller en Azure SQL Data Warehouse-mottagare**gör du följande:

* Tillåt utgående **TCP-kommunikation** på port **1433** för både Windows-brandväggen och företagets brandvägg.
* Konfigurera brandväggsinställningarna för Azure SQL-server för att lägga till IP-adressen för gateway-datorn i listan över tillåtna IP-adresser.

> [!NOTE]
> Om brandväggen inte tillåter utgående port 1433 kan gatewayen inte komma åt Azure SQL direkt. I det här fallet kan du använda [stegvis kopia](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) till SQL Azure Database/ SQL Azure DW. I det här fallet behöver du bara HTTPS (port 443) för dataflyttningen.
>
>

### <a name="proxy-server-considerations"></a>Överväganden för proxyserver
Om företagets nätverksmiljö använder en proxyserver för att ansluta till Internet konfigurerar du datahanteringsgatewayen så att lämplig proxyinställningar används. Du kan ställa in proxyn under den inledande registreringsfasen.

![Ange proxy under registreringen](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Gateway använder proxyservern för att ansluta till molntjänsten. Klicka på **Ändra** länk under den första installationen. Dialogrutan **proxyinställning** visas.

![Ange proxy med config-hanteraren](media/data-factory-data-management-gateway/SetProxySettings.png)

Det finns tre konfigurationsalternativ:

* **Använd inte proxy:** Gateway använder inte uttryckligen någon proxy för att ansluta till molntjänster.
* **Använd systemproxy:** Gateway använder proxyinställningen som är konfigurerad i diahost.exe.config och diawp.exe.config. Om ingen proxy har konfigurerats i diahost.exe.config och diawp.exe.config ansluter gatewayen till molntjänsten direkt utan att gå via proxy.
* **Använd anpassad proxy:** Konfigurera HTTP-proxyinställningen som ska användas för gateway, i stället för att använda konfigurationer i diahost.exe.config och diawp.exe.config. Adress och port krävs. Användarnamn och lösenord är valfria beroende på proxyns autentiseringsinställning. Alla inställningar krypteras med gatewayens autentiseringsuppgifter och lagras lokalt på gateway-värddatorn.

Värdtjänsten för datahanteringsgateway startar om automatiskt när du har sparat de uppdaterade proxyinställningarna.

När gatewayen har registrerats använder du Configuration Manager för Data Management Gateway om du vill visa eller uppdatera proxyinställningar.

1. Starta **Konfigurationshanteraren för datahanteringsgateway**.
2. Växla till fliken **Inställningar.**
3. Klicka på **Ändra** länk i **HTTP-proxyavsnittet** om du vill starta dialogrutan **Ange HTTP-proxy.**
4. När du har klickat på knappen **Nästa** visas en varningsdialogruta där du ber om din tillåtelse att spara proxyinställningen och starta om värdtjänsten för gatewayen.

Du kan visa och uppdatera HTTP-proxy med hjälp av Configuration Manager-verktyget.

![Ange proxy med config-hanteraren](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Om du konfigurerar en proxyserver med NTLM-autentisering körs Gateway Host Service under domänkontot. Om du ändrar lösenordet för domänkontot senare, kom ihåg att uppdatera konfigurationsinställningarna för tjänsten och starta om den i enlighet med detta. På grund av detta krav föreslår vi att du använder ett dedikerat domänkonto för att komma åt proxyservern som inte kräver att du uppdaterar lösenordet ofta.
>
>

### <a name="configure-proxy-server-settings"></a>Konfigurera proxyserverinställningar
Om du väljer **Använd systemproxyinställning** för HTTP-proxyn använder gateway proxyinställningen i diahost.exe.config och diawp.exe.config. Om ingen proxy anges i diahost.exe.config och diawp.exe.config ansluter gatewayen till molntjänsten direkt utan att gå via proxy. Följande procedur innehåller instruktioner för uppdatering av filen diahost.exe.config.

1. I Utforskaren gör du en säker kopia av *C:\\\\\\Program Files Microsoft Data Management Gateway\\2.0\\Shared\\diahost.exe.config* för att säkerhetskopiera originalfilen.
2. Starta Notepad.exe som körs som administratör och öppna textfilen *C:\\\\\\Programfiler Microsoft Data Management\\Gateway 2.0\\Delad\\diahost.exe.config*. Du hittar standardtaggen för system.net som visas i följande kod:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Du kan sedan lägga till information om proxyservern enligt följande exempel:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Ytterligare egenskaper tillåts i proxytaggen för att ange de inställningar som krävs som scriptLocation. Se [proxyelement (Nätverksinställningar)](https://msdn.microsoft.com/library/sa91de1e.aspx) på syntaxen.

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Spara konfigurationsfilen på den ursprungliga platsen och starta sedan om tjänsten Data Management Gateway Host, som hämtar ändringarna. Så här startar du om tjänsten: använd tjänstappel från kontrollpanelen eller från Configuration Manager för **DataHantering gateway** > klicka på knappen **Stoppa tjänst** och klicka sedan på **Starttjänsten**. Om tjänsten inte startar är det troligt att en felaktig XML-taggsyntax har lagts till i programkonfigurationsfilen som redigerades.

> [!IMPORTANT]
> Glöm inte att uppdatera **både** diahost.exe.config och diawp.exe.config.

Utöver dessa punkter måste du också se till att Microsoft Azure finns i företagets vitlista. Listan över giltiga Microsoft Azure IP-adresser kan hämtas från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Möjliga symptom för problem med brandvägg och proxyserver
Om du stöter på fel som liknar följande, är det sannolikt på grund av felaktig konfiguration av brandväggen eller proxyservern, som blockerar gateway från att ansluta till Data Factory för att autentisera sig själv. Se föregående avsnitt för att säkerställa att brandväggen och proxyservern är korrekt konfigurerade.

1. NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k fÃ¶rsÃ¶k att registrera gateway-gatewayen visas fÃ¶10fel: "Det gick inte att registrera gateway-nyckeln. Innan du försöker registrera gatewaynyckeln igen, bekräfta att datahanteringsgatewayen är i ett anslutet tillstånd och att värdtjänsten för datahantering gateway är startad."
2. När du öppnar Configuration Manager visas status som "Frånkopplad" eller "Ansluta". När du visar Windows-händelseloggar under "Loggboken" > "Program- och tjänstloggar" > "Data Management Gateway" visas felmeddelanden som följande fel:`Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Öppen port 8050 för kryptering av autentiseringsuppgifter
**Inställningsautentiseringsprogrammet** använder inkommande port **8050** för att vidarebefordra autentiseringsuppgifter till gatewayen när du konfigurerar en lokal länkad tjänst i Azure-portalen. Under gateway-installationen öppnar gateway-installationen som standard den på gateway-datorn.

Om du använder en brandvägg från tredje part kan du öppna porten 8050 manuellt. Om du stöter på brandväggsproblem under gateway-installationen kan du prova att använda följande kommando för att installera gatewayen utan att konfigurera brandväggen.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Om du väljer att inte öppna port 8050 på gateway-datorn använder du andra mekanismer än att använda programmet **Inställningsautentisering för** att konfigurera autentiseringsuppgifter för datalager. Du kan till exempel använda [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell cmdlet. Se Ange autentiseringsuppgifter och säkerhetsavsnitt om hur autentiseringsuppgifter för datalager kan ställas in.

## <a name="update"></a>Uppdatering
Som standard uppdateras datahanteringsgatewayen automatiskt när en nyare version av gatewayen är tillgänglig. Gatewayen uppdateras inte förrän alla schemalagda aktiviteter har utförts. Inga ytterligare uppgifter bearbetas av gatewayen förrän uppdateringsåtgärden har slutförts. Om uppdateringen misslyckas återställs gatewayen till den gamla versionen.

Du ser den schemalagda uppdateringstiden på följande platser:

* Sidan gateway-egenskaper i Azure-portalen.
* Startsida för Configuration Manager för datahanteringsgateway
* Meddelande om meddelande om meddelande om systemfack.

På fliken Start i Configuration Manager för datahanteringsgateway visas uppdateringsschemat och den senaste gången gatewayen installerades/uppdaterades.

![Schemauppdateringar](media/data-factory-data-management-gateway/UpdateSection.png)

Du kan installera uppdateringen direkt eller vänta tills gatewayen uppdateras automatiskt vid den schemalagda tiden. Följande bild visar till exempel meddelandemeddelandet som visas i Configuration Manager för gateway tillsammans med knappen Uppdatera som du kan klicka på för att installera det direkt.

![Uppdatering i DMG Configuration Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Meddelandemeddelandet i systemfältet skulle se ut som visas i följande bild:

![Meddelande om systemfack](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Du ser status för uppdateringsåtgärd (manuell eller automatisk) i systemfältet. När du startar Configuration Manager för Gateway nästa gång visas ett meddelande i meddelandefältet om att gatewayen har uppdaterats tillsammans med en länk till [det nya avsnittet](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Så här inaktiverar/aktiverar du funktionen för automatisk uppdatering
Du kan inaktivera/aktivera funktionen för automatisk uppdatering genom att göra följande:

[För ennodgateway]
1. Starta Windows PowerShell på gateway-datorn.
2. Växla till *mappen\\\\C: Program Files\\Microsoft Integration\\Runtime\\ \\3.0 PowerShellScript.*
3. Kör följande kommando för att aktivera funktionen för automatisk uppdatering AV (inaktivera).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Så här slår du på den igen:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [För flera nod som är mycket tillgänglig och skalbar gateway](data-factory-data-management-gateway-high-availability-scalability.md)
1. Starta Windows PowerShell på gateway-datorn.
2. Växla till *mappen\\\\C: Program Files\\Microsoft Integration\\Runtime\\ \\3.0 PowerShellScript.*
3. Kör följande kommando för att aktivera funktionen för automatisk uppdatering AV (inaktivera).

    För gateway med hög tillgänglighetsfunktion krävs en extra AuthKey-param.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Så här slår du på den igen:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
När du har installerat gatewayen kan du starta Configuration Manager för Data Management Gateway på något av följande sätt:

1. Skriv **Datahanteringsgateway** i **sökfönstret** för att komma åt det här verktyget.
2. Kör den körbara *ConfigManager.exe* i mappen: *C:\\\\Programfiler\\Microsoft Data Management Gateway\\2.0\\Delad*.

### <a name="home-page"></a>Startsida
På startsidan kan du utföra följande åtgärder:

* Visa status för gatewayen (ansluten till molntjänsten osv.).
* **Registrera dig** med hjälp av en nyckel från portalen.
* **Stoppa** och starta **tjänsten Data Management Gateway Host** på gateway-datorn.
* **Schemalägg uppdateringar** vid en viss tidpunkt på dagarna.
* Visa det datum då gatewayen **senast uppdaterades**.

### <a name="settings-page"></a>Sidan Inställningar
På sidan Inställningar kan du utföra följande åtgärder:

* Visa, ändra och exportera **certifikat** som används av gatewayen. Det här certifikatet används för att kryptera autentiseringsuppgifter för datakällan.
* Ändra **HTTPS-port** för slutpunkten. Gatewayen öppnar en port för att ange autentiseringsuppgifterna för datakällan.
* **Slutpunktens status**
* Visa **SSL-certifikat** används för TLS/SSL-kommunikation mellan portalen och gatewayen för att ange autentiseringsuppgifter för datakällor.

### <a name="remote-access-from-intranet"></a>Fjärråtkomst från intranät
Den här funktionen kommer att aktiveras i framtiden. I de kommande uppdateringarna (v3.4 eller senare) låter vi dig aktivera/inaktivera alla fjärranslutningar som idag sker med port 8050 (se avsnittet ovan) när du använder PowerShell- eller Autentiseringshanteraren-program för kryptering av autentiseringsuppgifter.

### <a name="diagnostics-page"></a>Sidan Diagnostik
På sidan Diagnostik kan du utföra följande åtgärder:

* Aktivera utförlig **loggning,** visa loggar i loggboken och skicka loggar till Microsoft om det uppstod ett fel.
* **Testa anslutningen** till en datakälla.

### <a name="help-page"></a>Hjälpsida
På hjälpsidan visas följande information:

* Kort beskrivning av gatewayen
* Versionsnummer
* Länkar till onlinehjälp, sekretesspolicy och licensavtal.

## <a name="monitor-gateway-in-the-portal"></a>Övervaka gateway i portalen
I Azure-portalen kan du visa ögonblicksbilder i nästan realtid av resursutnyttjande (CPU, minne, nätverk(in/ut) osv.) på en gateway-dator.

1. I Azure-portalen navigerar du till startsidan för datafabriken och klickar på panelen **Länkade tjänster.**

    ![Datafabrikens startsida](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Välj **gatewayen** på sidan **Länkade tjänster.**

    ![Sidan Länkade tjänster](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. På sidan **Gateway** kan du se gatewayens minne och CPU-användning.

    ![CPU- och minnesanvändning av gateway](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Aktivera **avancerade inställningar** för att se mer information, till exempel nätverksanvändning.
    
    ![Avancerad övervakning av gateway](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

I följande tabell finns beskrivningar av kolumner i listan **Gateway-noder:**

Egenskap för övervakning | Beskrivning
:------------------ | :----------
Namn | Namn på den logiska gatewayen och noder som är associerade med gatewayen. Nod är en lokal Windows-dator som har gatewayen installerad på den. Information om hur du har mer än en nod (upp till fyra noder) i en enda logisk gateway finns i [Data Management Gateway – hög tillgänglighet och skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md).
Status | Status för den logiska gatewayen och gatewaynoderna. Exempel: Online/Offline/Limited/etc. Information om dessa statusar finns i [statusavsnittet För gateway.](#gateway-status)
Version | Visar versionen av den logiska gatewayen och varje gatewaynod. Versionen av den logiska gatewayen bestäms baserat på version av majoriteten av noderna i gruppen. Om det finns noder med olika versioner i den logiska gateway-inställningen är det bara noderna med samma versionsnummer som den logiska gatewayfunktionen korrekt. Andra är i begränsat läge och måste uppdateras manuellt (endast om automatisk uppdatering misslyckas).
Tillgängligt minne | Tillgängligt minne på en gateway-nod. Det här värdet är en ögonblicksbild i nära realtid.
CPU-användning | CPU-användning av en gateway-nod. Det här värdet är en ögonblicksbild i nära realtid.
Nätverk (in/ut) | Nätverksanvändning av en gatewaynod. Det här värdet är en ögonblicksbild i nära realtid.
Samtidiga jobb (löpning/gräns) | Antal jobb eller aktiviteter som körs på varje nod. Det här värdet är en ögonblicksbild i nära realtid. Limit betyder maximalt antal samtidiga jobb för varje nod. Det här värdet definieras baserat på maskinens storlek. Du kan öka gränsen för att skala upp samtidig jobbkörning i avancerade scenarier, där CPU/minne/nätverk är underutnyttjat, men aktiviteter är tidsutnyttjande. Den här funktionen är också tillgänglig med en gateway med en nod (även när skalbarhets- och tillgänglighetsfunktionen inte är aktiverad).
Roll | Det finns två typer av roller i en multinodgateway - Dispatcher och worker. Alla noder är arbetare, vilket innebär att de alla kan användas för att utföra jobb. Det finns bara en dispatcher-nod, som används för att hämta uppgifter/jobb från molntjänster och skicka dem till olika arbetsnoder (inklusive sig själv).

På den här sidan visas vissa inställningar som är mer meningsfulla när det finns två eller flera noder (utskalningsscenario) i gatewayen. Se [Data Management Gateway – hög tillgänglighet och skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md) för mer information om hur du konfigurerar en gateway med flera nod.

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

## <a name="scale-up-gateway"></a>Skala upp gateway
Du kan konfigurera antalet **samtidiga dataförflyttningsjobb** som kan köras på en nod för att skala upp möjligheten att flytta data mellan lokala datalager och molndatalager.

När det tillgängliga minnet och processorn inte används väl, men inaktiv kapacitet är 0, bör du skala upp genom att öka antalet samtidiga jobb som kan köras på en nod. Du kanske också vill skala upp när aktiviteter är time out eftersom gatewayen är överbelastad. I de avancerade inställningarna för en gateway-nod kan du öka den maximala kapaciteten för en nod.

## <a name="troubleshooting-gateway-issues"></a>Felsöka gatewayproblem
Se [Artikeln felsökning av gatewayproblem](data-factory-troubleshoot-gateway-issues.md) för information/tips om felsökning av problem med att använda datahanteringsgatewayen.

## <a name="move-gateway-from-one-machine-to-another"></a>Flytta gateway från en dator till en annan
I det här avsnittet beskrivs steg för att flytta gatewayklienten från en dator till en annan dator.

1. Navigera till **startsidan för Data Factory**i portalen och klicka på panelen Länkade **tjänster.**

    ![Länk för datagateways](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Välj din gateway i avsnittet **DATA GATEWAYS** på sidan **Länkade tjänster.**

    ![Sidan Länkade tjänster med gateway markerad](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Klicka på **Hämta och installera datagateway**på sidan **Datagateway.**

    ![Länken Hämta gateway](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. Klicka på Hämta **och installera datagateway**på sidan **Konfigurera** och följ instruktionerna för att installera datagatewayen på datorn.

    ![Konfigurera sida](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Håll **Konfigurationshanteraren** för Microsoft Data Management Gateway öppen.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. Klicka på **Återskapa tangenten** i kommandofältet på sidan **Konfigurera** i portalen och klicka på **Ja** för varningsmeddelandet. Klicka på **knappen Kopiera** bredvid tangenttexten som kopierar nyckeln till Urklipp. Gatewayen på den gamla datorn slutar fungera så fort du återskapar nyckeln.

    ![Återskapa nyckel](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Klistra in **nyckeln** i textrutan på sidan **Registrera gateway** på konfigurationshanteraren för **datahanteringsgateway** på datorn. (valfritt) Klicka på **Visa gateway-tangenten** om du vill visa nyckeltexten.

    ![Kopiera nyckel och registrera](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Klicka på **Registrera** för att registrera gatewayen med molntjänsten.
9. Klicka på **Ändra** på fliken **Inställningar** om du vill välja samma certifikat som användes med den gamla gatewayen, ange **lösenordet**och klicka på **Slutför**.

   ![Ange certifikat](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Du kan exportera ett certifikat från den gamla gatewayen genom att göra följande steg: starta Configuration Manager för Data Management Gateway på den gamla datorn, växla till fliken **Certifikat,** klicka på **Exportera** och följ instruktionerna.
10. När gatewayen har registrerats bör du se **registreringsuppsättningen** till **Registrerad** och **Status** inställd på **Startad** på startsidan för Configuration Manager för gateway.

## <a name="encrypting-credentials"></a>Kryptera autentiseringsuppgifter
Så här krypterar du autentiseringsuppgifter i Data Factory Editor:

1. Starta webbläsaren på **gateway-datorn**, navigera till [Azure Portal](https://portal.azure.com). Sök efter din datafabrik om det behövs, öppna datafabriken på **sidan DATA FACTORY** och klicka sedan på Författare & **Distribuera** för att starta Data Factory Editor.
2. Klicka på en befintlig **länkad tjänst** i trädvyn om du vill se dess JSON-definition eller skapa en länkad tjänst som kräver en datahanteringsgateway (till exempel SQL Server eller Oracle).
3. Ange namnet på gatewayen för **gatewayName** i JSON-redigeraren för gatewayName-egenskapen.
4. Ange servernamn för egenskapen **Data Source** i **connectionString**.
5. Ange databasnamn för egenskapen **Initial Catalog** i **connectionString**.
6. Klicka på **Knappen Kryptera** i kommandofältet som startar programmet Click-Once **Autentiseringshanteraren.** Du bör se dialogrutan **Inställningsreferenser.**

    ![Dialogrutan Konfigurera autentiseringsuppgifter](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. Gör följande i dialogrutan **Inställningsautentiseringsuppgifter:**
   1. Välj **autentisering** som du vill att tjänsten Data Factory ska använda för att ansluta till databasen.
   2. Ange namnet på den användare som har åtkomst till databasen för **användarnamnsinställningen.**
   3. Ange lösenord för användaren för **lösenordsinställningen.**
   4. Klicka på **OK** om du vill kryptera autentiseringsuppgifter och stänga dialogrutan.
8. Du bör se en **krypteradCredential** egenskap i **connectionString** nu.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
   Om du öppnar portalen från en annan dator än gateway-datorn måste du se till att Autentiseringshanteraren-programmet kan ansluta till gateway-datorn. Om programmet inte kan nå gateway-datorn kan du inte ange autentiseringsuppgifter för datakällan och testa anslutningen till datakällan.

När du använder programmet **Inställning autentiseringsuppgifter** krypterar portalen autentiseringsuppgifterna med certifikatet som anges på fliken **Certifikat** i **Configuration Manager** för gateway på gateway-datorn.

Om du letar efter en API-baserad metod för att kryptera autentiseringsuppgifterna kan du använda [cmdleten New-AzDataFactoryEncryptValue PowerShell](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) för att kryptera autentiseringsuppgifter. Cmdlet använder certifikatet som gatewayen är konfigurerad att använda för att kryptera autentiseringsuppgifterna. Du lägger till krypterade autentiseringsuppgifter i elementet **EncryptedCredential** i **connectionString** i JSON. Du använder JSON med cmdleten [New-AzDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactorylinkedservice) eller i Data Factory Editor.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Det finns ytterligare en metod för att ange autentiseringsuppgifter med Hjälp av Data Factory Editor. Om du skapar en SQL Server-länkad tjänst med hjälp av redigeraren och anger autentiseringsuppgifter i oformaterad text krypteras autentiseringsuppgifterna med ett certifikat som tjänsten Data Factory äger. Det används INTE certifikatet som gatewayen är konfigurerad att använda. Även om detta tillvägagångssätt kan vara lite snabbare i vissa fall, är det mindre säkert. Därför rekommenderar vi att du följer den här metoden endast för utvecklings-/testningsändamål.

## <a name="powershell-cmdlets"></a>PowerShell-cmdletar
I det här avsnittet beskrivs hur du skapar och registrerar en gateway med Azure PowerShell-cmdletar.

1. Starta **Azure PowerShell** i administratörsläge.
2. Logga in på ditt Azure-konto genom att köra följande kommando och ange dina Azure-autentiseringsuppgifter.

    ```powershell
    Connect-AzAccount
    ```
3. Använd cmdleten **New-AzDataFactoryGateway** för att skapa en logisk gateway enligt följande:

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Exempel på kommando och utdata:**

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description "gateway for walkthrough"

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. I Azure PowerShell växlar du till mappen: *C:\\\\\\Programfiler Microsoft Integration Runtime\\3.0\\PowerShellScript\\*. Kör *RegisterGateway.ps1* som är associerad med den lokala variabeln **$Key** som visas i följande kommando. Det här skriptet registrerar klientagenten som är installerad på datorn med den logiska gateway som du skapade tidigare.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Du kan registrera gatewayen på en fjärrdator med parametern IsRegisterOnRemoteMachine. Exempel:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Du kan använda cmdleten **Get-AzDataFactoryGateway** för att få en lista över gateways i din datafabrik. När **statusen** visas **online**betyder det att din gateway är klar att användas.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   Du kan ta bort en gateway med cmdleten **Remove-AzDataFactoryGateway** och uppdatera för en gateway med cmdlets **Set-AzDataFactoryGateway.** Syntax och annan information om dessa cmdlets finns i Data Factory Cmdlet Reference.  

### <a name="list-gateways-using-powershell"></a>Lista gateways med PowerShell

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Ta bort gateway med PowerShell

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Nästa steg
* Se [Flytta data mellan lokal datakiv och molndatalager.](data-factory-move-data-between-onprem-and-cloud.md) I genomgången skapar du en pipeline som använder gatewayen för att flytta data från en lokal SQL Server-databas till en Azure-blob.
