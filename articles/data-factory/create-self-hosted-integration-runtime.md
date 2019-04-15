---
title: Skapa en lokal integration runtime i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar en lokal integration runtime i Azure Data Factory, vilket gör att datafabriker för att få åtkomst till datalager på ett privat nätverk.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: aaa72d3a29fee28ede336a2be350015bf3cbc9b4
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565558"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Skapa och konfigurera en lokal integration runtime
Integration runtime (IR) är beräkningsinfrastrukturen som Azure Data Factory använder för att tillhandahålla funktioner för dataintegrering olika nätverksmiljöer integrationsfunktioner. Mer information om IR finns [översikten över Integration runtime](concepts-integration-runtime.md).

En lokal integration runtime kan köra Kopieringsaktivitet mellan ett molndatalager och ett datalager i ett privat nätverk och den kan skicka transformeringsaktiviteter mot beräkningsresurser i ett lokalt nätverk eller ett Azure-nätverk. Installationen av en lokal integration runtime måste på en lokal dator eller en virtuell dator (VM) i ett privat nätverk.  

Det här dokumentet beskriver hur du kan skapa och konfigurera en lokal IR.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>Övergripande steg för att installera en lokal IR
1. Skapa Integration Runtime med egen värd. Du kan använda Azure Data Factory-Användargränssnittet för den här uppgiften. Här är ett PowerShell-exempel:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Ladda ned](https://www.microsoft.com/download/details.aspx?id=39717) och installera den lokala integreringskörningen på en lokal dator.

3. Hämta autentiseringsnyckeln och registrera den lokala integreringskörningen med nyckeln. Här är ett PowerShell-exempel:

    ```powershell

    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template-automation"></a>Konfigurera en lokal IR på en Azure virtuell dator med hjälp av en Azure Resource Manager-mall (automatiskt)
Du kan automatisera lokal IR-installationen på virtuella Azure-datorer med hjälp av [Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Den här mallen innehåller ett enkelt sätt att ha en fullt fungerande lokal IR i Azure-nätverk med hög tillgänglighet och skalbarhet (förutsatt att du ställer in antalet noder 2 eller högre).

## <a name="command-flow-and-data-flow"></a>Kommandot flödet och dataflöde
När du flyttar data mellan lokalt och molnet, aktiviteten använder en lokal integration runtime för att överföra data från en lokal datakälla till molnet och tvärtom.

Här är ett övergripande dataflöde för sammanfattning av stegen för att kopiera med en lokal IR:

![Översikt på hög nivå](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Data-utvecklare skapar en lokal integration runtime i en Azure-datafabrik med hjälp av en PowerShell-cmdlet. Azure-portalen stöder för närvarande inte den här funktionen.
2. Data-utvecklare skapar en länkad tjänst för ett lokalt datalager genom att ange lokal integration runtime-instans som den ska använda för att ansluta till datalager.
3. Lokal integration runtime-noden krypterar autentiseringsuppgifterna med hjälp av Windows Data Protection Application Programming Interface (DPAPI) och sparar autentiseringsuppgifter lokalt. Om flera noder ställs in för hög tillgänglighet, synkroniseras ytterligare autentiseringsuppgifterna på andra noder. Varje nod krypterar autentiseringsuppgifterna med hjälp av DPAPI och lagrar dem lokalt. Synkroniseringen av autentiseringsuppgifter är transparent för data-utvecklare och hanteras av lokal IR.    
4. Data Factory-tjänsten kommunicerar med den lokala integreringskörningen för schemaläggning och hantering av jobb via en *kontrollkanal* som använder en delad Azure Service Bus-kö. När en aktivitet jobbet måste köras, placerar Data Factory begäran tillsammans med eventuella autentiseringsuppgifter (om autentiseringsuppgifter inte lagras redan på den lokala integreringskörningen). Lokal integration runtime startar jobbet efter avsökning kön.
5. Den lokala integreringskörningen kopierar data från en lokal databas till en molnlagring, och vice versa beroende på konfigureringen av kopieringsaktiviteten i datapipelinen. För det här steget kommunicerar lokal integration runtime direkt med molnbaserade storage-tjänster som Azure Blob storage via en säker kanal (HTTPS).

## <a name="considerations-for-using-a-self-hosted-ir"></a>Överväganden för att använda en lokal IR

- En enda lokal integration runtime kan användas för flera lokala datakällor. En enda lokal integration runtime kan delas med en annan data factory inom samma Azure Active Directory-klientorganisation. Mer information finns i [delning av en lokal integration runtime](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories).
- Du kan ha endast en instans av en lokal integration runtime som installeras på en enda dator. Om du har två datafabriker som behöver åtkomst till lokala datakällor, måste du installera den lokala integreringskörningen på två lokala datorer varje från båda datafabriker eller använda den [lokal IR funktionenfördelning](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories)att dela en lokal integration runtime med en annan Data Factory.  
- Lokal integration runtime behöver inte finnas på samma dator som datakällan. Men minskar med den lokala integreringskörningen närmare datakällan tiden för lokal integration runtime kan ansluta till datakällan. Vi rekommenderar att du installerar den lokala integreringskörningen på en dator som skiljer sig från det som är värd för en lokal datakälla. När lokal integration runtime och datakällan finns på olika datorer, lokal integration runtime inte konkurrerar om resurser med datakällan.
- Du kan ha flera IR-körningar på olika datorer som ansluter till samma lokala datakällan. Till exempel kanske du har två IR-körningar som betjänar två datafabriker, men samma lokala datakälla har registrerats med båda datafabriker.
- Om du redan har ingen gateway installerad på datorn för att hantera ett scenario med Power BI kan du installera en separat lokal integration runtime för Azure Data Factory på en annan dator.
- Den lokala integreringskörningen måste användas för att stödja dataintegrering inom en Azure-nätverk.
- Hantera din datakälla som en lokal datakälla som finns bakom en brandvägg, även om du använder Azure ExpressRoute. Använd lokal integration runtime för att upprätta en anslutning mellan tjänsten och datakällan.
- Även om datalagret är i molnet på en virtuell Azure IaaS-dator måste du använda en lokal integration runtime.
- Uppgifter kan misslyckas på en lokal integration runtime som är installerad på en Windows-server på vilken FIPS-kompatibel kryptering är aktiverat. Undvik problemet genom att inaktivera FIPS-kompatibel kryptering på servern. Om du vill inaktivera FIPS-kompatibel kryptering, kan du ändra följande registervärde från 1 (aktiverad) på 0 (inaktiverad): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Förutsättningar

- Versionerna av operativsystem som stöds är Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Installationen av den lokala integreringskörningen på en domänkontrollant stöds inte.
- .NET framework 4.6.1 eller senare krävs. Om du installerar den lokala integreringskörningen på en Windows 7-dator, installera .NET Framework 4.6.1 eller senare. Se [systemkrav för .NET Framework](/dotnet/framework/get-started/system-requirements) mer information.
- Den rekommenderade konfigurationen för den lokala installationen av integration runtime-datorn är minst 2 GHz, fyra kärnor, 8 GB RAM och en 80GB-disk.
- Om värddatorn i viloläge, svarar lokal integration runtime inte på databegäranden. Konfigurera en lämplig energischema på datorn innan du installerar den lokala integreringskörningen. Om datorn är konfigurerad för att viloläge, anvisningarna lokal integration runtime för ett meddelande.
- Du måste vara administratör på datorn för att installera och konfigurera den lokala integreringskörningen har.
- Kopiera aktivitetskörningar sker på en specifik frekvens. Resursanvändning (processor, minne) på datorn följer samma mönster med högsta och inaktivitetstid. Resursutnyttjande beror också kraftigt på mängden data som flyttas. När flera kopia jobb pågår, visas gå upp under Högbelastningstider Resursanvändning.

## <a name="installation-best-practices"></a>Metodtips för installation
Du kan installera den lokala integreringskörningen genom att ladda ned en MSI-installationspaketet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Se [flytta data mellan lokala och molnbaserade artikeln](tutorial-hybrid-copy-powershell.md) stegvisa instruktioner.

- Konfigurera ett energischema på värddatorn för den lokala integreringskörningen så att datorn inte försättas i viloläge. Om värddatorn i viloläge, kopplas lokal integration runtime från.
- Säkerhetskopiera autentiseringsuppgifter som är associerade med den lokala integreringskörningen regelbundet.

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Installera och registrera lokal IR från Download Center

1. Gå till den [hämtningssidan för Microsoft integration runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Välj **hämta**, Välj 64-bitarsversionen (32-bitars inte stöds), och välj **nästa**.
3. Kör MSI-filen direkt, eller spara det på hårddisken och kör den.
4. På den **Välkommen** , Välj ett språk och välj **nästa**.
5. Acceptera licensvillkoren för programvara från Microsoft och välj **nästa**.
6. Välj **mappen** att installera den lokala integreringskörningen och välj **nästa**.
7. På den **redo att installera** väljer **installera**.
8. Klicka på **Slutför** att slutföra installationen.
9. Hämta autentiseringsnyckel för med hjälp av Azure PowerShell. Här är ett PowerShell-exempel för att hämta nyckeln:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. På den **registrera Integration Runtime (lokal installation)** sidan för Microsoft Integration Runtime Configuration Manager som körs på din dator, gör följande:

    a. Klistra in nyckeln i textområdet.

    b. Alternativt kan du välja **Show autentiseringsnyckeln** att se nyckeltexten.

    c. Välj **Registrera**.


## <a name="high-availability-and-scalability"></a>Hög tillgänglighet och skalbarhet
En lokal integration runtime kan associeras med flera lokala datorer eller virtuella datorer i Azure. Dessa datorer kallas noder. Du kan ha upp till fyra noder som är associerade med en lokal integration runtime. Fördelarna med att ha flera noder (lokala datorer med en installerad gateway) för en logisk gateway är:
* Högre tillgänglighet för lokal integration runtime så att den är inte längre felpunkt i dina stordata lösning eller molnet dataintegrering med Azure Data Factory, att säkerställa affärskontinuitet med upp till fyra noder.
* Förbättrad prestanda och genomflöde under dataförflyttning mellan lokala och molnbaserade datalager. Få mer information om [prestandajämförelser](copy-activity-performance.md).

Du kan associera flera noder genom att installera lokal integration runtime-programvara från den [Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Sedan, registrera den med någon av autentiseringsnycklarna har hämtats från den **New-AzDataFactoryV2IntegrationRuntimeKey** cmdlet, enligt beskrivningen i den [självstudien](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Du behöver inte skapa nya lokal integration runtime för att associera varje nod. Du kan installera den lokala integreringskörningen på en annan dator och registrera den med hjälp av samma autentiseringsnyckeln. 

> [!NOTE]
> Innan du lägger till en annan nod för hög tillgänglighet och skalbarhet, kontrollera att den **fjärråtkomst till intranät** är aktiverat på den första noden (**Konfigurationshanteraren för Microsoft Integration Runtime**  >  **Inställningar** > **fjärråtkomst till intranät**). 

### <a name="scale-considerations"></a>Skala överväganden

#### <a name="scale-out"></a>Skala ut

När det tillgängliga minnet på den lokala Integreringskörningen är låg och CPU-användning är hög, kan lägga till en ny nod skala ut belastningen över datorer. Om aktiviteter som misslyckas eftersom de når sin tidsgräns eller eftersom lokal IR-noden är offline, hjälper det om du lägger till en nod till gatewayen.

#### <a name="scale-up"></a>Skala upp

När tillgängligt minne och CPU används inte bra, men körningen av samtidiga jobb når gränsen, ska du skala upp genom att öka antalet samtidiga jobb som kan köras på en nod. Du kanske också vill skala upp när aktiviteter Tidsgränsen nåddes för eftersom lokal IR är överbelastad. Du kan öka den maximala kapaciteten för en nod som du ser i följande bild:  

![Öka samtidiga jobb som kan köras på en nod](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Krav för TLS/SSL-certifikat

Här följer kraven för TLS/SSL-certifikatet som används för att säkra kommunikationen mellan integration runtime-noder:

- Certifikatet måste vara ett offentligt betrott X509 v3-certifikat. Vi rekommenderar att du använder certifikat som utfärdas av en offentlig (partner) certifikatutfärdare (CA).
- Det här certifikatet måste lita på varje nod för integration runtime.
- Vi rekommenderar inte alternativt namn på CERTIFIKATMOTTAGARE certifikat eftersom endast den sista punkten SAN ska användas och alla andra kommer att ignoreras på grund av nuvarande begränsningar. Till exempel om du har ett SAN-certifikat som SAN är **node1.domain.contoso.com** och **node2.domain.contoso.com**, du kan använda det här certifikatet endast på en dator vars FQDN är  **node2.domain.contoso.com**.
- Certifikatet har stöd för alla nyckelstorlek som stöds av Windows Server 2012 R2 för SSL-certifikat.
- Certifikat med CNG-nycklar stöds inte.  

> [!NOTE]
> Det här certifikatet används för att kryptera portar på den lokala installationens IR-nod, används för **kommunikation från nod till nod** (för synkronisering av tillstånd som innehåller länkade tjänster autentiseringsuppgifter synkronisering mellan noder) medan **med hjälp av PowerShell-cmdlet: en för den länkade tjänsten autentiseringsuppgifter inställningen** från i lokala nätverket. Vi föreslår att du använder det här certifikatet om nätverksmiljön privata inte är säker eller om du vill att skydda kommunikationen mellan noderna i ditt privata nätverk. Dataförflyttning under överföring från lokal IR till datalager sker alltid med krypterad kanal, oavsett den här certifikat har angetts eller inte. 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>Dela en lokal integration runtime med flera olika datafabriker för

Du kan återanvända en befintlig lokal integration runtime-infrastruktur som du redan har konfigurerat i en datafabrik. Detta kan du skapa en *länkad lokal integration runtime* i en annan data factory genom att referera till en befintlig lokal IR (delad).

Om du vill dela en lokal integration runtime med hjälp av PowerShell, se [skapa en delad lokal integration runtime i Azure Data Factory med PowerShell](create-shared-self-hosted-integration-runtime-powershell.md).

Titta på följande videoklipp för en tolv minuters introduktion och demonstration av den här funktionen:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologi

- **Delade IR**: Den ursprungliga lokal IR som körs på en fysisk infrastruktur.  
- **Länkad IR**: IR som refererar till en annan delad IR. Detta är en logisk IR och infrastrukturen i en annan lokal IR (delad).

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Anvisningar för att skapa en länkad lokal IR

1. I den lokala Integreringskörningen som ska delas, bevilja behörighet till datafabriken som du vill skapa den länkade IR. 

   ![Knappen för att bevilja behörighet i delningsfliken](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![Val för att tilldela behörigheter](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. Observera resurs-ID för den lokala Integreringskörningen som ska delas.

   ![Platsen för resurs-ID](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. I data factory som behörigheten har beviljats, skapa en ny lokal IR (länk) och ange resurs-ID.

   ![Knappen för att skapa en länkad lokal integration runtime](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![Rutorna för namn och resurs-ID](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>Övervakning 

- **Delade IR**

  ![Val för att söka efter en delad integration runtime](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Flik för att övervaka](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **Länkade IR**

  ![Val för att hitta en länkad integration runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Flik för att övervaka](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Kända begränsningar i lokal IR-delning

* Data factory som en länkad IR ska skapas måste ha en [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Som standard datafabriker skapas i Azure portal eller PowerShell-cmdlets har en MSI som skapats implicit. Men när en datafabrik har skapats via en Azure Resource Manager-mall eller SDK, den **identitet** egenskapen måste anges uttryckligen att säkerställa att Azure Resource Manager skapar en datafabrik som innehåller en MSI. 

* Azure Data Factory .NET SDK som stöder den här funktionen är version 1.1.0 eller senare.

* Användaren måste rollen ägare eller ärvda ägarrollen i datafabriken där det finns delad IR för att ge behörighet.

* Funktionen för delning fungerar endast för Datafabriker inom samma Azure Active Directory-klientorganisation.

* För Active Directory [gästanvändare](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews), sökfunktionen (lista alla datafabriker med hjälp av en sökordet) i Användargränssnittet [fungerar inte](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Men så länge som gästanvändare är ägare till data factory, de kan dela IR utan att sökningen genom att direkt skriva MSI på data factory IR måste delas i den **tilldela behörighet** textruta och att välja **Lägg till** i Användargränssnittet för Azure Data Factory. 

  > [!NOTE]
  > Den här funktionen är endast tillgänglig i Azure Data Factory V2. 

## <a name="notification-area-icons-and-notifications"></a>Ikoner i meddelandefältet och meddelanden

Om du flyttar du markören över ikonen eller meddelande i meddelandefältet, kan du hitta information om tillståndet för lokal integration runtime.

![Meddelanden i meddelandefältet](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portar och brandvägg
Det finns två brandväggar att tänka på: den *företagsbrandväggen* som körs på den centrala routern för organisationen, och *Windows-brandväggen* konfigurerad som en daemon på den lokala datorn där den lokal integration runtime har installerats.

![Brandvägg](media/create-self-hosted-integration-runtime/firewall.png)

På den *företagets brandvägg* nivå, måste du konfigurera följande domäner och utgående portar:

Domännamn | Portar | Beskrivning
------------ | ----- | ------------
*.servicebus.windows.net | 443 | Används för kommunikation med av backend-data movement service
*.core.windows.net | 443 | Används för mellanlagrad kopiering via Azure Blob-lagringen (om konfigurerad)
*.frontend.clouddatahub.net | 443 | Används för kommunikation med av backend-data movement service
Download.microsoft.com | 443 | Används för att ladda ned uppdateringar

På den *Windows-brandväggen* nivå (datornivå), dessa utgående portar aktiveras normalt. Om inte, du kan konfigurera de domäner och portar i enlighet med detta på en lokal integration runtime-dator.

> [!NOTE]
> Baserat på din källa och mottagare, kan du behöva godkänna ytterligare domäner och utgående portar i företagets brandvägg eller Windows-brandväggen.
>
> För vissa molndatabaser (som Azure SQL Database och Azure Data Lake), kan du behöva placera IP-adresser för de lokala integrationskörningsdatorernas brandväggskonfiguration i listan över tillåtna.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopieringsdata från en källa till en mottagare
Kontrollera att brandväggsreglerna har aktiverats korrekt på företagets brandvägg och Windows-brandväggen på den lokala installation av integration runtime-datorn, och den datalagringen själva. Om du aktiverar de här reglerna kan den lokala integreringskörningen att ansluta till både källa och mottagare har. Aktivera regler för varje datalager som är inblandade i kopieringsåtgärden.

Till exempel för att kopiera från ett lokalt datalager till en Azure SQL Database-mottagare eller en Azure SQL Data Warehouse-mottagaren, gör du följande:

1. Tillåt utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företagets brandvägg.
2. Konfigurera brandväggsinställningarna för Azure SQL-databasen för att lägga till IP-adressen för den lokala installation av integration runtime-datorn i listan över tillåtna IP-adresser.

> [!NOTE]
> Om din brandvägg inte tillåter utgående port 1433, lokal integration runtime kan inte komma åt Azure SQL-databasen direkt. I det här fallet kan du använda en [mellanlagrad kopiering](copy-activity-performance.md) till Azure SQL Database och Azure SQL Data Warehouse. I det här scenariot skulle du behöver endast HTTPS (port 443) för dataförflyttning.


## <a name="proxy-server-considerations"></a>Proxyserver
Om nätverksmiljön företagets använder en proxyserver för att få åtkomst till internet, kan du konfigurera den lokala integreringskörningen för att använda rätt proxyinställningar. Du kan ange proxyservern under fasen för första registreringen.

![Ange proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

När konfigurerad, lokal integration runtime använder proxyservern kan ansluta till Molntjänsten, källa / mål (de som använder HTTP / HTTPS-protokollet). Det här är väljer **Ändra-länk** under installationen. Du ser dialogrutan proxy-inställning.

![Ställ in proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Det finns tre alternativ:

- **Använd inte proxy**: Lokal integration runtime använder inte uttryckligen alla proxy för att ansluta till molntjänster.
- **Använd systemproxy**: Lokal integration runtime använder Proxyinställningen som konfigureras i diahost.exe.config och diawp.exe.config. Om ingen proxy har konfigurerats i diahost.exe.config och diawp.exe.config, ansluter lokal integration runtime till Molntjänsten direkt utan att gå via en proxyserver.
- **Använd anpassad proxy**: Konfigurera HTTP-proxyinställning för lokal integration runtime, istället för att använda konfigurationer i diahost.exe.config och diawp.exe.config. **Adress** och **Port** krävs. **Användarnamn** och **lösenord** är valfria beroende på inställningen för autentisering av din proxyserver. Alla inställningar är krypterad med Windows DPAPI på den lokala integreringskörningen och lagras lokalt på datorn.

Integration runtime värdtjänsten startas om automatiskt när du har sparat de uppdaterade proxyinställningarna.

När den lokala integreringskörningen har registrerats, om du vill visa eller uppdatera proxyinställningarna, använda Konfigurationshanteraren för Integration Runtime.

1. Öppna **Konfigurationshanteraren för Microsoft Integration Runtime**.
2. Växla till fliken **Settings** (Inställningar).
3. Välj den **ändra** länken i den **HTTP-Proxy** avsnitt för att öppna den **ange HTTP-Proxy** dialogrutan.
4. Välj **Nästa**. Du kan sedan visas en varning som frågar efter din tillåtelse för att spara Proxyinställningen och starta om värdtjänsten för integration runtime.

Du kan visa och uppdatera HTTP-proxy med hjälp av verktyget Configuration Manager.

![Visa proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Om du har konfigurerat en proxyserver med NTLM-autentisering körs värdtjänsten för integration runtime under domänkontot. Om du ändrar lösenordet för domänkontot senare, Kom ihåg att uppdatera konfigurationsinställningarna för tjänsten och starta om den i enlighet med detta. Vi rekommenderar att du använder en särskild domän-konto för att få åtkomst till proxyservern som inte behöver du uppdatera lösenordet ofta att på grund av det här kravet.

### <a name="configure-proxy-server-settings"></a>Konfigurera inställningar för proxyserver

Om du väljer den **Använd systemproxy** ställa in för HTTP-proxy, lokal integration runtime använder Proxyinställningen i diahost.exe.config och diawp.exe.config. Om ingen proxy har angetts i diahost.exe.config och diawp.exe.config ansluter lokal integration runtime till Molntjänsten direkt utan att gå via proxy. Följande procedur innehåller instruktioner för att uppdatera filen diahost.exe.config:

1. Skapa en säker kopia av C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config att säkerhetskopiera den ursprungliga filen i Utforskaren.
2. Öppna Notepad.exe köra som administratör och öppna filen C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. Hitta Standardetiketten för system.net enligt följande kod:

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

    Ytterligare egenskaper tillåts i proxy-taggen anger du inställningarna som krävs som `scriptLocation`. Se [proxy Element (nätverksinställningar)](https://msdn.microsoft.com/library/sa91de1e.aspx) för syntax.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Spara konfigurationsfilen på den ursprungliga platsen. Starta sedan om den lokala integreringskörningen värdtjänsten som hämtar ändringarna. 

   Starta om tjänsten genom att använda tjänster-appleten på Kontrollpanelen. Eller från Integration Runtime Configuration Manager, Välj den **stoppa tjänsten** och välj sedan **starta tjänsten**. 
   
   Om tjänsten inte startar, är det troligt att en felaktig syntax för XML-taggen har lagts till i programmets konfigurationsfil som har redigerats.

> [!IMPORTANT]
> Glöm inte att uppdatera både diahost.exe.config och diawp.exe.config.

Du måste också se till att Microsoft Azure i ditt företags-vitlistan. Du kan hämta listan över giltiga Microsoft Azure IP-adresser från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Eventuella symptom för brandväggen och proxyservern serverproblem
Om det uppstår fel som liknar det följande är det troligen på grund av felaktig konfigurering av brandvägg eller proxy-server, som blockerar den lokala integreringskörningen från att ansluta till Data Factory för att autentisera sig själv. För att säkerställa att din brandvägg och proxy server har konfigurerats korrekt, se föregående avsnitt.

* När du försöker registrera den lokala integreringskörningen visas följande fel: ”Det gick inte att registrera den här Integration Runtime-noden! Bekräfta att autentiseringsnyckeln är giltig och integrationstjänsten värdtjänsten körs på den här datorn ”.
* När du öppnar Konfigurationshanteraren för Integration Runtime kan du se statusen **frånkopplad** eller **ansluter**. När du visar Windows-händelseloggar, under **Loggboken** > **program- och tjänstloggar** > **Microsoft Integration Runtime**, Du får felmeddelanden som den här:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Aktivera fjärråtkomst från intranät  
Om du använder PowerShell för att kryptera autentiseringsuppgifterna från en annan dator (i nätverket) än där lokal integration runtime är installerad, kan du aktivera den **fjärråtkomst från intranät** alternativet. Om du kör PowerShell för att kryptera autentiseringsuppgifterna på samma dator där den lokala integreringskörningen har installerats kan du inte aktivera **fjärråtkomst från intranät**.

Du bör aktivera **fjärråtkomst från intranät** innan du lägger till en annan nod för hög tillgänglighet och skalbarhet.  

Under lokal installation av integration runtime (senare version-3.3.xxxx.x), som standard, lokal integration runtime-installation inaktiverar **fjärråtkomst från intranät** på den lokala installation av integration runtime-datorn.

Om du använder en brandvägg från tredje part, kan du manuellt öppna port 8060 (eller användardefinierat-port). Om du har problem med brandväggen när du konfigurerar lokal integration runtime kan du prova med följande kommando för att installera den lokala integreringskörningen utan att konfigurera brandväggen:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
``` 

Om du inte väljer att öppna port 8060 på den lokala installation av integration runtime-datorn, kan du använda metoder än programmet ange autentiseringsuppgifter för att konfigurera autentiseringsuppgifter för datalagring. Du kan till exempel använda den **New AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell-cmdlet.


## <a name="next-steps"></a>Nästa steg
Se följande självstudie för stegvisa instruktioner: [Självstudier: Kopiera lokala data till molnet](tutorial-hybrid-copy-powershell.md).
