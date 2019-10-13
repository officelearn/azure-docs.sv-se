---
title: Skapa en integration runtime med egen värd i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar en integration runtime med egen värd i Azure Data Factory, vilket gör det möjligt för data fabriker att komma åt data lager i ett privat nätverk.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/18/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 8ea6a365b0c7bc6c254c1313445bb54231e161ae
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285639"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Skapa och konfigurera en integration runtime med egen värd
Integrerings körningen (IR) är den beräknings infrastruktur som Azure Data Factory använder för att tillhandahålla funktioner för data integrering i olika nätverks miljöer. Mer information om IR finns i [Översikt över integration runtime](concepts-integration-runtime.md).

En lokal integration runtime kan köra kopierings aktiviteter mellan ett moln data lager och ett data lager i ett privat nätverk, och det kan skicka Transform-aktiviteter mot beräknings resurser i ett lokalt nätverk eller ett virtuellt Azure-nätverk. Installationen av en lokal integration runtime måste finnas på en lokal dator eller en virtuell dator i ett privat nätverk.  

Det här dokumentet beskriver hur du kan skapa och konfigurera en lokal IR-anslutning.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>Steg på hög nivå för att installera en IR med egen värd
1. Skapa Integration Runtime med egen värd. Du kan använda Azure Data Factory användar gränssnitt för den här aktiviteten. Här är ett PowerShell-exempel:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Ladda ned](https://www.microsoft.com/download/details.aspx?id=39717) och installera integration runtime med egen värd på en lokal dator.

3. Hämta autentiseringsnyckel och registrera den egna värdbaserade integrerings körningen med nyckeln. Här är ett PowerShell-exempel:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template"></a>Konfigurera ett IR med egen värd på en virtuell Azure-dator med hjälp av en Azure Resource Manager mall 
Du kan automatisera IR-installationen med egen värd på en virtuell Azure-dator med hjälp av [den här Azure Resource Manager mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Den här mallen är ett enkelt sätt att ha en helt fungerande IR-överföring i ett virtuellt Azure-nätverk med funktioner för hög tillgänglighet och skalbarhet (så länge du ställer in antalet noder på 2 eller högre).

## <a name="command-flow-and-data-flow"></a>Kommando flöde och data flöde
När du flyttar data mellan lokala platser och molnet använder aktiviteten en lokal integrerings körning för att överföra data från en lokal data källa till molnet och vice versa.

Här är ett högnivå data flöde för en sammanfattning av stegen för att kopiera med en lokal IR-överföring:

![Översikt på hög nivå](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Data utvecklaren skapar en integration runtime med egen värd i en Azure-datafabrik med hjälp av en PowerShell-cmdlet. Azure Portal har för närvarande inte stöd för den här funktionen.
2. Data utvecklaren skapar en länkad tjänst för ett lokalt data lager genom att ange den lokala integration runtime-instansen som den ska använda för att ansluta till data lager.
3. Noden för integration runtime med egen värd krypterar autentiseringsuppgifterna med hjälp av Windows Data Protection Application Programming Interface (DPAPI) och sparar autentiseringsuppgifterna lokalt. Om flera noder är inställda för hög tillgänglighet synkroniseras autentiseringsuppgifterna ytterligare mellan andra noder. Varje nod krypterar autentiseringsuppgifterna med DPAPI och lagrar dem lokalt. Synkronisering av autentiseringsuppgifter är transparent för data utvecklaren och hanteras av IR med egen värd.    
4. Den Data Factory tjänsten kommunicerar med den egen värdbaserade integrerings körningen för schemaläggning och hantering av jobb via en *kontroll kanal* som använder ett delat [Azure Service Bus relä](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay). När ett aktivitets jobb måste köras Data Factory köar begäran tillsammans med all autentiseringsinformation (om autentiseringsuppgifter inte redan lagras på den lokala integrerings körningen). Den egen värdbaserade integrerings körningen avslutar jobbet när kön har avsökts.
5. Den lokala integrerings körningen kopierar data från en lokal lagrings plats till en moln lagrings plats, eller vice versa, beroende på hur kopierings aktiviteten har kon figurer ATS i data pipelinen. I det här steget kommunicerar den självbetjänings körnings miljön direkt med molnbaserade lagrings tjänster, till exempel Azure Blob Storage över en säker kanal (HTTPS).

## <a name="considerations-for-using-a-self-hosted-ir"></a>Att tänka på vid användning av en egen värd IR

- En enda integration runtime med egen värd kan användas för flera lokala data källor. En enda integration runtime med egen värd kan delas med en annan data fabrik inom samma Azure Active Directory klient organisation. Mer information finns i [dela en integration runtime med egen värd](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories).
- Du kan bara ha en instans av en lokal integration runtime som är installerad på en enskild dator. Om du har två data fabriker som behöver åtkomst till lokala data källor kan du antingen använda funktionen för [IR-delning med egen värd](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories) för att dela den lokala integrerings körningen eller installera den lokala integrerings körningen på två lokala datorer, en för varje data fabrik.  
- Integration runtime med egen värd måste inte finnas på samma dator som data källan. Men med den egen värdbaserade integrerings körningen närmare data källan minskas tiden för att integration runtime med egen värd ska ansluta till data källan. Vi rekommenderar att du installerar den lokala integrerings körningen på en dator som skiljer sig från den som är värd för en lokal data källa. När den lokala integrerings körningen och data källan finns på olika datorer, konkurrerar inte den lokala integration Runtime om resurser med data källan.
- Du kan ha flera egen värdbaserade integrerings körningar på olika datorer som ansluter till samma lokala data källa. Du kan till exempel ha två egen värdbaserade integrerings körningar som har två data fabriker, men samma lokala data källa har registrerats med båda data fabrikerna.
- Om du redan har en gateway installerad på datorn för att hantera ett Power BI scenario, installerar du en separat integration runtime för egen värd för Azure Data Factory på en annan dator.
- Integration runtime med egen värd måste användas för att stödja data integrering i ett virtuellt Azure-nätverk.
- Behandla din data källa som en lokal data källa som ligger bakom en brand vägg, även när du använder Azure-ExpressRoute. Använd integration runtime med egen värd för att upprätta anslutningar mellan tjänsten och data källan.
- Du måste använda integration runtime med egen värd även om data lagret finns i molnet på en virtuell Azure IaaS-dator.
- Aktiviteter kan Miss Miss kan utföras i en integration runtime med egen värd som är installerad på en Windows-Server där FIPS-kompatibel kryptering är aktive rad. Undvik det här problemet genom att inaktivera FIPS-kompatibel kryptering på servern. Om du vill inaktivera FIPS-kompatibel kryptering ändrar du följande register värde från 1 (aktiverat) till 0 (inaktiverat): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Krav

- De operativ system versioner som stöds är Windows 7 Service Pack 1, Windows 8,1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 och Windows Server 2019. Installation av integration runtime med egen värd på en domänkontrollant stöds inte.
- .NET Framework 4.6.1 eller senare krävs. Om du installerar integration runtime med egen värd på en dator med Windows 7 installerar du .NET Framework 4.6.1 eller senare. Mer information finns i [.NET Framework system krav](/dotnet/framework/get-started/system-requirements) .
- Den rekommenderade konfigurationen för den egen värdbaserade integration runtime-datorn är minst 2 GHz, fyra kärnor, 8 GB RAM-minne och en 80 GB-disk.
- Om värd datorn försätts i vilo läge svarar inte den egna värdbaserade integrerings körningen med data begär Anden. Konfigurera ett lämpligt energi schema på datorn innan du installerar integration runtime med egen värd. Om datorn är konfigurerad att försättas i vilo läge visas ett meddelande om att installationen av den egna värdbaserade integrerings körningen sker.
- Du måste vara administratör på datorn för att installera och konfigurera integration runtime med egen värd.
- Kopierings aktiviteten körs på en angiven frekvens. Resursanvändningen (CPU, minne) på datorn följer samma mönster med hög belastning och låg belastnings tid. Resursutnyttjande beror också på mängden data som flyttas. När flera kopierings jobb pågår, ser du att resursanvändningen går upp under hög belastnings tider.
- Aktiviteter kan Miss lyckas om data extraheras i Parquet-, ORC-eller Avro-format. Att skapa filer körs på den lokala integrations datorn och kräver att följande förutsättningar fungerar som förväntat (se [Parquet-format i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)).
    - [Visual C++ 2010 Redistributable](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Package (x64)
    - Java Runtime (JRE) version 8 från en JRE-Provider, till exempel [anta openjdk](https://adoptopenjdk.net/), vilket säkerställer att miljövariabeln `JAVA_HOME` har angetts.

## <a name="installation-best-practices"></a>Metod tips för installation
Du kan installera integration runtime med egen värd genom att ladda ned ett MSI-installationspaketet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Se [Flytta data mellan lokala och moln artiklar](tutorial-hybrid-copy-powershell.md) för stegvisa instruktioner.

- Konfigurera ett energi schema på värddatorn för den lokala integrerings körningen så att datorn inte försätts i vilo läge. Om värddatorn försätts i vilo läge är den egna värdbaserade integrerings körningen offline.
- Säkerhetskopiera de autentiseringsuppgifter som är kopplade till den egen värdbaserade integrerings körningen regelbundet.
- Information om hur du automatiserar IR-installationen finns i [avsnittet nedan](#automation-support-for-self-hosted-ir-function).  

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Installera och registrera egen värd-IR från Download Center

1. Gå till [hämtnings sidan för Microsoft integration runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Välj **Hämta**, Välj 64-bitars versionen (32-bit stöds inte) och välj **Nästa**.
3. Kör MSI-filen direkt eller spara den på hård disken och kör den.
4. På sidan **Välkommen** väljer du ett språk och väljer **Nästa**.
5. Godkänn licens villkoren för program vara från Microsoft och välj **Nästa**.
6. Välj **mapp** för att installera integration runtime med egen värd och välj **Nästa**.
7. På sidan **klar att installera** väljer du **Installera**.
8. Klicka på **Slutför** för att slutföra installationen.
9. Hämta nyckeln för autentisering med hjälp av Azure PowerShell. Här är ett PowerShell-exempel för att hämta nyckeln för autentisering:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. På sidan **registrera integration Runtime (lokal installation)** i Microsoft integration runtime Configuration Manager som körs på datorn utför du följande steg:

    a. Klistra in nyckeln Authentication i text arean.

    b. Du kan också välja **Visa autentiseringsnyckel** för att se nyckel texten.

    c. Välj **Registrera**.

## <a name="automation-support-for-self-hosted-ir-function"></a>Automation-stöd för IR-funktion med egen värd


> [!NOTE]
> Om du planerar att konfigurera den lokala IR-enheten på en virtuell Azure-dator och vill automatisera installationen med hjälp av Azure Resource Manager mallar, se [avsnittet](#setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template).

Du kan använda kommando raden för att konfigurera eller hantera en befintlig egen IR. Detta kan användas särskilt för att automatisera installationen, registrering av IR-noder med egen värd. 

**Dmgcmd. exe** ingår i den självbetjänings installation som vanligt vis finns: C:\Program Files\Microsoft integration Runtime\3.0\Shared\ Folder. Detta stöder olika parametrar och kan anropas via kommando tolken med hjälp av batch-skript för Automation. 

*Användningsvyn* 

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["password"] -Loglevel <logLevel> ] 
```

 *Information (parametrar/egenskap):* 

| Egenskap                                                    | Beskrivning                                                  | Krävs |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| RegisterNewNode "`<AuthenticationKey>`"                     | Registrera Integration Runtime (lokal installation) nod med den angivna autentiseringsnyckel | Nej       |
| EnableRemoteAccess "`<port>`" ["`<thumbprint>`"]            | Aktivera fjärråtkomst på den aktuella noden för att konfigurera ett kluster med hög tillgänglighet och/eller aktivera inställning av autentiseringsuppgifter direkt mot den lokala IR-filen (utan att gå via ADF-tjänsten) med  **New-AzDataFactoryV2LinkedServiceEncryptedCredential-** cmdlet från en fjärrdator i samma nätverk. | Nej       |
| EnableRemoteAccessInContainer "`<port>`" ["`<thumbprint>`"] | Aktivera fjärråtkomst till den aktuella noden när noden körs i behållaren | Nej       |
| DisableRemoteAccess                                         | Inaktivera fjärråtkomst till den aktuella noden. Fjärråtkomst krävs för installation med flera noder. PowerShell-cmdleten New-**AzDataFactoryV2LinkedServiceEncryptedCredential** fungerar fortfarande även om fjärråtkomst är inaktive rad så länge som den körs på samma dator som den lokala IR-noden. | Nej       |
| Nyckel "`<AuthenticationKey>`"                                 | Skriv över/uppdatera föregående autentiseringsnyckel. Var noga med att det kan leda till att din tidigare IR-nod med egen värd blir offline, om nyckeln är en ny integrerings körning. | Nej       |
| GenerateBackupFile "`<filePath>`" "`<password>`"            | Skapa säkerhets kopia för den aktuella noden, innehåller säkerhets kopian den nod-och autentiseringsuppgifter för data lager | Nej       |
| ImportBackupFile "`<filePath>`" "`<password>`"              | Återställa noden från en säkerhets kopia                          | Nej       |
| Starta om                                                     | Starta om Integration Runtime (lokal installation) värd tjänsten   | Nej       |
| Start                                                       | Starta Integration Runtime (lokal installation) värd tjänsten     | Nej       |
| Stopp                                                        | Stoppa Integration Runtime (lokal installation) uppdaterings tjänst        | Nej       |
| StartUpgradeService                                         | Starta Integration Runtime (lokal installation) uppdaterings tjänst       | Nej       |
| StopUpgradeService                                          | Stoppa Integration Runtime (lokal installation) uppdaterings tjänst        | Nej       |
| TurnOnAutoUpdate                                            | Aktivera Integration Runtime (lokal installation) automatisk uppdatering        | Nej       |
| TurnOffAutoUpdate                                           | Inaktivera Integration Runtime (lokal installation) automatisk uppdatering       | Nej       |
| SwitchServiceAccount "< domän \ användare >" ["lösen ord"]           | Ange dia Host service som ska köras som ett nytt konto. Använd ett tomt lösen ord ("") för system konto eller virtuellt konto | Nej       |
| LogLevel `<logLevel>`                                       | Ange ETW-loggnings nivå (av, fel, utförlig eller alla). Används vanligt vis av Microsofts support vid fel sökning. | Nej       |

   


## <a name="high-availability-and-scalability"></a>Hög tillgänglighet och skalbarhet
En integration runtime med egen värd kan associeras med flera lokala datorer eller Virtual Machines i Azure. De här datorerna kallas noder. Du kan ha upp till fyra noder som är kopplade till en egen värd integrerings körning. Fördelarna med att ha flera noder (lokala datorer med en gateway installerad) för en logisk Gateway är:
* Högre tillgänglighet för integration runtime med egen värd så att den inte längre är en enskild felpunkt i din Big data-lösning eller Cloud data integration med Azure Data Factory, vilket säkerställer kontinuitet med upp till fyra noder.
* Bättre prestanda och data flöde vid data förflyttning mellan lokala och molnbaserade data lager. Få mer information om [prestanda jämförelser](copy-activity-performance.md).

Du kan associera flera noder genom att installera program varan för egen värd integrerings körning från [Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Registrera sedan den med hjälp av någon av de autentiseringsinställningar som erhålls från cmdleten **New-AzDataFactoryV2IntegrationRuntimeKey** , enligt beskrivningen i [självstudien](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Du behöver inte skapa en ny egen värd för integration runtime för att associera varje nod. Du kan installera integration runtime med egen värd på en annan dator och registrera den med samma autentiseringsnyckel. 

> [!NOTE]
> Innan du lägger till en annan nod för hög tillgänglighet och skalbarhet måste du kontrol lera att alternativet **fjärråtkomst till intranät** är aktiverat på den första noden (**Microsoft integration runtime Configuration Manager**@no__t 2**Inställningar** >  **Fjärråtkomst till intranät**). 

### <a name="scale-considerations"></a>Skalnings överväganden

#### <a name="scale-out"></a>Skala ut

När det tillgängliga minnet på den lokala IR-datorn är låg och CPU-användningen är hög hjälper det att öka belastningen mellan datorer genom att lägga till en ny nod. Om aktiviteterna inte kan utföras på grund av timeout eller om den egna IR-noden är offline, hjälper det om du lägger till en nod i gatewayen.

#### <a name="scale-up"></a>Skala upp

Om det tillgängliga minnet och CPU: n inte används samtidigt, men körningen av samtidiga jobb når gränsen, bör du skala upp genom att öka antalet samtidiga jobb som kan köras på en nod. Du kanske också vill skala upp när aktiviteterna är tids gräns eftersom den infraröda IR-filen är överbelastad. Som du ser i följande bild kan du öka den maximala kapaciteten för en nod:  

![Öka samtidiga jobb som kan köras på en nod](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Krav för TLS/SSL-certifikat

Här följer kraven för TLS/SSL-certifikatet som används för att skydda kommunikationen mellan noderna för integration Runtime:

- Certifikatet måste vara ett offentligt betrott X509 v3-certifikat. Vi rekommenderar att du använder certifikat som utfärdats av en offentlig (partner) certifikat utfärdare (CA).
- Varje nod för integration runtime måste ha förtroende för det här certifikatet.
- Vi rekommenderar inte certifikat för certifikat mottagarens alternativa namn eftersom endast det sista SAN-objektet kommer att användas och alla andra kommer att ignoreras på grund av aktuella begränsningar. Om du till exempel har ett SAN-certifikat vars San-nätverk är **node1.domain.contoso.com** och **node2.domain.contoso.com**, kan du bara använda det här certifikatet på en dator vars FQDN är **node2.domain.contoso.com**.
- Certifikatet stöder alla nyckel storlekar som stöds av Windows Server 2012 R2 för SSL-certifikat.
- Certifikat som använder CNG-nycklar stöds inte.  

> [!NOTE]
> Det här certifikatet används för att kryptera portar på en IR-nod med egen värd som används för **nod-till-nod-kommunikation** (för tillstånds synkronisering som innehåller länkade tjänsters autentiseringsuppgifter för synkronisering över noder) och samtidigt som du **använder PowerShell-cmdleten för inställningen för länkade tjänst referenser** från det lokala nätverket. Vi rekommenderar att du använder det här certifikatet om din privata nätverks miljö inte är säker eller om du vill skydda kommunikationen mellan noderna i ditt privata nätverk också. Data förflyttning under överföring från egen värd-IR till andra data lager sker alltid med hjälp av en krypterad kanal, oberoende av den här certifikat uppsättningen. 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>Dela den egna värdbaserade integrerings körningen med flera data fabriker

Du kan återanvända en befintlig egen värd för integrerings körning som du redan har konfigurerat i en data fabrik. På så sätt kan du skapa en *länkad egen värd för integration runtime* i en annan data fabrik genom att referera till en befintlig lokal IR (delad).

Om du vill dela en integration runtime med egen värd med hjälp av PowerShell läser du [skapa en delad integration runtime med egen värd i Azure Data Factory med PowerShell](create-shared-self-hosted-integration-runtime-powershell.md).

Om du vill visa en 12-minuters introduktion och demonstration av den här funktionen kan du titta på följande video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologi

- **Delad IR**: den ursprungliga lokala IR som körs på en fysisk infrastruktur.  
- **Länkad IR**: IR som refererar till en annan delad IR. Det här är en logisk IR och använder infrastrukturen för en annan egen värd-IR (delad).

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Steg på hög nivå för att skapa en länkad IR med egen värd

1. I den egen värdbaserade IR-filen som ska delas ger du behörighet till den data fabrik där du vill skapa den länkade IR-filen. 

   ![Knapp för att bevilja behörighet på fliken Delning](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![Val för att tilldela behörigheter](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. Observera resurs-ID: t för den egen värd-IR som ska delas.

   ![Plats för resurs-ID](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. I data fabriken som behörigheterna har beviljats skapar du en ny lokal IR-anslutning (länkad) och anger resurs-ID: t.

   ![Knapp för att skapa en länkad integration runtime med egen värd](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![Rutor för namn och resurs-ID](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>Övervakning 

- **Delad IR**

  ![Val för att hitta en delad integrerings körning](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Flik för övervakning](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **Länkad IR**

  ![Val för att hitta en länkad integration runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Flik för övervakning](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Kända begränsningar för IR-delning med egen värd

* Data fabriken som en länkad IR ska skapas i måste ha en [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Som standard har de data fabriker som skapats i Azure Portal-eller PowerShell-cmdlets ett MSI-format som har skapats implicit. Men när en data fabrik skapas via en Azure Resource Manager mall eller SDK måste **identitets** egenskapen anges explicit för att Azure Resource Manager skapar en data fabrik som innehåller en MSI. 

* Den Azure Data Factory .NET SDK som stöder den här funktionen är version 1.1.0 eller senare.

* För att bevilja behörighet måste användaren ha ägar rollen eller den ärvda ägar rollen i data fabriken där den delade IR-filen finns.

* Delnings funktionen fungerar bara för data fabriker inom samma Azure Active Directory-klient.

* För Active Directory [gäst användare](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) [fungerar inte](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits)Sök funktionen (som visar alla data fabriker med hjälp av nyckelordet search) i användar gränssnittet. Men så länge gäst användaren är ägare till data fabriken kan de dela IR-filen utan Sök funktionen genom att direkt skriva MSI-filen för den data fabrik som IR måste delas med i text rutan **tilldela behörighet** och välja **Lägg till** i Azure Data Factory användar gränssnitt. 

  > [!NOTE]
  > Den här funktionen är endast tillgänglig i Azure Data Factory v2. 

## <a name="notification-area-icons-and-notifications"></a>Ikoner och meddelanden i meddelande fältet

Om du flyttar markören över ikonen eller meddelandet i meddelande fältet kan du hitta information om status för den lokala integrerings körningen.

![Meddelanden i meddelande fältet](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portar och brand vägg
Det finns två brand väggar att tänka på: *företags brand väggen* som körs på den centrala routern i organisationen och *Windows-brandväggen* konfigurerad som daemon på den lokala datorn där den lokala integrerings körningen är installerad.

![Brandvägg](media/create-self-hosted-integration-runtime/firewall.png)

På *företags brand Väggs* nivå måste du konfigurera följande domäner och utgående portar:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]


På *Windows brand Väggs* nivå (dator nivå) är dessa utgående portar normalt aktiverade. Om inte kan du konfigurera domänerna och portarna på motsvarande sätt på en egen värd för integration runtime-datorn.

> [!NOTE]
> Utifrån din källa och dina mottagare kan du behöva tillåta ytterligare domäner och utgående portar i företags brand väggen eller Windows-brandväggen.
>
> För vissa moln databaser (till exempel Azure SQL Database och Azure Data Lake) kan du behöva tillåta IP-adresser för datorer med egen värd integrerings körning i brand Väggs konfigurationen.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopiera data från en källa till en mottagare
Kontrol lera att brand Väggs reglerna är korrekt aktiverade i företags brand väggen, Windows-brandväggen på den lokala datorn för integration Runtime och själva data lagret. Genom att aktivera dessa regler kan integrerings körning med egen värd ansluta till både källan och mottagaren. Aktivera regler för varje data lager som ingår i kopierings åtgärden.

Gör så här om du till exempel vill kopiera från ett lokalt data lager till en Azure SQL Database mottagare eller en Azure SQL Data Warehouse mottagare:

1. Tillåt utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företags brand väggen.
2. Konfigurera brand Väggs inställningarna för Azure SQL Database för att lägga till IP-adressen för den egen värdbaserade integration runtime-datorn i listan över tillåtna IP-adresser.

> [!NOTE]
> Om brand väggen inte tillåter utgående port 1433, kan inte den egen värdbaserade integrerings körningen komma åt Azure SQL Database direkt. I det här fallet kan du använda en [mellanlagrad kopia](copy-activity-performance.md) för att Azure SQL Database och Azure SQL Data Warehouse. I det här scenariot behöver du bara HTTPS (port 443) för data flytten.


## <a name="proxy-server-considerations"></a>Överväganden för proxyserver
Om företagets nätverks miljö använder en proxyserver för att få åtkomst till Internet konfigurerar du den egen värdbaserade integrerings körningen så att rätt proxyinställningar används. Du kan ställa in proxyn under den inledande registrerings fasen.

![Ange proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

När det konfigureras använder den egen värdbaserade integrerings körningen proxyservern för att ansluta till moln tjänsten, källan/målet (som använder HTTP/HTTPS-protokollet). Välj **ändra länk** under den första installationen. Dialog rutan proxy-inställningar visas.

![Ange proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Det finns tre konfigurations alternativ:

- **Använd inte proxy**: den egna värdbaserade integrerings körningen använder inte uttryckligen någon proxy för att ansluta till moln tjänster.
- **Använd systemproxy**: den egna värdbaserade integrerings körningen använder den proxyserver som kon figurer ATS i diahost. exe. config och diawp. exe. config. Om ingen proxy har kon figurer ATS i diahost. exe. config och diawp. exe. config ansluter den egna värdbaserade integrerings körningen till moln tjänsten direkt utan att gå via en proxy.
- **Använd anpassad proxy**: konfigurera inställningen för http-proxy som ska användas för integration runtime med egen värd i stället för konfigurationer i diahost. exe. config och diawp. exe. config. **Adress** och **port** måste anges. **Användar namn** och **lösen ord** är valfria beroende på proxyns autentiseringsinställningar. Alla inställningar krypteras med Windows DPAPI på integration runtime med egen värd och lagras lokalt på datorn.

Värd tjänsten för integration runtime startas om automatiskt när du har sparat de uppdaterade proxyinställningarna.

När den egen värdbaserade integrerings körningen har registrerats kan du använda Integration Runtime Configuration Manager om du vill visa eller uppdatera proxyinställningarna.

1. Öppna **Microsoft Integration Runtime Configuration Manager**.
2. Växla till fliken **Settings** (Inställningar).
3. Välj länken **ändra** i avsnittet **http-proxy** för att öppna dialog rutan **ange http-proxy** .
4. Välj **Nästa**. Sedan visas en varning som ber om din tillåtelse att spara proxyinställningarna och starta om värd tjänsten för integration Runtime.

Du kan visa och uppdatera HTTP-proxyn med hjälp av Configuration Manager-verktyget.

![Visa proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Om du konfigurerar en proxyserver med NTLM-autentisering körs värd tjänsten för integration runtime under domän kontot. Om du ändrar lösen ordet för domän kontot senare måste du komma ihåg att uppdatera konfigurations inställningarna för tjänsten och sedan starta om den. På grund av detta krav rekommenderar vi att du använder ett dedikerat domän konto för att få åtkomst till proxyservern som inte kräver att du uppdaterar lösen ordet ofta.

### <a name="configure-proxy-server-settings"></a>Konfigurera inställningar för proxyserver

Om du väljer inställningen **Använd systemproxy** för HTTP-proxyn använder den egen värdbaserade integrerings körningen proxykonfigurationen i diahost. exe. config och diawp. exe. config. Om ingen proxy anges i diahost. exe. config och diawp. exe. config ansluter den egna värdbaserade integrerings körningen till moln tjänsten direkt utan att gå via proxy. Följande procedur innehåller instruktioner för att uppdatera filen diahost. exe. config:

1. I Utforskaren skapar du en säker kopia av C:\Program Files\Microsoft integration Runtime\3.0\Shared\diahost.exe.config för att säkerhetskopiera original filen.
2. Öppna Notepad. exe som körs som administratör och öppna text filen C:\Program Files\Microsoft integration Runtime\3.0\Shared\diahost.exe.config. Hitta standard tag gen för system.net så som visas i följande kod:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Du kan sedan lägga till information om proxyservern som visas i följande exempel:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Ytterligare egenskaper tillåts i proxy-taggen för att ange nödvändiga inställningar som `scriptLocation`. Se [proxy-elementet (nätverks inställningar)](https://msdn.microsoft.com/library/sa91de1e.aspx) för syntax.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Spara konfigurations filen på den ursprungliga platsen. Starta sedan om värd tjänsten för integration runtime med egen värd, som hämtar ändringarna. 

   Om du vill starta om tjänsten använder du tjänst-appleten från kontroll panelen. Eller från Integration Runtime Configuration Manager, Välj knappen **stoppa tjänst** och välj sedan **Starta tjänst**. 
   
   Om tjänsten inte startar är det troligt att en felaktig XML-tagg har lagts till i program konfigurations filen som redigerades.

> [!IMPORTANT]
> Glöm inte att uppdatera både diahost. exe. config och diawp. exe. config.

Du måste också se till att Microsoft Azure finns i företagets lista över tillåtna. Du kan ladda ned listan över giltiga Microsoft Azure IP-adresser från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Möjliga problem för brand Väggs-och proxy server-relaterade problem
Om du stöter på fel som liknar följande kan det bero på felaktig konfiguration av brand väggen eller proxyservern, vilket blockerar den egna värdbaserade integrerings körningen från att ansluta till Data Factory att autentisera sig själv. Se föregående avsnitt för att kontrol lera att din brand vägg och proxyserver är korrekt konfigurerade.

* När du försöker registrera den egna värdbaserade integrerings körningen får du följande fel meddelande: "Det gick inte att registrera den här Integration Runtime noden! Bekräfta att autentiseringsnyckel är giltig och att värd tjänsten för integrations tjänsten körs på den här datorn. "
* När du öppnar Integration Runtime Configuration Manager visas statusen **frånkopplad** eller **ansluten**. När du visar Windows-händelseloggar, under **Loggboken** > **program-och tjänst loggar** > **Microsoft integration runtime**, visas fel meddelanden som detta:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Aktivera fjärråtkomst från ett intranät  
Om du använder PowerShell för att kryptera autentiseringsuppgifter från en annan dator (i nätverket) än den som den egna värdbaserade integrerings körningen är installerad på, kan du aktivera alternativet **fjärråtkomst från intranät** . Om du kör PowerShell för att kryptera autentiseringsuppgifter på samma dator där den lokala integrerings körningen är installerad, kan du inte aktivera **fjärråtkomst från intranätet**.

Du bör aktivera **fjärråtkomst från intranätet** innan du lägger till en annan nod för hög tillgänglighet och skalbarhet.  

Under installationen av den lokala integrerings körningen (version 3.3. xxxx. x senare) inaktive **ras fjärråtkomst från intranätet** på den lokala datorn för integration Runtime.

Om du använder en brand vägg från en tredje part kan du manuellt öppna port 8060 (eller den användardefinierade porten). Om du har problem med brand väggen när du konfigurerar den lokal integrerings körningen kan du försöka med att använda följande kommando för att installera integration runtime med egen värd utan att konfigurera brand väggen:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Om du väljer att inte öppna port 8060 på den lokala integration runtime-datorn använder du andra metoder än inställningen ange autentiseringsuppgifter för att konfigurera autentiseringsuppgifter för data arkivet. Du kan till exempel använda PowerShell-cmdleten **New-AzDataFactoryV2LinkedServiceEncryptCredential** .


## <a name="next-steps"></a>Nästa steg
I följande självstudie finns stegvisa anvisningar: [Självstudier: Kopiera lokala data till molnet](tutorial-hybrid-copy-powershell.md).
