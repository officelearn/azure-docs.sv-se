---
title: Skapa automatisk värdbaserade integration körning i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar själv värdbaserade integration körning i Azure Data Factory, vilket gör att datafabriker att komma åt data lagras i ett privat nätverk.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: b18fa2879056600cfc9c6a85afc599e2695af711
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>Hur du skapar och konfigurerar Self-hosted integrering Runtime
Integration Runtime (IR) är beräkningsinfrastrukturen som används av Azure Data Factory för att tillhandahålla data integrationsmöjligheter mellan olika nätverksmiljöer. Mer information om IR finns [integrering Runtime översikt](concepts-integration-runtime.md).

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen om Data Factory version 1](v1/data-factory-introduction.md).

En egen värdbaserade integration runtime är kan köra kopiera aktiviteter mellan en molndata butiker och ett datalager i privata nätverk och sändning av transform aktiviteter mot beräkningsresurser i en lokal eller Azure Virtual Network. Installera själva värdbaserade integreringsbehov för körning på en lokal dator eller en virtuell dator i ett privat nätverk.  

Det här dokumentet beskriver hur du kan skapa och konfigurera Self-hosted IR.

## <a name="high-level-steps-to-install-self-hosted-ir"></a>Anvisningar för att installera själva värdbaserade IR
1.  Skapa en egen värdbaserade integration runtime. Här är ett PowerShell-exempel:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2.  Hämta och installera egenvärdbaserat integrering runtime (på den lokala datorn).
3.  Hämta autentiseringsnyckel och registrera själva värdbaserade integration runtime med nyckel. Här är ett PowerShell-exempel:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="command-flow-and-data-flow"></a>Kommandot trafikflöde och dataflöde
När du flyttar data mellan lokalt och i molnet använder aktiviteten en egen värdbaserade integration runtime för att överföra data från lokala datakälla till molnet och vice versa.

Här är ett övergripande dataflöde för sammanfattning av steg för att kopiera med automatisk värdbaserade IR:

![Översikt på hög nivå](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. Data utvecklare skapar en automatisk värdbaserade integration runtime inom ett Azure data factory med hjälp av en PowerShell-cmdlet. Azure portal stöder för närvarande inte den här funktionen.
2. Data utvecklare skapar en länkad tjänst för ett lokalt datalager genom att ange själva värdbaserade integration runtime-instans som den ska använda för att ansluta till datalager. Som en del av hur du konfigurerar den länkade tjänsten, data utvecklare använder ' Autentiseringshanteraren-programmet (för närvarande stöds inte) för att ange typer av autentisering och autentiseringsuppgifter. Autentiseringsdialogrutan manager program kommunicerar med datalagret att testa anslutningen och automatisk värdbaserade integration runtime spara autentiseringsuppgifter.
4.  Automatisk värdbaserade integration runtime nod krypterar autentiseringsuppgifter med Windows Data Protection Application Programming Interface (DPAPI) som sparas lokalt. Om flera noder är för hög tillgänglighet, synkroniseras ytterligare autentiseringsuppgifterna över andra noder. Varje nod krypteras den med hjälp av DPAPI och lagras lokalt. Synkronisering av autentiseringsuppgifter är transparent för data-utvecklare och hanteras av själva värdbaserade IR.    
5.  Data Factory-tjänsten som kommunicerar med automatisk värdbaserade integration runtime för schemaläggning och hantering av jobb via **kontrollkanal** som använder en delad Azure service bus-kö. När ett jobb för aktiviteten ska köras köar Data Factory begäran tillsammans med några autentiseringsuppgifter (om autentiseringsuppgifterna inte är redan finns på själva värdbaserade integration runtime). Automatisk värdbaserade integration körning av systemtillstånd aktiveras jobbet efter avsökning kön.
6.  Automatisk värdbaserade integration runtime kopierar data från ett lokalt Arkiv till lagringsutrymmet i molnet, och vice versa beroende på hur kopieringsaktiviteten har konfigurerats i data-pipeline. För det här steget kommunicerar själva värdbaserade integration runtime direkt med molnbaserade storage-tjänster som Azure Blob Storage via en säker kanal (HTTPS).

## <a name="considerations-for-using-self-hosted-ir"></a>Överväganden för att använda automatisk värdbaserade IR

- En enda själva värdbaserade integration runtime kan användas för flera lokala datakällor. Men en **enda själva värdbaserade integration runtime är knutna till endast en Azure data factory** och kan inte delas med en annan data factory.
- Du kan ha **endast en instans av själva värdbaserade integration runtime** installeras på en enda dator. Anta att du har två datafabriker som behöver åtkomst till lokala datakällor som du behöver installera själva värdbaserade integration runtime på två lokala datorer. Med andra ord är en egen värdbaserade integration runtime knuten till en specifik data factory
- Den **själva värdbaserade integration runtime behöver inte finnas på samma dator som datakällan**. Dock minskar med egenvärdbaserat integrering runtime närmare till datakällan tiden för körningen själva värdbaserade integration att ansluta till datakällan. Vi rekommenderar att du installerar själv värdbaserade integration körning på en dator som skiljer sig från det värdar lokala datakällan. När automatisk värdbaserade integration runtime och datakälla finns på olika datorer, konkurrerar automatisk värdbaserade integration körningsmiljön inte för resurser med datakällan.
- Du kan ha **flera själva värdbaserade integration körningar på olika datorer som ansluter till samma lokala datakälla**. Till exempel du kanske har två själva värdbaserade integration runtime betjänar två datafabriker men samma lokala datakälla har registrerats med båda datafabriker.
- Om du redan har en gateway som har installerats på din dator fungerar en **Power BI** scenario, installera en **separat själva värdbaserade integration runtime för Azure Data Factory** på en annan dator.
- Automatisk värdbaserade integration runtime måste användas för att stödja dataintegrering i Azure Virtual Network.
- Hantera din datakälla som en lokal datakälla (som finns bakom en brandvägg) även om du använder **ExpressRoute**. Använda automatisk värdbaserade integration körningsmiljön för att upprätta en anslutning mellan service och datakällan.
- Du måste använda automatisk värdbaserade integration runtime även om datalagret finns i molnet på ett **Azure IaaS-virtuella**.
- Uppgifter kan misslyckas på en Self-hosted Integration som är installerad på en Windows-Server på vilken FIPS-kompatibla kryptering är aktiverat. Undvik problemet genom att inaktivera FIPS-kompatibla kryptering på servern. Ändra följande registervärde för att inaktivera FIPS-kompatibla kryptering, från 1 (aktiverad) till 0 (inaktiverat): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Förutsättningar

- Den stöds **operativsystemet** versioner är Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. Installation av själva värdbaserade integration körningsmiljön på en **domänkontrollant stöds inte**.
- **.NET framework 4.6.1 eller senare** krävs. Om du installerar själv värdbaserade integration körning på en Windows 7-dator, installera .NET Framework 4.6.1 eller senare. Se [systemkrav för .NET Framework](/dotnet/framework/get-started/system-requirements) mer information.
- Den rekommenderade **configuration** för automatisk värdbaserade integration runtime datorn är minst 2 GHz, 4 kärnor, 8 GB RAM-minne och 80 GB-disk.
- Om värddatorn i viloläge, svarar inte själva värdbaserade integration körningsmiljön på begäranden. Därför konfigurera en lämplig energischema på datorn innan du installerar själv värdbaserade integration körningsmiljön. Om datorn är konfigurerad för viloläge, frågar automatisk värdbaserade integration runtime-installation ett meddelande.
- Du måste vara administratör på datorn för att installera och konfigurera automatisk värdbaserade integration körningsmiljön har.
- Som uppstår kopiera aktiviteten körs på en specifik frekvens följer Resursanvändning (CPU, minne) på datorn också samma mönster med belastning och ledig tid. Resursutnyttjande beror också kraftigt på mängden data som flyttas. När flera kopiera jobb pågår, se Resursanvändning gå upp under tider med låg belastning.

## <a name="installation-best-practices"></a>Metodtips för installation
Automatisk värdbaserade integration körning kan installeras genom att hämta en MSI-installationspaketet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Se [flytta data mellan lokalt och i molnet artikel](tutorial-hybrid-copy-powershell.md) stegvisa instruktioner.

- Konfigurera energischema på värddatorn för automatisk värdbaserade integration runtime så att datorn inte försättas i viloläge. Om värddatorn i viloläge, aktiveras automatisk värdbaserade integration runtime offline.
- Säkerhetskopiera de behörigheter som associeras med själva värdbaserade integration runtime regelbundet.

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>Installera och registrera Self-hosted IR från download center

1. Gå till [hämtningssidan för Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Klicka på **hämta**, Välj lämplig version (**32-bitars** vs. **64-bitars**), och klicka på **nästa**.
3. Kör den **MSI** direkt eller spara den på hårddisken och kör.
4. På den **Välkommen** väljer en **språk** klickar du på **nästa**.
5. **Acceptera** licensavtalet och klicka på **nästa**.
6. Välj **mappen** installera själva värdbaserade integration runtime och klicka på **nästa**.
7. På den **redo att installera** klickar du på **installera**.
8. Klicka på **Slutför** att slutföra installationen.
9. Hämta nyckel för autentisering med hjälp av Azure PowerShell. PowerShell-exempel för att hämta autentiseringsnyckel:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. På den **registrera Integration Runtime (egenvärdbaserat)** sidan för Microsoft Integration Runtime Configuration Manager körs på datorn, utför följande steg:
    1. Klistra in den **autentiseringsnyckel** texten.
    2. Du kan också klicka på **visa autentiseringsnyckel** att se nyckeltexten.
    3. Klicka på **registrera**.


## <a name="high-availability-and-scalability"></a>Hög tillgänglighet och skalbarhet
En Self-hosted integrering Runtime kan vara kopplad till flera lokala datorer. Dessa datorer kallas noder. Du kan ha upp till fyra noder som är associerade med en Self-hosted integrering Runtime. Fördelarna med flera noder (lokala datorer med installerade-gateway) för en logisk gateway är:
1. Högre tillgänglighet för Self-hosted integrering körningen så att den inte längre felpunkt i din Stordata lösning eller molnet dataintegrering med Azure Data Factory säkerställt kontinuitet med upp till 4 noder.
2. Förbättrad prestanda och dataflöde vid flytt av data mellan lokala och moln datalager. Få mer information om [prestandajämförelser](copy-activity-performance.md).

Du kan associera flera noder genom att bara installera Self-hosted integrering Runtime-programvara från den [hämtningssidan](https://www.microsoft.com/download/details.aspx?id=39717) och genom att registrera den genom att antingen autentiseringsnycklar som hämtats från Nya AzureRmDataFactoryV2IntegrationRuntimeKey cmdlet som beskrivs i den [självstudiekursen](tutorial-hybrid-copy-powershell.md)

> [!NOTE]
> Du behöver inte skapa en ny Self-hosted integrering Runtime för att associera varje nod. Du kan installera själva värdbaserade integration körning på en annan dator och registrera den med samma nyckel för autentisering. 

> [!NOTE]
> Innan du lägger till en annan nod för **hög tillgänglighet och skalbarhet**, kontrollera **'Fjärråtkomst till intranät'** alternativet är **aktiverat** på noden 1 (Microsoft Integration Runtime Configuration Manager -> Inställningar -> fjärransluten åtkomst till intranät). 

### <a name="tlsssl-certificate-requirements"></a>Krav för TLS/SSL-certifikat
Här följer kraven för TLS/SSL-certifikatet som används för att skydda kommunikationen mellan integration runtime noder:

- Certifikatet måste vara offentligt betrodda X509 v3-certifikat. Vi rekommenderar att du använder certifikat som utfärdas av en offentlig (tredje parts) certifikatutfärdare (CA).
- Det här certifikatet måste ha förtroende för varje integration runtime-nod.
- Certifikat med jokertecken stöds. Om din FQDN-namn är **node1.domain.contoso.com**, du kan använda ***. domain.contoso.com** som ämnesnamnet för certifikatet.
- SAN-certifikat rekommenderas inte eftersom det sista objektet i Alternativt ämnesnamn används och alla andra kommer att ignoreras på grund av aktuell begränsning. T.ex. du har ett SAN-certifikat vars SAN är **node1.domain.contoso.com** och **node2.domain.contoso.com**, du kan bara använda det här certifikatet på datorn vars FQDN är **node2.domain.contoso.com**.
- Stöder alla nyckelstorlek som stöds av Windows Server 2012 R2 för SSL-certifikat.
- Certifikat med CNG nycklar stöds inte. Doesrted DoesDoes har inte stöd för certifikat med CNG-nycklar.

## <a name="system-tray-icons-notifications"></a>Ikoner i Systemstatusfältet / meddelanden
Om du flyttar markören över system fack ikonen/meddelandet hittar du information om tillståndet för automatisk värdbaserade integration runtime.

![Fack systemmeddelanden](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portar och brandvägg
Det finns två brandväggar som du behöver tänka på: **företagsbrandvägg** körs på den centrala routern för organisationen, och **Windows-brandväggen** konfigurerad som en daemon på den lokala datorn där den automatisk värdbaserade integration runtime är installerat.

![Brandvägg](media\create-self-hosted-integration-runtime\firewall.png)

Vid **företagsbrandvägg** nivån, du behöver konfigurera följande domäner och utgående portar:

Domännamn | Portar | Beskrivning
------------ | ----- | ------------
*.servicebus.windows.net | 443, 80 | Används för kommunikation med Data Movement Service-serverdelen
*.core.windows.net | 443 | Används för mellanlagrad kopia med Azure Blob (om konfigurerad)
*.frontend.clouddatahub.net | 443 | Används för kommunikation med Data Movement Service-serverdelen

Vid **Windows-brandväggen** nivån (datornivån), utgående portarna är normalt aktiverat. Om inte, kan du konfigurera domäner och därefter på egenvärdbaserat portar integration runtime-datorn.

> [!NOTE]
> Baserat på käll- / sänkor, du kan behöva godkända ytterligare domäner och utgående portar i företagets/Windows-brandväggen.
>
> För vissa databaser i molnet (till exempel: Azure SQL Database, Azure Data Lake osv), du kan behöva godkända IP-adress för själva värdbaserade integration runtime datorn på deras brandväggskonfigurationen.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopiera data från en datakälla till en mottagare
Kontrollera att brandväggsregler är aktiverade på korrekt på företagets brandvägg och Windows-brandväggen på själva värdbaserade integration runtime-datorn och datalager sig själv. Om du aktiverar de här reglerna kan själva värdbaserade integration körningsmiljön att ansluta till både källa och mottagare har. Aktivera regler för varje datalager som är inblandade i kopieringen.

Om du vill kopiera från en **lokalt datalager till en Azure SQL Database-sink eller en Azure SQL Data Warehouse sink**, gör du följande:

- Tillåt utgående **TCP** -kommunikation på port **1433** för både Windows-brandväggen och företagets brandvägg.
- Konfigurera brandväggsinställningar för Azure SQL-server för att lägga till IP-adressen för automatisk värdbaserade integration runtime datorn i listan över tillåtna IP-adresser.

> [!NOTE]
> Om brandväggen inte tillåter att utgående port 1433, automatisk värdbaserade integration körning kan inte komma åt Azure SQL direkt. I det här fallet kan du använda [mellanlagrad kopiera](copy-activity-performance.md) till SQL Azure Database / SQL Azure DW. I det här scenariot skulle du endast kräva HTTPS (port 443) för flytt av data.


## <a name="proxy-server-considerations"></a>Proxy server-överväganden
Om din företagets nätverksmiljö använder en proxyserver för åtkomst till internet, konfigurera automatisk värdbaserade integration runtime för att använda rätt proxyinställningar. Du kan ange proxyn under fasen registreringen.

![Ange proxy](media\create-self-hosted-integration-runtime\specify-proxy.png)

Automatisk värdbaserade integration runtime använder proxyservern för att ansluta till Molntjänsten. Klicka på Ändra-länk under installationen. Du ser dialogrutan proxy inställningen.

![Ange proxy](media\create-self-hosted-integration-runtime\set-http-proxy.png)

Det finns tre konfigurationsalternativ:

- **Använd inte proxyservern**: automatisk värdbaserade integration runtime inte använder alla proxy uttryckligen för att ansluta till molntjänster.
- **Använd system proxy**: egenvärdbaserat integrering runtime använder Proxyinställningen som har konfigurerats för diahost.exe.config och diawp.exe.config. Om ingen proxyserver har konfigurerats i diahost.exe.config och diawp.exe.config ansluter själva värdbaserade integration runtime till Molntjänsten direkt utan att gå via proxy.
- **Använda anpassade proxy**: konfigurera proxyinställningar för HTTP ska användas för automatisk värdbaserade integration körning, istället för att använda konfigurationer i diahost.exe.config och diawp.exe.config. Adress och Port krävs. Användarnamn och lösenord är valfria beroende på inställningen för autentisering av din proxyserver. Alla inställningar är krypterat med Windows DPAPI på själva värdbaserade integration runtime och lagras lokalt på datorn.

Integration runtime Host-tjänsten startas om automatiskt när du sparar de uppdaterade proxyinställningarna.

När automatisk värdbaserade integration runtime har registrerats, om du vill visa eller uppdatera proxyinställningarna, använda Integration Runtime Configuration Manager.

1.  Starta **Konfigurationshanteraren för Microsoft Integration Runtime**.
2.  Växla till fliken **Settings** (Inställningar).
3.  Klicka på **ändra** länken i **HTTP-Proxy** avsnittet för att starta den **ange HTTP-Proxy** dialogrutan.
4.  När du klickar på den **nästa** knappen, visas ett varningsmeddelande som ber om din tillåtelse för att spara Proxyinställningen och starta om tjänsten Integration Runtime.

Du kan visa och uppdatera HTTP-proxy med verktyget Configuration Manager.

![Visa proxy](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> Om du konfigurerar en proxyserver med NTLM-autentisering körs Integration runtime värdtjänsten under domänkontot. Om du ändrar lösenordet för domänkontot senare, Kom ihåg att uppdatera konfigurationsinställningarna för tjänsten och därefter starta om den. På grund av det här kravet rekommenderar vi att du använder ett dedikerat domänkonto att få åtkomst till proxyservern kräver inte att uppdatera lösenordet ofta.

### <a name="configure-proxy-server-settings"></a>Konfigurera inställningar för proxyserver

Om du väljer **använder system-proxy** inställningen för HTTP-proxy automatisk värdbaserade integration runtime använder Proxyinställningen i diahost.exe.config och diawp.exe.config. Om ingen proxyserver har angetts i diahost.exe.config och diawp.exe.config ansluter själva värdbaserade integration runtime till Molntjänsten direkt utan att gå via proxy. Följande procedur innehåller instruktioner för att uppdatera filen diahost.exe.config.

1. Skapa en säker kopia av C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config att säkerhetskopiera den ursprungliga filen i Utforskaren.
2. Starta Notepad.exe kör som administratör och öppna filen ”C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. Du hittar Standardetiketten för system.net som visas i följande kod:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Du kan sedan lägga till proxy serverinformation som visas i följande exempel:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Ytterligare egenskaper som är tillåtna i proxy-taggen och ange nödvändiga inställningar som scriptLocation. Se [proxy Element (nätverksinställningar)](https://msdn.microsoft.com/library/sa91de1e.aspx) för syntax.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Spara konfigurationsfilen till den ursprungliga platsen och sedan starta om tjänsten Self-hosted integrering Körningsmiljövärden hämtar ändringarna. Starta om tjänsten: använda tjänster appleten från Kontrollpanelen eller från den **integrering Runtime Configuration Manager** > klickar du på den **stoppa tjänsten** knappen och klicka sedan på den **Start Tjänsten**. Om tjänsten inte startar, är det troligt att en felaktig syntax för XML-taggen har lagts till i programmets konfigurationsfil som har redigerats.

> [!IMPORTANT]
> Glöm inte att uppdatera både diahost.exe.config och diawp.exe.config.

Förutom dessa punkter måste du också kontrollera att Microsoft Azure finns i ditt företags godkända. Listan över giltiga Microsoft Azure-IP-adresser kan hämtas från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Möjliga problem för brandväggar och proxyservrar serverproblem
Om du stöter på fel liknande följande dem, är det troligen på grund av felaktig konfiguration av servern brandvägg eller proxyserver, vilket blockerar automatisk värdbaserade integration runtime från att ansluta till Data Factory för att autentisera sig själv. Se föregående avsnitt för att se till att brandväggen och proxyservern konfigureras korrekt.

1.  När du försöker registrera själva värdbaserade integration runtime du följande felmeddelande: ”Det gick inte att registrera den här noden Integration Runtime! Bekräfta att autentiseringsnyckeln är giltig och att tjänsten värd för Integration Service körs på den här datorn. "
2.  När du öppnar Integration Runtime Configuration Manager kan du se status som ”**frånkopplad**” eller ”**ansluta**”. När du visar Windows-händelseloggar, under ”Loggboken” > ”program och tjänstloggar” > ”Microsoft Integration Runtime” felmeddelanden visas till exempel följande fel:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-intranet"></a>Aktivera fjärråtkomst från intranätet  
I fallet om du använder **PowerShell** eller **Autentiseringshanteraren programmet** att kryptera autentiseringsuppgifterna från en annan dator (i nätverket) än där själva värdbaserade integration runtime installeras sedan du kräver det **fjärråtkomst från intranätet** alternativ ska aktiveras. Om du kör den **PowerShell** eller **Autentiseringshanteraren programmet** att kryptera autentiseringsuppgifter på samma dator där själva värdbaserade integration runtime installeras sedan **-fjärråtkomst från intranätet '** får inte aktiveras.

Fjärråtkomst från intranätet ska vara **aktiverat** innan du lägger till en annan nod för **hög tillgänglighet och skalbarhet**.  

Under själva värdbaserade integration runtime installationen (och senare för v 3.3.xxxx.x), som standard inaktiverar automatisk värdbaserade integration runtime-installation av **fjärråtkomst från intranätet** på själva värdbaserade integration runtime-datorn.

Om du använder en brandvägg från tredje part, kan du manuellt öppna port 8060 (eller den konfigurerade användaren-porten). Om du stöter på problem med brandväggen under installationen av själva värdbaserade integration runtime, kan du med följande kommando för att installera den själva värdbaserade integration runtime utan att konfigurera brandväggen.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> **Autentiseringshanteraren programmet** är inte tillgänglig för kryptering av autentiseringsuppgifter i ADFv2 ännu. Vi lägger till det här stödet senare.  

Om du inte väljer att öppna port 8060 på själva värdbaserade integration runtime-datorn, använda metoder än med hjälp av den ** ställa in autentiseringsuppgifter ** tillämpningsprogrammet kan konfigurera autentiseringsuppgifterna för datalager. Exempelvis kan du använda New-AzureRmDataFactoryV2LinkedServiceEncryptCredential PowerShell-cmdlet. Se avsnittet Ställa in autentiseringsuppgifter och säkerhet på hur data lagra autentiseringsuppgifter som kan anges.


## <a name="next-steps"></a>Nästa steg
Se följande självstudierna för stegvisa instruktioner: [Självstudier: kopiera lokala data till molnet](tutorial-hybrid-copy-powershell.md).
