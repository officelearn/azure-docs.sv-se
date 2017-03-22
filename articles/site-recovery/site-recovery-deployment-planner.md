---
title: "Azure Site Recovery Deployment Planner för VMware till Azure| Microsoft Docs"
description: "Det här är användarhandboken för Azure Site Recovery Deployment Planner."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 2/21/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 2575621d72b7db2b090ba923324697b7fa7b8308
ms.lasthandoff: 03/15/2017


---
#<a name="azure-site-recovery-deployment-planner"></a>Azure Site Recovery Deployment Planner
Det här är användarhandboken för Azure Site Recovery Deployment Planner för produktionsdistribution av VMware till Azure.


##<a name="overview"></a>Översikt

Innan du börjar skyddar virtuella VMware-datorer med hjälp av Azure Site Recovery måste du allokera tillräckligt mycket bandbredd sett till din dagliga dataändringshastighet så att du når önskat RPO-mål. Du måste distribuera tillräckligt många konfigurationsservrar och processervrar lokalt. Du måste också skapa tillräckligt många Azure Storage-konton av rätt typ, antingen standard eller premium, och ta hänsyn till tillväxten på dina källproduktionsservrar eftersom användningen tenderar att öka med tiden. Lagringstypen avgörs för varje virtuell dator baserat på typen av arbetsbelastning (IOPS, dataomsättning) och gränser i Azure Site Recovery.  

Azure Site Recovery Deployment Planner Public Preview är ett kommandoradsverktyg som för närvarande bara är tillgängligt för scenariot med VMware till Azure. Du kan profilera dina virtuella VMware-datorer via fjärranslutning med det här verktyget (utan att produktionen påverkas alls) så att du får en uppfattning om vilken bandbredd och vilket lagringsutrymme som kommer att behövas för replikering och redundansväxling.  Du kan köra verktyget utan att installera några Azure Site Recovery-komponenter lokalt, men du får bättre dataflödesresultat om du kör planeringsverktyget på en Windows-server som uppfyller minimikraven för den konfigurationsserver för Azure Site Recovery som du kommer att ta i drift som ett av de första stegen i produktionsdistributionen.

Du kan se följande information i verktyget:

**Utvärdering av kompatibilitet**<br>
* En utvärdering av om den virtuella datorn stöds som baseras på antalet diskar, diskstorleken, IOPS och dataomsättningen

**Behovet av bandbredd i nätverket kontra RPO-bedömning**<br>
* Beräknad nätverksbandbredd som krävs för deltareplikering<br>
* Genomströmning som Azure Site Recovery kan få från lokala datorer till Azure<br>
* Antalet virtuella datorer att bearbeta per batch sett till den uppskattade bandbredd som krävs för att slutföra replikeringen på en viss tid<br>

**Krav på infrastruktur för Microsoft Azure**<br>
* Lagringsutrymme och typ (standard- eller premium-lagring) som krävs för varje virtuell dator<br>
* Totalt antal konton för standard- och premium-lagring som ska etableras för replikering<br>
* Namnförslag för lagringskonton baserade på riktlinjer i Azure Storage<br>
* Lagringskontots placering för varje virtuell dator<br>
* Antalet Microsoft Azure-kärnor som ska etableras innan redundanstest/redundansväxling för prenumerationen<br>
* Rekommenderad storlek för virtuell Microsoft Azure-dator för varje lokal virtuell dator<br>

**Krav på lokal infrastruktur**<br>
* Antalet konfigurationsservrar och processervrar som måste distribueras lokalt<br>

>[!IMPORTANT]
>
>Dessa beräkningar i verktyget görs med antagandet att arbetsbelastningens tillväxtfaktor är 30 % eftersom användningen kan öka med tiden, och med 95:e percentilen för alla profileringsmått (IOPS, dataomsättning osv.) Båda parametrarna, tillväxtfaktorn och percentilen, kan konfigureras. Läs mer om [tillväxtfaktorn](site-recovery-deployment-planner.md#growth-factor) och [percentilvärdet som används för beräkning](site-recovery-deployment-planner.md#percentile-value-used-for-the-calculation).
>


## <a name="requirements"></a>Krav
Verktyget har två huvudfaser – profilering och rapportgenerering. Det finns också ett tredje alternativ som endast beräknar dataflödet. Nedan visas kraven för servern där profileringen/mätning av dataflödet initieras.

| Krav | Beskrivning|
|---|---|
|Profilering och mätning av dataflöde| <br>Operativsystem: Microsoft Windows Server 2012 R2 <br>Ska helst matcha minst följande [storlek](https://aka.ms/asr-v2a-on-prem-components) på konfigurationsservern<br>Serverkonfiguration: 8 virtuella processorer, 16 GB RAM, 300 GB Hårddisk<br [Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)<br>[VMware vSphere PowerCLI 6.0 R3](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)<br>[Microsoft Visual C++ Redistributable for Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)<br> Internetåtkomst till Microsoft Azure från den här servern<br> Lagringskonto för Microsoft Azure<Br>Administratörsbehörighet till servern<br>Minst 100 GB ledigt diskutrymme (gäller för 1 000 virtuella datorer med i genomsnitt 3 diskar var som profileras under 30 dagar)|
| Rapportgenerering| Valfri Windows PC/Windows Server med Microsoft Excel 2013 eller senare |
| Användarbehörigheter | Läsbehörighet för det användarkonto som ska användas för åtkomst till VMware vCenter-/vSphere-servern under profilering|


> [!NOTE]
>
> Verktyget kan bara profilera virtuella datorer med VMDK- och RDM-diskar. Du kan inte profilera virtuella datorer med iSCSI- eller NFS-diskar. Även om Azure Site Recovery har stöd för iSCSI och NFS-diskar för VMware-servrar så kan inte verktyget se dessa eftersom planeringsverktyget inte körs i gästen och profileringen bara utförs med prestandaräknare för vCenter.
>


##<a name="download"></a>Ladda ned
[Ladda ned](https://aka.ms/asr-deployment-planner) den senaste versionen av Azure Site Recovery Deployment Planner Public Preview.  Verktyget är paketerat i zip-format.  Den aktuella versionen av verktyget har endast stöd för scenariot med VMware till Azure.

Kopiera zip-filen till den Windows Server du vill köra verktyget från. Även om du kan köra verktyget från valfri Windows Server 2012 R2 med nätverksåtkomst för anslutning till VMware vCenter-servern eller VMware vSphere ESXi-värden som innehåller de virtuella datorer som ska profileras så bör du köra verktyget på en server vars maskinvarukonfiguration uppfyller [riktlinjerna för konfigurationsserverns storlek](https://aka.ms/asr-v2a-on-prem-components).  Om du redan har distribuerat Azure Site Recovery-komponenter lokalt bör du köra verktyget från konfigurationsservern. Du bör ha samma maskinvarukonfiguration som för konfigurationsservern (som har en inbyggd processerver) på den server där du kör verktyget, så att det dataflöde som verktyget rapporterar matchar det faktiska dataflöde som Azure Site Recovery kan uppnå vid replikeringen – beräkningen av dataflödet beror på serverns tillgängliga nätverksbandbredd samt på serverns maskinvarukonfiguration (processor, lagring osv.). Om du kör verktyget från en annan server kommer dataflödet att beräknas för den servern till Microsoft Azure, och dessutom kan serverns maskinvarukonfiguration vara en annan än för konfigurationsservern, så att det uppnådda dataflödet som verktyget rapporterar inte stämmer.

Extrahera zip-filen. Den innehåller flera filer och undermappar. Den körbara filen är ASRDeploymentPlanner.exe i den överordnade mappen.

Exempel: Kopiera .zip-filen till enheten E:\ och packa upp den.
E:\ASR Deployment Planner-Preview_v1.1.zip

E:\ASR Deployment Planner-Preview_v1.1\ ASR Deployment Planner-Preview_v1.1\ ASRDeploymentPlanner.exe

##<a name="capabilities"></a>Funktioner
Du kan köra kommandoradsverktyget (ASRDeploymentPlanner.exe) i någon av följande tre lägen:

1.    Profilering  
2.    Rapportgenerering
3.    Beräkna dataflöde

Du måste först köra verktyget i profileringsläget så att du samlar in information om dataomsättning och IOPS för de virtuella datorerna.  Kör sedan en rapportgenerering när du ska avgöra kraven på nätverksbandbredd och lagring.

##<a name="profiling"></a>Profilering
I profileringsläge ansluter planeringsverktyget till vCenter-servern eller vSphere ESXi-värden och samlar in prestandadata om den virtuella datorn.

* Profileringen påverkar inte prestanda för de virtuella produktionsdatorerna eftersom det inte upprättas någon anslutning till produktionsmiljön. Alla prestandadata samlas in från vCenter-servern/vSphere ESXi-värden.
* Det skickas frågor till VCenter-servern/vSphere EXSi-värden var 15:e minut, så att servern ska påverkas minimalt av profileringen. Detta äventyrar dock inte profileringens noggrannhet eftersom verktyget lagrar prestandaräknardata varje minut.

####<a name="create-a-list-of-virtual-machines-to-profile"></a>Skapa en lista med virtuella datorer att profilera
Först måste du ha en lista med virtuella datorer som ska profileras. Du kan hämta namnen på alla virtuella datorer på en VMware vCenter-server eller VMware vSphere ESXi-värd med hjälp av följande VMware vSphere PowerCLI-kommandon. Du kan också ange namn och IP-adresser för de virtuella datorer du vill profilera manuellt i en fil.

1.    Logga in på den virtuella dator där VMware vSphere PowerCLI är installerat
2.    Öppna VMware vSphere PowerCLI-konsolen
3.    Se till att körningsprincipen inte är inaktiverad för skriptet. Om den är inaktiverad så startar du VMware vSphere PowerCLI-konsolen i administratörsläge och aktiverar den med följande kommando:

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4.    Du kan hämta namnen på alla virtuella datorer på en VMware vCenter-server eller VMware vSphere ESXi-värd och lagra dem i en .txt-fil med följande två kommandon.
Ersätt &lsaquo;servernamn&rsaquo;, &lsaquo;användarnamn&rsaquo;, &lsaquo;lösenord&rsaquo; och &lsaquo;utdatafil.txt&rsaquo; med egna värden.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>


5.    Öppna utdatafilen i Anteckningar. Kopiera namnen på alla virtuella datorer du vill profilera till en annan fil (till exempel ProfileVMList.txt) och ange ett namn per rad. Den här filen används som indata för parametern -VMListFile i kommandoradsverktyget.

    ![Deployment Planner](./media/site-recovery-deployment-planner/profile-vm-list.png)


####<a name="start-profiling"></a>Starta profilering
När du har skapat listan med virtuella datorer att profilera kan du köra verktyget i profileringsläge. Här är listan med obligatoriska och valfria parametrar när du ska köra verktyget i profileringsläge. Parametrar omslutna med [] är valfria.

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| Parameternamn | Beskrivning |
|---|---|
| -Operation |      StartProfiling |
| -Server | Fullständigt domännamn eller IP-adress för den vCenter-server/ESXi-värd vars virtuella datorer ska profileras.|
| -User | Användarnamn för anslutning till vCenter-servern/ESXi-värden. Användaren måste minst ha skrivbehörighet.|
| -VMListFile |    Filen som innehåller listan med virtuella datorer som ska profileras. Filsökvägen kan vara absolut eller relativ. Den här filen ska innehålla ett datornamn/en IP-adress per rad. Namnen på de virtuella datorerna i filen ska vara samma som VM-namnen på vCenter-servern eller ESXi-värden. <br> Exempel: filen VMList.txt innehåller följande virtuella datorer:<br>virtuell_dator_A <br>10.150.29.110<br>virtuell_dator_B |
| -NoOfDaysToProfile | Antal dagar som profileringen ska köras. Du bör köra profileringen i minst 15 dagar så att du fångar upp mönster för arbetsbelastningen i din miljö och kan generera korrekta rekommendationer. |
| [-Directory] |    UNC eller lokal sökväg för lagring av de profildata som genereras under profileringen. Om du inte anger något värde används katalogen ProfiledData under den aktuella sökvägen som standardkatalog. |
| [-Password ] | Lösenord för anslutning till vCenter-servern/ESXi-värden. Om du inte anger något värde för parametern uppmanas du att ange lösenordet när kommandot körs.|
|  [-StorageAccountName]  | Namnet på det Azure Storage-konto som används för beräkning av dataflödet som kan uppnås för datareplikering lokalt till Azure. Verktyget överför testdata till det här lagringskontot när dataflödet ska beräknas.|
| [-StorageAccountKey] | Den kontonyckel som används för åtkomst till lagringskontot. Gå till Azure Portal > Lagringskonton > [lagringskontots namn] > Inställningar > Åtkomstnycklar > Key1 (eller Primär åtkomstnyckel för klassiska lagringskonton). |

Du bör profilera dina virtuella datorer i minst 15 till 30 dagar. ASRDeploymentPlanner.exe körs under hela profileringsperioden. Profileringstiden anges i dagar i verktyget. Om du vill profilera i några timmar eller minuter för att snabbtesta verktyget måste du omvandla tiden till motsvarande antal dagar i Public Preview.  Om du till exempel vill profilera i 30 minuter ska du ange 30/(60*24) = 0,021 dagar.  Minsta tillåtna profileringstid är 30 minuter.

Under profileringen kan du skicka namn och nyckel för ett Azure Storage-konto om du vill se vilket dataflöde som Azure Site Recovery kan uppnå för replikeringen från konfigurationsservern/processervern till Azure. Om du inte skickar namn och nyckel för ett Azure Storage-konto under profileringen beräknas inte potentiellt dataflöde.


Du kan köra flera instanser av verktyget för olika uppsättningar av virtuella datorer. Se till att det inte finns dubbletter av namn på virtuella datorer i profileringsuppsättningarna. Som exempel kanske du har profilerat tio virtuella datorer (VM1–VM10). Efter några dagar vill du profilera ytterligare fem virtuella datorer (VM11–VM15), och då kan du köra verktyget från en annan kommandoradskonsol för den andra uppsättningen av virtuella datorer (VM11–VM15). Du måste däremot se till att den andra uppsättningen av virtuella datorer inte innehåller något av datornamnen från den första profileringsinstansen, eller att du använder en annan utdatakatalog för den andra körningen. Om två instanser av verktyget används för profilering av samma virtuella datorer och samma utdatakatalog används kommer den genererade rapporten att vara felaktig.

Den virtuella datorns konfiguration inhämtas en gång i början av profileringen och lagras i en fil med namnet VMDetailList.xml. Den här informationen används när rapporten ska genereras. Ändringar i VM-konfiguration (till exempel fler kärnor, diskar, nätverkskort osv.) efter det att profileringen startades kommer inte att registreras. Om konfigurationen för en profilerad virtuell dator ändrats under profileringen finns det en lösning i Public Preview för att hämta den senaste datorinformationen när rapporten ska genereras.   

* Säkerhetskopiera filen VMdetailList.xml och ta bort den från dess nuvarande plats.
* Skicka argumenten -User och -Password när rapporten ska genereras.

Med profileringskommandot skapas flera filer i profileringskatalogen. Ta inte bort någon av dem eftersom det kan påverka genereringen av rapporten.

#####<a name="example-1-to-profile-virtual-machines-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Exempel 1: profilering av virtuella datorer i 30 dagar beräkning av dataflödet från lokala datorer till Azure
ASRDeploymentPlanner.exe **-Operation** StartProfiling -Directory ”E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** ”E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  30  **-User** vCenterUser1 **-StorageAccountName**  asrspfarm1 **-StorageAccountKey** Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

#####<a name="example-2-to-profile-virtual-machines-for-15-days"></a>Exempel 2: profilering av virtuella datorer i 15 dagar
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** ”E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** ”E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  15  -User vCenterUser1

#####<a name="example-3-to-profile-virtual-machines-for-1-hour-for-a-quick-test-of-the-tool"></a>Example 3: profilering av virtuella datorer under 1 timmes snabbtest av verktyget
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** ”E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** ”E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  0.04  **-User** vCenterUser1


>[!NOTE]
>
> * Om servern där verktyget körs startas om eller kraschar, eller om du avslutar verktyget med Ctrl + C, så bevaras profileringsdata. Du riskerar däremot att förlora de senaste 15 minuternas profileringsdata. Du måste köra verktyget i profileringsläge när servern startas igen.
>
> * När du skickar namn och nyckel för ett Azure Storage-konto mäter verktyget dataflödet vid profileringens sista steg. Om verktyget avslutas innan profileringen slutförts normalt så beräknas inte dataflödet. Du kan köra åtgärden GetThroughput från kommandoraden om du vill se dataflödet innan du genererar rapporten, annars innehåller inte rapporten information om uppnått dataflöde.
>

##<a name="generate-report"></a>Generera en rapport
Verktyget genererar en XLSM-fil (en Microsoft Excel-fil med makrofunktioner) som rapportutdata och där ges en sammanfattning av alla rekommendationer för distributionen. Rapporten har namnet DeploymentPlannerReport_<Unique Numeric Identifier>.xlsm och placeras i den angivna katalogen.

När profileringen är färdig kan köra du verktyget i läget för rapportgenerering. Här är listan med obligatoriska och valfria parametrar när du ska köra verktyget i läget för rapportgenerering. Parametrar omslutna med [] är valfria.

ASRDeploymentPlanner.exe -Operation GenerateReport /?

|Parameternamn | Beskrivning |
|-|-|
| -Operation | GenerateReport |
| -Server |  Fullständigt domännamn eller IP-adress för vCenter-/vSphere-servern (använd exakt samma namn eller IP-adress som vid profileringen) där de profilerade virtuella datorer som rapporten ska gälla finns. Om du använde en vCenter-server vid profileringen kan du inte använda en vSphere-server till rapportgenerering och tvärtom.|
| -VMListFile | Filen som innehåller listan med profilerade virtuella datorer som rapporten ska genereras för. Filsökvägen kan vara absolut eller relativ. Den här filen ska innehålla ett datornamn/en IP-adress per rad. Namnen på virtuella datorer i filen ska vara samma som namnen på de virtuella datorerna på vCenter-servern eller ESXi-värden, och vara samma som vid profileringen.|
| [-Directory] | UNC eller lokal sökväg där profileringsdata (filer som genererats under profileringen) lagras. Dessa data krävs när rapporten ska genereras. Om du inte anger något värde så används katalogen ProfiledData. |
| [-GoalToCompleteIR] |    Antal timmar som den initiala replikeringen av de profilerade virtuella datorerna måste slutföras på. I den genererade rapporten anges för hur många virtuella datorer som den initiala replikeringen kan slutföras inom den angivna tiden. Standardvärdet är 72 timmar. |
| [-User] | Användarnamn för anslutning till vCenter-/vSphere-servern. Det här används till att hämta den senaste konfigurationsinformationen för de virtuella datorerna som ska användas i rapporten, som antal diskar, antal kärnor och antal nätverkskort. Om du inte anger något värde så används den konfigurationsinformation som samlades in i början av profileringen. |
| [-Password] | Lösenord för anslutning till vCenter-servern/ESXi-värden. Om du inte anger något värde för parametern uppmanas du att ange lösenordet senare när kommandot körs. |
| [-DesiredRPO] | Önskat mål för återställningspunkt (RPO) i minuter. Standardvärdet är 15 minuter.|
| [-Bandwidth] | Bandbredd i Mbit/s. Det här värdet används till att beräkna det RPO som kan uppnås för den angivna bandbredden. |
| [-StartDate]  | Startdatum och tidpunkt på formatet MM-DD-ÅÅÅÅ:HH:MM (24-timmarsformat). StartDate måste anges tillsammans med EndDate. När du anger de här parametrarna genereras rapporten för profileringsdata som samlats in mellan StartDate och EndDate. |
| [-EndDate] | Slutdatum och tidpunkt på formatet MM-DD-ÅÅÅÅ:HH:MM (24-timmarsformat). EndDate måste anges tillsammans med StartDate. När du anger de här parametrarna genereras rapporten för profileringsdata som samlats in mellan StartDate och EndDate. |
| [-GrowthFactor] |Tillväxtfaktor i procent. Standardvärdet är 30 %.  |


##### <a name="example-1-to-generate-report-with-default-values-when-profiled-data-is-on-the-local-drive"></a>Exempel 1: generera en rapport med standardvärden när profileringsdata ligger på den lokala enheten
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** ”E:\vCenter1_ProfiledData” **-VMListFile** ”E:\vCenter1_ProfiledData\ProfileVMList1.txt”


##### <a name="example-2-to-generate-report-when-profiled-data-is-on-a-remote-server-user-should-have-readwrite-access-on-the-remote-directory"></a>Example 2: generera en rapport när profileringsdata ligger på en fjärrserver. Användaren ska ha läs-/skrivbehörighet för fjärrkatalogen.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “\\\\PS1-W2K12R2\vCenter1_ProfiledData” **-VMListFile** “\\\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”

##### <a name="example-3-generate-report-with-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Exempel 3: generera en rapport med specifik bandbredd och mål för att slutföra IR inom angiven tid
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** ”E:\vCenter1_ProfiledData” **-VMListFile** ”E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-Bandwidth** 100 **-GoalToCompleteIR** 24

##### <a name="example-4-generate-report-with-5-growth-factor-instead-of-the-default-30"></a>Exempel 4: generera en rapport med 5 % tillväxtfaktor i stället för standardvärdet 30 %
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** ”E:\vCenter1_ProfiledData” **-VMListFile** ”E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-GrowthFactor** 5

##### <a name="example-5-generate-report-with-a-subset-of-profiled-data-say-you-have-30-days-of-profiled-data-and-want-to-generate-the-report-for-only-20-days"></a>Exempel 5: generera en rapport med en delmängd av profileringsdata. Anta att du har profileringsdata för 30 dagar och bara vill generera rapporten för 20 dagar.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** ”E:\vCenter1_ProfiledData” **-VMListFile** ”E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-StartDate**  01-10-2017:12:30 -**EndDate** 01-19-2017:12:30

##### <a name="example-6-generate-report-for-5-minutes-rpo"></a>Exempel 6: generera en rapport för en RPO på 5 minuter.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** ”E:\vCenter1_ProfiledData” **-VMListFile** ”E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-DesiredRPO** 5

### <a name="percentile-value-used-for-the-calculation"></a>Percentilvärdet som används för beräkningen
**Vilket standardvärde för percentilen för resultatmåtten som samlas in under profileringen används när rapporten genereras?**

Standardvärdet i verktyget är den 95:e percentilen för IOPS och dataomsättningar som samlas in när de virtuella datorerna profileras. Det innebär att de tillfälliga värdetopparna i den 100:e percentilen inte används till att beräkna krav för Azure Storage och bandbredd. Sådana värdetoppar kan handla om säkerhetskopieringar som körs en gång om dagen, regelbunden databasindexering, generering av analysrapporter eller någon annan kortare händelse under profileringsperioden. Med värden från den 95:e percentilen får du en korrekt bild av den faktiska arbetsbelastningen, och då får du bästa möjliga prestanda när dessa arbetsbelastningar sedan körs på Microsoft Azure. Du kommer förmodligen inte ändra det här värdet särskilt ofta, men om du bestämmer dig för ett ännu lägre värde, som den 90:e percentilen, så kan du uppdatera konfigurationsfilen ASRDeploymentPlanner.exe.config i standardmappen, spara den och generera en ny rapport för befintliga profileringsdata.

        &lsaquo;add key="WriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="ReadWriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="DataChurnPercentile" value="95" /&rsaquo;

### <a name="growth-factor"></a>Tillväxtfaktor
**Varför ska jag ta hänsyn till en tillväxtfaktor när jag planerar min distribution?**

Det är viktigt att ge utrymme för arbetsbelastningen att öka eftersom dataanvändningen tenderar att öka med tiden. När du väl har skyddat dina data och arbetsbelastningen skulle förändras finns det nämligen för närvarande inget sätt att byta till ett annat Azure Storage-konto utan att inaktivera och sedan återaktivera skyddet. T.ex. kan du tänka dig en virtuell dator som idag får plats i ett standardkonto för replikeringslagring. Om tre månader kan det ha tillkommit fler användare i det program som körs på den virtuella datorn och dataomsättningen kan ha ökat, så nu behövs ett premiumkonto för lagring så att replikeringen i Azure Site Recovery kan hålla jämna steg. Då måste du inaktivera skyddet och uppgradera till ett premiumlagringskonto. Du bör alltså planera för tillväxt inför distribueringen, och standardvärdet är 30 %. Det är du som känner till användningsmönster och tillväxtprognoser i dina program, och du kan ändra det här värdet när du genererar rapporter. Du kan i själva verket skapa flera rapporter med olika tillväxtfaktorer för samma profileringsdata och se vilka rekommendationer för mållagring i Azure Storage och källbandbredd som fungerar bäst för dig.

Den genererade Microsoft Excel-rapporten innehåller följande blad:

* [Indata](site-recovery-deployment-planner.md#input)
* [Rekommendationer](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input)
* [Recommedations-Bandwidth Input](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input) (Rekommendationer - bandbredd som indata)
* [VM<->Storage Placement](site-recovery-deployment-planner.md#vm-storage-placement) (VM<->lagringsplacering)
* [Compatible VMs](site-recovery-deployment-planner.md#compatible-vms) (Kompatibla virtuella datorer)
* [Incompatible VMs](site-recovery-deployment-planner.md#incompatible-vms) (Inkompatibla virtuella datorer)

![Deployment Planner](./media/site-recovery-deployment-planner/dp-report.png)


##<a name="get-throughput"></a>Beräkna dataflöde
Om du vill få en uppskattning av vilket dataflöde som Azure Site Recovery kan uppnå från de lokala datorerna under replikeringen ska du köra verktyget i dataflödesläget. Verktyget beräknar dataflödet från servern där verktyget körs (helst en server som matchar riktlinjerna för konfigurationsservern).  Om du redan har distribuerat infrastrukturkomponenter för Azure Site Recovery lokalt ska du köra verktyget på konfigurationsservern.

Öppna en kommandorad och gå till mappen för planeringsverktyget för ASR.  Kör ASRDeploymentPlanner.exe med följande parametrar. Parametrar omslutna med [] är valfria.

ASRDeploymentPlanner.exe -Operation GetThroughput /?

|Parameternamn | Beskrivning |
|-|-|
| -operation | GetThroughput |
| [-Directory] | UNC eller lokal sökväg där profileringsdata (filer som genererats under profileringen) lagras. Dessa data krävs när rapporten ska genereras. Om du inte anger något värde så används katalogen ProfiledData.  |
| -StorageAccountName | Namnet på det Azure Storage-konto som används för beräkning av den bandbredd som används för datareplikering lokalt till Azure. Verktyget överför testdata till det här lagringskontot när bandbredden ska beräknas. |
| -StorageAccountKey | Den kontonyckel som används för åtkomst till lagringskontot. Gå till Azure Portal > Lagringskonton > [lagringskontots namn] > Inställningar > Åtkomstnycklar > Key1 (eller Primär åtkomstnyckel för klassiska lagringskonton). |
| -VMListFile | En fil som innehåller listan med virtuella datorer som ska profileras när bandbredden ska beräknas. Filsökvägen kan vara absolut eller relativ. Den här filen ska innehålla ett datornamn/en IP-adress per rad. Namnen på virtuella datorer i filen ska vara samma som namnen på de virtuella datorerna på vCenter-servern eller ESXi-värden.<br>T.ex. så innehåller filen VMList.txt följande virtuella datorer:<br>virtuell_dator_A <br>10.150.29.110<br>virtuell_dator_B|

Verktyget skapar flera 64 MB stora filer med namnet asrvhdfile<#>.vhd (där # är ett tal) i den angivna katalogen.  Dessa filer överförs till Azure Storage-kontot när dataflödet ska beräknas. När dataflödet har beräknats så tas dessa filer bort både från Azure Storage-kontot och från den lokala servern. Om verktyget av någon anledning skulle avslutas i förtid under beräkningen så kommer de här filerna inte tas bort från Azure Storage eller från den lokala servern, och då måste du ta bort dem manuellt.

Dataflödet beräknas för en viss tidpunkt och är största möjliga dataflöde som Azure Site Recovery kan uppnå under replikeringen givet att alla andra faktorer är oförändrade. Om ett annat program till exempel börjar förbruka mer bandbredd i samma nätverk kommer det faktiska dataflödet att variera under replikeringen. Om du kör kommandot GetThroughput från en konfigureringsserver så kommer verktyget inte att ha någon information om skyddade virtuella datorer eller pågående replikering. Resultatet av mätningen kommer att variera mellan tidpunkter när skyddade virtuella datorer har hög dataomsättning och tidpunkter när omsättningen är låg.  Du bör därför köra verktyget flera gånger under profileringen så att du får förståelse för vilket dataflöde som kan uppnås vid olika tidpunkter. I rapporten visas det senaste uppmätta dataflödet.


##### <a name="example"></a>Exempel
ASRDeploymentPlanner.exe **-Operation** GetThroughput **-Directory**  E:\vCenter1_ProfiledData **-VMListFile** E:\vCenter1_ProfiledData\ProfileVMList1.txt  **-StorageAccountName**  asrspfarm1 **-StorageAccountKey** by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

>[!NOTE]
>
> * Kör verktyget på en server som har samma lagringsutrymme och processorer som konfigurationsservern
>
> * När det gäller replikering ska du tilldela den bandbredd som rekommenderas för att uppfylla RPO-målet 100 % av gångerna. Om inte verktyget rapporterar ökade dataflöden även när du har tilldelat rätt mängd bandbredd kan du kontrollera följande:
>
> a. Kontrollera om det finns någon tjänstkvalitet (QoS) för nätverket som begränsar dataflödet för Azure Site Recovery
>
> b. Kontrollera om valvet för Azure Site Recovery ligger i den närmaste fysiska Microsoft Azure-regionen som stöds, så att du minimerar svarstiden i nätverket
>
> c. Kontrollera den lokala lagringen och försök uppgradera maskinvaran (till exempel vanlig hårddisk till SSD osv.)
>
> d. Ändra inställningarna på processervern för Azure Site Recovery och [öka mängden bandbredd i nätverket som används till replikering](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
>

##<a name="recommendations-with-desired-rpo-as-input"></a>Rekommendationer med önskat RPO-mål som indata

###<a name="profiled-data"></a>Profileringsdata

![Deployment Planner](./media/site-recovery-deployment-planner/profiled-data-period.png)

**Profiled data period** (Profileringsdataperiod) är den period som profileringen kördes under. Som standard används alla profileringsdata till beräkningen i verktyget, om inte rapporten genereras för en viss tidsperiod med hjälp av parametrarna StartDate och EndDate.

**Servernamn** är namnet eller IP-adressen för VMware vCenter-servern eller ESXi-värden vars virtuella datorer rapporten genereras för.

**Desired RPO** (Önskat RPO-mål) är RPO-målet för din distribution. Som standard beräknas vilken nätverksbandbredd som krävs för RPO-värden på 15, 30 och 60 minuter. Värdena på bladet uppdateras baserat på vad du väljer. Om du använde parametern DesiredRPOinMin när du genererade rapporten så visas det här värdet i listrutan Desired RPO (Önskat RPO-mål).

###<a name="profiling-overview"></a>Profileringsöversikt

![Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview.png)

**Total Profiled Virtual Machines** (Totalt antal profilerade virtuella datorer) är det totala antalet virtuella datorer som det finns profileringsdata för. Om VMListFile innehåller namn på virtuella datorer som inte har profilerats så beaktas inte dessa virtuella datorer i rapporten och de ingår inte i värdet för antalet virtuella datorer som profilerats.

**Compatible Virtual Machines** (Kompatibla virtuella datorer) är antalet virtuella datorer som kan skyddas med Azure Site Recovery. Det här är det totala antalet kompatibla virtuella datorer som nödvändig nätverksbandbredd, antal Azure Storage-konton, antal Microsoft Azure-kärnor samt antal konfigurationsservrar och ytterligare processervrar beräknas för. På bladet Compatible VMs (Kompatibla virtuella datorer) i rapporten finns information om de enskilda kompatibla virtuella datorerna.

**Incompatible Virtual Machines** (Inkompatibla virtuella datorer) är antalet profilerade virtuella datorer som inte kan skyddas med Azure Site Recovery. Orsaken till inkompatibiliteten beskrivs i avsnittet Inkompatibla virtuella datorer nedan. Om VMListFile innehåller namn på virtuella datorer som inte har profilerats så ingår inte dessa virtuella datorer i antalet inkompatibla virtuella datorer. Dessa virtuella datorer visas under Data not found (Inga data hittades) i slutet av bladet Incompatible VMs (Inkompatibla virtuella datorer).

**Desired RPO** (Önskat RPO-mål) är ditt önskade RPO-mål i minuter. Rapporten genereras för tre RPO-värden – 15, 30 och 60 minuter, där 15 minuter är standardvärdet. Rekommendationen angående bandbredd i rapporten ändras baserat på vilket alternativ du väljer i listrutan Desired RPO (Önskat RPO-mål) uppe till höger på bladet. Om du har genererat rapporten med ett anpassat värde för parametern "-DesiredRPO" visas det som standardvärde i listrutan Desired RPO (Önskat RPO-mål).

###<a name="required-network-bandwidth-mbps"></a>Required Network Bandwidth (Mbps) (Nödvändig nätverksbandbredd (Mbit/s))

![Deployment Planner](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**To meet RPO 100% of the time:** (För att nå RPO-målet 100 % av gångerna) det här är den bandbredd i Mbit/s du bör allokera om du alltid ska nå ditt RPO-mål. Den här mängden bandbredd måste vara reserverad för stabil deltareplikering av samtliga kompatibla virtuella datorer om du helt ska undvika överträdelser av RPO-målet.

**To meet RPO 90% of the time**: (För att nå RPO-målet 90 % av gångerna) om bandbreddspriserna är för höga eller om du av någon annan anledning inte kan tilldela den bandbredd som krävs för att uppnå RPO-målet 100 % av gångerna så kan du välja att tilldela en bandbredd som gör att du uppnår önskat RPO 90 % av gångerna. I rapporten ges även en ”tänk om”-analys av hur många RPO-överträdelser du kan förvänta dig och deras varaktighet, så att du bättre ska förstå vad som kan hända om du tilldelar den här lägre bandbredden.

**Achieved Throughput:** (Uppnått dataflöde) är dataflödet från servern där du körde kommandot GetThroughput till den Microsoft Azure-region där Azure Storage-kontot är beläget. Värdet är en uppskattning av det dataflöde du kan uppnå när du skyddar de kompatibla virtuella datorerna med Azure Site Recovery, förutsatt att egenskaperna för konfigurationsservern/processervern, lagringen och nätverket är samma som för servern där du körde verktyget.    

När det gäller replikering ska du tilldela den bandbredd som rekommenderas för att uppfylla RPO-målet 100 % av gångerna. Om inte verktyget rapporterar ökade dataflöden även när du har tilldelat rätt mängd bandbredd kan du kontrollera följande:

a.    Kontrollera om det finns någon tjänstkvalitet (QoS) för nätverket som begränsar dataflödet för Azure Site Recovery

b.    Kontrollera om valvet för Azure Site Recovery ligger i den närmaste fysiska Microsoft Azure-regionen som stöds, så att du minimerar svarstiden i nätverket

c.    Kontrollera den lokala lagringen och försök uppgradera maskinvaran (till exempel vanlig hårddisk till SSD osv.)

d. Ändra inställningarna på processervern för Azure Site Recovery och [öka mängden bandbredd i nätverket som används till replikering](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Om du kör verktyget på en konfigurationsserver/processerver som redan innehåller skyddade virtuella datorer så ska du köra verktyget några gånger, eftersom värdet för uppnått dataflöde ändras beroende på hur mycket data som omsätts vid varje given tidpunkt.

För alla företagsdistributioner av Azure Site Recovery bör du använda [ExpressRoute](https://aka.ms/expressroute).

###<a name="required-azure-storage-accounts"></a>Required Azure Storage Accounts (Nödvändiga Azure Storage-konton)
I det här diagrammet visas hur många Azure Storage-konton (standard och premium) som behövs till att skydda alla kompatibla virtuella datorer.  Klicka på [Recommended VM placement plan](site-recovery-deployment-planner.md#vm-storage-placement) (Rekommenderad plan för VM-placering) om du vill se vilket lagringskonto som bör användas för de olika virtuell datorerna.  

![Deployment Planner](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

###<a name="required-number-of-azure-cores"></a>Required Number of Azure Cores (Nödvändigt antal Azure-kärnor)
Det här är det totala antalet kärnor som ska etableras innan redundansväxling eller redundanstest av alla kompatibla virtuella datorer. Om det inte finns tillräckligt många kärnor tillgängliga i prenumerationen så kan inte Azure Site Recovery skapa virtuella datorer vid redundanstestet eller redundansväxlingen.

![Deployment Planner](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

###<a name="required-on-premises-infrastructure"></a>Required On-premises Infrastructure (Krav på lokal infrastruktur)
Det här är det totala antalet konfigurationsservrar och ytterligare processervrar som måste konfigureras för att skydda alla kompatibla virtuella datorer. Baserat på angivna [gränser](https://aka.ms/asr-v2a-on-prem-components) för den största konfigurationen, antingen i dataomsättning per dag eller största antal skyddade virtuella datorer (förutsatt i genomsnitt tre diskar per virtuell dator), så rekommenderar verktyget ytterligare servrar beroende på vilken gräns som uppnås först på konfigurationsservern eller processervern. Du hittar detaljerad information om den totala dataomsättningen per dag och det totala antalet skyddade diskar på bladet [Indata](site-recovery-deployment-planner.md#input).

![Deployment Planner](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

###<a name="what-if-analysis"></a>Tänk om-analys
I den här analysen beskrivs hur många överträdelser som kan inträffa under profileringsperioden när du tilldelar en lägre bandbredd för att önskat RPO-mål ska uppfyllas 90 % av gångerna. En eller flera RPO-överträdelser kan inträffa en viss dag. I diagrammet visas dagens största RPO-överträdelse.
Utifrån den här analysen kan du avgöra om du kan godta antalet RPO-överträdelser och dagens största RPO-överträdelse sett till den lägre bandbredden. Om värdena är godtagbara kan du allokera den lägre bandbredden för replikering. Annars tilldelar du den bandbredd som rekommenderas för att du ska uppnå önskat RPO-mål 100 % av gångerna.

![Deployment Planner](./media/site-recovery-deployment-planner/what-if-analysis.png)

###<a name="recommended-vm-batch-size-for-initial-replication"></a>Recommended VM batch size for initial replication (Rekommenderad VM-batchstorlek för den initiala replikeringen)
I det här avsnittet ges en rekommendation för hur många virtuella datorer som kan skyddas parallellt så att den initiala replikeringen kan slutföras inom 72 timmar (det här värdet kan konfigureras, du ändrar det med parametern GoalToCompleteIR när du genererar rapporten) med den föreslagna bandbredden för att uppnå önskat RPO-mål 100 % av gångerna.  I diagrammet visas olika bandbreddsvärden och beräknad batchstorlek för virtuella datorer där den initiala replikeringen kan slutföras inom 72 timmar baserat på den genomsnittliga storleken för de virtuella datorerna bland alla kompatibla virtuella datorer.  

I Public Preview anger inte rapporten vilka virtuella datorer som ska ingå i en batch. Du kan använda diskstorleken som visas på bladet Compatible VMs (Kompatibla virtuella datorer) till att se de virtuella datorernas storlek och välja virtuella datorer till en batch, eller så kan du välja datorer sett till kända arbetsbelastningar.  Tidsåtgången för den inledande replikeringen ändras proportionellt med avseende på diskstorlek, förbrukat diskutrymme och tillgängligt dataflöde i nätverket.

![Deployment Planner](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

###<a name="growth-factor-and-percentile-values-used"></a>Tillväxtfaktor och percentilvärde som används
I det här avsnittet längst ned på bladet visas vilket percentilvärde som använts för prestandaräknarna för de profilerade virtuella datorerna (standardvärdet är den 95:e percentilen) och vilken tillväxtfaktor i % som använts i alla beräkningar (standardvärdet är 30 %).

![Deployment Planner](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

##<a name="recommendations-with-available-bandwidth-as-input"></a>Rekommendationer med tillgänglig bandbredd som indata

![Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

Det kan vara så att du vet att du inte kan tilldela mer än x Mbit/s bandbredd för Azure Site Recovery-replikeringen. Du kan välja att ange tillgänglig bandbredd i verktyget (med parametern -Bandwidth när du genererar rapporten) och då få se vilket RPO-mål i minuter du kan uppnå. Utifrån det här möjliga RPO-målet kan du avgöra om du måste tilldela ytterligare bandbredd eller om du nöjer dig med en lösning för haveriberedskap med det aktuella RPO-målet.

![Deployment Planner](./media/site-recovery-deployment-planner/achievable-rpos.png)

##<a name="input"></a>Indata
På sidan indata ges en översikt över den profilerade VMware-miljön.

![Deployment Planner](./media/site-recovery-deployment-planner/Input.png)

**Startdatum och Slutdatum** är start- och slutdatum för de profileringsdata som ingår i rapportgenereringen. Som standard är startdatumet det datum då profileringen startades och slutdatumet är det datum när profileringen avslutades.  Om du angav parametrarna -StartDate och -EndDate när du genererade rapporten visas dessa värden. Startdatum och Slutdatum: det här är start- och slutdatum för de profileringsdata som ingår i rapportgenereringen. Som standard är startdatumet det datum då profileringen startades och slutdatumet är det datum när profileringen avslutades.  Om du angav parametrarna -StartDate och -EndDate när du genererade rapporten visas dessa värden.

**Total number of profiling days** (Totalt antal dagar för profilering) är det totala antalet dagar mellan start- och slutdatumen som rapporten genererats för. Total number of profiling days (Totalt antal dagar för profilering) är det totala antalet dagar mellan start- och slutdatumen som rapporten genererats för.

**Number of compatible virtual machines** (Antal kompatibla virtuella datorer) är det totala antalet kompatibla virtuella datorer som nödvändig nätverksbandbredd, antal Azure Storage-konton, antal Microsoft Azure-kärnor samt antal konfigurationsservrar och ytterligare processervrar beräknas för.
Total number of disks across all compatible virtual machines (Totalt antal diskar för samtliga kompatibla virtuella datorer) är det totala antalet diskar för samtliga kompatibla virtuella datorer. Det här värdet används som indata i rekommendationen för antalet konfigurationsservrar och ytterligare processervrar som ska användas i distributionen.

**Average number of disks per compatible virtual machine** (Genomsnittligt antal diskar per kompatibel virtuell dator) är det genomsnittliga antalet diskar för samtliga kompatibla virtuella datorer.

**Average disk size (GB)** (Genomsnittlig diskstorlek (GB)) är den genomsnittliga diskstorleken för samtliga kompatibla virtuella datorer.

**Desired RPO (minutes)** (Önskat RPO-mål (minuter)) är antingen standardvärdet för RPO-mål eller det värde som angavs för parametern -DesiredRPO när rapporten genererades för att uppskatta nödvändig bandbredd.

**Desired bandwidth (Mbps)** (Önskad bandbredd (Mbit/s)) är det värde du angav för parametern -Bandwidth när du genererade rapporten för att uppskatta vilket RPO-mål som kan uppnås.

**Observed typical data churn per day (GB)** (Observerad normal dataomsättning per dag (GB)) är den genomsnittliga dataomsättning som observerats under profileringen. Det här värdet används som indata i rekommendationen för antalet konfigurationsservrar och ytterligare processervrar som ska användas i distributionen.


##<a name="vm-storage-placement"></a>Placering av VM-lagring

![Deployment Planner](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**Disk Storage Type** (Typ av disklagring) är antingen Standard eller Premium och avser det Azure Storage-konto som ska användas för replikering av motsvarande virtuella datorer i kolumnen VMs to Place (Virtuella datorer att placera ut).

**Suggested Prefix** (Föreslaget prefix) är ett prefix på tre tecken som du kan använda till att namnge Azure Storage-kontot. Du kan alltid använda ett eget prefix, men förslaget i verktyget följer [riktlinjerna för namngivning av partitioner i Azure Storage-konton](https://aka.ms/storage-performance-checklist).

**Suggested Account Name** (Föreslaget kontonamn) anger hur namnet på Azure Storage-kontot skulle se ut med det föreslagna prefixet. Ersätt namnet inom < > med egna indata.

**Log Storage Account:** (Lagringskonto för loggar:) alla replikeringsloggar lagras i ett Azure Storage-konto av standardtyp. För virtuella datorer som replikeras till ett Azure Storage-konto av premiumtyp måste du etablera ytterligare ett Azure Storage-konto av standardtyp som ska innehålla loggarna. Flera konton för premiumreplikering kan använda samma standardkonto för logglagring. Virtuella datorer som replikeras till lagringskonton av standardtyp använder samma konto för loggarna.

**Suggested Log Account Name** (Föreslaget namn på loggkonto) anger hur namnet på Azure Storage-kontot för loggar skulle se ut med det föreslagna prefixet. Ersätt namnet inom < > med egna indata.

**Placement Summary** (Placeringsöversikt) innehåller en översikt över den totala VM-belastningen på Azure Storage-kontot vid replikeringen samt vid redundanstest/redundansväxling. I översikten ingår det totala antalet virtuella datorer som har mappats till Azure Storage-kontot, totalt antal IOPS för de virtuella datorer som placerats i Azure Storage-kontot, totalt antal skrivoperationer (replikering), totalt etablerad storlek sett till alla diskar och det totala antalet diskar.

**Virtual Machines to Place** (Virtuella datorer att placera ut) innehåller de virtuella datorer som ska placeras i det angivna Azure Storage-kontot för att prestanda och användningsgrad ska vara optimala.

## <a name="compatible-vms"></a>Compatible VMs (Kompatibla virtuella datorer)
![Deployment Planner](./media/site-recovery-deployment-planner/compatible-vms.png)

**VM Name** (Namn på virtuell dator) är namnet eller IP-adressen för den virtuella datorn i filen VMListFile när rapporten genererades. I den här kolumnen visas även de diskar (VMDK:er) som är kopplade till de virtuella datorerna. Virtuella datorer på en vCenter med dubbla namn eller IP-adresser anges med ESXi-värdnamn för att skilja mellan varje virtuell dator. ESXi-värden i listan är den värd där den virtuella datorn placerades när verktyget identifierade den för första gången under profileringsperioden.

**VM Compatibility** (VM-kompatibilitet) har två värden – Ja/Ja*. Ja* är till för de fall där den virtuella datorn passar för [premiumlagring i Azure Storage](https://aka.ms/premium-storage-workload) eftersom diskens omsättning/IOPS hamnar i kategorin P20 eller P30, men där diskens storlek gör att den hamnar i P10 eller P20. Azure Storage avgör vilken typ av premiumlagring en disk ska mappas till baserat på storleken: < 128 GB är en P10, 128 till 512 GB är en P20 och 512 GB till 1 023 GB är en P30. Så om arbetsbelastningens egenskaper för en disk gör den lämplig för P20 eller P30, men storleken mappas till en lägre premiumlagringstyp, markeras den virtuella datorn med Ja* i verktyget. Du rekommenderas då att antingen ändra källdiskens storlek så att den passar den rekommenderade premiumlagringen eller att ändra måldisktypen efter redundansväxling.
Lagringstyp är antingen standard eller premium.

**Suggested Prefix** (Föreslaget prefix) är ett prefix på tre tecken för Azure Storage-kontot

**Lagringskonto** är namnet med det föreslagna prefixet

**R/W IOPS (with Growth Factor)** (IOPS (med tillväxtfaktor)) är den högsta IOPS-arbetsbelastningen på disken (standardvärdet är den 95:e percentilen), inklusive faktorn för framtida tillväxt (standardvärdet är 30 %). Observera att det totala antalet IOPS för den virtuella datorn inte alltid är summan av de enskilda diskarnas IOPS, eftersom den virtuella datorns högsta IOPS är den högsta summan av de enskilda diskarnas IOPS under varje minut av profileringsperioden.

**Data Churn in Mbps (with Growth Factor)** (Dataomsättning i Mbit/s (med tillväxtfaktor)) är den högsta kundomsättningsfrekvensen på disken (standardvärdet är den 95:e percentilen) inklusive faktorn för framtida tillväxt (standardvärdet är 30 %). Observera att den totala dataomsättningen för den virtuella datorn inte alltid är summan av de enskilda diskarnas dataomsättning, eftersom den virtuella datorns högsta dataomsättning är den högsta summan av de enskilda diskarnas dataomsättning under varje minut av profileringsperioden.

**Azure VM Size** (Storlek för virtuell Azure-dator) är lämplig storlek för den virtuella Azure Compute-datorn för den här lokala virtuella datorn. Den här mappningen görs baserat på den lokala virtuella datorns minne, antal diskar/kärnor/nätverkskort och IOPS. Rekommendationen är alltid den minsta virtuella Azure-dator som matchar alla egenskaper hos den lokala virtuella datorn.

**Antal diskar** är det totala antalet diskar (VMDK:er) i den virtuella datorn.

**Diskstorlek (GB)** är den totala etablerade storleken för alla diskar i den virtuella datorn. Storleken för de enskilda diskarna i den virtuella datorn visas också i verktyget.

**Kärnor** är antalet processorkärnor i den virtuella datorn.

**Minne (MB)** är mängden RAM-minne i den virtuella datorn.

**Nätverkskort** är antalet nätverkskort i den virtuella datorn.

##<a name="incompatible-vms"></a>Incompatible VMs (Inkompatibla virtuella datorer)

![Deployment Planner](./media/site-recovery-deployment-planner/incompatible-vms.png)

**VM Name** (Namn på virtuell dator) är namnet eller IP-adressen för den virtuella datorn i filen VMListFile när rapporten genererades. I den här kolumnen visas även de diskar (VMDK:er) som är kopplade till de virtuella datorerna. Virtuella datorer på en vCenter med dubbla namn eller IP-adresser anges med ESXi-värdnamn för att skilja mellan varje virtuell dator. ESXi-värden i listan är den värd där den virtuella datorn placerades när verktyget identifierade den för första gången under profileringsperioden.

**VM Compatibility** (VM-kompatibilitet) anger varför den här virtuella datorn inte kan skyddas med Azure Site Recovery. Orsakerna beskrivs per inkompatibel disk i den virtuella datorn och kan vara något av följande, baserat på de angivna [gränserna](https://aka.ms/azure-storage-scalbility-performance) för Azure Storage.

* Diskstorlek > 1 023 GB – Azure Storage har för närvarande inte stöd för diskar som är större än 1 TB
* Den totala VM-storleken (replikering + redundanstest) överskrider storleksgränsen för Azure Storage-konton (35 TB) – detta inträffar vanligtvis när en av diskarna i den virtuella datorn har prestandaegenskaper som överskrider de maxvärden som stöds i Microsoft Azure/Azure Site Recovery gällande standardlagring, vilket gör att den virtuella datorn hamnar i segmentet för premiumlagring. Maxgränsen för ett Azure Storage-konto av premiumtyp är däremot 35 TB, och det går inte att skydda en enda virtuell dator över flera lagringskonton. När du kör ett redundanstest för en skyddad virtuell dator ska du också tänka på att det körs i samma lagringskonto som replikeringen, så det behövs ett utrymme som är dubbelt så stort som disken om replikeringen och redundanstestet ska kunna köras parallellt.
* Käll-IOPS överskrider IOPS-gränsen för Azure Storage på 5 000 per disk
* Käll-IOPS överskrider IOPS-gränsen för Azure Storage på 80 000 per virtuell dator
* Den genomsnittliga dataomsättningen för disken överskrider gränsen i Azure Site Recovery på 10 Mbit/s för den genomsnittliga I/O-storleken per disk
* Den totala dataomsättningen för alla diskar i den virtuella datorn överskrider gränsen i Azure Site Recovery på 54 Mbit/s per virtuell dator
* Genomsnittligt antal effektiva skrivåtgärder (IOPS) överskrider gränsen i Azure Site Recovery på 840 per disk
* Beräknat lagringsutrymme för ögonblicksbilder överskrider gränsen på 10 TB

**R/W IOPS (with Growth Factor)** (IOPS (med tillväxtfaktor)) är den högsta IOPS-arbetsbelastningen på disken (standardvärdet är den 95:e percentilen), inklusive faktorn för framtida tillväxt (standardvärdet är 30 %). Observera att det totala antalet IOPS för den virtuella datorn inte alltid är summan av de enskilda diskarnas IOPS, eftersom den virtuella datorns högsta IOPS är den högsta summan av de enskilda diskarnas IOPS under varje minut av profileringsperioden.

**Data Churn in Mbps (with Growth Factor)** (Dataomsättning i Mbit/s (med tillväxtfaktor)) är den högsta kundomsättningsfrekvensen på disken (standardvärdet är den 95:e percentilen) inklusive faktorn för framtida tillväxt (standardvärdet är 30 %). Observera att den totala dataomsättningen för den virtuella datorn inte alltid är summan av de enskilda diskarnas dataomsättning, eftersom den virtuella datorns högsta dataomsättning är den högsta summan av de enskilda diskarnas dataomsättning under varje minut av profileringsperioden.

**Antal diskar** är det totala antalet diskar (VMDK:er) i den virtuella datorn.

**Diskstorlek (GB)** är den totala etablerade storleken för alla diskar i den virtuella datorn. Storleken för de enskilda diskarna i den virtuella datorn visas också i verktyget.

**Kärnor** är antalet processorkärnor i den virtuella datorn.

**Minne (MB)** är mängden RAM-minne i den virtuella datorn.

**Nätverkskort** är antalet nätverkskort i den virtuella datorn.


##<a name="azure-site-recovery-limits"></a>Gränser för Azure Site Recovery

**Replication Storage Target** (Lagringsmål för replikering) | **Average Source Disk I/O Size** (Genomsnittlig I/O-storlek för källdisk) |**Average Source Disk Data Churn** (Genomsnittlig dataomsättning för källdisk) | **Total Source Disk Data Churn Per Day** (Total dataomsättning per dag för källdisk)
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10-disk | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10-disk | 16 kB | 4 MB/s |    336 GB per disk
Premium P10-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-/P30-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-/P30-disk | 16 kB eller mer |10 MB/s    | 842 GB per disk


Det här är genomsnittliga värden baserade på en I/O-överlappning på 30 %. Azure Site Recovery kan hantera högre dataflöden med annan överlappning, större skrivningsstorlek och verkligt I/O-beteende under arbetsbelastningen. Värdena ovan förutsätter en typisk eftersläpning på ~ 5 minuter, d.v.s. att överförda data kommer att bearbetas och en återställningspunkt skapas inom 5 minuter.

De gränser som anges ovan är baserade på våra tester, men de täcker inte alla möjliga kombinationer av program-I/O. De faktiska värdena varierar beroende på blandningen av I/O i ditt program. Du får bästa resultat och en bättre prestandabild om du fortsätter att köra omfattande programtester med redundanstester även efter distributionsplaneringen.

## <a name="how-to-update-the-deployment-planner"></a>Hur uppdaterar man Deployment Planner?
[Ladda ned](site-recovery-deployment-planner.md#download) den senaste versionen av Azure Site Recovery Deployment Planner. Kopiera zip-filen till en server där du vill köra. Extrahera zip-filen.
Om du redan har en tidigare version av Deployment Planner och profilering pågår behöver du inte stoppa profileringen, om det inte är så att den nya versionen har en profileringskorrigering. Om versionen innehåller korrigeringar i profileringskomponenten rekommenderar vi att du stoppar profilering med den äldre versionen och startar profileringen igen med den nya versionen. Tänk på att när du startar profilering med den nya versionen måste du skicka samma katalogsökväg för utdata så att verktyget bifogar profildata på befintliga filer och så att en fullständig uppsättning profilerade data används när rapporter skapas. Om du skickar en annan utdatakatalog kommer nya filer att skapas och gamla profilerade data kan inte användas för att skapa rapporter.<br> Alla uppdateringar är kumulativa uppdateringar med en zip-fil. Du behöver inte kopiera de nya versionsfilerna till mappen med den tidigare versionen för att använda den. Du kan använda en ny mapp för den.


##<a name="version-history"></a>Versionshistorik
### <a name="11"></a>1.1
Uppdaterat den: 09-mar-2017 <br>

Åtgärdat följande problem<br>

* Kan inte profilera virtuella datorer om vCenter har två eller fler virtuella datorer med samma namn/IP-adress hos olika ESXi-värdar.<br>
* Kopiering och sökning har inaktiverats för blad för kompatibla virtuella datorer och inkompatibla virtuella datorer.


### <a name="10"></a>1.0 
Uppdaterat den: 23-feb-2017 

Azure Site Recovery Deployment Planner Public Preview 1.0 har följande kända problem som kommer att åtgärdas i senare uppdateringar.

* Verktyget fungerar bara för scenariot VMware till Azure, inte för distributioner med Hyper-V till Azure. För scenarier av typen Hyper-V till Azure ska du använda [kapacitetsplaneringsverktyget för Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
* Åtgärden GetThroughput stöds inte i Microsoft Azure-regionerna US Government och Kina.
* Verktyget kan inte profilera virtuella datorer om vCenter har två eller fler virtuella datorer med samma namn/IP-adress hos olika ESXi-värdar. I den här versionen profileras inte virtuella datorer med dubbletter av namn/IP-adress i VMListFile. Lösningen är att profilera virtuella datorer med en ESXi-värd i stället för med vCenter-servern. Du måste köra en instans för varje ESXi-värd.

