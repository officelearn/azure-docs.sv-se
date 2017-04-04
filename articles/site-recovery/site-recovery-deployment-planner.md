---
title: "Azure Site Recovery-kapacitetsplaneraren för VMware till Azure| Microsoft Docs"
description: "Det här är användarhandboken för Azure Site Recovery-kapacitetsplaneraren."
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
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 431f73e1be45dec9aa0fe186cb22078f8d95588d
ms.lasthandoff: 03/28/2017


---
# <a name="azure-site-recovery-deployment-planner"></a>Kapacitetsplaneraren i Azure Site Recovery
Det här artikeln är användarhandboken för Azure Site Recovery för produktionsdistributioner av VMware till Azure.

## <a name="overview"></a>Översikt

Innan du börjar skyddar virtuella VMware-datorer med hjälp av Site Recovery måste du allokera tillräckligt mycket bandbredd sett till din dagliga dataändringshastighet så att du når önskat mål för återställningspunkt. Du måste distribuera tillräckligt många konfigurationsservrar och processervrar lokalt.

Du måste också skapa rätt typ och antal Azure-mållagringskonton. Du skapar antingen Standard Storage- eller Premium Storage-konton, och väger in tillväxt på källproduktionsservrarna på grund av ökad användning under en viss tid. Du väljer lagringstyp per virtuell dator baserat på arbetsbelastningens egenskaper (exempelvis läs- och skrivbehörighet, i/o-åtgärder per sekund [IOPS] eller dataomsättningen) och Site Recovery-begränsningarna.

Site Recovery Deployment Planner Public Preview är ett kommandoradsverktyg som för närvarande bara är tillgängligt för scenariot med VMware till Azure. Du kan profilera dina virtuella VMware-datorer via fjärranslutning med det här verktyget (utan att produktionen påverkas alls) så att du får en uppfattning om vilken bandbredd och hur stort Azure Storage-lagringsutrymme som kommer att behövas för replikering och redundansväxling. Du kan köra verktyget utan att installera alla Site Recovery-komponenter lokalt. Du får dock bättre dataflödesresultat om du kör planeringsverktyget på en Windows-server som uppfyller minimikraven för den konfigurationsserver för Site Recovery som du kommer att ta i drift som ett av de första stegen i produktionsdistributionen.

Du kan se följande information i verktyget:

**Utvärdering av kompatibilitet**

* En utvärdering av om den virtuella datorn stöds som baseras på antalet diskar, diskstorleken, IOPS och dataomsättningen
* Beräknad nätverksbandbredd som krävs för deltareplikering

**Nätverkets bandbreddsbehov kontra utvärdering av återställningspunktmål**

* Beräknad nätverksbandbredd som krävs för deltareplikering
* Dataflödet som Site Recovery kan få från lokala datorer till Azure
* Antalet virtuella datorer att bearbeta per batch sett till den uppskattade bandbredd som krävs för att slutföra den inledande replikeringen inom viss tid

**Krav på infrastruktur för Azure**

* Typ av lagringsutrymme (Standard Storage- eller Premium Storage-konto) som krävs för varje virtuell dator
* Totalt antal Standard Storage- och Premium Storage-konton som ska konfigureras för replikering
* Namnförslag för lagringskonton baserade på riktlinjer för Azure Storage
* Lagringskontots placering för alla virtuella datorer
* Antalet Azure-kärnor som ska etableras innan redundanstest/redundansväxling för prenumerationen
* Den rekommenderade storleken på den virtuella Azure-datorn för varje lokal virtuell dator

**Krav på lokal infrastruktur**
* Antalet konfigurationsservrar och processervrar som måste distribueras lokalt

>[!IMPORTANT]
>
>Eftersom användningen troligtvis ökar med tiden utförs alla föregående verktygsberäkningar med förutsättningen att en 30-procentig tillväxtfaktor för arbetsbelastningen egenskaper och ett 95-procentigt percentilvärde används för alla profileringsmått (skrivbar IOPS, omsättning och så vidare). Båda parametrarna (tillväxtfaktorn och percentilberäkningen) kan konfigureras. Om du vill veta mer om tillväxtfaktor, se avsnittet ”Överväganden för tillväxtfaktorer”. Mer information om percentilvärdet finns i avsnittet ”Percentilvärdet som används för beräkningen”.
>

## <a name="requirements"></a>Krav
Verktyget har två huvudfaser: profilering och rapportgenerering. Det finns också ett tredje alternativ som endast beräknar dataflödet. Kraven för servern som profilering och dataflödesmätning initieras från visas i följande tabell:

| Serverkrav | Beskrivning|
|---|---|
|Profilering och mätning av dataflöde| <ul><li>Operativsystem: Microsoft Windows Server 2012 R2<br>(matchar helst åtminstone [storleksrekommendationerna för konfigurationsservern](https://aka.ms/asr-v2a-on-prem-components))</li><li>Datorkonfiguration: 8 virtuella processorer, 16 GB RAM-minne, 300 GB hårddisk</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)</li><li>[Microsoft Visual C++ Redistributable for Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Internetåtkomst till Azure från den här servern</li><li>Azure Storage-konto</li><li>Administratörsbehörighet till servern</li><li>Minst 100 GB ledigt diskutrymme (förutsatt 1 000 virtuella datorer med ett medeltal av de tre diskar vardera, profilerade under 30 dagar)</li><li>Nivåinställningarna för VMware vCenter-statistik bör anges till 2 eller hög nivå</li></ul>|
| Rapportgenerering | En Windows-dator eller Windows Server med Microsoft Excel 2013 eller senare |
| Användarbehörigheter | Läsbehörighet för det användarkonto som ska användas för åtkomst till VMware vCenter-servern/VMware vSphere ESXi-värden under profilering |

> [!NOTE]
>
>Verktyget kan enbart profilera virtuella datorer med VMDK- och RDM-diskar. Den kan inte profilera virtuella datorer med iSCSI- eller NFS-diskar. Även om Site Recovery har stöd för iSCSI- och NFS-diskar för VMware-servrar kan inte verktyget se dessa disktyper eftersom kapacitetsplaneraren inte körs i gästen och profileringen bara utförs med prestandaräknare för vCenter.
>

## <a name="download-and-extract-the-public-preview"></a>Hämta och extrahera den offentliga förhandsutgåvan
1. Ladda ned den senaste versionen av [Site Recovery Deployment Planner Public Preview](https://aka.ms/asr-deployment-planner).  
Verktyget är paketerat i en komprimerad mapp. Den aktuella versionen av verktyget har endast stöd för scenariot med VMware till Azure.

2. Kopiera den komprimerade mappen till den Windows Server som du vill köra verktyget från.  
Du kan köra verktyget från Windows Server 2012 R2 om servern har nätverksåtkomst för att ansluta till vCenter-servern/vSphere ESXi-värden som innehåller de virtuella datorer som ska profileras. Vi rekommenderar dock att du kör verktyget på en server vars maskinvarukonfiguration uppfyller [riktlinjen för förändring av storleken på konfigurationsservrar](https://aka.ms/asr-v2a-on-prem-components). Om du redan har distribuerat Site Recovery-komponenter lokalt bör du köra verktyget från konfigurationsservern.

 Vi rekommenderar att du har samma maskinvarukonfiguration som konfigurationsservern (som har en inbyggd processerver) på den server där du kör verktyget. En sådan konfiguration garanterar att det uppnådda genomflödet som verktyget rapporterar matchar det faktiska dataflöde som Site Recovery kan uppnå under replikeringen. Dataflödesberäkningen är beroende av den tillgängliga nätverksbandbredden och maskinvarukonfigurationen (processor, lagringsutrymme och så vidare) på servern. Om du kör verktyget från andra servrar beräknas dataflödet från den här servern till Microsoft Azure. Också eftersom maskinvarukonfigurationen på servern kan skilja sig från konfigurationsservern kan det dataflöde som verktyget rapporterar vara felaktigt.

3. Extrahera .zip-filen.  
Mappen innehåller flera filer och undermappar. Den körbara filen är ASRDeploymentPlanner.exe i den överordnade mappen.

    Exempel:  
    Kopiera .zip-filen till enhet E:\ och packa upp den.
   E:\ASR Deployment Planner-Preview_v1.1.zip

    E:\ASR Deployment Planner-Preview_v1.1\ ASR Deployment Planner-Preview_v1.1\ ASRDeploymentPlanner.exe

## <a name="capabilities"></a>Funktioner
Du kan köra kommandoradsverktyget (ASRDeploymentPlanner.exe) i någon av följande tre lägen:

1. Profilering  
2. Rapportgenerering
3. Beräkna dataflöde

Först kör du verktyget i profileringsläge för att samla in uppgifter om den virtuella datorns dataomsättning och IOPS. Kör sedan verktyget för att generera rapporten för att bedöma kraven på nätverksbandbredd och lagring.

## <a name="profiling"></a>Profilering
I profileringsläge ansluter distributionskapacitetsplaneraren till vCenter-servern/vSphere ESXi-värden och samlar in prestandadata om den virtuella datorn.

* Profilering påverkar inte prestanda hos de virtuella produktionsdatorerna eftersom ingen direktanslutning upprättas till dem. Alla prestandadata samlas in från vCenter-servern/vSphere ESXi-värden.
* Verktyget skickar frågor till vCenter-servern/vSphere EXSi-värden var 15:e minut, så att servern ska påverkas minimalt av profileringen. Frågeintervallet äventyrar dock inte profileringens noggrannhet eftersom verktyget lagrar prestandaräknardata varje minut.

### <a name="create-a-list-of-vms-to-profile"></a>Skapa en lista över virtuella datorer att profilera
Du behöver först en lista över de virtuella datorer som ska profileras. Du kan hämta namnen på alla virtuella datorer på en vCenter-server/vSphere ESXi-värd med hjälp av VMware vSphere PowerCLI-kommandon i följande procedur. Alternativt kan du lista de egna namnen eller IP-adresserna till de virtuella datorer som du vill profilera manuellt i en fil.

1. Logga in till den virtuella dator som VMware vSphere PowerCLI är installerad på.
2. Öppna VMware vSphere PowerCLI-konsolen.
3. Se till att körningsprincipen är aktiverad för skriptet. Om den är inaktiverad startar du VMware vSphere PowerCLI-konsolen i administratörsläge och aktiverar den med följande kommando:

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4. Kör de två kommandona i listan här för att hämta alla namnen på virtuella datorer på en vCenter-server/sShere ESXi-värd och spara den i en txt-fil.
Ersätt &lsaquo;servernamn&rsaquo;, &lsaquo;användarnamn&rsaquo;, &lsaquo;lösenord&rsaquo; och &lsaquo;utdatafil.txt&rsaquo; med egna värden.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>

5. Öppna utdatafilen i Anteckningar och kopiera sedan namnen på alla virtuella datorer som du vill profilera till en annan fil (till exempel ProfileVMList.txt), med ett namn på en virtuell dator per rad. Den här filen används som indata för parametern *-VMListFile* i kommandoradsverktyget.

    ![Lista med namn på virtuella datorer i kapacitetsplaneraren](./media/site-recovery-deployment-planner/profile-vm-list.png)

### <a name="start-profiling"></a>Starta profilering
När du har skapat listan med virtuella datorer att profilera kan du köra verktyget i profileringsläge. Här är listan med obligatoriska och valfria parametrar när du ska köra verktyget i profileringsläge.

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| Parameternamn | Beskrivning |
|---|---|
| -Operation | StartProfiling |
| -Server | Fullständigt domännamn eller IP-adress för den vCenter-server/vSphere ESXi-värd vars virtuella datorer ska profileras.|
| -User | Användarnamn för anslutning till vCenter-servern/vSphere ESXi-värden. Användaren måste minst ha läsbehörighet.|
| -VMListFile |    En fil som innehåller en lista över virtuella datorer som ska profileras. Filsökvägen kan vara absolut eller relativ. Den här filen ska innehålla ett virtuellt datornamn/en IP-adress per rad. Namnen på de virtuella datorerna i filen ska vara detsamma som namnen på de virtuella datorerna på vCenter-servern/vSphere ESXi-värden.<br>Filen VMList.txt innehåller exempelvis följande virtuella datorer:<ul><li>virtuell_dator_A</li><li>10.150.29.110</li><li>virtuell_dator_B</li><ul> |
| -NoOfDaysToProfile | Antal dagar som profileringen ska köras. Du bör köra profileringen i minst 15 dagar så att du fångar upp mönster för arbetsbelastningen i din miljö under den angivna perioden och kan generera en korrekt rekommendation. |
| -Directory | (Valfritt) UNC (Universal Naming Convention) eller lokal katalogsökväg för lagring av de profildata som genereras under profileringen. Om inget katalognamn anges används katalogen ”ProfiledData” under den aktuella sökvägen som standardkatalog. |
| -Password | (Valfritt) Lösenord för att ansluta till vCenter-servern/vSphere ESXi-värden. Om du inte anger något värde nu uppmanas du att ange det när kommandot körs.|
| -StorageAccountName | (Valfritt) Namnet på det lagringskonto som används för beräkning av dataflödet som kan uppnås för datareplikering lokalt till Azure. Verktyget överför testdata till det här lagringskontot när dataflödet ska beräknas.|
| -StorageAccountKey | (Valfritt) Den lagringskontonyckel som används för åtkomst till lagringskontot. Gå till Azure Portal > Lagringskonton > <*[lagringskontots namn]*> > Inställningar > Åtkomstnycklar > Key1 (eller primär åtkomstnyckel för det klassiska lagringskontot). |

Vi rekommenderar att du profilerar dina virtuella datorer under minst 15 till 30 dagar. ASRDeploymentPlanner.exe körs under hela profileringsperioden. Profileringstiden anges i dagar i verktyget. Om du vill profilera i några timmar eller minuter för att snabbtesta verktyget måste du omvandla tiden till motsvarande antal dagar i den offentliga förhandsutgåvan. Om du till exempel vill profilera i 30 minuter ska du ange 30/(60 * 24) = 0,021 dagar. Minsta tillåtna profileringstid är 30 minuter.

Under profileringen kan du välja att skicka namn och nyckel för ett lagringskonto om du vill se vilket dataflöde som Site Recovery kan uppnå för replikeringen från konfigurationsservern/processervern till Azure. Om du inte skickar namn och nyckel för ett lagringskonto under profileringen beräknar inte verktyget det dataflöde som kan uppnås.

Du kan köra flera instanser av verktyget för olika uppsättningar av virtuella datorer. Se till att det inte finns dubbletter av namn på virtuella datorer i profileringsuppsättningarna. Till exempel kanske du har profilerat tio virtuella datorer (VM1–VM10). Efter några dagar vill du profilera ytterligare fem virtuella datorer (VM11–VM15), och då kan du köra verktyget från en annan kommandotolk för den andra uppsättningen av virtuella datorer (VM11–VM15). Se till att den andra uppsättningen av virtuella datorer inte innehåller något av namnen på de virtuella datorerna från den första profileringsinstansen, eller använd en annan utdatakatalog för den andra körningen. Om två instanser av verktyget används för profilering av samma virtuella datorer och samma utdatakatalog används kommer den genererade rapporten att vara felaktig.

Den virtuella datorns konfiguration inhämtas en gång i början av profileringsåtgärden och lagras i en fil med namnet VMDetailList.xml. Den här informationen används när rapporten genereras. Ändringar i den virtuella datorkonfigurationen (till exempel ett ökat antal kärnor, diskar och nätverkskort) från början till slutet av profileringen registreras inte. Om konfigurationen för en profilerad virtuell dator ändrats under profileringen finns det en lösning i den offentliga förhandsutgåvan för att hämta den senaste virtuella datorinformationen när rapporten ska genereras:

* Säkerhetskopiera filen VMdetailList.xml och ta bort den från dess nuvarande plats.
* Skicka argumenten -User och -Password när rapporten ska genereras.

Profileringskommandot genererar flera filer i profileringskatalogen. Ta inte bort några filer, eftersom detta påverkar rapportgenereringen.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Exempel 1: Profilera virtuella datorer i 30 dagar och beräkna dataflödet från lokala datorer till Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Exempel 2: Profilera virtuella datorer i 15 dagar

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-1-hour-for-a-quick-test-of-the-tool"></a>Exempel 3: Profilera virtuella datorer under 1 timme för ett snabbtest av verktyget
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  0.04  -User vCenterUser1
```

>[!NOTE]
>
>* Om servern där verktyget körs startas om eller kraschar, eller om du avslutar verktyget med Ctrl + C, bevaras profileringsdata. Du riskerar dock att förlora de senaste 15 minuternas profileringsdata. I en sådan instans måste du köra om verktyget i profileringsläge när servern startas igen.
>* När du skickar namn och nyckel för ett lagringskonto mäter verktyget dataflödet vid profileringens sista steg. Om verktyget avslutas innan profileringen har slutförts normalt beräknas inte dataflödet. Du kan hitta dataflödet innan du genererar rapporten genom att köra åtgärden GetThroughput från kommandotolken. Annars innehåller inte den genererade rapporten dataflödesinformationen.


## <a name="generate-a-report"></a>Generera en rapport
Verktyget genererar en makroaktiverad Microsoft Excel-fil (XLSM) som rapportutdata med en sammanfattning av alla distributionsrekommendationer. Rapporten har namnet DeploymentPlannerReport_<*unik numerisk identifierare*>.xlsm och placeras i den angivna katalogen.

När profileringen är färdig kan köra du verktyget i läget för rapportgenerering. Följande tabell innehåller en lista med obligatoriska och valfria verktygsparametrar som ska köras i läget för rapportgenerering.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Parameternamn | Beskrivning |
|-|-|
| -Operation | GenerateReport |
| -Server |  Fullständigt domännamn eller IP-adress för vCenter-/vSphere-servern (använd samma namn eller IP-adress som du använde vid profileringen) där de profilerade virtuella datorer som rapporten ska gälla finns. Tänk på att om du har använt en vCenter-server vid profileringen kan du inte använda en vSphere-server till rapportgenerering och tvärtom.|
| -VMListFile | Den fil som innehåller listan över profilerade virtuella datorer som rapporten ska genereras för. Filsökvägen kan vara absolut eller relativ. Den här filen ska innehålla ett virtuellt datornamn eller en IP-adress per rad. Namnen på de virtuella datorerna i filen ska vara identiska med namnen på de virtuella datorerna på vCenter-servern/vSphere ESXi-värden, och vara desamma som vid profileringen.|
| -Directory | (Valfritt) UNC eller lokal katalogsökväg där profileringsdata (filer som genererats under profileringen) lagras. Dessa data krävs när rapporten ska genereras. Om du inte anger något namn används katalogen ProfiledData. |
| -GoalToCompleteIR | (Valfritt) Antalet timmar som den inledande replikeringen av de profilerade virtuella datorerna måste slutföras på. I den genererade rapporten anges det hur många virtuella datorer som den inledande replikeringen kan slutföras på inom den angivna tiden. Standardvärdet är 72 timmar. |
| -User | (Valfritt) Användarnamn som ska användas för anslutning till vCenter-/vSphere-servern. Namnet används för att hämta den senaste konfigurationsinformationen för de virtuella datorerna, exempelvis antal diskar, antal kärnor och antal nätverkskort som ska användas i rapporten. Om du inte anger något namn används den konfigurationsinformation som samlades in i början av profileringen. |
| -Password | (Valfritt) Lösenord för att ansluta till vCenter-servern/vSphere ESXi-värden. Om inget lösenord anges som parameter uppmanas du att ange lösenordet senare när kommandot körs. |
| -DesiredRPO | (Valfritt) Önskat mål för återställningspunkt (RPO) i minuter. Standardvärdet är 15 minuter.|
| -Bandwidth | Bandbredd i Mbit/s. Det här värdet används till att beräkna det RPO som kan uppnås för den angivna bandbredden. |
| -StartDate | (Valfritt) Startdatum och tidpunkt i formatet MM-DD-ÅÅÅÅ:HH:MM (24-timmarsformat). *StartDate* måste anges tillsammans med *EndDate*. När StartDate anges genereras rapporten för de profileringsdata som samlats in mellan StartDate och EndDate. |
| -EndDate | (Valfritt) Slutdatum och tidpunkt i formatet MM-DD-ÅÅÅÅ:HH:MM (24-timmarsformat). *EndDate* måste anges tillsammans med *StartDate*. När du anger EndDate genereras rapporten för profileringsdata som samlats in mellan StartDate och EndDate. |
| -GrowthFactor | (Valfritt) Tillväxtfaktor, uttryckt i procent. Standardvärdet är 30 procent. |

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Exempel 1: Generera en rapport med standardvärden när profileringsdata ligger på den lokala enheten
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Exempel 2: Generera en rapport när profileringsdata ligger på en fjärrserver
Användaren ska ha läs-/skrivbehörighet för fjärrkatalogen.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Exempel 3: Generera en rapport med specifik bandbredd och specifikt mål för att slutföra IR inom angiven tid
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Exempel 4: Generera en rapport med 5 procents tillväxtfaktor i stället för standardvärdet 30 procent
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Exempel 5: Generera en rapport med en delmängd av profileringsdata
Anta exempelvis att du har profileringsdata för 30 dagar och bara vill generera rapporten för 20 dagar.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Exempel 6: Generera en rapport för ett återställningspunktmål på 5 minuter
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

## <a name="percentile-value-used-for-the-calculation"></a>Percentilvärdet som används för beräkningen
**Vilket standardvärde för percentilen för resultatmåtten som samlas in under profileringen använder verktyget när en rapport genereras?**

Standardvärdet i verktyget är den 95:e percentilen för läs/skriv-IOPS och dataomsättningar som samlas in när alla de virtuella datorerna profileras. Det här måttet garanterar att den topp vid den 100:e percentilen som de virtuella datorerna kan nå eftersom de tillfälliga händelserna inte används för att fastställa mållagringskontot och kraven på källbandbredd. Till exempel kan en tillfällig händelse vara ett säkerhetskopieringsjobb som körs en gång om dagen, periodisk databasindexering eller en analysrapportgenereringsaktivitet eller andra liknande kortvariga händelser vid vissa tidpunkter.

Med värden från den 95:e percentilen får du en korrekt bild av den faktiska arbetsbelastningen, och då får du bästa möjliga prestanda när dessa arbetsbelastningar sedan körs på Azure. Vi tror inte att du kommer att behöva ändra det här antalet. Om du inte ändrar det här värdet (till 90:e percentilen exempelvis) kan du uppdatera konfigurationsfilen *ASRDeploymentPlanner.exe.config*i standardmappen, spara den och generera en ny rapport för befintliga profileringsdata.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Överväganden för tillväxtfaktorer
**Varför bör jag överväga för tillväxtfaktor när jag planerar distributioner?**

Det är viktigt att ge utrymme för ökande arbetsbelastning eftersom dataanvändningen tenderar att öka med tiden. När du väl har skyddat dina data kan du inte byta till ett annat lagringskonto för skydd om arbetsbelastningen skulle förändras utan att inaktivera och sedan återaktivera skyddet.

Anta exempelvis att din virtuella dator i dag passar på ett standardlagringskonto för replikering. Under de kommande tre månaderna kan flera ändringar förekomma:

* Antalet användare av program som körs på den virtuella datorn kommer att öka.
* Den resulterande ökade omsättning på den virtuella datorn kräver att den virtuella datorn ska gå över till Premium Storage så att Site Recovery-replikering kan hålla jämna steg.
* Därför måste du inaktivera och återaktivera skyddet för ett Premium Storage-konto.

Vi rekommenderar starkt att du planerar för tillväxt vid distributionsplanering och medan standardvärdet är 30 procent. Det är du som är expert på användningsmönster och tillväxtprognoser i dina program, och du kan ändra det här värdet när du genererar rapporter. Du kan i själva verket skapa flera rapporter med olika tillväxtfaktorer för samma profileringsdata och se vilka rekommendationer för mållagring och källbandbredd som fungerar bäst för dig.

Den genererade rapporten i Microsoft Excel innehåller följande information:

* [Indata](site-recovery-deployment-planner.md#input)
* [Rekommendationer](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input)
* [Rekommendationer för bandbreddsindata](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input)
* [VM<->Storage Placement](site-recovery-deployment-planner.md#vm-storage-placement) (VM<->lagringsplacering)
* [Compatible VMs](site-recovery-deployment-planner.md#compatible-vms) (Kompatibla virtuella datorer)
* [Incompatible VMs](site-recovery-deployment-planner.md#incompatible-vms) (Inkompatibla virtuella datorer)

![Kapacitetsplaneraren](./media/site-recovery-deployment-planner/dp-report.png)

## <a name="get-throughput"></a>Beräkna dataflöde

Om du vill få en uppskattning av vilket dataflöde som Site Recovery kan uppnå från de lokala datorerna till Azure under replikeringen ska du köra verktyget i GetThroughput-läget. Verktyget beräknar dataflödet från den server som verktyget körs på. Den här servern ska helst vara baserad på guiden för storleksändring av konfigurationsservern. Om du redan har distribuerat infrastrukturkomponenter för Site Recovery lokalt kör du verktyget på konfigurationsservern.

Öppna en kommandotolk och gå till mappen för planeringsverktyget för Site Recovery. Kör ASRDeploymentPlanner.exe med följande parametrar.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Parameternamn | Beskrivning |
|-|-|
| -operation | GetThroughput |
| -Directory | (Valfritt) UNC eller lokal katalogsökväg där profileringsdata (filer som genererats under profileringen) lagras. Dessa data krävs när rapporten ska genereras. Om namnet på en katalog inte anges används katalogen ProfiledData. |
| -StorageAccountName | Namnet på det lagringskonto som används för beräkning av den bandbredd som används för datareplikering lokalt till Azure. Verktyget överför testdata till det här lagringskontot när bandbredden ska beräknas. |
| -StorageAccountKey | Den lagringskontonyckel som används för åtkomst till lagringskontot. Gå till Azure Portal > Lagringskonton > <*[lagringskontots namn]*> Inställningar > Åtkomstnycklar > Key1 (eller en primär åtkomstnyckel för ett klassiskt lagringskonto). |
| -VMListFile | En fil som innehåller listan med virtuella datorer som ska profileras när den förbrukade bandbredden ska beräknas. Filsökvägen kan vara absolut eller relativ. Den här filen ska innehålla ett virtuellt datornamn/en IP-adress per rad. Namnen på de virtuella datorerna i filen ska vara samma som namnen på de virtuella datorerna på vCenter-servern/vSphere ESXi-värden.<br>Filen VMList.txt innehåller exempelvis följande virtuella datorer:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|

Verktyget skapar flera 64 MB stora filer med namnet asrvhdfile<#>.vhd (där # är antalet filer) i den angivna katalogen. Verktyget överför filerna till lagringskontot när dataflödet ska beräknas. När dataflödet har beräknats tar verktyget bort alla filer både från lagringskontot och från den lokala servern. Om verktyget avslutas av någon anledning medan det beräknar dataflödet tar det inte bort filerna från lagringsutrymmet eller från den lokala servern. Du måste ta bort dem manuellt.

Dataflödet beräknas för en viss tidpunkt och är största möjliga dataflöde som Site Recovery kan uppnå under replikeringen givet att alla andra faktorer är oförändrade. Om ett annat program till exempel börjar förbruka mer bandbredd i samma nätverk kommer det faktiska dataflödet att variera under replikeringen. Om du kör kommandot GetThroughput från en konfigureringsserver så kommer verktyget inte att ha någon information om skyddade virtuella datorer eller pågående replikering. Resultatet av det uppmätta dataflödet ser annorlunda ut om GetThroughput-åtgärden körs när de skyddade virtuella datorerna har hög dataomsättning. Du bör därför köra verktyget flera gånger under profileringen så att du får förståelse för vilka dataflödesnivåer som kan uppnås vid olika tidpunkter. I rapporten visas det senaste uppmätta dataflödet.

### <a name="example"></a>Exempel
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Kör verktyget på en server som har samma lagringsutrymme och processoregenskaper som konfigurationsservern.
>
> Ange den rekommenderade bandbredden för att uppfylla återställningspunktmålet 100 procent av tiden för replikering. Om verktyget inte rapporterar en ökning av uppnådda dataflöden trots att du har konfigurerat rätt mängd bandbredd kan du göra följande:
>
>  1. Kontrollera om det finns någon tjänstkvalitet (QoS) för nätverket som begränsar dataflödet för Site Recovery.
>
>  2. Kontrollera om valvet för Site Recovery ligger i den närmaste fysiska Microsoft Azure-region som stöds, så att svarstiden i nätverket minimeras.
>
>  3. Kontrollera de lokala lagringsegenskaperna för att avgöra om du kan förbättra maskinvaran (till exempel hårddisk till SSD).
>
>  4. Ändra inställningarna på processervern för Site Recovery och [öka mängden bandbredd i nätverket som används till replikering](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

## <a name="recommendations-with-desired-rpo-as-input"></a>Rekommendationer med önskat RPO-mål som indata

### <a name="profiled-data"></a>Profileringsdata

![Den profilerade datavyn i kapacitetsplaneraren](./media/site-recovery-deployment-planner/profiled-data-period.png)

**Profiled data period** (Profileringsdataperiod): Den period då profileringen kördes. Som standard innehåller verktyget alla profileringsdata i beräkningen, om inte rapporten genereras för en viss tidsperiod med hjälp av parametrarna StartDate och EndDate.

**Server Name** (Servernamn): Är namnet eller IP-adressen för den VMware vCenter-server eller ESXi-värd vars virtuella datorer rapporten genereras för.

**Desired RPO** (Önskat återställningspunktmål): Återställningspunktmålet för din distribution. Som standard beräknas vilken nätverksbandbredd som krävs för RPO-värden på 15, 30 respektive 60 minuter. De aktuella värdena på bladet uppdateras baserat på vad du väljer. Om du använde parametern *DesiredRPOinMin* när du genererade rapporten så visas det här värdet i resultatet Desired RPO (Önskat RPO-mål).

### <a name="profiling-overview"></a>Profileringsöversikt

![Profilering av resultat i kapacitetsplaneraren](./media/site-recovery-deployment-planner/profiling-overview.png)

**Total Profiled Virtual Machines** (Totalt antal profilerade virtuella datorer): Det totala antalet virtuella datorer som det finns profileringsdata för. Om VMListFile innehåller namn på virtuella datorer som inte har profilerats så beaktas inte dessa virtuella datorer i rapporten och de ingår inte i värdet för antalet virtuella datorer som har profilerats.

**Compatible Virtual Machines** (Kompatibla virtuella datorer): Det antal virtuella datorer som kan skyddas i Azure med Site Recovery. Det här är det totala antalet kompatibla virtuella datorer som nödvändig nätverksbandbredd, antal lagringskonton, antal Azure-kärnor samt antal konfigurationsservrar och ytterligare processervrar beräknas för. Information om varje kompatibel virtuell dator finns i avsnittet ”Kompatibla virtuella datorer”.

**Incompatible Virtual Machines** (Inkompatibla virtuella datorer): Antalet profilerade virtuella datorer som inte kan skyddas med Site Recovery. Orsaken till inkompatibiliteten beskrivs i avsnittet Inkompatibla virtuella datorer. Om VMListFile innehåller namnen på virtuella datorer som inte har profilerats undantas dessa virtuella datorer från antalet inkompatibla virtuella datorer. Dessa virtuella datorer visas under Data not found (Inga data hittades) i slutet av avsnittet Incompatible VMs (Inkompatibla virtuella datorer).

**Desired RPO** (Önskat återställningspunktmål): Önskat mål för återställningspunkten (RPO) i minuter. Rapporten genereras för tre värden för återställningspunktmål: 15 (standard), 30 respektive 60 minuter. Rekommendationen angående bandbredd i rapporten förändras baserat på vilket alternativ du väljer i listrutan Desired RPO (Önskat RPO-mål) uppe till höger på bladet. Om du har genererat rapporten med ett anpassat värde för parametern *-DesiredRPO* visas det här anpassade värdet som standardvärde i listrutan Desired RPO (Önskat återställningspunktmål).

### <a name="required-network-bandwidth-mbps"></a>Required Network Bandwidth (Mbps) (Nödvändig nätverksbandbredd (Mbit/s))

![Nödvändig nätverksbandbredd i kapacitetsplaneraren](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**To meet RPO 100 percent of the time** (För att nå RPO-målet 100 procent av gångerna): Det här är den rekommenderade bandbredd i mbit/s som du bör allokera om RPO-målet ska nås 100 procent av tiden. Den här mängden bandbredd måste vara reserverad för stabil deltareplikering av samtliga kompatibla virtuella datorer om du helt ska undvika överträdelser av RPO-målet.

**To meet RPO 90 percent of the time** (För att nå RPO-målet 90 procent av gångerna): Om bandbreddspriserna är för höga eller om du av någon annan anledning inte kan tilldela den bandbredd som krävs för att uppnå RPO-målet 100 procent av tiden kan du välja en lägre bandbreddsinställning som gör att du uppnår önskat RPO 90 procent av tiden. I rapporten ges även en ”tänk om”-analys av hur många RPO-överträdelser du kan förvänta dig och deras varaktighet, så att du bättre ska förstå vad som kan hända om du tilldelar den här lägre bandbredden.

**Achieved Throughput** (Uppnått dataflöde): Dataflödet från servern där du körde kommandot GetThroughput till den Microsoft Azure-region där lagringskontot finns. Dataflödesvärdet är en uppskattning av den nivå du kan uppnå när du skyddar de kompatibla virtuella datorerna med Site Recovery, förutsatt att lagrings- och nätverksegenskaperna för konfigurationsservern/processervern förblir desamma som för den server där du körde verktyget.

Du bör ange den rekommenderade bandbredden för att uppfylla återställningspunktmålet 100 procent av tiden för replikering. Om inte verktyget rapporterar ökade dataflöden trots att du har ställt in bandbredden gör du följande:

1. Kontrollera om det finns någon tjänstkvalitet (QoS) för nätverket som begränsar dataflödet för Site Recovery.

2. Kontrollera om valvet för Site Recovery ligger i den närmaste Microsoft Azure-region som stöds fysiskt, så att du minimerar svarstiden i nätverket.

3. Kontrollera de lokala lagringsegenskaperna för att avgöra om du kan förbättra maskinvaran (till exempel hårddisk till SSD).

4. Ändra inställningarna för Site Recovery på processervern och [öka den mängd bandbredd i nätverket som används till replikering](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Om du kör verktyget på en konfigurations- eller processerver som redan har skyddade virtuella datorer bör du köra verktyget några gånger. Det uppnådda antalet dataflödesändringar beror på mängden omsättningsuppdateringar som bearbetas vid den aktuella tidpunkten.

För alla företagsdistributioner av Site Recovery bör du använda [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Nödvändiga lagringskonton
I följande diagram visas hur många lagringskonton (standard och premium) som behövs för att skydda alla kompatibla virtuella datorer. Om du vill veta vilket lagringskonto som ska användas för varje virtuell dator kan du läsa avsnittet ”Placering av VM-lagring”.

![Nödvändiga lagringskonton i kapacitetsplaneraren](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

### <a name="required-number-of-azure-cores"></a>Nödvändigt antal Azure-kärnor
Resultatet är det totala antalet kärnor som ska konfigureras före redundansväxling eller redundanstest av alla kompatibla virtuella datorer. Om det inte finns tillräckligt många kärnor tillgängliga i prenumerationen kan inte Site Recovery skapa virtuella datorer vid redundanstestet eller redundansväxlingen.

![Nödvändigt antal Azure-kärnor i kapacitetsplaneraren](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

### <a name="required-on-premises-infrastructure"></a>Krav på lokal infrastruktur
Det här är det totala antalet konfigurationsservrar och ytterligare processervrar som måste konfigureras för att skydda alla kompatibla virtuella datorer. Beroende på vilka [storleksrekommendationer för konfigurationsservern som stöds](https://aka.ms/asr-v2a-on-prem-components), kan verktyget rekommendera ytterligare servrar. Rekommendationen bygger på det största värdet för antingen dataomsättning per dag eller det största antal skyddade virtuella datorer (förutsatt i genomsnitt tre diskar per virtuell dator), beroende på vilken gräns som uppnås först på konfigurationsservern eller den kompletterande processervern. Du hittar detaljerad information om den totala dataomsättningen per dag och det totala antalet skyddade diskar i avsnittet Input (Indata).

![Lokal infrastruktur som krävs för kapacitetsplaneraren](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

### <a name="what-if-analysis"></a>Konsekvensanalys
I den här analysen beskrivs hur många överträdelser som kan inträffa under profileringsperioden när du tilldelar en lägre bandbredd för att önskat RPO-mål ska uppfyllas 90 procent av gångerna. En eller flera överträdelser av återställningspunktmålen kan inträffa på en viss dag. Grafen visar toppåterställningspunktmålet för dagen.
Utifrån den här analysen kan du avgöra om du kan godta antalet RPO-överträdelser och dagens största RPO-överträdelse sett till den lägre bandbredden. Om värdena är godtagbara kan du allokera den lägre bandbredden för replikering. Annars allokerar du den högre bandbredd som rekommenderas för att du ska uppnå önskat RPO-mål 100 % av gångerna.

![Konsekvensanalys i kapacitetsplaneraren](./media/site-recovery-deployment-planner/what-if-analysis.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Recommended VM batch size for initial replication (Rekommenderad VM-batchstorlek för den initiala replikeringen)
I det här avsnittet rekommenderar vi det antal virtuella datorer som kan skyddas parallellt för att slutföra den inledande replikeringen inom 72 timmar med den föreslagna bandbredden för att uppfylla önskade återställningspunktmål 100 procent av den tid som anges. Det här värdet är ett konfigurerbart värde. Du kan ändra värdet då rapporten skapas med parametern *GoalToCompleteIR*.

I diagrammet här visas olika bandbreddsvärden och beräknad batchstorlek för virtuella datorer där den initiala replikeringen kan slutföras inom 72 timmar baserat på den genomsnittliga identifierade storleken för de virtuella datorerna bland alla kompatibla virtuella datorer.

I den offentliga förhandsutgåvan anger inte rapporten vilka virtuella datorer som ska ingå i en batch. Du kan använda diskstorleken som visas i avsnittet Compatible VMs (Kompatibla virtuella datorer) för att se de virtuella datorernas storlek och välja virtuella datorer till en batch, eller så kan du välja virtuella datorer sett till kända arbetsbelastningsegenskaper. Tidsåtgången för den inledande replikeringen förändras proportionellt med avseende på faktisk diskstorlek på den virtuella datorn, förbrukat diskutrymme och tillgängligt dataflöde i nätverket.

![Rekommenderad batchstorlek för virtuella datorer](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

### <a name="growth-factor-and-percentile-values-used"></a>Tillväxtfaktor och percentilvärde som används
I det här avsnittet längst ned på bladet visas vilket percentilvärde som använts för prestandaräknarna för de profilerade virtuella datorerna (standardvärdet är den 95:e percentilen) och vilken tillväxtfaktor som använts i alla beräkningar (standardvärdet är 30 procent).

![Tillväxtfaktor och percentilvärde som används](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Rekommendationer med tillgänglig bandbredd som indata

![Rekommendationer med tillgänglig bandbredd som indata](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

Du kan ha en situation där du vet att du inte kan ange en bandbredd på mer än x Mbit/s för Site Recovery-replikering. Du kan välja att ange tillgänglig bandbredd i verktyget (med parametern -Bandwidth när du genererar rapporten) och då få se vilket RPO-mål i minuter du kan uppnå. Utifrån det här möjliga RPO-värdet kan du avgöra om du måste konfigurera ytterligare bandbredd eller om du nöjer dig med en lösning för haveriberedskap med det aktuella RPO-värdet.

![Möjligt återställningspunktmål för 500 Mbit/s bandbredd](./media/site-recovery-deployment-planner/achievable-rpos.png)

## <a name="input"></a>Indata
På sidan Input (Indata) visas en översikt över den profilerade VMware-miljön.

![Översikt över den profilerade VMware-miljön](./media/site-recovery-deployment-planner/Input.png)

**Startdatum** och **Slutdatum**: Start- och slutdatum för de profileringsdata som ingår i rapportgenereringen. Som standard är startdatumet det datum då profileringen startades och slutdatumet är det datum när profileringen avslutades. Om du angav parametrarna -StartDate och -EndDate när du genererade rapporten visas dessa värden.

**Total number of profiling days** (Totalt antal dagar för profilering): Det totala antalet profileringsdagar mellan start- och slutdatumen som rapporten genererats för.

**Number of compatible virtual machines** (Antal kompatibla virtuella datorer): Det totala antalet kompatibla virtuella datorer som nödvändig nätverksbandbredd, nödvändigt antal lagringskonton, antal Microsoft Azure-kärnor, konfigurationsservrar och ytterligare processervrar beräknas för.

**Total number of disks across all compatible virtual machines** (Totalt antal diskar för alla kompatibla virtuella datorer): Det här värdet används som en av indatauppgifterna för att bestämma antalet konfigurationsservrar och ytterligare processervrar som ska användas i distributionen.

**Average number of disks per compatible virtual machine** (Genomsnittligt antal diskar per kompatibel virtuell dator): Det genomsnittliga antalet diskar beräknat för samtliga kompatibla virtuella datorer.

**Average disk size (GB)** (Genomsnittlig diskstorlek (GB)): Den genomsnittliga diskstorleken beräknad för samtliga kompatibla virtuella datorer.

**Desired RPO (minutes)** (Önskat RPO-mål (minuter)): Antingen standardvärdet för RPO-mål eller det värde som angavs för parametern ”DesiredRPO” när rapporten genererades för att uppskatta nödvändig bandbredd.

**Desired bandwidth (Mbps)** (Önskad bandbredd (Mbit/s)): Det värde du angav för parametern ”Bandwidth” när du genererade rapporten för att uppskatta vilket RPO-mål som kan uppnås.

**Observed typical data churn per day (GB)** (Observerad normal dataomsättning per dag (GB)) är den genomsnittliga dataomsättning som observerats under alla profileringsdagar. Det här värdet används som ett av invärdena i rekommendationen för att fastställa antalet konfigurationsservrar och ytterligare processervrar som ska användas i distributionen.


## <a name="vm-storage-placement"></a>Placering av VM-lagring

![Placering av VM-lagring](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**Disk Storage Type** (Typ av disklagring): Är antingen Standard eller Premium och avser det lagringskonto som ska användas för replikering av motsvarande virtuella datorer i kolumnen **VMs to Place** (Virtuella datorer att placera ut).

**Suggested Prefix** (Föreslaget prefix): Det föreslagna prefixet på tre tecken som du kan använda för att namnge lagringskontot. Du kan använda ditt eget prefix, men verktygets förslag följer [namngivningskonventionen för partitioner av lagringskonton](https://aka.ms/storage-performance-checklist).

**Suggested Account Name** (Föreslaget kontonamn): Namnet på lagringskontot när du inkluderar det föreslagna prefixet. Ersätt namnet inom hakparenteser (< och >) med egna indata.

**Log Storage Account** (Lagringskonto för loggar): alla replikeringsloggar lagras på ett lagringskonto av standardtyp. För virtuella datorer som replikerar till ett Premium Storage-konto konfigurerar du ytterligare ett Standard Storage-konto för logglagringsutrymme. Flera lagringskonton för premiumreplikering kan använda samma standardkonto för logglagring. Virtuella datorer som replikeras till lagringskonton av standardtyp använder samma lagringskonto för loggarna.

**Suggested Log Account Name** (Föreslaget loggkontonamn): Namnet på lagringsloggkontot när du inkluderar det föreslagna prefixet. Ersätt namnet inom hakparenteser (< och >) med egna indata.

**Placement Summary** (Placeringsöversikt): En översikt över den totala virtuella datorbelastningen på lagringskontot vid replikeringen samt vid redundanstest/redundansväxling. I översikten ingår det totala antalet virtuella datorer som har mappats till lagringskontot, totalt antal läs- och skrivåtgärder (IOPS) för de virtuella datorer som placerats på lagringskontot, totalt antal skrivoperationer (replikering), total etablerad storlek sett till alla diskar och det totala antalet diskar.

**Virtual Machines to Place** (Virtuella datorer att placera ut): En lista över de virtuella datorer som ska placeras på det angivna lagringskontot för att prestanda och användningsgrad ska vara optimala.

## <a name="compatible-vms"></a>Compatible VMs (Kompatibla virtuella datorer)
![Excel-kalkylblad med kompatibla virtuella datorer](./media/site-recovery-deployment-planner/compatible-vms.png)

**VM Name** (Namn på virtuell dator): Den virtuella datorns namn eller den IP-adress som används i VMListFile när en rapport skapas. I den här kolumnen visas även de diskar (VMDK:er) som är kopplade till de virtuella datorerna. För att skilja virtuella vCenter-datorer med samma namn eller IP-adresser åt innefattar namnen ESXi-värdnamnet. Den angivna ESXi-värden är den värd där den virtuella datorn har placerats när verktyget identifierades under profileringsperioden.

**VM-kompatibilitet**: Värdena är **Ja** och **Ja**\*. **Ja**\* för instanser där den virtuella datorn är en anpassning för [Azure Premium Storage](https://aka.ms/premium-storage-workload). Här passar den profilerade högomsättnings- eller IOPS-disken i kategorin P20 eller P30, men storleken på disken gör att den mappas ned till en P10 eller P20. Lagringskontot avgör vilken Premium Storage-disktyp som en disk ska mappas till, baserat på dess storlek. Exempel:
* < 128 GB är en P10.
* 128 till 512 GB är en P20.
* 512 till 1 023 GB är en P30.

Om arbetsbelastningsegenskaperna för en disk placerar den i kategorin P20 eller P30, men storleken mappar den till en lägre Premium Storage-disktyp, markerar verktyget den här virtuella datorn som **Ja**\*. Verktyget rekommenderar också att du antingen ändrar källdiskens storlek så att den passar den rekommenderade Premium Storage-disktypen eller ändrar måldisktypen efter redundansväxling.

**Lagringstyp**: Standard eller premium.

**Suggested Prefix** (Föreslaget prefix): Ett prefix på tre tecken för lagringskontot.

**Lagringskontot**: Namnet med prefixet till det föreslagna lagringskontot.

**R/W IOPS (with Growth Factor)** (R/W IOPS (med tillväxtfaktor)): Den högsta IOPS-arbetsbelastningen för läsning/skrivning på disken (standardvärdet är den 95:e percentilen), inklusive faktorn för framtida tillväxt (standardvärdet är 30 procent). Observera att det totala antalet läs/skriv-IOPS för en virtuell dator inte alltid är summan av de enskilda diskarnas läs/skriv-IOPS, eftersom den virtuella datorns högsta läs/skriv-IOPS är den högsta summan av de enskilda diskarnas läs/skriv-IOPS under varje minut av profileringsperioden.

**Data Churn in Mbps (with Growth Factor)** (Dataomsättning i Mbit/s (med tillväxtfaktor)): Den högsta dataomsättningsfrekvensen på disken (standardvärdet är den 95:e percentilen) inklusive faktorn för framtida tillväxt (standardvärdet är 30 procent). Observera att den totala dataomsättningen för den virtuella datorn inte alltid är summan av de enskilda diskarnas dataomsättning, eftersom den virtuella datorns högsta dataomsättning är den högsta summan av de enskilda diskarnas dataomsättning under varje minut av profileringsperioden.

**Azure VM Size** (Storlek för virtuell Azure-dator): Lämplig mappad storlek på den virtuella Azure Cloud Services-datorn för den här lokala virtuella datorn. Mappningen baseras på det lokala virtuella datorminnet, antalet diskar/kärnor/nätverkskort och läs- och skrivåtgärder, IOPS. Rekommendationen är alltid den lägsta virtuella Azure-datorstorlek som matchar alla lokala virtuella datoregenskaper.

**Number of Disks** (Antal diskar): Det totala antalet virtuella datordiskar (VMDK:er) på den virtuella datorn.

**Disk size (GB)** (Diskstorlek (GB)): Total installationsstorlek för alla diskar på den virtuella datorn. Storleken för de enskilda diskarna i den virtuella datorn visas också i verktyget.

**Kärnor**: Antalet processorkärnor i den virtuella datorn.

**Minne (MB)**: Den virtuella datorns RAM-minne.

**Nätverkskort**: Antalet nätverkskort på den virtuella datorn.

## <a name="incompatible-vms"></a>Incompatible VMs (Inkompatibla virtuella datorer)

![Excel-ark med inkompatibla virtuella datorer](./media/site-recovery-deployment-planner/incompatible-vms.png)

**VM Name** (Namn på virtuell dator): Den virtuella datorns namn eller den IP-adress som används i VMListFile när en rapport skapas. I den här kolumnen visas även de diskar (VMDK:er) som är kopplade till de virtuella datorerna. För att skilja virtuella vCenter-datorer med samma namn eller IP-adresser åt innefattar namnen ESXi-värdnamnet. Den angivna ESXi-värden är den värd där den virtuella datorn har placerats när verktyget identifierades under profileringsperioden.

**VM Compatibility** (VM-kompatibilitet): Anger varför den här virtuella datorn inte kan skyddas med Site Recovery. Anledningarna beskrivs för varje inkompatibel disk av den virtuella datorn och kan, baserat på publicerade [lagringsgränser](https://aka.ms/azure-storage-scalbility-performance), vara något av följande:

* Diskstorleken är > 1 023 GB. Azure Storage har för närvarande inte stöd för diskar som är större än 1 TB.

* Total storlek för den virtuella datorn (replikering + TFO) överskrider den gräns för lagringskontostorlek som stöds (35 TB). Den här inkompatibiliteten uppstår vanligen när en enskild disk i den virtuella datorn har en prestandaegenskap som överskrider den maxgräns som stöds av Azure- eller Site Recovery-gränserna för standardlagring. Denna instans skickar den virtuella datorn till Premium Storage-zonen. Maxgränsen för ett lagringskonto av premiumtyp är däremot 35 TB, och det går inte att skydda en enda virtuell dator över flera lagringskonton. Tänk också på att när ett redundanstest körs på en skyddad virtuell dator körs det på samma lagringskonto där replikeringen körs. I den här instansen ställer du in 2 ggr storleken på disken för att replikeringen ska fortskrida samtidigt som redundanstestningen genomförs.
* Käll-IOPS överskrider IOPS-gränsen för lagring på 5 000 per disk.
* Käll-IOPS överskrider IOPS-gränsen för lagring på 80 000 per virtuell dator.
* Den genomsnittliga dataomsättningen överskrider den dataomsättningsgräns som stöds av Site Recovery på 10 Mbit/s för den genomsnittliga I/O-storleken för disken.
* Den totala dataomsättningen för alla diskar i den virtuella datorn överskrider högsta gränsen i Site Recovery på 54 Mbit/s per virtuell dator.
* Genomsnittligt antal effektiva skrivåtgärder (IOPS) överskrider gränsen i Site Recovery på 840 per disk.
* Beräknat lagringsutrymme för ögonblicksbilder överskrider gränsen på 10 TB.

**R/W IOPS (with Growth Factor)** (R/W IOPS (med tillväxtfaktor)): Den högsta IOPS-arbetsbelastningen på disken (standardvärdet är den 95:e percentilen), inklusive faktorn för framtida tillväxt (standardvärdet är 30 procent). Observera att det totala antalet läs/skriv-IOPS för den virtuella datorn inte alltid är summan av de enskilda diskarnas läs/skriv-IOPS, eftersom den virtuella datorns högsta läs/skriv-IOPS är den högsta summan av de enskilda diskarnas läs/skriv-IOPS under varje minut av profileringsperioden.

**Data Churn in Mbps (with Growth Factor)** (Dataomsättning i Mbit/s (med tillväxtfaktor)) Den högsta dataomsättningsfrekvensen på disken (standardvärdet är den 95:e percentilen) inklusive faktorn för framtida tillväxt (standardvärdet är 30 procent). Observera att den totala dataomsättningen för den virtuella datorn inte alltid är summan av de enskilda diskarnas dataomsättning, eftersom den virtuella datorns högsta dataomsättning är den högsta summan av de enskilda diskarnas dataomsättning under varje minut av profileringsperioden.

**Number of Disks** (Antal diskar): Det totala antalet VMDK:er på den virtuella datorn.

**Disk size (GB)** (Diskstorlek (GB)): Total installationsstorlek för alla diskar på den virtuella datorn. Storleken för de enskilda diskarna i den virtuella datorn visas också i verktyget.

**Kärnor**: Antalet processorkärnor i den virtuella datorn.

**Minne (MB)**: Mängden RAM-minne på den virtuella datorn.

**Nätverkskort**: Antalet nätverkskort på den virtuella datorn.


## <a name="site-recovery-limits"></a>Gränser för Site Recovery

**Replication Storage Target** (Lagringsmål för replikering) | **Average Source Disk I/O Size** (Genomsnittlig I/O-storlek för källdisk) |**Average Source Disk Data Churn** (Genomsnittlig dataomsättning för källdisk) | **Total Source Disk Data Churn Per Day** (Total dataomsättning per dag för källdisk)
---|---|---|---
Standard Storage | 8 kB    | 2 Mbit/s | 168 GB per disk
Premium P10-disk | 8 kB    | 2 Mbit/s | 168 GB per disk
Premium P10-disk | 16 kB | 4 Mbit/s |    336 GB per disk
Premium P10-disk | 32 kB eller mer | 8 Mbit/s | 672 GB per disk
P20- eller P30-premiumdisk | 8 kB    | 5 Mbit/s | 421 GB per disk
P20- eller P30-premiumdisk | minst 16 kB |10 Mbit/s | 842 GB per disk

Det här är genomsnittliga värden baserade på en I/O-överlappning på 30 procent. Site Recovery kan hantera högre dataflöden med annan överlappning, större skrivningsstorlek och verkligt I/O-beteende under arbetsbelastningen. Föregående antal antar en typisk eftersläpning på cirka fem minuter. Det vill säga, när data har överförts bearbetas de och en återställningspunkt skapas inom fem minuter.

Dessa gränser är baserade på våra tester, men de täcker inte alla möjliga kombinationer av program-I/O. De faktiska resultaten kan variera beroende på blandningen av I/O i ditt program. För bästa resultat även efter distributionsplaneringen rekommenderar vi alltid att du kör omfattande programtester med redundanstest för att få en bild av verklig prestanda.

## <a name="updating-the-deployment-planner"></a>Uppdatera kapacitetsplaneraren
Så här gör du om du vill uppdatera kapacitetsplaneraren:

1. Ladda ned den senaste versionen av [Azure Site Recovery-kapacitetsplaneraren](https://aka.ms/asr-deployment-planner).

2. Kopiera .zip-mappen till en server som du vill köra den på.

3. Extrahera .zip-filen.

4. Gör något av följande:
 * Om den senaste versionen innehåller inte en profileringskorrigering och profileringen pågår redan på den aktuella versionen av planeringsverktyget fortsätter du profileringen.
 * Om den senaste versionen innehåller en profileringskorrigering rekommenderar vi att du stoppar profileringen av din aktuella version och startar om profileringen med den nya versionen.

  >[!NOTE]
  >
  >När du startar profilering med den nya versionen skickar du samma sökväg för utdatakatalogen så att verktyget lägger till profildata i de befintliga filerna. En fullständig uppsättning profilerade data används för att generera rapporten. Om du skickar en annan utdatakatalog kommer nya filer att skapas och gamla profildata använda inte för att skapa rapporten.
  >
  >Varje ny kapacitetsplanerare är en ackumulerad uppdatering av .zip-filen. Du behöver inte kopiera de senaste filerna till föregående mapp. Du kan skapa och använda en ny mapp.


## <a name="version-history"></a>Versionshistorik
### <a name="11"></a>1.1
Uppdaterad: 9 mars 2017

Åtgärdat följande problem:

* Verktyget kan inte profilera virtuella datorer om vCenter har två eller fler virtuella datorer med samma namn eller IP-adress hos olika ESXi-värdar.
* Kopiering och sökning har inaktiverats för arbetsblad för kompatibla virtuella datorer och inkompatibla virtuella datorer.

### <a name="10"></a>1.0
Uppdaterat: 23 februari 2017

Azure Site Recovery Deployment Planner Public Preview 1.0 har följande kända problem (kommer att åtgärdas i senare uppdateringar):

* Verktyget fungerar bara för scenariot VMware till Azure, inte för distributioner med Hyper-V till Azure. För scenarier av typen Hyper-V till Azure ska du använda [kapacitetsplaneringsverktyget för Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
* Åtgärden GetThroughput stöds inte i Microsoft Azure-regionerna US Government och Kina.
* Verktyget kan inte profilera virtuella datorer om vCenter-servern har två eller flera virtuella datorer med samma namn eller IP-adress i olika ESXi-värdar. I den här versionen hoppar verktyget över profilering för dubbletter av namn på virtuella datorer eller IP-adresser i VMListFile. Lösningen är att profilera de virtuella datorerna med hjälp av en ESXi-värd i stället för vCenter-servern. Du måste köra en instans för varje ESXi-värd.

