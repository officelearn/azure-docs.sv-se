---
title: Data Management Gateway för Data Factory | Microsoft Docs
description: Konfigurera en gateway för att flytta data mellan lokalt och molnet. Använd Data Management Gateway i Azure Data Factory för att flytta dina data.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 6bb37008b6a8b37e575dcf83dffcf57ab08996fd
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540002"
---
# <a name="data-management-gateway"></a>Gateway för datahantering
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [lokal IR i](../create-self-hosted-integration-runtime.md).

> [!NOTE]
> Har nu tagits byta namn till Gateway för datahantering som lokal Integration Runtime.

Data management gateway är en klientagent som du måste installera i din lokala miljö för att kopiera data mellan datalager i molnet och lokalt. Lokala data datalager som stöds av Data Factory visas i den [datakällor som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) avsnittet.

Den här artikeln kompletterar den här genomgången i den [flytta data mellan lokala och molnbaserade datalager](data-factory-move-data-between-onprem-and-cloud.md) artikeln. I den här genomgången skapar du en pipeline som använder gatewayen för att flytta data från en lokal SQL Server-databas till en Azure-blob. Den här artikeln innehåller detaljerad information om data management gateway.

Du kan skala ut en data management gateway genom att associera flera lokala datorer med gatewayen. Du kan skala upp genom att öka antalet data movement jobb som kan köras samtidigt på en nod. Den här funktionen finns även för en logisk gateway med en enda nod. Se [skalning gateway för datahantering i Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) nedan för information.

> [!NOTE]
> Gateway stöder för närvarande endast Kopieringsaktivitet och lagrad proceduraktivitet i Data Factory. Det går inte att använda gatewayen från en anpassad aktivitet för att komma åt lokala datakällor.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
### <a name="capabilities-of-data-management-gateway"></a>Funktionerna för gateway för datahantering
Gateway för datahantering innehåller följande funktioner:

* Modellen lokala datakällor och molndatakällor inom samma data factory och flytta data.
* Ha en enda glasruta för övervakning och hantering med insyn i gatewaystatus från sidan Datafabrik.
* Hantera åtkomst till lokala datakällor på ett säkert sätt.
  * Inga ändringar som krävs för att företagets brandvägg. Gateway gör bara utgående HTTP-baserade anslutningar till öppet internet.
  * Kryptera autentiseringsuppgifter för dina lokala datalager med ditt certifikat.
* Flytta data effektivt – data överförs parallellt, motståndskraftig mot tillfälliga nätverksproblem med automatisk logik för omprövning.

### <a name="command-flow-and-data-flow"></a>Kommandot flödet och dataflöde
När du använder en Kopieringsaktivitet som kopierar data mellan lokala och molnbaserade använder aktiviteten en gateway för att överföra data från en lokal datakälla till molnet och tvärtom.

Här är det övergripande dataflödet för och sammanfattning av stegen för att kopiera med datagateway: ![Med hjälp av gateway-dataflöde](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Dataexperter skapar en gateway för en Azure Data Factory med hjälp av antingen den [Azure-portalen](https://portal.azure.com) eller [PowerShell-cmdleten](https://docs.microsoft.com/powershell/module/az.datafactory/).
2. Dataexperter skapar en länkad tjänst för ett lokalt datalager genom att ange gatewayen. Som en del av konfigurationen av den länkade tjänsten använder dataexperter programmet ange autentiseringsuppgifter för att ange typer av autentisering och autentiseringsuppgifter. Dialogrutan Ange autentiseringsuppgifter program kommunicerar med datalagret att testa anslutningen och gatewayen att spara autentiseringsuppgifter.
3. Gateway krypterar autentiseringsuppgifterna med det certifikat som är kopplad till gatewayen (som tillhandahålls av dataexperter) innan du sparar autentiseringsuppgifter i molnet.
4. Data Factory-tjänsten kommunicerar med en gateway för schemaläggning och hantering av jobb via en kontrollkanal som använder en delad Azure service bus-kö. När ett kopieringsjobb för aktiviteten måste vara bearbetningsenhet, köer begäran tillsammans med autentiseringsuppgifter i Data Factory. Gateway startar jobbet efter avsökning kön.
5. Gatewayen dekrypterar autentiseringsuppgifterna med samma certifikat och sedan ansluter till det lokala datalagringen med rätt autentiseringstyp och autentiseringsuppgifter.
6. Gatewayen kopierar data från en lokal databas till en molnlagring, och vice versa beroende på konfigureringen av Kopieringsaktiviteten i datapipelinen. Det här steget kommunicerar gatewayen direkt med molnbaserad lagringstjänster som Azure Blob Storage via en säker kanal (HTTPS).

### <a name="considerations-for-using-gateway"></a>Att tänka på när gateway
* En enda instans av data management gateway kan användas för flera lokala datakällor. Dock **en enda gateway-instans som är knuten till endast en Azure data factory** och kan inte delas med en annan data factory.
* Du kan ha **endast en instans av gateway för datahantering** installeras på en enda dator. Anta att du har två datafabriker som behöver åtkomst till lokala datakällor kan behöva du installera gateways på två lokala datorer. Med andra ord är en gateway knuten till en specifik data factory
* Den **gateway behöver inte finnas på samma dator som datakällan**. Dock minskar har gateway närmare till datakällan tid som gatewayen ska ansluta till datakällan. Vi rekommenderar att du installerar gatewayen på en dator som skiljer sig från det som är värd för en lokal datakälla. När gatewayen och datakällan finns på olika datorer, konkurrerar inte gatewayen om resurserna med datakällan.
* Du kan ha **flera gateways på olika datorer som ansluter till samma lokala datakälla**. Exempelvis kan du kan ha två gateways som betjänar två datafabriker men samma lokala datakälla har registrerats med båda datafabriker.
* Om du redan har en gateway som installerats på din dator fungerar en **Power BI** scenariot installerar ett **separat gateway för Azure Data Factory** på en annan dator.
* Gatewayen måste användas, även om du använder **ExpressRoute**.
* Hantera din datakälla som en lokal datakälla (som finns bakom en brandvägg) även när du använder **ExpressRoute**. Du kan använda gatewayen för att upprätta en anslutning mellan tjänsten och datakällan.
* Du måste **använda gatewayen** även om datalagret finns i molnet på ett **Azure IaaS VM**.

## <a name="installation"></a>Installation
### <a name="prerequisites"></a>Förutsättningar
* Den stöds **operativsystemet** versioner är Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Installation av data management gateway på en domänkontrollant stöds inte för närvarande.
* .NET framework 4.5.1 eller senare krävs. Om du installerar gatewayen på en Windows 7-dator måste du installera .NET Framework 4.5 eller senare. Se [systemkrav för .NET Framework](https://msdn.microsoft.com/library/8z6watww.aspx) mer information.
* Den rekommenderade **configuration** för gateway-datorn och är minst 2 GHz, 4 kärnor, 8 GB RAM-minne och 80 GB-disk.
* Om värddatorn i viloläge, svarar gatewayen inte på databegäranden. Därför konfigurera ett lämpligt **energischema** på datorn innan du installerar gatewayen. Om datorn är konfigurerad för att viloläge, uppmanas gatewayinstallationen ett meddelande.
* Du måste vara administratör på datorn för att installera och konfigurera data management gateway har. Du kan lägga till fler användare att de **datahanteringsgateway användare** lokala Windows-gruppen. Medlemmarna i den här gruppen ska kunna använda den **Data Management Gateway Configuration Manager** verktyg för att konfigurera gatewayen.

Som kopiera aktivitet körs sker på en specifik frekvens, följer Resursanvändning (processor, minne) på datorn också samma mönster med högsta och inaktivitetstid. Resursutnyttjande beror också kraftigt på mängden data som flyttas. När flera kopia jobb pågår, visas gå upp under Högbelastningstider Resursanvändning.

### <a name="installation-options"></a>Installationsalternativ
Data management gateway kan installeras på följande sätt:

* Genom att ladda ned en MSI-installationspaketet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). MSI kan också användas för att uppgradera befintliga data management gateway till den senaste versionen med alla inställningar som bevaras.
* Genom att klicka på **ladda ned och installera datagatewayen** länken under manuell installation eller **installera direkt på den här datorn** under SNABBINSTALLATIONEN. Se [flytta data mellan lokala och molnbaserade](data-factory-move-data-between-onprem-and-cloud.md) artikeln stegvisa instruktioner om hur du använder Expressinstallationen. Den manuella åtgärden tar dig till download center. Anvisningar för att hämta och installera gatewayen från download center finns i nästa avsnitt.

### <a name="installation-best-practices"></a>Metodtips för installation:
1. Konfigurera energischema på värddatorn för gatewayen så att datorn inte försättas i viloläge. Om värddatorn i viloläge, svarar gatewayen inte på databegäranden.
2. Säkerhetskopiera certifikatet som är associerade med gatewayen.

### <a name="install-the-gateway-from-download-center"></a>Installera gatewayen från download center
1. Gå till [hämtningssidan för Microsoft Data Management Gateway](https://www.microsoft.com/download/details.aspx?id=39717).
2. Klicka på **hämta**, Välj lämplig version (**32-bitars** vs. **64-bitars**), och klicka på **nästa**.
3. Kör den **MSI** direkt eller spara den på hårddisken och kör.
4. På den **Välkommen** väljer en **språk** klickar du på **nästa**.
5. **Acceptera** licensavtalet och klicka på **nästa**.
6. Välj **mappen** installera gatewayen och klicka **nästa**.
7. På den **redo att installera** klickar du på **installera**.
8. Klicka på **Slutför** att slutföra installationen.
9. Hämta nyckeln från Azure-portalen. Se avsnittet nästa steg för steg-instruktioner.
10. På den **registrera gatewayen** sidan **Data Management Gateway Configuration Manager** som körs på din dator, gör följande:
    1. Klistra in nyckeln i texten.
    2. Du kan också klicka på **Show gateway key** att se nyckeltexten.
    3. Klicka på **registrera**.

### <a name="register-gateway-using-key"></a>Registrera gateway med hjälp av nyckel
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Om du inte redan har skapat en logisk gateway i portalen
Att skapa en gateway i portalen och hämta nyckeln från den **konfigurera** sidan, Följ steg i genomgången i den [flytta data mellan lokala och molnbaserade](data-factory-move-data-between-onprem-and-cloud.md) artikeln.

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Om du redan har skapat en logisk gateway i portalen
1. I Azure-portalen går du till den **Data Factory** och klicka på **länkade tjänster** panelen.

    ![Data Factory-sida](media/data-factory-data-management-gateway/data-factory-blade.png)
2. I den **länkade tjänster** väljer du den logiska **gateway** du skapade i portalen.

    ![logisk gateway](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. I den **Datagateway** klickar du på **ladda ned och installera datagateway**.

    ![Ladda ned länken i portalen](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. I den **konfigurera** klickar du på **återskapa nyckeln**. Klicka på Ja i varningsmeddelandet när du har läst den noggrant.

    ![Återskapa nyckel](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Klicka på kopieringsknappen bredvid nyckeln. Nyckeln har kopierats till Urklipp.

    ![Kopiera nyckel](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ikoner i systemfältet / meddelanden
Följande bild visar några av systemfältet ikoner som visas.

![ikoner i systemfältet](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Om du flyttar markören över system systemfältet ikonen/meddelandet visas information om tillståndet för gateway/update-åtgärder i ett popup-fönster.

### <a name="ports-and-firewall"></a>Portar och brandvägg
Det finns två brandväggar som du behöver tänka på: **företagsbrandväggen** som körs på den centrala routern för organisationen, och **Windows-brandväggen** konfigurerad som en daemon på den lokala datorn där gatewayen är installerad.

![brandväggar](./media/data-factory-data-management-gateway/firewalls2.png)

På nivån för företagets brandvägg måste du konfigurera följande domäner och utgående portar:

| Domännamn | Portar | Beskrivning |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Används för kommunikation med Data Movement Service-serverdelen |
| *.core.windows.net |443 |Används för mellanlagrad kopiering med hjälp av Azure Blob (om konfigurerad)|
| *.frontend.clouddatahub.net |443 |Används för kommunikation med Data Movement Service-serverdelen |
| *.servicebus.windows.net |9350-9354, 5671 |Valfri service bus-relä via TCP som används av guiden Kopiera |

På Windows-brandväggen nivå aktiveras normalt dessa utgående portar. Om inte, du kan konfigurera de domäner och portar i enlighet med detta på gateway-datorn.

> [!NOTE]
> 1. Baserat på din källa / mottagare, du kan behöva godkänna ytterligare domäner och utgående portar i företagets/Windows-brandväggen.
> 2. För vissa Molndatabaser (till exempel: [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access)och så vidare), du kan behöva listan över godkända IP-adressen för Gateway-datorn på sina brandväggskonfigurationen.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Kopiera data från källans datalager till mottagarens datalager
Kontrollera att brandväggsreglerna har aktiverats korrekt på företagets brandvägg och Windows-brandväggen på gateway-datorn och den datalagringen själva. Att aktivera dessa regler gör att gatewayen kan ansluta till både källa och mottagare har. Aktivera regler för varje datalager som är inblandade i kopieringsåtgärden.

Till exempel att kopiera från **ett lokalt datalager till en Azure SQL Database-mottagare eller en Azure SQL Data Warehouse sink**, gör följande:

* Tillåt utgående **TCP** kommunikation på port **1433** för både Windows-brandväggen och företagets brandvägg.
* Konfigurera brandväggsinställningarna för Azure SQL-server för att lägga till IP-adressen för gateway-datorn i listan över tillåtna IP-adresser.

> [!NOTE]
> Om brandväggen inte tillåter utgående port 1433 gatewayen kommer inte åt Azure SQL direkt. I det här fallet kan du använda [mellanlagrad kopiering](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) till SQL Azure-databas / SQL Azure DW. I det här scenariot kan kräver du bara HTTPS (port 443) för dataförflyttning.
>
>

### <a name="proxy-server-considerations"></a>Proxyserver
Om nätverksmiljön företagets använder en proxyserver för att få åtkomst till internet, konfigurera data management gateway för att använda rätt proxyinställningar. Du kan ange proxyservern under fasen för första registreringen.

![Ställ in proxy under registreringen](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Proxyservern använder gatewayen för att ansluta till Molntjänsten. Klicka på **ändra** länken under installationen. Du ser den **proxyinställning** dialogrutan.

![Set-proxyn med Configuration manager](media/data-factory-data-management-gateway/SetProxySettings.png)

Det finns tre alternativ:

* **Använd inte proxy**: Gateway använder inte uttryckligen alla proxy för att ansluta till molntjänster.
* **Använd systemproxy**: Proxyinställningen som konfigureras i diahost.exe.config och diawp.exe.config använder gatewayen. Om ingen proxy har konfigurerats i diahost.exe.config och diawp.exe.config ansluter gateway till Molntjänsten direkt utan att gå via proxy.
* **Använd anpassad proxy**: Konfigurera HTTP-proxyinställning för gateway, istället för att använda konfigurationer i diahost.exe.config och diawp.exe.config. Adress och Port krävs. Användarnamn och lösenord är valfria beroende på inställningen för autentisering av din proxyserver. Alla inställningar är krypterad med Autentiseringscertifikatet för gatewayen och lagras lokalt på gateway-värddatorn.

Data management gateway värdtjänsten startas om automatiskt när du har sparat de uppdaterade proxyinställningarna.

När gatewayen har registrerats, om du vill visa eller uppdatera proxyinställningarna, kan du använda Data Management Gateway Configuration Manager.

1. Starta **Data Management Gateway Configuration Manager**.
2. Växla till fliken **Settings** (Inställningar).
3. Klicka på **ändra** länken i **HTTP-Proxy** avsnitt för att starta den **ange HTTP-Proxy** dialogrutan.
4. När du klickar på den **nästa** knappen, visas en varningsdialogruta som ber om din tillåtelse för att spara Proxyinställningen och starta om tjänsten Gateway.

Du kan visa och uppdatera HTTP-proxy med verktyget Configuration Manager.

![Set-proxyn med Configuration manager](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Om du har konfigurerat en proxyserver med NTLM-autentisering, körs Gateway-värdtjänsten under domänkontot. Om du ändrar lösenordet för domänkontot senare, Kom ihåg att uppdatera konfigurationsinställningarna för tjänsten och starta om den i enlighet med detta. På grund av det här kravet föreslår vi att du använder en särskild domän-konto för att få åtkomst till proxyservern kräver inte att uppdatera lösenordet för ofta.
>
>

### <a name="configure-proxy-server-settings"></a>Konfigurera inställningar för proxyserver
Om du väljer **Använd systemproxy** ställa in för HTTP-proxy, använder gatewayen Proxyinställningen i diahost.exe.config och diawp.exe.config. Om ingen proxy har angetts i diahost.exe.config och diawp.exe.config ansluter gateway till Molntjänsten direkt utan att gå via proxy. Följande procedur innehåller instruktioner för att uppdatera filen diahost.exe.config.

1. Skapa en säker kopia av C:\Program Files\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config att säkerhetskopiera den ursprungliga filen i Utforskaren.
2. Starta Notepad.exe kör som administratör och öppna textfil ”C:\Program Files\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config. Du kan hitta Standardetiketten för system.net enligt följande kod:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Du kan sedan lägga till information om proxy-server som du ser i följande exempel:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Ytterligare egenskaper tillåts i proxy-taggen för att ange inställningarna som krävs som scriptLocation. Referera till [proxy Element (nätverksinställningar)](https://msdn.microsoft.com/library/sa91de1e.aspx) på syntax.

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Spara konfigurationsfilen till den ursprungliga platsen och sedan starta om tjänsten värd för Data Management Gateway, som hämtar ändringarna. Starta om tjänsten: använda tjänster-appleten på Kontrollpanelen, eller från den **Data Management Gateway Configuration Manager** > klickar du på den **stoppa tjänsten** knappen och klicka sedan på den **Start Tjänsten**. Om tjänsten inte startar, är det troligt att en felaktig syntax för XML-taggen har lagts till i programmets konfigurationsfil som har redigerats.

> [!IMPORTANT]
> Glöm inte att uppdatera **både** diahost.exe.config och diawp.exe.config.

Förutom de här punkterna måste du också se till att Microsoft Azure är ditt företags vitlistan. Lista över giltiga Microsoft Azure-IP-adresser kan laddas ned från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Eventuella symptom för brandväggen och proxyservern serverproblem
Om det uppstår fel som liknar det följande är det troligen på grund av felaktig konfigurering av brandvägg eller proxy-servern, vilket blockerar gatewayen från att ansluta till Data Factory för att autentisera sig själv. Se föregående avsnitt för att se till att brandväggen och proxyservern konfigureras korrekt.

1. När du försöker registrera gatewayen, visas följande fel: ”Det gick inte att registrera den gateway-nyckeln. Innan du försöker registrera den gateway-nyckeln igen, bekräfta att data management gateway är i anslutet tillstånd och Data Management Gateway-värdtjänsten har startats ”.
2. När du öppnar Configuration Manager kan se du status som ”frånkopplad” eller ”ansluter”. När du visar Windows-händelseloggar, under ”Loggboken” > ”program och tjänstloggar” > ”Data Management Gateway” felmeddelanden visas till exempel följande fel: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Öppna port 8050 för kryptering av autentiseringsuppgifter
Den **ange autentiseringsuppgifter** programmet använder den inkommande porten **8050** till relay autentiseringsuppgifter till gateway när du konfigurerar en lokal länkad tjänst i Azure-portalen. Under installationen av gateway öppnas som standard gateway-installationen det på gatewaydatorn.

Om du använder en brandvägg från tredje part, kan du öppna port 8050 manuellt. Om du stöter på problem med brandväggen under installationen av gateway, kan du använda följande kommando för att installera gatewayen utan att konfigurera brandväggen.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Om du inte väljer att öppna port 8050 på gateway-datorn, använda metoder än med hjälp av den **ange autentiseringsuppgifter** program för att konfigurera autentiseringsuppgifter för datalagring. Du kan till exempel använda [New AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell-cmdlet. Se avsnittet Ange autentiseringsuppgifter och säkerhet på hur data lagrar autentiseringsuppgifter kan ställas in.

## <a name="update"></a>Uppdatering
Som standard uppdateras data management gateway automatiskt när en nyare version av gatewayen är tillgänglig. Gatewayen uppdateras inte förrän alla schemalagda uppgifter är klar. Inga ytterligare aktiviteter bearbetas av gatewayen tills uppdateringen har slutförts. Om uppdateringen misslyckas återställs gateway till den gamla versionen.

Du kan se den schemalagda uppdateringstiden på följande platser:

* Sidan gateway egenskaper i Azure-portalen.
* Startsida för Data Management Gateway Configuration Manager
* Systemmeddelande i systemfältet.

Fliken Start av Data Management Gateway Configuration Manager visar schema för uppdatering och den senaste gången gatewayen har installerats/uppdateras.

![Schemauppdateringar](media/data-factory-data-management-gateway/UpdateSection.png)

Du kan installera uppdateringen direkt eller vänta tills gateway uppdateras automatiskt vid den schemalagda tiden. Följande bild visar exempelvis meddelandet som visas i Gateway Configuration Manager tillsammans med knappen Uppdatera som du kan klicka på för att installera den direkt.

![Uppdatering i DMG Configuration Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Meddelandet i systemfältet skulle se ut enligt följande bild:

![Systemfältet Systemmeddelande](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Du ser status för uppdateringsåtgärden (manuellt eller automatiskt) i systemfältet. När du startar Gateway Configuration Manager nästa gång du ser ett meddelande i meddelandefältet att gatewayen har uppdaterats tillsammans med en länk till [vad är nytt avsnitt](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Att aktivera/inaktivera funktionen för automatisk uppdatering
Du kan aktivera/inaktivera funktionen för automatisk uppdatering genom att göra följande:

[För enskild nod gateway]
1. Starta Windows PowerShell på gateway-datorn.
2. Växla till mappen C:\Program Files\Microsoft Integration Runtime\3.0\PowerShellScript\.
3. Kör följande kommando för att aktivera automatisk uppdatering funktion av (inaktivera).

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Om du vill aktivera den igen:

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
[För flera noder högtillgängliga och skalbara gateway](data-factory-data-management-gateway-high-availability-scalability.md)
1. Starta Windows PowerShell på gateway-datorn.
2. Växla till mappen C:\Program Files\Microsoft Integration Runtime\3.0\PowerShellScript\.
3. Kör följande kommando för att aktivera automatisk uppdatering funktion av (inaktivera).

    En extra AuthKey param krävs för gateway med funktionen för hög tillgänglighet.
    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Om du vill aktivera den igen:

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
När du har installerat gatewayen kan du starta konfigurationshanteraren för Data Management Gateway på något av följande sätt:

1. I den **Search** fönster, Skriv in **Data Management Gateway** att komma åt det här verktyget.
2. Kör den körbara filen **ConfigManager.exe** i mappen: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

### <a name="home-page"></a>Startsida
Startsidan kan du göra följande:

* Visa status för gateway (ansluten till Molntjänsten osv.).
* **Registrera** med hjälp av en nyckel från portalen.
* **Stoppa** och starta den **tjänsten värd för Data Management Gateway** på gateway-datorn.
* **Schemalägga uppdateringar av** vid en viss tidpunkt dagar.
* Visa det datum när gatewayen har **senast uppdaterad**.

### <a name="settings-page"></a>Sidan Inställningar
Sidan Inställningar kan du göra följande:

* Visa, ändra och exportera **certifikat** används av gatewayen. Det här certifikatet används för att kryptera autentiseringsuppgifterna för datakällan.
* Ändra **HTTPS-port** för slutpunkten. Gatewayen öppnar en port för att ange datakällans autentiseringsuppgifter.
* **Status för** av slutpunkt
* Visa **SSL-certifikat** används för SSL-kommunikation mellan portalen och gateway för att ange autentiseringsuppgifter för datakällor.

### <a name="remote-access-from-intranet"></a>Fjärråtkomst från intranätet
Den här funktionen aktiveras i framtiden. I kommande uppdateringar (v3.4 eller senare) meddelar vi dig aktivera / inaktivera alla fjärranslutningar som sker i dag använder port 8050 (se ovan) när du använder PowerShell eller Autentiseringshanteraren för kryptering av autentiseringsuppgifter.

### <a name="diagnostics-page"></a>Sidan diagnostik
Sidan diagnostik kan du göra följande:

* Aktivera utförlig **loggning**, visa loggarna i Loggboken och skicka loggar till Microsoft om det uppstod ett fel.
* **Testa anslutning** till en datakälla.

### <a name="help-page"></a>Hjälpsidan
Sidan Hjälp visar följande information:

* Kort beskrivning av gateway
* Versionsnummer
* Länkar till onlinehjälpen, sekretesspolicy och licensavtal.

## <a name="monitor-gateway-in-the-portal"></a>Övervaka gateway i portalen
Du kan visa nästan i realtid ögonblicksbild av Resursanvändning (CPU, minne, network(in/out) osv.) på en gateway-dator i Azure-portalen.

1. Gå till startsidan för din datafabrik i Azure-portalen och klicka på **länkade tjänster** panelen.

    ![Datafabrikens startsida](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Välj den **gateway** i den **länkade tjänster** sidan.

    ![Länkade tjänster-sidan](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. I den **Gateway** sidan ser du minne och CPU-användningen för gatewayen.

    ![Processor- och användning av gateway](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Aktivera **avancerade inställningar** vill ha mer information, till exempel nätverksanvändning.
    
    ![Avancerad övervakning av gateway](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

Följande tabell innehåller beskrivningar av kolumner i den **Gateway-noder** lista:

Övervakning av egenskap | Beskrivning
:------------------ | :----------
Namn | Namnet på logisk gateway och noder som är associerade med gatewayen. Noden är en lokal Windows-dator som gatewayen har installerats på den. Information om att ha fler än en nod (upp till fyra noder) i en enda logisk gateway finns i [Data Management Gateway - hög tillgänglighet och skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md).
Status | Status för logisk gateway och gateway-noderna. Exempel: Online/Offline/Limited/osv. Läs om hur dessa statusar [gatewaystatus](#gateway-status) avsnittet.
Version | Visar vilken version av logiska gatewayen och varje gateway-noden. Version av den logiska gatewayen bestäms baserat på version av merparten av noder i gruppen. Om det finns noder med olika versioner i logisk gateway-installationen endast noder med samma versionsnummer som funktionen logisk gateway korrekt. Andra är i begränsat läge och måste uppdateras manuellt (endast om automatisk uppdatering misslyckas).
Ledigt minne | Tillgängligt minne på en gateway-noden. Det här värdet är en nästan i realtid ögonblicksbild.
Processoranvändning | CPU-utnyttjande på en gateway-noden. Det här värdet är en nästan i realtid ögonblicksbild.
Nätverk (In/ut) | Nätverksanvändningen för en gateway-noden. Det här värdet är en nästan i realtid ögonblicksbild.
Samtidiga jobb (körs / begränsa) | Antal jobb eller aktiviteter som körs på varje nod. Det här värdet är en nästan i realtid ögonblicksbild. Gränsen innebär det att maximalt antal samtidiga jobb för varje nod. Det här värdet definieras baserat på storleken på datorn. Du kan höja gränsen att skala upp samtidiga jobbkörning i avancerade scenarier där CPU/minne/nätverk är underutnyttjade, men Tidsgränsen nåddes för aktiviteter. Den här funktionen är också tillgängliga med en enda nod gateway (även om funktionen för skalbarhet och tillgänglighet inte är aktiverad).
Roll | Det finns två typer av roller i en gateway med flera noder – Dispatcher- och arbetsroller. Alla noder är arbetare, vilket innebär att de kan användas för att köra jobb. Det finns bara en dispatcher-noden som används för att hämta uppgifter/jobb från cloud services och skicka dem till olika arbetsnoder (inklusive själva).

I den här sidan kan se du några inställningar som gör bättre när det finns två eller flera noder (skalar ut scenario) i gatewayen. Se [Data Management Gateway - hög tillgänglighet och skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md) mer information om hur du konfigurerar en gateway med flera noder.

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
Needs Registration | Någon nod är ännu registrerad på den här logiska gatewayen
Online | Gateway-noderna är online
Offline | Någon nod i onlinestatus.
Begränsad | Inte alla noder i den här gatewayen är i felfritt tillstånd. Denna status är en varning om att en eller flera noder kanske inte är tillgänglig! <br/><br/>Kan bero på credential synkroniseringsproblem på dispatcher-/ arbetsnoden.

## <a name="scale-up-gateway"></a>Skala upp gateway
Du kan konfigurera antalet **samtidiga data movement jobb** som kan köras på en nod kan du utöka möjligheterna för att flytta data mellan lokala och molnbaserade datalager.

När tillgängligt minne och CPU används inte bra, men outnyttjad kapacitet är 0, bör du skala upp genom att öka antalet samtidiga jobb som kan köras på en nod. Du kanske också vill skala upp när aktiviteter Tidsgränsen nåddes för eftersom gatewayen är överbelastad. Du kan öka den maximala kapaciteten för en nod i de avancerade inställningarna för en gateway-noden.

## <a name="troubleshooting-gateway-issues"></a>Felsökning av problem med gateway
Se [felsökning av problem med gateway](data-factory-troubleshoot-gateway-issues.md) artikeln för information/tips för felsökning av problem med data management gateway.

## <a name="move-gateway-from-one-machine-to-another"></a>Flytta gateway från en dator till en annan
Det här avsnittet innehåller steg för glidande gatewayklienten från en dator till en annan dator.

1. I portalen navigerar du till den **startsidan Datafabrik**, och klicka på den **länkade tjänster** panelen.

    ![Data-gatewayer länk](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Välj din gateway på den **DATAGATEWAYER** delen av den **länkade tjänster** sidan.

    ![Länkade tjänster-sida med valda gatewayen](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. I den **datagateway** klickar du på **ladda ned och installera datagateway**.

    ![Ladda ned gateway-länk](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. I den **konfigurera** klickar du på **ladda ned och installera datagatewayen**, och följ instruktionerna för att installera datagateway på datorn.

    ![Konfigurera sidan](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Behåll den **Microsoft Data Management Gateway Configuration Manager** öppna.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. I den **konfigurera** i portalen, klickar du på **återskapa nyckeln** till kommandofältet och klicka på **Ja** för varningsmeddelandet. Klicka på **kopieringsknappen** bredvid texten som kopierar nyckeln till Urklipp. Gatewayen på den gamla datorn slutar att fungera så snart du återskapa nyckeln.

    ![Återskapa nyckel](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Klistra in den **nyckel** i textrutan i den **registrera gatewayen** för den **Data Management Gateway Configuration Manager** på din dator. (valfritt) Klicka på **Show gateway key** kryssrutan för att se nyckeltexten.

    ![Kopiera nyckel och registrera](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Klicka på **registrera** att registrera gatewayen med Molntjänsten.
9. På den **inställningar** fliken **ändra** för att välja samma certifikat som har använts med den gamla gatewayen, ange den **lösenord**, och klicka på **Slutför**.

   ![Ange certifikat](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Du kan exportera ett certifikat från den gamla gatewayen genom att göra följande steg: starta Data Management Gateway Configuration Manager på den gamla datorn och växla till den **certifikat** fliken **exportera** knappen och följ instruktionerna.
10. Efter lyckad registrering av gateway, bör du se den **registrering** inställd **registrerad** och **Status** inställd **startad** på startsidan för Gateway Configuration Manager.

## <a name="encrypting-credentials"></a>Kryptering av autentiseringsuppgifter
För att kryptera autentiseringsuppgifter i Data Factory Editor, gör du följande:

1. Starta webbläsare på den **gatewaydatorn**, gå till [Azure-portalen](https://portal.azure.com). Sök efter din datafabrik om det behövs, öppna data factory i den **DATA FACTORY** och klicka sedan på **författare och distribuera** att starta Data Factory-redigeraren.
2. Klicka på en befintlig **länkad tjänst** i trädvyn att se jobbets JSON-definition eller skapa en länkad tjänst som kräver en data management gateway (till exempel: SQLServer eller Oracle).
3. I JSON-redigerare för den **gatewayName** egenskap, anger du namnet på gatewayen.
4. Ange servernamnet för den **datakälla** -egenskapen i den **connectionString**.
5. Ange databasnamnet för den **Initial Catalog** -egenskapen i den **connectionString**.
6. Klicka på **Encrypt** knappen i kommandofältet som startar Klicka-när **Autentiseringshanteraren** program. Du bör se den **ange autentiseringsuppgifter** dialogrutan.

    ![Dialogrutan för inställningen autentiseringsuppgifter](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. I den **ange autentiseringsuppgifter** dialogrutan gör du följande steg:
   1. Välj **autentisering** som du vill att Data Factory-tjänsten för att ansluta till databasen.
   2. Anger namnet på den användare som har åtkomst till databasen för den **användarnamn** inställningen.
   3. Ange lösenord för den **lösenord** inställningen.
   4. Klicka på **OK** att kryptera autentiseringsuppgifter och stänga dialogrutan.
8. Du bör se en **encryptedCredential** -egenskapen i den **connectionString** nu.

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
Om du har åtkomst till portalen från en dator som skiljer sig från gateway-datorn måste du se till att Hanteraren för autentiseringsuppgifter programmet kan ansluta till gateway-datorn. Om programmet inte kan nå gateway-datorn, tillåter det inte att du kan ange autentiseringsuppgifter för datakällan och att testa anslutningen till datakällan.

När du använder den **ange autentiseringsuppgifter** program, portalen krypterar autentiseringsuppgifterna med certifikatet som anges i den **certifikat** fliken den **Gateway Configuration Manager**  på gateway-datorn.

Om du letar efter en API-baserad metod för att kryptera autentiseringsuppgifterna, kan du använda den [New AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell-cmdlet för att kryptera autentiseringsuppgifterna. Cmdlet: en använder certifikatet som gatewayen är konfigurerad för att använda för att kryptera autentiseringsuppgifterna. Du lägger till krypterade referenser till den **EncryptedCredential** elementet i den **connectionString** i JSON. Du använder JSON med den [New AzDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactorylinkedservice) cmdlet eller i Data Factory-redigeraren.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Det finns en mer metod för att ange autentiseringsuppgifter med hjälp av Data Factory-redigeraren. Om du skapar en länkad SQL Server-tjänst med hjälp av redigeraren och du anger autentiseringsuppgifter i klartext, krypteras autentiseringsuppgifterna med hjälp av ett certifikat som äger Data Factory-tjänsten. Den inte använder certifikaten som gatewayen är konfigurerad för att använda. Den här metoden kan vara lite snabbare i vissa fall, är det mindre säker. Därför rekommenderar vi att du följer den här metoden endast för utveckling och testning.

## <a name="powershell-cmdlets"></a>PowerShell-cmdletar
Det här avsnittet beskriver hur du skapar och registrerar en gateway med Azure PowerShell-cmdlets.

1. Starta **Azure PowerShell** i administratörsläge.
2. Logga in på ditt Azure-konto genom att köra följande kommando och ange dina autentiseringsuppgifter för Azure.

    ```PowerShell
    Connect-AzAccount
    ```
3. Använd den **New AzDataFactoryGateway** cmdlet för att skapa en logisk gateway enligt följande:

    ```PowerShell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Exempel på kommando- och utdata**:

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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

1. Växla till mappen i Azure PowerShell: **C:\Program Files\Microsoft Data Management Gateway\2.0\PowerShellScript\**. Kör **RegisterGateway.ps1** som är associerade med den lokala variabeln **$Key** som visas i följande kommando. Det här skriptet registrerar klientagenten installerad på datorn med en logisk gateway som du skapat tidigare.

    ```PowerShell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Du kan registrera gatewayen på en fjärrdator med hjälp av parametern IsRegisterOnRemoteMachine. Exempel:

    ```PowerShell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Du kan använda den **Get-AzDataFactoryGateway** cmdlet för att hämta listan över gatewayer i din datafabrik. När den **Status** visar **online**, det innebär att din gateway är klar att användas.

    ```PowerShell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
Du kan ta bort en gateway med hjälp av den **Remove-AzDataFactoryGateway** cmdlet och uppdatera beskrivning för en gateway med hjälp av den **Set-AzDataFactoryGateway** cmdletar. Syntax och annan information om dessa cmdletar finns i Cmdlet-referens för Data Factory.  

### <a name="list-gateways-using-powershell"></a>Visa gateways med hjälp av PowerShell

```PowerShell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Ta bort gateway med PowerShell

```PowerShell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Nästa steg
* Se [flytta data mellan lokala och molnbaserade datalager](data-factory-move-data-between-onprem-and-cloud.md) artikeln. I den här genomgången skapar du en pipeline som använder gatewayen för att flytta data från en lokal SQL Server-databas till en Azure-blob.
