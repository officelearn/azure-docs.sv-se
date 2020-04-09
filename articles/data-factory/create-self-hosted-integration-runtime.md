---
title: Skapa en lokal Integration Runtime
description: Lär dig hur du skapar en självvärd integreringskörning i Azure Data Factory, som gör att datafabriker kan komma åt datalager i ett privat nätverk.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 03/13/2020
ms.openlocfilehash: 3fe5a58046776d00ce68189cf724a995380869eb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887322"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Skapa och konfigurera lokalt installerad integrationskörning

Integrationskörningen (IR) är den beräkningsinfrastruktur som Azure Data Factory använder för att tillhandahålla dataintegrationsfunktioner i olika nätverksmiljöer. Mer information om IR finns i [Översikt över integrationskörning](concepts-integration-runtime.md).

En självvärd integrationskörning kan köra kopieringsaktiviteter mellan ett molndatalager och ett datalager i ett privat nätverk. Det kan också skicka transformeringsaktiviteter mot beräkningsresurser i ett lokalt nätverk eller ett virtuellt Azure-nätverk. Installationen av en självvärderad integrationskörning kräver en lokal dator eller en virtuell dator i ett privat nätverk.  

I den här artikeln beskrivs hur du kan skapa och konfigurera en självvärd iR.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Ställa in en självvärd integrationskörning

Om du vill skapa och konfigurera en självvärderad integrationskörning använder du följande procedurer.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Skapa en självvärd IR via Azure PowerShell

1. Du kan använda Azure PowerShell för den här uppgiften. Här är ett exempel:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Hämta](https://www.microsoft.com/download/details.aspx?id=39717) och installera den självvärderade integrationskörningen på en lokal dator.

3. Hämta autentiseringsnyckeln och registrera den självvärderade integrationskörningen med nyckeln. Här är ett PowerShell-exempel:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Skapa en självvärd IR via Azure Data Factory UI

Följ följande steg för att skapa en självvärd IR med Azure Data Factory UI.

1. På sidan **Låt oss komma igång i** Azure Data Factory-användargränssnittet väljer du fliken **Författare** i fönstret längst till vänster.

   ![Knappen Författarsida på startsidan](media/doc-common-process/get-started-page-author-button.png)

1. Välj **Anslutningar** längst ned i fönstret längst till vänster och välj **Integrationskörningar** i fönstret **Anslutningar.** Välj **+Nytt**.

   ![Skapa Integration Runtime](media/create-self-hosted-integration-runtime/new-integration-runtime.png)

1. På **inställningssidan för integreringskörning** väljer du **Azure, Självvärd och**väljer sedan **Fortsätt**. 

1. På följande sida väljer du **Självvärd** för att skapa en självvärd ir och väljer sedan **Fortsätt**.
   ![Skapa en självbehärskade IR](media/create-self-hosted-integration-runtime/new-selfhosted-ir.png)

1. Ange ett namn på din IR och välj **Skapa**.

1. På **inställningssidan för integreringskörning** väljer du länken under **Alternativ 1** för att öppna expressinställningarna på datorn. Eller följ stegen under **Alternativ 2** för att ställa in manuellt. Följande instruktioner baseras på manuell installation:

   ![Installation av Integration Runtime](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Kopiera och klistra in autentiseringsnyckeln. Välj **Hämta och installera integrationskörningstid**.

    1. Ladda ned Integration Runtime med egen värd på en lokal Windows-dator. Kör installationsprogrammet.

    1. På sidan **Registrera integrationskörning (självvärd)** klistrar du in nyckeln som du sparade tidigare och väljer **Registrera**.
    
       ![Registrera Integration Runtime](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. På sidan **Ny integrationskörningsnod (självvärd)** väljer du **Slutför**.

1. När den självvärderade integrationskörningen har registrerats visas följande fönster:

    ![Lyckad registrering](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Konfigurera en självvärd ir på en virtuell Azure-dator via en Azure Resource Manager-mall

Du kan automatisera självvärderade IR-inställningar på en virtuell Azure-dator med hjälp av [IR-mallen Skapa självvärden](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Mallen är ett enkelt sätt att ha en fullt fungerande självvärd IR i ett virtuellt Azure-nätverk. IR har funktioner med hög tillgänglighet och skalbarhet, så länge du ställer in nodantalet till 2 eller högre.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Konfigurera en befintlig självvärd IR via lokala PowerShell

Du kan använda en kommandorad för att ställa in eller hantera en befintlig självvärdbaserad IR. Den här användningen kan särskilt hjälpa till att automatisera installation och registrering av självvärderade IR-noder.

Dmgcmd.exe ingår i den självvärdbaserade installationsprogrammet. Den finns vanligtvis i mappen C:\Program\Microsoft Integration Runtime\3.0\Shared\. Det här programmet stöder olika parametrar och kan anropas via en kommandorad med batchskript för automatisering.

Använd programmet på följande sätt:

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

Här är information om programmets parametrar och egenskaper: 

| Egenskap                                                    | Beskrivning                                                  | Krävs |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **RegistreraNewNode** "`<AuthenticationKey>`"                     | Registrera en självvärdbaserad integrationskörningsnod med den angivna autentiseringsnyckeln. | Inga       |
| **RegistreraNewNode** "`<AuthenticationKey>`" "`<NodeName>`"      | Registrera en självvärdbaserad integrationskörningsnod med den angivna autentiseringsnyckeln och nodnamnet. | Inga       |
| **AktiveraRemoteAccess** "`<port>`" ["`<thumbprint>`"]            | Aktivera fjärråtkomst på den aktuella noden för att konfigurera ett kluster med hög tillgänglighet. Eller aktivera inställningsautentiseringsuppgifter direkt mot den självvärdbaserade IR utan att gå igenom Azure Data Factory. Du gör det senare genom att använda cmdleten **New-AzDataFactoryV2LinkedServiceEncryptedCredential** från en fjärrdator i samma nätverk. | Inga       |
| **AktiveraRemoteAccessInContainer** "`<port>`" ["`<thumbprint>`"] | Aktivera fjärråtkomst till den aktuella noden när noden körs i en behållare. | Inga       |
| **InaktiveraRemoteAccess**                                         | Inaktivera fjärråtkomst till den aktuella noden. Fjärråtkomst behövs för installation av fleranoder. **Den Nya AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell cmdlet fungerar fortfarande även när fjärråtkomst är inaktiverad. Detta är sant så länge cmdlet körs på samma dator som den självvärderade IR-noden. | Inga       |
| **Nyckel** "`<AuthenticationKey>`"                                 | Skriv över eller uppdatera den tidigare autentiseringsnyckeln. Var försiktig med denna åtgärd. Din tidigare självvärderade IR-nod kan kopplas från om nyckeln är för en ny integrationskörning. | Inga       |
| **GenereraBackupFile** "`<filePath>`" "`<password>`"            | Generera en säkerhetskopia för den aktuella noden. Säkerhetskopian innehåller nodnyckeln och autentiseringsuppgifterna för datalagring. | Inga       |
| **ImportBackupFile** "`<filePath>`" "`<password>`"              | Återställ noden från en säkerhetskopia.                          | Inga       |
| **Starta**                                                     | Starta om den självvärdbaserade värdtjänsten för integrering av programkörning.   | Inga       |
| **Start**                                                       | Starta den självvärdbaserade värdtjänsten för integrering av körning.     | Inga       |
| **Stopp**                                                        | Stoppa den självvärdbaserade värdtjänsten för integrationskörning.        | Inga       |
| **StartUpgradeService**                                         | Starta uppgraderingstjänsten för integrering med självvärdbaserad integrering.       | Inga       |
| **StopUpgradeService**                                          | Stoppa uppgraderingstjänsten för integrering med självvärdbaserad integrering.        | Inga       |
| **TurnOnAutoUpdate**                                            | Aktivera automatisk uppdatering av självvärdbaserad integrering.        | Inga       |
| **TurnOffAutoUpdate**                                           | Inaktivera automatisk uppdatering av självvärdbaserad integreringskörning.       | Inga       |
| **SwitchServiceKonto** "`<domain\user>`" ["`<password>`"]           | Ange att DIAHostService ska köras som ett nytt konto. Använd det tomma lösenordet "" för systemkonton och virtuella konton. | Inga       |


## <a name="command-flow-and-data-flow"></a>Kommandoflöde och dataflöde

När du flyttar data mellan lokala och molnet använder aktiviteten en självvärderad integrationskörning för att överföra data mellan en lokal datakälla och molnet.

Här är en sammanfattning på hög nivå av dataflödesstegen för kopiering med en självvärd IR:

![Översikt på hög nivå av dataflödet](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. En datautvecklare skapar en självvärd integreringskörning i en Azure-datafabrik med hjälp av en PowerShell-cmdlet. Azure-portalen stöder för närvarande inte den här funktionen.
1. Datautvecklaren skapar en länkad tjänst för ett lokalt datalager. Utvecklaren gör det genom att ange den självvärderade integrationskörningsinstansen som tjänsten ska använda för att ansluta till datalager.
1. Den självvärderade integrationsnoden krypterar autentiseringsuppgifterna med hjälp av DPAPI (Windows Data Protection Application Programming Interface) och sparar autentiseringsuppgifterna lokalt. Om flera noder är inställda för hög tillgänglighet synkroniseras autentiseringsuppgifterna ytterligare mellan andra noder. Varje nod krypterar autentiseringsuppgifterna med hjälp av DPAPI och lagrar dem lokalt. Synkronisering av autentiseringsuppgifter är transparent för datautvecklaren och hanteras av den självvärdbaserade IR:et.
1. Azure Data Factory kommunicerar med den självvärderade integrationskörningen för att schemalägga och hantera jobb. Kommunikation sker via en kontrollkanal som använder en delad [Azure Service Bus Relay-anslutning.](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) När ett aktivitetsjobb måste köras köar Data Factory begäran tillsammans med all information om autentiseringsuppgifter. Det gör det om autentiseringsuppgifterna inte redan lagras på den självvärderade integrationskörningen. Den självvärderade integrationskörningen startar jobbet efter att kön har avläts.
1. Den självvärderade integrationskörningen kopierar data mellan ett lokalt arkiv och molnlagring. Kopians riktning beror på hur kopieringsaktiviteten konfigureras i datapipelinen. I det här steget kommunicerar den självvärderade integrationskörningen direkt med molnbaserade lagringstjänster som Azure Blob-lagring via en säker HTTPS-kanal.

## <a name="considerations-for-using-a-self-hosted-ir"></a>Överväganden för att använda en självvärd IR

- Du kan använda en enda självvärderad integrationskörning för flera lokala datakällor. Du kan också dela den med en annan datafabrik i samma Azure Active Directory -klientorganisation (Azure AD). Mer information finns i [Dela en självvärd för integrationskörning](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory).
- Du kan bara installera en instans av en självvärdbaserad integreringskörning på en enda dator. Om du har två datafabriker som behöver komma åt lokala datakällor, antingen använda den [självvärderade IR-delningsfunktionen](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) för att dela den självvärdbaserade IR:et eller installera den självvärdbaserade IR:et på två lokala datorer, en för varje datafabrik.  
- Den självvärderade integrationskörningen behöver inte finnas på samma dator som datakällan. Men om du har den självvärderade integrationskörningen nära datakällan minskar tiden för den självvärderade integrationskörningen att ansluta till datakällan. Vi rekommenderar att du installerar den självvärderade integrationskörningen på en dator som skiljer sig från den som är värd för den lokala datakällan. När den självvärderade integrationskörningen och datakällan finns på olika datorer konkurrerar inte den självvärderade integrationskörningen med datakällan för resurser.
- Du kan ha flera självvärderade integrationskörningar på olika datorer som ansluter till samma lokala datakälla. Om du till exempel har två självvärderade integrationskörningar som betjänar två datafabriker kan samma lokala datakälla registreras med båda datafabrikerna.
- Om du redan har en gateway installerad på datorn för att kunna visa ett Power BI-scenario installerar du en separat självvärderad integrationskörning för Data Factory på en annan dator.
- Använd en självvärd för integrationskörning för att stödja dataintegrering i ett virtuellt Azure-nätverk.
- Behandla din datakälla som en lokal datakälla som finns bakom en brandvägg, även när du använder Azure ExpressRoute. Använd den självvärderade integrationskörningen för att ansluta tjänsten till datakällan.
- Använd den självvärderade integrationskörningen även om datalagret finns i molnet på en virtuell Azure Infrastructure as a Service -dator (IaaS).
- Aktiviteter kan misslyckas i en självvärd integreringskörning som du har installerat på en Windows-server för vilken FIPS-kompatibel kryptering är aktiverad. Du kan lösa problemet genom att inaktivera FIPS-kompatibel kryptering på servern. Om du vill inaktivera FIPS-kompatibel kryptering ändrar du följande registerundernyckels värde från `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`1 (aktiverat) till 0 (inaktiverat): .

## <a name="prerequisites"></a>Krav

- De versioner av Windows som stöds är:
  + Windows 7 Service Pack 1
  + Windows 8.1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   Installation av den självvärderade integrationskörningen på en domänkontrollant stöds inte.
- .NET Framework 4.6.1 eller senare krävs. Om du installerar den självvärderade integrationskörningen på en Windows 7-dator installerar du .NET Framework 4.6.1 eller senare. Mer information finns i [.NET Framework System Requirements.](/dotnet/framework/get-started/system-requirements)
- Den rekommenderade minsta konfigurationen för den självvärderade integrationskörningsdatorn är en 2 GHz-processor med 4 kärnor, 8 GB RAM och 80 GB tillgängligt hårddiskutrymme.
- Om värddatorn övervintrar svarar inte den självvärdbaserade integrationskörningen på databegäranden. Konfigurera ett lämpligt energischema på datorn innan du installerar den självvärderade integrationskörningen. Om datorn är konfigurerad för viloläge uppmanas installationsprogrammet för självvärderade integreringskörningar med ett meddelande.
- Du måste vara administratör på datorn för att kunna installera och konfigurera den självvärderade integrationskörningen.
- Kopieringsaktivitetskörningar sker med en viss frekvens. Processor- och RAM-användning på maskinen följer samma mönster med topp- och inaktiva tider. Resursanvändningen beror också mycket på mängden data som flyttas. När flera kopieringsjobb pågår visas resursanvändningen under rusningstid.
- Aktiviteter kan misslyckas under extrahering av data i parkett-, ORC- eller Avro-format. Mer information om Parkett finns [i Parkettformat i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime). Skapande av filer körs på den självvärderade integrationsdatorn. För att fungera som förväntat kräver filskapande följande förutsättningar:
    - [Visual C++ 2010 Redistributable](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Paket (x64)
    - Java Runtime (JRE) version 8 från en JRE-leverantör som [Anta OpenJDK](https://adoptopenjdk.net/). Se till `JAVA_HOME` att miljövariabeln är inställd.

## <a name="installation-best-practices"></a>Metodtips för installation

Du kan installera den självvärderade integrationskörningen genom att hämta ett installationspaket för hanterad identitet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Se artikeln [Flytta data mellan lokala och moln](tutorial-hybrid-copy-powershell.md) för steg-för-steg-instruktioner.

- Konfigurera ett energischema på värddatorn för den självvärdbaserade integrationskörningen så att datorn inte försvinerar. Om värddatorn försätts i viloläge kopplas den självvärderade integrationskörningen från.
- Säkerhetskopiera regelbundet autentiseringsuppgifterna som är associerade med den självvärderade integrationskörningen.
- Om du vill automatisera ir-installationsåtgärder med självvärd läser du [Konfigurera en befintlig självvärd IR via PowerShell](#setting-up-a-self-hosted-integration-runtime).  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Installera och registrera en självvärd IR från Microsoft Download Center

1. Gå till [nedladdningssidan för Microsoft-integreringskörning](https://www.microsoft.com/download/details.aspx?id=39717).
1. Välj **Hämta**, välj 64-bitarsversionen och välj **Nästa**. 32-bitarsversionen stöds inte.
1. Kör filen Hanterad identitet direkt eller spara den på hårddisken och kör den.
1. Välj ett språk i **välkomstfönstret** och välj **Nästa**.
1. Acceptera licensvillkoren för programvara från Microsoft och välj **Nästa**.
1. Välj **mapp** för att installera den självvärderade integrationskörningen och välj **Nästa**.
1. På sidan **Klar att installera** väljer du **Installera**.
1. Välj **Slutför** för att slutföra installationen.
1. Hämta autentiseringsnyckeln med PowerShell. Här är ett PowerShell-exempel för att hämta autentiseringsnyckeln:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. Gör följande i fönstret **Registrera integrationskörning (självvärd)** i Microsoft Integration Runtime Configuration Manager som körs på datorn:

    1. Klistra in autentiseringsnyckeln i textområdet.

    1. Du kan också välja **Visa autentiseringsnyckel** för att visa nyckeltexten.

    1. Välj **Registrera**.

## <a name="high-availability-and-scalability"></a>Hög tillgänglighet och skalbarhet

Du kan associera en självvärdbaserad integrationskörning med flera lokala datorer eller virtuella datorer i Azure. Dessa maskiner kallas noder. Du kan ha upp till fyra noder som är associerade med en självvärderad integrationskörning. Fördelarna med att ha flera noder på lokala datorer som har en gateway installerad för en logisk gateway är:

* Högre tillgänglighet för den självvärderade integrationskörningen så att den inte längre är den enda felpunkten i din stordatalösning eller molndataintegrering med Data Factory. Den här tillgängligheten säkerställer kontinuitet när du använder upp till fyra noder.
* Förbättrad prestanda och dataflöde under dataförflyttning mellan lokala datalager och molndatalager. Få mer information om [prestandajämnningar](copy-activity-performance.md).

Du kan associera flera noder genom att installera programmet för integrering av självbaserad integrering från [Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Registrera den sedan med någon av de autentiseringsnycklar som erhölls från cmdleten **New-AzDataFactoryV2IntegrationRuntimeKey,** enligt beskrivningen i [självstudien](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Du behöver inte skapa en ny självvärderad integrationskörning för att associera varje nod. Du kan installera den självvärderade integrationskörningen på en annan dator och registrera den med samma autentiseringsnyckel.

> [!NOTE]
> Innan du lägger till en annan nod för hög tillgänglighet och skalbarhet, se till att alternativet **Fjärråtkomst till intranät** är aktiverat på den första noden. Det gör du genom att välja > **Fjärråtkomst till fjärråtkomst till intranät**för Microsoft Integration **Runtime Configuration Manager.** > **Settings**

### <a name="scale-considerations"></a>Skala överväganden

#### <a name="scale-out"></a>Skala ut

När processoranvändningen är hög och det tillgängliga minnet är lågt på den självvärdbaserade IR:n lägger du till en ny nod för att skala ut belastningen mellan datorer. Om aktiviteter misslyckas eftersom de time out eller den självvärderade IR-noden är offline, hjälper det om du lägger till en nod i gatewayen.

#### <a name="scale-up"></a>Skala upp

När processorn och det tillgängliga RAM-minnet inte utnyttjas väl, men körningen av samtidiga jobb når en nods gränser, skala upp genom att öka antalet samtidiga jobb som en nod kan köra. Du kanske också vill skala upp när aktiviteter time out eftersom den självvärderade IR är överbelastad. Som visas i följande bild kan du öka den maximala kapaciteten för en nod:  

![Öka antalet samtidiga jobb som kan köras på en nod](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL-certifikatkrav

Här är kraven för TLS/SSL-certifikatet som du använder för att skydda kommunikationen mellan integrationskörningsnoder:

- Certifikatet måste vara ett offentligt betrott X509 v3-certifikat. Vi rekommenderar att du använder certifikat som utfärdas av en certifikatutfärdare för offentlig partner.
- Varje nod för integrationskörning måste lita på certifikatet.
- Vi rekommenderar inte SAN-certifikat (Subject Alternative Name) eftersom endast det senaste SAN-objektet används. Alla andra SAN-objekt ignoreras. Om du till exempel har ett SAN-certifikat vars SAN-nätverk **är node1.domain.contoso.com** och **node2.domain.contoso.com**kan du endast använda det här certifikatet på en dator vars fullständigt kvalificerade domännamn (FQDN) är **node2.domain.contoso.com**.
- Certifikatet kan använda valfri nyckelstorlek som stöds av Windows Server 2012 R2 för TLS/SSL-certifikat.
- Certifikat som använder CNG-nycklar stöds inte.  

> [!NOTE]
> Detta certifikat används:
>
> - Så här krypterar du portar på en IR-nod med självvärd.
> - För nod-till-nodkommunikation för tillståndssynkronisering, vilket inkluderar autentiseringsuppgifter synkronisering av länkade tjänster mellan noder.
> - När en PowerShell-cmdlet används för inställningar för autentiseringsuppgifter för länkade tjänster från ett lokalt nätverk.
>
> Vi föreslår att du använder det här certifikatet om din privata nätverksmiljö inte är säker eller om du vill skydda kommunikationen mellan noder i ditt privata nätverk.
>
> Dataförflyttning under överföring från en självvärd IR till andra datalager sker alltid inom en krypterad kanal, oavsett om certifikatet är inställt eller inte.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Skapa en delad självvärd för integrationskörning i Azure Data Factory

Du kan återanvända en befintlig självvärdbaserad integrationskörningsinfrastruktur som du redan har konfigurerat i en datafabrik. Med den här återanvändningen kan du skapa en länkad självvärdbaserad integrationskörning i en annan datafabrik genom att referera till en befintlig delad självvärd iR.

Om du vill se en introduktion och demonstration av den här funktionen kan du titta på följande 12-minutersvideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologi

- **Delad IR**: En ursprunglig självvärd ir som körs på en fysisk infrastruktur.  
- **Länkad IR:** En IR som refererar till en annan delad IR. Den länkade IR är en logisk IR och använder infrastrukturen för en annan delad självvärd IR.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Metoder för att dela en självvärd integrationskörning

Information om hur du delar en självvärd integreringskörning med flera datafabriker finns i [Skapa en delad självvärderad integrationskörning](create-shared-self-hosted-integration-runtime-powershell.md) för mer information.

### <a name="monitoring"></a>Övervakning

#### <a name="shared-ir"></a>Delad IR

![Val för att hitta en delad integrationskörning](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Övervaka en delad integrationskörning](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>Länkad IR

![Val för att hitta en länkad integrationskörning](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Övervaka en länkad integrationskörning](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Kända begränsningar av egen värd IR-delning

* Datafabriken där en länkad IR skapas måste ha en [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Som standard har datafabrikerna som skapats i Azure-portalen eller PowerShell-cmdlets en implicit skapad hanterad identitet. Men när en datafabrik skapas via en Azure Resource Manager-mall eller SDK måste du uttryckligen ange egenskapen **Identity.** Den här inställningen säkerställer att Resource Manager skapar en datafabrik som innehåller en hanterad identitet.

* Data Factory .NET SDK som stöder den här funktionen måste vara version 1.1.0 eller senare.

* För att bevilja behörighet behöver du rollen Ägare eller rollen ärvd ägare i datafabriken där den delade IR finns.

* Delningsfunktionen fungerar endast för datafabriker i samma Azure AD-klientorganisation.

* För Azure [AD-gästanvändare](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) [fungerar inte](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits)sökfunktionen i användargränssnittet, som listar alla datafabriker med hjälp av ett sökord. Men så länge gästanvändaren är ägare till datafabriken kan du dela IR utan sökfunktionen. För den hanterade identiteten för den datafabrik som behöver dela IR anger du den hanterade identiteten i rutan **Tilldela behörighet** och väljer **Lägg till** i datafabrikens användargränssnitt.

  > [!NOTE]
  > Den här funktionen är endast tillgänglig i Data Factory V2.

## <a name="notification-area-icons-and-notifications"></a>Ikoner och meddelanden om meddelandeområde

Om du flyttar markören över ikonen eller meddelandet i meddelandefältet kan du se information om tillståndet för den självvärderade integrationskörningen.

![Anmälningar i anmälningsområdet](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>Portar och brandväggar

Det finns två brandväggar att tänka på:

- *Företagsbrandväggen* som körs på organisationens centrala router
- *Windows-brandväggen* som är konfigurerad som en demon på den lokala datorn där den självvärderade integrationskörningen är installerad

![Brandväggarna](media/create-self-hosted-integration-runtime/firewall.png)

På företagets brandväggsnivå måste du konfigurera följande domäner och utgående portar:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

På Windows-brandväggsnivå eller datornivå är dessa utgående portar normalt aktiverade. Om de inte är det kan du konfigurera domäner och portar på en självvärderad integrationskörningsdator.

> [!NOTE]
> Baserat på din källa och sänkor kan du behöva tillåta ytterligare domäner och utgående portar i företagets brandvägg eller Windows-brandväggen.
>
> För vissa molndatabaser, till exempel Azure SQL Database och Azure Data Lake, kan du behöva tillåta IP-adresser för självvärderade integrationskörningsdatorer i brandväggskonfigurationen.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopiera data från en källa till en diskho

Se till att du aktiverar brandväggsregler på rätt sätt i företagets brandvägg, Windows-brandväggen för den självvärderade integrationsdatorn och själva datalagret. Om du aktiverar dessa regler kan den självvärderade integrationen anslutas till både käll- och diskho. Aktivera regler för varje datalager som ingår i kopieringen.

Om du till exempel vill kopiera från ett lokalt datalager till en SQL Database-diskho eller en Azure SQL Data Warehouse-mottagare gör du följande:

1. Tillåt utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företagsbrandväggen.
1. Konfigurera brandväggsinställningarna för SQL-databasen för att lägga till IP-adressen för den självvärderade integrationskörningsdatorn i listan över tillåtna IP-adresser.

> [!NOTE]
> Om brandväggen inte tillåter utgående port 1433 kan den självvärderade integrationskörningen inte komma åt SQL-databasen direkt. I det här fallet kan du använda en [stegvis kopia](copy-activity-performance.md) till SQL Database och SQL Data Warehouse. I det här fallet behöver du bara HTTPS (port 443) för dataflyttningen.

## <a name="proxy-server-considerations"></a>Överväganden för proxyserver

Om företagets nätverksmiljö använder en proxyserver för att ansluta till Internet konfigurerar du den självvärderade integrationskörningen så att lämpliga proxyinställningar används. Du kan ställa in proxyn under den inledande registreringsfasen.

![Ange proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

När den självvärderade integrationskörningen är konfigurerad används proxyservern för att ansluta till molntjänstens källa och mål (som använder HTTP- eller HTTPS-protokollet). Det är därför du väljer **Ändra länk** under den första installationen.

![Ställ in proxyn](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Det finns tre konfigurationsalternativ:

- **Använd inte proxy:** Den självvärderade integrationskörningen använder inte uttryckligen någon proxy för att ansluta till molntjänster.
- **Använd systemproxy:** Den självvärderade integrationskörningen använder proxyinställningen som är konfigurerad i diahost.exe.config och diawp.exe.config. Om dessa filer inte anger någon proxykonfiguration ansluter den självvärderade integrationskörningen till molntjänsten direkt utan att gå igenom en proxy.
- **Använd anpassad proxy:** Konfigurera HTTP-proxyinställningen som ska användas för den självvärderade integrationskörningen, i stället för att använda konfigurationer i diahost.exe.config och diawp.exe.config. **Adress-** och **portvärden** krävs. **Värden för användarnamn** och **lösenord** är valfria, beroende på proxyns autentiseringsinställning. Alla inställningar krypteras med Windows DPAPI på den självvärderade integrationskörningen och lagras lokalt på datorn.

Värdtjänsten för integrationskörning startar om automatiskt när du har sparat de uppdaterade proxyinställningarna.

När du har registrerat den självvärderade integrationskörningen använder du Microsoft Integration Runtime Configuration Manager om du vill visa eller uppdatera proxyinställningar.

1. Öppna **Konfigurationshanteraren för Microsoft-integreringskörning**.
1. Välj fliken **Inställningar**.
1. Under **HTTP-proxy**väljer du länken **Ändra** för att öppna dialogrutan **Ange HTTP-proxy.**
1. Välj **Nästa**. Du ser sedan en varning som ber om din tillåtelse att spara proxyinställningen och starta om värdtjänsten för integrationskörning.

Du kan använda verktyget konfigurationshanteraren för att visa och uppdatera HTTP-proxyn.

![Visa och uppdatera proxyn](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Om du konfigurerar en proxyserver med NTLM-autentisering körs värdtjänsten för integreringskörning under domänkontot. Om du senare ändrar lösenordet för domänkontot bör du komma ihåg att uppdatera konfigurationsinställningarna för tjänsten och starta om tjänsten. På grund av detta krav föreslår vi att du kommer åt proxyservern med hjälp av ett dedikerat domänkonto som inte kräver att du uppdaterar lösenordet ofta.

### <a name="configure-proxy-server-settings"></a>Konfigurera proxyserverinställningar

Om du väljer alternativet **Använd systemproxy** för HTTP-proxyn använder den självvärderade integrationskörningen proxyinställningarna i diahost.exe.config och diawp.exe.config. När dessa filer anger ingen proxy ansluter den självvärderade integrationskörningen till molntjänsten direkt utan att gå igenom en proxy. Följande procedur innehåller instruktioner för uppdatering av filen diahost.exe.config:

1. I Utforskaren gör du en säker kopia av C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config som en säkerhetskopia av originalfilen.
1. Öppna Anteckningar som körs som administratör.
1. Öppna textfilen C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config i Anteckningar.
1. Leta reda på **standardtaggen system.net** som visas i följande kod:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Du kan sedan lägga till information om proxyservern enligt följande exempel:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Proxytaggen tillåter ytterligare egenskaper för `scriptLocation`att ange nödvändiga inställningar som . Se [ \<\> proxyelement (Nätverksinställningar)](https://msdn.microsoft.com/library/sa91de1e.aspx) för syntax.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. Spara konfigurationsfilen på den ursprungliga platsen. Starta sedan om den självvärdbaserade värdtjänsten för integrationskörning, som hämtar ändringarna.

   Om du vill starta om tjänsten använder du tjänstappleten från Kontrollpanelen. Eller från Integration Runtime Configuration Manager väljer du knappen **Stoppa tjänst** och väljer sedan **Starta tjänst**.

   Om tjänsten inte startar har du förmodligen lagt till felaktig XML-taggsyntax i programkonfigurationsfilen som du redigerade.

> [!IMPORTANT]
> Glöm inte att uppdatera både diahost.exe.config och diawp.exe.config.

Du måste också se till att Microsoft Azure finns i företagets tillåtlista. Du kan hämta listan över giltiga Azure IP-adresser från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Möjliga symptom på problem relaterade till brandväggen och proxyservern

Om du ser felmeddelanden som följande är den troliga orsaken felaktig konfiguration av brandväggen eller proxyservern. En sådan konfiguration förhindrar att den självvärderade integrationskörningen ansluter till Data Factory för att autentisera sig själv. Om du vill vara säkra på att brandväggen och proxyservern är korrekt konfigurerade läser du föregående avsnitt.

* NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k fÃ¶rsÃ¶k att registrera den självvärdsbaserade integrationskörningen visas fÃ¶¶ande: "Det gick inte att registrera den här noden Integration Runtime! Bekräfta att autentiseringsnyckeln är giltig och att värdtjänsten för integrationstjänsten körs på den här datorn."
* När du öppnar Configuration Manager för Integration Runtime visas statusen **Frånkopplad** eller **Ansluta**. När du visar Windows-händelseloggar visas felmeddelanden som den här under **Loggbokensprogram** > **och tjänster** > loggar**Microsoft Integration Runtime:**

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>Aktivera fjärråtkomst från ett intranät

Om du använder PowerShell för att kryptera autentiseringsuppgifter från en annan nätverksdator än där du installerade den självvärderade integrationskörningen kan du aktivera alternativet **Fjärråtkomst från intranät.** Om du kör PowerShell för att kryptera autentiseringsuppgifter på datorn där du installerade den självvärderade integrationskörningen kan du inte aktivera **Fjärråtkomst från intranät**.

Aktivera **Fjärråtkomst från intranät** innan du lägger till en annan nod för hög tillgänglighet och skalbarhet.  

När du kör den självvärderade integrationskörningsinstallationsversionen 3.3 eller senare inaktiverar som standard självvärderade integrationskörningsinstallationsprogrammet **Fjärråtkomst från intranät** på den självvärderade integrationskörningsdatorn.

När du använder en brandvägg från en eller flera partner kan du öppna port 8060 eller den användarkonfigurerade porten manuellt. Om du har ett brandväggsproblem när du konfigurerar den självvärderade integrationskörningen använder du följande kommando för att installera den självvärderade integrationskörningen utan att konfigurera brandväggen:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Om du väljer att inte öppna port 8060 på den självvärderade integrationsdatorn använder du andra mekanismer än inställningsautentiseringsprogrammet för att konfigurera autentiseringsuppgifter för datalagring. Du kan till exempel använda **den nya AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell-cmdleten.

## <a name="next-steps"></a>Nästa steg

Stegvisa instruktioner finns i [Självstudiekurs: Kopiera lokala data till molnet](tutorial-hybrid-copy-powershell.md).
