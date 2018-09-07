---
title: Skapa lokal integration runtime i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar en lokal integration runtime i Azure Data Factory, vilket gör att datafabriker för att få åtkomst till datalager på ett privat nätverk.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: bb5e58c0cb06baabf76d7d0443e94b368549176f
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44051348"
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>Skapa och konfigurera lokal Integration Runtime
Integration Runtime (IR) är beräkningsinfrastrukturen som används av Azure Data Factory för att tillhandahålla funktioner för dataintegrering i olika nätverksmiljöer. Mer information om IR finns [översikten över Integration Runtime](concepts-integration-runtime.md).

En lokal integration runtime är kan köra Kopieringsaktivitet mellan butiker och ett datalager i privata nätverk och skicka transformeringsaktiviteter mot beräkningsresurser i en lokal eller Azure Virtual Network. Installera lokal integration runtime behov på en lokal dator eller en virtuell dator i ett privat nätverk.  

Det här dokumentet beskriver hur du kan skapa och konfigurera lokal IR.

## <a name="high-level-steps-to-install-self-hosted-ir"></a>Övergripande steg för att installera lokal IR
1. Skapa en lokal integration runtime. Du kan använda ADF UI för att skapa lokal IR. Här är ett PowerShell-exempel:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2. Hämta och installera lokal integration runtime (på lokal dator).
3. Hämta autentiseringsnyckeln och registrera lokal integration runtime med nyckeln. Här är ett PowerShell-exempel:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="setting-up-self-hosted-ir-on-azure-vm-using-azure-resource-manager-template-automation"></a>Konfigurera lokal IR på Azure VM med hjälp av Azure Resource Manager-mall (automatiskt)
Du kan automatisera lokal IR-installationen på en virtuell Azure-dator med hjälp av [Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Detta ger ett enkelt sätt att ha en fullt fungerande lokal IR i Azure virtuellt nätverk med hög Avalaibility och skalbarhet (förutsatt att du ställer in antalet noder vara 2 eller högre).

## <a name="command-flow-and-data-flow"></a>Kommandot flödet och dataflöde
När du flyttar data mellan lokala och molnbaserade använder aktiviteten en lokal integration runtime för att överföra data från en lokal datakälla till molnet och tvärtom.

Här är ett övergripande dataflöde för sammanfattning av stegen för att kopiera med lokal IR:

![Översikt på hög nivå](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. Dataexperter skapar en lokal integration runtime i en Azure-datafabrik med en PowerShell-cmdlet. Azure-portalen stöder för närvarande inte den här funktionen.
2. Dataexperter skapar en länkad tjänst för ett lokalt datalager genom att ange lokal integration runtime-instans som den ska använda för att ansluta till datalager. Som en del av konfigurationen av den länkade tjänsten, dataexperter använder programmet ' Autentiseringshanteraren ”(för närvarande stöds inte) för att ställa in typer av autentisering och autentiseringsuppgifter. I dialogrutan med Nätverksautentiseringsuppgifter manager program kommunicerar med datalagret för att testa anslutningen och den lokala integreringskörningen att spara autentiseringsuppgifter.
   - Lokal integration runtime-noden krypterar autentiseringsuppgifter med Windows Data Protection Application Programming Interface (DPAPI) och sparar den lokalt. Om flera noder ställs in för hög tillgänglighet, synkroniseras ytterligare autentiseringsuppgifterna på andra noder. Varje nod krypterar den med hjälp av DPAPI och lagrar dem lokalt. Synkroniseringen av autentiseringsuppgifter är transparent för data-utvecklare och hanteras av lokal IR.    
   - Data Factory-tjänsten kommunicerar med den lokala integreringskörningen för schemaläggning och hantering av jobb via **kontrollkanal** som använder en delad Azure service bus-kö. När en aktivitet jobbet måste köras, placerar Data Factory begäran tillsammans med eventuella autentiseringsuppgifter (om autentiseringsuppgifter inte lagras redan på den lokala integreringskörningen). Lokal integration runtime startar jobbet efter avsökning kön.
   - Lokala integreringskörningen kopierar data från en lokal databas till en molnlagring, och vice versa beroende på konfigureringen av kopieringsaktiviteten i datapipelinen. För det här steget kommunicerar direkt lokal integration runtime med molnbaserade storage-tjänster som Azure Blob Storage via en säker kanal (HTTPS).

## <a name="considerations-for-using-self-hosted-ir"></a>Att tänka på när lokal IR

- En enda lokal integration runtime kan användas för flera lokala datakällor. Men en **enda lokal integration runtime är knuten till endast en Azure data factory** och kan inte delas med en annan data factory.
- Du kan ha **endast en instans av lokal integration runtime** installeras på en enda dator. Anta att du har två datafabriker som behöver åtkomst till lokala datakällor kan behöva du installera lokal integration runtime på två lokala datorer. Med andra ord är en lokal integration runtime knuten till en specifik data factory
- Den **lokal integration runtime behöver inte finnas på samma dator som datakällan**. Men minskar integration runtime med egen värd närmare datakällan tiden för lokal integration runtime kan ansluta till datakällan. Vi rekommenderar att du installerar den lokala integreringskörningen på en dator som skiljer sig från det som är värd för en lokal datakälla. När lokal integration runtime och datakällan finns på olika datorer, lokal integration runtime inte konkurrerar om resurser med datakällan.
- Du kan ha **flera IR-körningar på olika datorer som ansluter till samma lokala datakälla**. Exempelvis kan du kan ha två lokal integration runtime som betjänar två datafabriker men samma lokala datakälla har registrerats med båda datafabriker.
- Om du redan har en gateway som installerats på din dator fungerar en **Power BI** scenariot installerar ett **separat lokal integration runtime för Azure Data Factory** på en annan dator.
- Lokal integration runtime måste användas för att stödja integrering i Azure Virtual Network.
- Hantera din datakälla som en lokal datakälla (som finns bakom en brandvägg) även när du använder **ExpressRoute**. Använd lokal integration runtime för att upprätta en anslutning mellan tjänsten och datakällan.
- Du måste använda den lokala integreringskörningen även om datalagret finns i molnet på ett **Azure IaaS-dator**.
- Uppgifter kan misslyckas på en lokal Integration Runtime som installeras på en Windows-Server på vilken FIPS-kompatibel kryptering är aktiverat. Undvik problemet genom att inaktivera FIPS-kompatibel kryptering på servern. Om du vill inaktivera FIPS-kompatibel kryptering, kan du ändra följande registervärde från 1 (aktiverad) på 0 (inaktiverad): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Förutsättningar

- Den stöds **operativsystemet** versioner är Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. Installationen av den lokala integreringskörningen på en **domänkontrollant stöds inte**.
- **.NET framework 4.6.1 eller senare** krävs. Om du installerar lokal integration runtime på en Windows 7-dator, installera .NET Framework 4.6.1 eller senare. Se [systemkrav för .NET Framework](/dotnet/framework/get-started/system-requirements) mer information.
- Den rekommenderade **configuration** för lokal integration runtime datorn är minst 2 GHz, 4 kärnor, 8 GB RAM-minne och 80 GB-disk.
- Om värddatorn i viloläge, svarar lokal integration runtime inte på databegäranden. Därför konfigurera en lämplig energischema på datorn innan du installerar den lokala integreringskörningen. Om datorn är konfigurerad för att viloläge, anvisningarna lokal integration runtime för ett meddelande.
- Du måste vara administratör på datorn för att installera och konfigurera den lokala integreringskörningen har.
- Som kopiera aktivitet körs sker på en specifik frekvens, följer Resursanvändning (processor, minne) på datorn också samma mönster med högsta och inaktivitetstid. Resursutnyttjande beror också kraftigt på mängden data som flyttas. När flera kopia jobb pågår, visas gå upp under Högbelastningstider Resursanvändning.

## <a name="installation-best-practices"></a>Metodtips för installation
Lokal integration runtime kan installeras genom att ladda ned en MSI-installationspaketet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Se [flytta data mellan lokala och molnbaserade artikeln](tutorial-hybrid-copy-powershell.md) stegvisa instruktioner.

- Konfigurera energischema på värddatorn för den lokala integreringskörningen så att datorn inte försättas i viloläge. Om värddatorn i viloläge, aktiveras den lokala integreringskörningen offline.
- Säkerhetskopiera autentiseringsuppgifter som är associerade med den lokala integreringskörningen regelbundet.

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>Installera och registrera lokal IR från download center

1. Gå till [hämtningssidan för Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Klicka på **hämta**, Välj lämplig version (**32-bitars** vs. **64-bitars**), och klicka på **nästa**.
3. Kör den **MSI** direkt eller spara den på hårddisken och kör.
4. På den **Välkommen** väljer en **språk** klickar du på **nästa**.
5. **Acceptera** licensavtalet och klicka på **nästa**.
6. Välj **mappen** installera den lokala integreringskörningen och klicka **nästa**.
7. På den **redo att installera** klickar du på **installera**.
8. Klicka på **Slutför** att slutföra installationen.
9. Hämta nyckel för autentisering med Azure PowerShell. PowerShell-exempel för att hämta autentiseringsnyckel:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. På den **registrera Integration Runtime (lokal installation)** sidan för Microsoft Integration Runtime Configuration Manager som körs på din dator, gör följande:
    1. Klistra in den **autentiseringsnyckeln** i textområdet.
    2. Du kan också klicka på **Show autentiseringsnyckeln** att se nyckeltexten.
    3. Klicka på **registrera**.


## <a name="high-availability-and-scalability"></a>Hög tillgänglighet och skalbarhet
En lokal Integration Runtime kan kopplas till flera lokala datorer. Dessa datorer kallas noder. Du kan ha upp till fyra noder som är associerade med en lokal Integration Runtime. Fördelarna med att ha flera noder (lokala datorer med installerad gateway) för en logisk gateway är:
1. Högre tillgänglighet för lokal Integration Runtime så att den inte längre felpunkt i Big Data-lösning eller molnet dataintegreringen med Azure Data Factory, att säkerställa affärskontinuitet med upp till 4 noder.
2. Förbättrad prestanda och genomflöde under dataförflyttning mellan lokala och molnbaserade datalager. Få mer information om [prestandajämförelser](copy-activity-performance.md).

Du kan associera flera noder genom att helt enkelt installera lokal Integration Runtime-programvara från den [hämtningssidan](https://www.microsoft.com/download/details.aspx?id=39717) och genom att registrera den med någon av autentiseringsnycklar som hämtats från Ny AzureRmDataFactoryV2IntegrationRuntimeKey cmdlet som beskrivs i den [självstudien](tutorial-hybrid-copy-powershell.md)

> [!NOTE]
> Du behöver inte skapa en ny lokal Integration Runtime för att associera varje nod. Du kan installera den lokala integreringskörningen på en annan dator och registrera den med samma nyckel för autentisering. 

> [!NOTE]
> Innan du lägger till en annan nod för **hög tillgänglighet och skalbarhet**, kontrollera att **”fjärråtkomst till intranät'** alternativet är **aktiverat** på 1 nod (Microsoft Konfigurationshanteraren för integration Runtime -> Inställningar -> Remote åtkomst till intranät). 

### <a name="scale-considerations"></a>Skala överväganden

#### <a name="scale-out"></a>Skala ut

När den **tillgängligt minne på den lokala Integreringskörningen är lågt** och **processoranvändningen är hög**, lägga till en ny nod hjälper till att skala ut belastningen över datorer. Om aktiviteter som misslyckas på grund av timeout eller egenvärdbaserade IR-noden är offline, hjälper det om du lägger till en nod till gatewayen.

#### <a name="scale-up"></a>Skala upp

När tillgängligt minne och CPU används inte bra, men körningen samtidiga jobb når gränsen, ska du skala upp genom att öka antalet samtidiga jobb som kan köras på en nod. Du kanske också vill skala upp när aktiviteter Tidsgränsen nåddes för eftersom lokal IR är överbelastad. I följande bild visas kan du öka maximal kapacitet för en nod.  

![](media\create-self-hosted-integration-runtime\scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Krav för TLS/SSL-certifikat

Här följer kraven för TLS/SSL-certifikatet som används för att säkra kommunikationen mellan integration runtime-noder:

- Certifikatet måste vara ett offentligt betrott X509 v3-certifikat. Vi rekommenderar att du använder certifikat som utfärdas av en offentlig (tredje parts) certifikatutfärdare (CA).
- Det här certifikatet måste lita på varje nod för integration runtime.
- Certifikat med jokertecken stöds. Om din FQDN-namn är **node1.domain.contoso.com**, du kan använda ***. domain.contoso.com** som ämnesnamnet för certifikatet.
- SAN-certifikat rekommenderas inte eftersom det sista objektet i Alternativt ämnesnamn används och alla andra kommer att ignoreras på grund av aktuell begränsning. T.ex. du har ett SAN-certifikat vars SAN är **node1.domain.contoso.com** och **node2.domain.contoso.com**, du kan bara använda det här certifikatet på datorn vars FQDN är **node2.domain.contoso.com**.
- Stöder alla nyckelstorlek som stöds av Windows Server 2012 R2 för SSL-certifikat.
- Certifikat med CNG-nycklar stöds inte.  

## <a name="sharing-the-self-hosted-integration-runtime-ir-with-multiple-data-factories"></a>Dela lokal Integration Runtime (IR) med flera olika datafabriker för

Du kan återanvända en befintlig lokal integration runtime-infrastruktur kan du redan har installationen i en datafabrik. På så sätt kan du skapa en **länkad lokal integration runtime** i en annan data factory genom att referera till en redan befintlig lokal IR (delad).

#### <a name="terminologies"></a>**Termer**

- **Delade IR** – ursprungligt lokal IR som körs på en fysisk infrastruktur.  
- **Länkad IR** – The IR som refererar till en annan delad IR. Detta är en logisk IR och infrastrukturen i en annan lokal IR (delad).

#### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Hög nivå stegen för att skapa en länkad lokal IR

I den lokala Integreringskörningen som ska delas

1. Bevilja behörighet till Datafabriken som du vill skapa den länkade IR. 

   ![](media\create-self-hosted-integration-runtime\grant-permissions-IR-sharing.png)

   ![](media\create-self-hosted-integration-runtime\3_rbac_permissions.png)

2. Obs den **resurs-ID** av lokal IR som ska delas.

   ![](media\create-self-hosted-integration-runtime\4_ResourceID_self-hostedIR.png)

I Data Factory som behörigheten har beviljats,

3. Skapa en ny lokal IR (länk) och ange ovanstående **resurs-ID**

   ![](media\create-self-hosted-integration-runtime\6_create-linkedIR_2.png)

   ![](media\create-self-hosted-integration-runtime\6_create-linkedIR_3.png)

#### <a name="monitoring"></a>Övervakning 

- **Delade IR**

  ![](media\create-self-hosted-integration-runtime\Contoso-shared-IR.png)

  ![](media\create-self-hosted-integration-runtime\contoso-shared-ir-monitoring.png)

- **Länkade IR**

  ![](media\create-self-hosted-integration-runtime\Contoso-linked-ir.png)

  ![](media\create-self-hosted-integration-runtime\Contoso-linked-ir-monitoring.png)

#### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Kända begränsningar i lokal IR-delning

1. Standardvärdet för länkade IR som kan skapas under en enda lokal IR är **20**. Kontakta supporten om du behöver fler. 

2. Data factory som är länkade IR som ska skapas måste ha en MSI ([hanterad tjänstidentitet](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)). Som standard datafabriker skapas i Ibiza-portalen eller PowerShell-cmdlets har skapats implicit MSI. Men i vissa fall när datafabriken har skapats med hjälp av en Azure Resorce Manager-mall eller SDK, den ”**identitet**” **egenskapen måste anges** explicit för att säkerställa Azure Resorce Manager skapar en datafabrik som innehåller en MSI. 

3. Lokal IR-version måste vara lika med eller större än 3.8.xxxx.xx. . [Hämta den senaste versionen](https://www.microsoft.com/download/details.aspx?id=39717) av lokal IR

4. Data factory som är länkade IR som ska skapas måste ha en MSI ([hanterad tjänstidentitet](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)). Som standard datafabriker som skapats i Ibiza-portalen eller PowerShell-cmdlets har MSI ([hanterad tjänstidentitet](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)).
skapas implicit datafabriker som skapats med Azure Resource Manager (ARM)-mall eller SDK kräver dock ”identitet”-egenskapen anges till att säkerställa att en MSI skapas.

5. ADF .net SDK som stöder den här funktionen är version > = 1.1.0

6. Azure PowerShell som stöder den här funktionen är version > = 6.6.0 (AzureRM.DataFactoryV2 > = 0.5.7)

7. Om du vill bevilja behörighet, kräver användaren ”ägare” eller ärvda ”ägare” i Datafabriken där delade IR finns. 

  > [!NOTE]
  > Den här funktionen är endast tillgänglig i Azure Data Factory version 2 

## <a name="system-tray-icons-notifications"></a>Ikoner i systemfältet / meddelanden

Om du flyttar markören över system systemfältet ikonen/meddelandet hittar du information om tillståndet för lokal integration runtime.

![Systemfältet systemmeddelanden](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portar och brandvägg
Det finns två brandväggar som du behöver tänka på: **företagsbrandväggen** som körs på den centrala routern för organisationen, och **Windows-brandväggen** konfigurerad som en daemon på den lokala datorn där den lokal integration runtime har installerats.

![Brandvägg](media\create-self-hosted-integration-runtime\firewall.png)

Vid **företagets brandvägg** nivån, du behöver konfigurera följande domäner och utgående portar:

Domännamn | Portar | Beskrivning
------------ | ----- | ------------
*.servicebus.windows.net | 443 | Används för kommunikation med Data Movement Service-serverdelen
*.core.windows.net | 443 | Används för mellanlagrad kopiering med hjälp av Azure Blob (om konfigurerad)
*.frontend.clouddatahub.net | 443 | Används för kommunikation med Data Movement Service-serverdelen
Download.microsoft.com | 443 | Används för att ladda ned uppdateringar

Vid **Windows-brandväggen** nivå (datornivå), dessa utgående portar aktiveras normalt. Om inte, du kan konfigurera de domäner och portar i enlighet med detta på egenvärdbaserade datorn.

> [!NOTE]
> Baserat på din källa / mottagare, du kan behöva godkänna ytterligare domäner och utgående portar i företagets/Windows-brandväggen.
>
> För vissa Molndatabaser (till exempel: Azure SQL Database, Azure Data Lake osv), du kan behöva listan över godkända IP-adressen för lokal integration runtime-dator på sina brandväggskonfigurationen.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopieringsdata från en källa till en mottagare
Kontrollera att brandväggsreglerna har aktiverats korrekt på företagets brandvägg och Windows-brandväggen på den lokala installation av integration runtime-datorn, och den datalagringen själva. Om du aktiverar de här reglerna kan den lokala integreringskörningen att ansluta till både källa och mottagare har. Aktivera regler för varje datalager som är inblandade i kopieringsåtgärden.

Till exempel att kopiera från en **lokala datalager till en Azure SQL Database-mottagare eller en Azure SQL Data Warehouse sink**, gör följande:

- Tillåt utgående **TCP** kommunikation på port **1433** för både Windows-brandväggen och företagets brandvägg.
- Konfigurera brandväggsinställningarna för Azure SQL-server för att lägga till IP-adressen för den lokala installation av integration runtime-datorn i listan över tillåtna IP-adresser.

> [!NOTE]
> Om din brandvägg inte tillåter utgående port 1433, lokal integration runtime kan inte komma åt Azure SQL direkt. I det här fallet kan du använda [mellanlagrad kopiering](copy-activity-performance.md) till SQL Azure-databas / SQL Azure DW. I det här scenariot kan kräver du bara HTTPS (port 443) för dataförflyttning.


## <a name="proxy-server-considerations"></a>Proxyserver
Om nätverksmiljön företagets använder en proxyserver för att få åtkomst till internet, kan du konfigurera lokal integration runtime för att använda rätt proxyinställningar. Du kan ange proxyservern under fasen för första registreringen.

![Ange proxy](media\create-self-hosted-integration-runtime\specify-proxy.png)

Lokal integration runtime använder proxyservern för att ansluta till Molntjänsten. Klicka på Ändra-länk under installationen. Du ser en dialogruta för proxy-inställningen.

![Ställ in proxy](media\create-self-hosted-integration-runtime\set-http-proxy.png)

Det finns tre alternativ:

- **Använd inte proxy**: lokal integration runtime inte uttryckligen använder alla proxy för att ansluta till molntjänster.
- **Använd systemproxy**: lokal installation av integration runtime använder Proxyinställningen som har konfigurerats för diahost.exe.config och diawp.exe.config. Om ingen proxy har konfigurerats i diahost.exe.config och diawp.exe.config, ansluter lokal integration runtime till Molntjänsten direkt utan att gå via proxy.
- **Använd anpassad proxy**: konfigurera HTTP-proxyinställning för lokal integration runtime, istället för att använda konfigurationer i diahost.exe.config och diawp.exe.config. Adress och Port krävs. Användarnamn och lösenord är valfria beroende på inställningen för autentisering av din proxyserver. Alla inställningar är krypterad med Windows DPAPI på den lokala integreringskörningen och lagras lokalt på datorn.

Integration runtime värdtjänsten startas om automatiskt när du har sparat de uppdaterade proxyinställningarna.

När lokal integration runtime har registrerats, om du vill visa eller uppdatera proxyinställningarna, använda Konfigurationshanteraren för Integration Runtime.

1. Starta **Konfigurationshanteraren för Microsoft Integration Runtime**.
   - Växla till fliken **Settings** (Inställningar).
   - Klicka på **ändra** länken i **HTTP-Proxy** avsnitt för att starta den **ange HTTP-Proxy** dialogrutan.
   - När du klickar på den **nästa** knappen, visas en varningsdialogruta som ber om din tillåtelse för att spara Proxyinställningen och starta om värdtjänsten för Integration Runtime.

Du kan visa och uppdatera HTTP-proxy med verktyget Configuration Manager.

![Visa proxy](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> Om du har konfigurerat en proxyserver med NTLM-autentisering, Integration runtime värdtjänsten körs under domänkontot. Om du ändrar lösenordet för domänkontot senare, Kom ihåg att uppdatera konfigurationsinställningarna för tjänsten och starta om den i enlighet med detta. På grund av det här kravet föreslår vi att du använder en särskild domän-konto för att få åtkomst till proxyservern kräver inte att uppdatera lösenordet för ofta.

### <a name="configure-proxy-server-settings"></a>Konfigurera inställningar för proxyserver

Om du väljer **Använd systemproxy** ställa in för HTTP-proxy, lokal integration runtime använder Proxyinställningen i diahost.exe.config och diawp.exe.config. Om ingen proxy har angetts i diahost.exe.config och diawp.exe.config ansluter lokal integration runtime till Molntjänsten direkt utan att gå via proxy. Följande procedur innehåller instruktioner för att uppdatera filen diahost.exe.config.

1. Skapa en säker kopia av C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config att säkerhetskopiera den ursprungliga filen i Utforskaren.
2. Starta Notepad.exe kör som administratör och öppna textfil ”C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. Du kan hitta Standardetiketten för system.net enligt följande kod:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Du kan sedan lägga till information om proxy-server som du ser i följande exempel:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Ytterligare egenskaper tillåts i proxy-taggen för att ange inställningarna som krävs som scriptLocation. Se [proxy Element (nätverksinställningar)](https://msdn.microsoft.com/library/sa91de1e.aspx) för syntax.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Spara konfigurationsfilen till den ursprungliga platsen och sedan starta om tjänsten lokal Integration Runtime-värden, som hämtar ändringarna. Starta om tjänsten: använda tjänster-appleten på Kontrollpanelen, eller från den **Konfigurationshanteraren för Integration Runtime** > klickar du på den **stoppa tjänsten** knappen och klicka sedan på den **Start Tjänsten**. Om tjänsten inte startar, är det troligt att en felaktig syntax för XML-taggen har lagts till i programmets konfigurationsfil som har redigerats.

> [!IMPORTANT]
> Glöm inte att uppdatera både diahost.exe.config och diawp.exe.config.

Förutom de här punkterna måste du också se till att Microsoft Azure är ditt företags vitlistan. Lista över giltiga Microsoft Azure-IP-adresser kan laddas ned från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Eventuella symptom för brandväggen och proxyservern serverproblem
Om det uppstår fel som liknar det följande är det troligen på grund av felaktig konfigurering av brandvägg eller proxy-servern, vilket blockerar lokal integration runtime från att ansluta till Data Factory för att autentisera sig själv. Se föregående avsnitt för att se till att brandväggen och proxyservern konfigureras korrekt.

1. När du försöker registrera den lokala integreringskörningen visas följande felmeddelande: ”Det gick inte att registrera den här Integration Runtime-noden! Bekräfta att autentiseringsnyckeln är giltig och värdtjänsten för Integration-tjänsten körs på den här datorn. "
   - När du öppnar Konfigurationshanteraren för Integration Runtime kan du se statusen ”**frånkopplad**” eller ”**ansluter**”. När du visar Windows-händelseloggar, under ”Loggboken” > ”program och tjänstloggar” > ”Microsoft Integration Runtime” felmeddelanden visas till exempel följande fel:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-intranet"></a>Aktivera fjärråtkomst från intranät  
I fallet om du använder **PowerShell** eller **Autentiseringshanteraren** att kryptera autentiseringsuppgifter från en annan dator (i nätverket) än där den lokala integreringskörningen installeras sedan Du skulle kräva den **fjärråtkomst från intranät** alternativet är aktiverat. Om du kör den **PowerShell** eller **Autentiseringshanteraren** att kryptera autentiseringsuppgifter på samma dator där den lokala integreringskörningen installeras sedan **' fjärråtkomst från intranätet '** får inte aktiveras.

Fjärråtkomst från intranätet ska vara **aktiverat** innan du lägger till en annan nod för **hög tillgänglighet och skalbarhet**.  

Under lokal installation av integration runtime (och senare för v 3.3.xxxx.x), som standard, lokal integration runtime-installation inaktiverar den **fjärråtkomst från intranät** på den lokala installation av integration runtime-datorn.

Om du använder en brandvägg från tredje part, kan du manuellt öppna port 8060 (eller den konfigurerade användaren-porten). Om du stöter på problem med brandväggen under installationen av lokal integration runtime, kan du använda följande kommando för att installera den lokala integreringskörningen utan att konfigurera brandväggen.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> **Autentiseringshanteraren** ännu inte är tillgängliga för kryptering av autentiseringsuppgifter i ADFv2. Vi ska lägga till det här stödet senare.  

Om du inte väljer att öppna port 8060 på den lokala installation av integration runtime-datorn, använda metoder än med hjälp av den ** inställningen autentiseringsuppgifter ** program för att konfigurera autentiseringsuppgifter för datalagring. Du kan till exempel använda New-AzureRmDataFactoryV2LinkedServiceEncryptCredential PowerShell-cmdlet. Se avsnittet Ange autentiseringsuppgifter och säkerhet på hur data lagrar autentiseringsuppgifter kan ställas in.


## <a name="next-steps"></a>Nästa steg
Finns i följande självstudiekurs stegvisa instruktioner: [självstudie: kopiera lokala data till molnet](tutorial-hybrid-copy-powershell.md).
