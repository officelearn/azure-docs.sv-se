---
title: Automatisera StorSimple filresursen DR med Azure Site Recovery | Microsoft Docs
description: Beskriver stegen och bästa praxis för att skapa en lösning för haveriberedskap för filresurser som finns på Microsoft Azure StorSimple-lagring.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: vidarmsft
ms.openlocfilehash: c88df7ba1a9a60ffcda9a5235197037088abca4e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249276"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatiserad Disaster Recovery-lösning med Azure Site Recovery för filresurser som finns på StorSimple
## <a name="overview"></a>Översikt
Microsoft Azure StorSimple är en hybridmolnlagringslösning som åtgärdar komplexiteten i Ostrukturerade data som vanligtvis är kopplad filresurser. StorSimple använder lagringsutrymmet i molnet som ett tillägg av lokal lösning och data i nivåer i den lokala lagringen och molnlagring. Integrerat dataskydd, med lokala och molnbaserade ögonblicksbilder, eliminerar behovet av en sprawling lagringsinfrastruktur.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) är en Azure-baserad tjänst som tillhandahåller haveriberedskap (DR) funktioner genom att samordna replikering, redundans och återställning av virtuella datorer. Azure Site Recovery stöder ett antal tekniker för replikering av att replikera konsekvent, skydda och smidigt växla över virtuella datorer och program i privata/offentliga eller värdbaserade moln.

Med hjälp av Azure Site Recovery och replikeringen StorSimple funktionerna för ögonblicksbilder av molnet, kan du skydda hela filen server-miljö. Du kan använda ett enda klick i händelse av avbrott, för att ge dina filresurser online i Azure på bara några minuter.

Det här dokumentet beskriver i detalj hur du kan skapa en lösning för haveriberedskap för dina filresurser som finns på StorSimple-lagring och utföra planerad och oplanerad och testa redundans med hjälp av en återställningsplan med ett klick. I princip visar den hur du kan ändra den Återställningsplanen för i din Azure Site Recovery-valvet för att aktivera StorSimple redundans under katastrofscenarier. Dessutom beskrivs förutsättningar och konfigurationer som stöds. Det här dokumentet förutsätter att du är bekant med grunderna för Azure Site Recovery och StorSimple arkitekturer.

## <a name="supported-azure-site-recovery-deployment-options"></a>Stöds Azure Site Recovery-distributionsalternativ
Kunder kan distribuera filservrar som fysiska eller virtuella datorer (VM) som körs på Hyper-V eller VMware och skapa filresurser från volymerna högg av StorSimple-lagring. Azure Site Recovery kan skydda både fysiska och virtuella distributioner till en sekundär plats eller till Azure. Det här dokumentet innehåller information om en katastrofåterställningslösning med Azure som återställningsplats för en filserver som är värd för virtuell dator på Hyper-V och filresurser på StorSimple-lagring. Andra scenarier där filen server-dator är på en VMware-VM eller en fysisk dator kan implementeras på samma sätt.

## <a name="prerequisites"></a>Förutsättningar
Implementera en lösning för katastrofåterställning med ett klick som använder Azure Site Recovery för filresurser som finns på StorSimple-lagring har följande krav:

   - En lokal fil för Windows Server 2012 R2-servern virtuell dator i Hyper-V eller VMware eller en fysisk dator
   - StorSimple storage enheten lokala registrerad med Azure StorSimple manager
   - StorSimple-Molninstallationen har skapats i Azure StorSimple manager. Installationen kan förvaras i en avstängningstillstånd.
   - Filresurser på volymer som konfigurerats på StorSimple-lagringsenhet
   - [Azure Site Recovery services-valv](../site-recovery/site-recovery-vmm-to-vmm.md) skapas i en Microsoft Azure-prenumeration

Dessutom, om du använder Azure som återställningsplats kör den [Azure VM gratisverktyget för utvärdering av](https://azure.microsoft.com/downloads/vm-readiness-assessment/) på virtuella datorer så att de är kompatibla med Azure virtuella datorer och Azure Site Recovery services.

Att undvika fördröjning problem (vilket kan resultera i högre kostnader), se till att du skapar din StorSimple-Molninstallationen, automation-konto och lagring eller konton i samma region.

## <a name="enable-dr-for-storsimple-file-shares"></a>Aktivera DR för StorSimple-filresurser
Varje komponent i den lokala miljön måste skyddas om du vill aktivera fullständig replikering och återställning. Det här avsnittet beskrivs hur du:
    
   - Konfigurera Active Directory och DNS-replikering (valfritt)
   - Använd Azure Site Recovery för att aktivera skydd för den virtuella datorn
   - Aktivera skydd för StorSimple-volymer
   - Konfigurera nätverket

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Konfigurera Active Directory och DNS-replikering (valfritt)
Om du vill skydda datorer som kör Active Directory och DNS så att de är tillgängliga på DR-plats måste du uttryckligen skyddar dem (så att filservrarna som är tillgängliga efter redundans med autentisering). Det finns två rekommenderade alternativ baserat på komplexitet och kundens lokala miljö.

#### <a name="option-1"></a>Alternativ 1
Om kunden har ett litet antal program, en enda domänkontrollant för hela lokal plats och vara växling över hela platsen, och vi rekommenderar att du använder Azure Site Recovery-replikering för att replikera den domain controller-datorn till en sekundär plats (Detta gäller för både plats-till-plats och plats till Azure).

#### <a name="option-2"></a>Alternativ 2
Om kunden har ett stort antal program, körs en Active Directory-skog och misslyckas över några program i taget, så rekommenderar vi att konfigurera ytterligare en domänkontrollant på DR-plats (en sekundär plats eller i Azure).

Referera till [automatisk katastrofåterställningslösning för Active Directory och DNS med hjälp av Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) anvisningar när du gör en domänkontrollant som är tillgängliga på DR-plats. I resten av det här dokumentet förutsätter vi en domänkontrollant är tillgänglig på webbplatsen DR.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Använd Azure Site Recovery för att aktivera skydd för den virtuella datorn
Det här steget måste du förbereda den lokala filen server-miljö, skapa och Förbered en Azure Site Recovery-valvet och aktivera Filskydd för den virtuella datorn.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>För att förbereda filen lokalt server-miljö
1. Ange den **User Account Control** till **meddela aldrig**. Detta krävs så att du kan använda Azure automatiseringsskript för att ansluta iSCSI-mål efter redundans av Azure Site Recovery.
   
   1. Tryck på Windows-tangenten + Q och Sök efter **UAC**.  
   1. Välj **ändra User Account Control inställningar**.  
   1. Dra fältet till botten mot **meddela aldrig**.  
   1. Klicka på **OK** och välj sedan **Ja** när du tillfrågas.  
   
      ![Inställningar för kontroll av användarkonto](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Installera VM-agenten på varje filserver virtuella datorer. Detta krävs så att du kan köra Azure automatiseringsskript på den virtuella datorer.
   
   1. [Ladda ned agenten](https://aka.ms/vmagentwin) till `C:\\Users\\<username>\\Downloads`.
   1. Öppna Windows PowerShell i administratörsläge (Kör som administratör) och ange sedan följande kommando för att navigera till nedladdningsplatsen:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > Filnamnet kan ändras beroende på vilken version.
      
1. Klicka på **Nästa**.
1. Acceptera den **av villkor** och klicka sedan på **nästa**.
1. Klicka på **Slutför**.
1. Skapa filresurser med hjälp av volymer högg av StorSimple-lagring. Mer information finns i [använda StorSimple Manager-tjänsten för att hantera volymer](storsimple-manage-volumes.md).
   
   1. Tryck på Windows-tangenten + Q på dina lokala virtuella datorer, och Sök efter **iSCSI**.
   1. Välj **iSCSI-initieraren**.
   1. Välj den **Configuration** fliken och kopiera initierarnamnet.
   1. Logga in på [Azure-portalen](https://portal.azure.com/).
   1. Välj den **StorSimple** fliken och välj sedan StorSimple Manager-tjänsten som innehåller den fysiska enheten.
   1. Skapa volymbehållare och skapa sedan volymer. (Dessa volymer är för fil-resurs/er på filservern virtuella datorer). Kopiera namnet på initieraren och ge ett lämpligt namn för åtkomstkontrollposterna när du skapar volymer.
   1. Välj den **konfigurera** och notera ned IP-adressen för enheten.
   1. På din lokala virtuella datorer går du till den **iSCSI-initieraren** igen och ange den IP-Adressen i avsnittet Snabbanslut. Klicka på **Snabbanslut** (enheten bör nu vara ansluten).
   1. Öppna Azure portal och väljer den **volymer och enheter** fliken. Klicka på **automatiskt konfigurera**. Den volym som du skapade bör visas.
   1. I portalen, väljer du den **enheter** fliken och välj sedan **skapa en ny virtuell enhet.** (Den här virtuella enheten kommer att användas om det uppstår redundans). Den här nya virtuella enheten kan hållas i offline-tillstånd att undvika extra kostnader. Om du vill koppla från den virtuella enheten, går du till den **virtuella datorer** avsnittet på portalen och Stäng av den.
   1. Gå tillbaka till de lokala virtuella datorerna och öppna Diskhantering (tryck på Windows-tangenten + X och välj **Diskhantering**).
   1. Du ser några extra diskar (beroende på antalet volymer som du har skapat). Högerklicka på den första bilden, Välj **initiera Disk**, och välj **OK**. Högerklicka på den **inte allokerat** väljer **ny enkel volym**, tilldela den en enhetsbeteckning och slutför guiden.
   1. Upprepa steget l för alla diskar. Du kan nu se alla diskar på **den här datorn** i Windows Explorer.
   1. Använda rollen fil- och lagringstjänster för att skapa filresurser på dessa volymer.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Att skapa och förbereda ett Azure Site Recovery-valv
Referera till den [dokumentation om Azure Site Recovery](../site-recovery/site-recovery-hyper-v-site-to-azure.md) att komma igång med Azure Site Recovery innan du skyddar servern VM.

#### <a name="to-enable-protection"></a>Att aktivera skydd
1. Koppla från iSCSI-mål från de lokala virtuella datorer som du vill skydda via Azure Site Recovery:
   
   1. Tryck på Windows-tangenten + Q och Sök efter **iSCSI**.
   1. Välj **Konfigurera iSCSI-initieraren**.
   1. Koppla från StorSimple-enheten som du tidigare har anslutit. Du kan också du kan inaktivera filservern för ett par minuter när skyddet aktiverades.
      
   > [!NOTE]
   > Detta innebär att filresurserna för att vara temporärt otillgänglig.
   
1. [Aktivera skydd för virtuella datorer](../site-recovery/site-recovery-hyper-v-site-to-azure.md) på filservern virtuell dator från Azure Site Recovery-portalen.
1. När den första synkroniseringen börjar återansluta du målet igen. Gå till iSCSI-initierare Välj StorSimple-enheten och klicka på **Connect**.
1. När synkroniseringen har slutförts och statusen för den virtuella datorn är **skyddade**, Välj den virtuella datorn, Välj den **konfigurera** fliken och uppdateras nätverket för den virtuella datorn (det här är nätverket som den över Virtuella datorer blir en del av). Om nätverket inte visas, betyder det att synkroniseringen som fortfarande pågår.

### <a name="enable-protection-of-storsimple-volumes"></a>Aktivera skydd för StorSimple-volymer
Om du inte har valt den **aktiverar en standardsäkerhetskopiering för den här volymen** för StorSimple-volymer, gå till **Säkerhetskopieringsprinciper** i StorSimple Manager-tjänsten och skapa en lämplig princip för säkerhetskopiering för alla volymer. Vi rekommenderar att du ställer in säkerhetskopieringsfrekvensen mål för återställningspunkt (RPO) som du skulle vilja se för programmet.

### <a name="configure-the-network"></a>Konfigurera nätverket
Konfigurera nätverksinställningar i Azure Site Recovery för filserver VM så att de Virtuella datornätverk som är kopplade till rätt DR-nätverk efter redundansväxling.

Du kan välja den virtuella datorn i den **replikerade objekt** fliken Konfigurera nätverk, enligt följande bild.

![Beräkning och nätverk](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan
Du kan skapa en återställningsplan i ASR att automatisera redundansprocessen av filresurserna. Om ett avbrott inträffar, kan du öppna filresurserna på några minuter med ett enda klick. Om du vill aktivera det här automation, behöver du ett Azure automation-konto.

#### <a name="to-create-an-automation-account"></a>Skapa ett Automation-konto
1. Gå till Azure-portalen &gt; **Automation** avsnittet.
1. Klicka på **+ Lägg till** knappen öppnas under bladet.
   
   ![Lägga till ett Automation-konto](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Name - ange ett nytt automation-konto
   - Prenumeration – Välj prenumeration
   - Resource group - skapa nya/Välj befintlig resursgrupp
   - Plats – Välj plats, förvara den på samma geo/region som den StorSimple-Molninstallationen och Storage-konton har skapats.
   - Skapa Azure kör som-konto – Välj **Ja** alternativet.
   
1. Gå till Automation-kontot, klickar du på **Runbooks** &gt; **Bläddringsgalleriegenskapen** att importera alla nödvändiga runbooks till automation-kontot.
1. Lägg till följande runbooks genom att söka efter **Disaster Recovery** tagg i galleriet:
   
   - Rensa av StorSimple-volymer efter Test Failover (TFO)
   - Failover StorSimple volymbehållare
   - Montera volymer på StorSimple-enhet efter en redundansväxling
   - Avinstallera tillägget för anpassat skript i Azure VM
   - Starta StorSimple Virtual Appliance
   
      ![Sök i galleri](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Publicera alla skript genom att välja runbook i automation-kontot och klicka på **redigera** &gt; **publicera** och sedan **Ja** i verifieringsmeddelandet. Efter det här steget i **Runbooks** fliken visas på följande sätt:
   
   ![Runbooks](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. I automation-konto klickar du på **variabler** &gt; **Lägg till en variabel** och Lägg till följande variabler. Du kan välja att kryptera dessa tillgångar. Dessa variabler finns specifika återställningsplan. Om din återställningsplanen är som du skapar i nästa steg namn TestPlan, dina variabler ska vara TestPlan-StorSimRegKey, TestPlan AzureSubscriptionName och så vidare.

   - **BaseUrl**: Resource Manager-url för Azure-molnet. Få med **Get-AzureRmEnvironment | Select-Object-namn, ResourceManagerUrl** cmdlet.
   - * RecoveryPlanName ***- ResourceGroupName**: Resource Manager-grupp med StorSimple-resursen.
   - * RecoveryPlanName ***- ManagerName**: The StorSimple-resurs som har StorSimple-enheten.
   - * RecoveryPlanName ***- DeviceName**: The StorSimple-enhet som har växlas.
   - * RecoveryPlanName ***- DeviceIpAddress**: IP-adressen för enheten (detta finns i den **enheter** fliken StorSimple Device Manager-avsnittet &gt; **inställningar** &gt; **Nätverk** &gt; **DNS-inställningarna** grupp).
   - * RecoveryPlanName ***- VolumeContainers**: en kommaavgränsad sträng med volymbehållare på enheten som behöver att växlas över, till exempel: volcon1 volcon2, volcon3.
   - * RecoveryPlanName ***- TargetDeviceName**: The StorSimple-Molninstallation som är behållarna som ska redundansväxlas.
   - * RecoveryPlanName ***- TargetDeviceIpAddress**: IP-adressen för målenheten (detta finns i den **VM** avsnittet &gt; **inställningar** gruppen &gt; **nätverk** fliken).
   - * RecoveryPlanName ***- StorageAccountName**: namnet på lagringskontot där skriptet (som måste köras på den redundansväxlade virtuella datorn) kommer att lagras. Detta kan vara ett storage-konto som har utrymme att lagra skriptet tillfälligt.
   - * RecoveryPlanName ***- StorageAccountKey**: åtkomstnyckeln för lagringskontot ovan.
   - * RecoveryPlanName ***- VMGUIDS**: när du skyddar en virtuell dator, Azure Site Recovery tilldelar varje virtuell dator ett unikt ID som ger information om den misslyckade VM. Om du vill ha VMGUID, Välj den **återställningstjänster** fliken och klicka på **skyddade objektet** &gt; **Skyddsgrupper** &gt;  **Datorer** &gt; **egenskaper**. Om du har flera virtuella datorer kan sedan lägga till GUID som en kommaavgränsad sträng.

    Om namnet på återställningsplanen är fileServerpredayRP, till exempel sedan din **variabler**, **anslutningar** och **certifikat** fliken bör visas på följande sätt när du lägger till alla tillgångar.

      ![Tillgångar](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Ladda upp StorSimple 8000-serien Runbook-modulen i ditt Automation-konto. Använd den nedanstående steg för att lägga till en modul:
   
   1. Öppna powershell, skapa en ny mapp och ändra katalogen till mappen.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Ladda ned nuget CLI under samma mapp i steg 1.
      Olika versioner av nuget.exe finns på [nuget hämtar](https://www.nuget.org/downloads). Varje nedladdningslänk pekar direkt på en .exe-fil, så se till att högerklicka på och spara filen på datorn i stället körs från webbläsaren.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Ladda ned beroende SDK
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Skapa en Azure Automation Runbook-modul för enhetshantering i StorSimple 8000-serien. Använd den nedan kommandon för att skapa ett Automation-modul zip-filen.
         
      ```
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"

            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
      ```
         
   1. Importera Azure Automation-modul zip-filen (Microsoft.Azure.Management.StorSimple8000Series.zip) skapade i steget ovan. Detta kan göras genom att välja det Automation-kontot, klickar du på **moduler** under delade resurser och klicka sedan på **lägger till en modul**.
   
   När du har importerat modulen StorSimple 8000-serien i **moduler** fliken bör visas på följande sätt:
   
      ![Moduler](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Gå till den **återställningstjänster** och väljer Azure Site Recovery-valvet som du skapade tidigare.
1. Välj den **Återställningsplaner (Site Recovery)** alternativet från **hantera** gruppera och skapa en ny återställningsplan enligt följande:
   
   - Klicka på **+ Återställ plan** knappen öppnas under bladet.
      
      ![Skapa en återställningsplan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Ange ett namn på recovery plan, Välj källa, mål och distribution modell-värden.
   
   - Välj de virtuella datorerna från skyddsgruppen som du vill inkludera i återställningsplanen och klicka på **OK** knappen.
   
   - Välj återställningsplan som du skapade tidigare, klicka på **anpassa** knappen för att öppna vyn Recovery plan anpassning.
   
   - Högerklicka på **stänger alla grupper** och klicka på **Lägg till åtgärd i förväg**.
   
   - Öppnar Infoga åtgärd bladet, anger du ett namn, Välj **primär sida** alternativet att köra alternativet markerar du Automation-konto (som du har lagt till runbooks) och välj sedan den **redundans-StorSimple-volym-behållare**  runbook.
   
   - Högerklicka på **grupp 1: starta** och klicka på **Lägg till skyddade objekt** alternativet och välj sedan de virtuella datorer som ska skyddas i återställningsplanen och på **Ok** knappen. Valfritt, om den redan valt virtuella datorer.
   
   - Högerklicka på **grupp 1: starta** och klicka på **publicera åtgärden** alternativet och sedan lägga till följande skript:  
      
      - Start-StorSimple-virtuell-installation-runbook  
      - Det gick inte over-StorSimple-volym-behållare runbook  
      - Montera volymer efter redundans-runbook  
      - Avinstallera –--tillägget för anpassat skript runbook  
        
   - Lägg till en manuell åtgärd efter ovan 4 skripten i samma **grupp 1: steg efter** avsnittet. Den här åtgärden är den punkt där du kan kontrollera att allt fungerar korrekt. Den här åtgärden måste du lägga till som en del av redundanstest (endast väljer den **Redundanstest** kryssrutan).
    
   - När den manuella åtgärden, lägger du till den **Rensa** skript på samma sätt som du använde för den andra runbooks. **Spara** återställningsplanen.
    
   > [!NOTE]
   > När du kör ett redundanstest, bör du kontrollera allt på den manuella åtgärden eftersom StorSimple-volymer som hade varit klonas på målenheten kommer att tas bort som en del av rensningen när den manuella åtgärden har slutförts.
       
      ![Recoery plan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Utför ett redundanstest
Referera till den [Active Directory katastrofåterställningslösning](../site-recovery/site-recovery-active-directory.md) tillhörande guide för specifika saker du bör Active Directory under redundanstestningen. Den lokala installationen påverkas inte alls när testet av redundansväxling sker. StorSimple-volymer som kopplats till en lokal virtuell dator klonas till StorSimple-Molninstallation i Azure. En virtuell dator för testning tas i Azure och de klonade volymerna är kopplade till den virtuella datorn.

#### <a name="to-perform-the-test-failover"></a>Att testa redundans
1. I Azure-portalen väljer du valvet för Site Recovery.
1. Klicka på återställningsplanen som skapats för filservern VM.
1. Klicka på **Redundanstest**.
1. Välj den Azure-nätverk som virtuella Azure-datorer ska ansluta till efter redundansväxlingen.
   
   ![Starta redundans](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet genom att klicka på den virtuella datorn för att öppna dess egenskaper eller på den **testjobb för ett redundanskluster** i valvnamnet &gt; **jobb** &gt; **Site Recovery-jobb**.
1. När redundansväxlingen är klar bör du även att kunna se Azure datorn visas i Azure-portalen &gt; **virtuella datorer**. Du kan utföra dina verifieringar.
1. När validering är klar klickar du på **verifieringar fullständig**. Bort StorSimple-volymer tas och stänga av StorSimple Cloud Appliance.
1. När du är klar klickar du på **Rensa redundanstestning** på återställningsplanen. I anteckningar och sparar eventuella observationer från redundanstestningen. Detta tar bort den virtuella datorn som skapades under redundanstestningen.

## <a name="perform-a-planned-failover"></a>Utför en planerad redundansväxling
   Under en planerad redundans för lokalt filservern Virtuella datorn stängs av ett smidigt sätt och ett moln som tas ögonblicksbilden av säkerhetskopian volymer på StorSimple-enhet. StorSimple-volymer växlas över till den virtuella enheten, en virtuell replikdator tas i Azure och volymerna som är kopplade till den virtuella datorn.

#### <a name="to-perform-a-planned-failover"></a>Att utföra en planerad redundans
1. I Azure-portalen väljer du **återställningstjänster** vault &gt; **återställningsplaner (Site Recovery)** &gt; **recoveryplan_name** skapade för den filen server-dator.
1. Klicka på bladet Recovery plan **mer** &gt; **planerad redundans**.

   ![Återställningsplan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. På den **bekräfta planerad redundans** bladet Välj käll- och målplatserna och väljer målnätverk och klicka på kryssikonen ✓ att starta redundansprocessen.
1. När replikering av virtuella datorer skapas är de i ett åtagande väntetillstånd. Klicka på **Commit** att genomföra redundans.
1. När replikeringen är klar, virtuella datorer som startas på den sekundära platsen.

## <a name="perform-a-failover"></a>Utför en redundansväxling
StorSimple-volymer växlas över till den virtuella enheten under en oplanerad redundans, en virtuell replikdator ska göras tillgänglig på Azure och volymerna som är kopplade till den virtuella datorn.

#### <a name="to-perform-a-failover"></a>Att utföra en redundansväxling
1. I Azure-portalen väljer du **återställningstjänster** vault &gt; **återställningsplaner (Site Recovery)** &gt; **recoveryplan_name** skapade för den filen server-dator.
1. Klicka på bladet Recovery plan **mer** &gt; **redundans**.
1. På den **bekräfta redundans** bladet Välj källa och mål platser.
1. Välj **stänga av virtuella datorer och synkronisera senaste data** att ange att Site Recovery bör försöka att Stäng av den skyddade virtuella datorn och synkronisera data så att den senaste versionen av data kommer att redundansväxlas.
1. Efter redundansen är de virtuella datorerna i ett åtagande väntetillstånd. Klicka på **Commit** att genomföra redundans.


## <a name="perform-a-failback"></a>Utföra en återställning efter fel
Under en återställning efter fel växlas StorSimple volymbehållare över tillbaka till den fysiska enheten när en säkerhetskopia görs.

#### <a name="to-perform-a-failback"></a>Att utföra en återställning efter fel
1. I Azure-portalen väljer du **återställningstjänster** vault &gt; **återställningsplaner (Site Recovery)** &gt; **recoveryplan_name** skapade för den filen server-dator.
1. Klicka på bladet Recovery plan **mer** &gt; **planerad redundans**.
1. Välj käll- och målplatserna, väljer du lämplig datasynkronisering och alternativ för skapande av virtuell dator.
1. Klicka på **OK** för att starta processen för återställning efter fel.
   
   ![Starta återställning efter fel](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Metodtips
### <a name="capacity-planning-and-readiness-assessment"></a>Kapacitet planerings- och readiness assessment
#### <a name="hyper-v-site"></a>Hyper-V-plats
Använd den [användaren kapacitetsplaneringsverktyget](https://www.microsoft.com/download/details.aspx?id=39057) att designa den server, lagring och nätverksinfrastruktur för din miljö för Hyper-V-repliken.

#### <a name="azure"></a>Azure
Du kan köra den [Azure VM gratisverktyget för utvärdering av](https://azure.microsoft.com/downloads/vm-readiness-assessment/) på virtuella datorer så att de är kompatibla med Azure virtuella datorer och Azure Site Recovery Services. Verktyget för diagnostisk utvärdering kontrollerar VM-konfigurationer och varnar när konfigurationer som inte är kompatibla med Azure. Till exempel utfärdar den en varning om en enhet C: är större än 127 GB.

Kapacitetsplanering består av minst två viktiga processer:

   - Mappning av en lokal Hyper-V-datorer till Azure VM-storlekar (till exempel A6, A7, A8 och A9).
   - Fastställa nödvändig Internet-bandbredd.

## <a name="limitations"></a>Begränsningar
   - För närvarande kan endast 1 StorSimple-enhet redundansväxlas (för att en enda StorSimple Cloud Appliance). Scenario för en filserver som sträcker sig över flera StorSimple-enheter stöds inte ännu.
   - Om du får ett fel medan skyddet aktiveras för en virtuell dator kan du kontrollera att du har kopplat från iSCSI-mål.
   - Alla volymcontainrar som grupperas tillsammans på grund av säkerhetskopiering principer som täcker över volymbehållare redundansväxlas tillsammans.
   - Alla volymer i de volymbehållare som du har valt kommer att redundansväxlas.
   - Volymer som lägga till upp till fler än 64 TB redundansväxlas inte eftersom den maximala kapaciteten på ett enda StorSimple Cloud Appliance är 64 TB.
   - Om planerad/oplanerad redundans misslyckas och de virtuella datorerna skapas i Azure, sedan du inte rensa upp de virtuella datorerna. I stället gör en återställning efter fel. Om du tar bort de virtuella datorerna kan sedan lokala virtuella datorer inte aktiveras igen.
   - Efter en redundansväxling om du inte kan se volymerna, går du till de virtuella datorerna, öppnar du Diskhantering, skanna om diskarna och tar dem online.
   - I vissa fall kan Enhetsbokstäverna i DR-plats vara annorlunda än de bokstäver lokalt. Om detta inträffar kommer du behöva korrigera problemet manuellt när redundansen är klar.
   - Tidsgräns för redundans-jobb: The StorSimple skriptet når tidsgränsen om redundans för volymbehållare tar längre tid än Azure Site Recovery-gränsen per skript (för närvarande 120 minuter).
   - Tidsgräns för säkerhetskopieringsjobbet: The StorSimple skriptet sin tidsgräns om säkerhetskopiering av volymer tar längre tid än Azure Site Recovery-gränsen per skript (för närvarande 120 minuter).
   
   > [!IMPORTANT]
   > Köra säkerhetskopieringen manuellt från Azure-portalen och kör sedan återställningsplanen igen.
   
   - Klona jobb timeout: The StorSimple skriptet sin tidsgräns om kloning av volymer tar längre tid än Azure Site Recovery-gränsen per skript (för närvarande 120 minuter).
   - Tid synkroniseringsfel: The StorSimple skript fel ut säger att säkerhetskopieringarna inte genomfördes trots att säkerhetskopieringen är klar i portalen. En möjlig orsak till detta kan vara att tiden för StorSimple-installation kan vara synkroniserad med den aktuella tiden i den aktuella tidszonen.
   
   > [!IMPORTANT]
   > Synkronisera tiden för installationen med den aktuella tiden i den aktuella tidszonen.
   
   - Installationen redundans fel: The StorSimple skript kan misslyckas om det är en installation redundans när återställningsplanen körs.
   
   > [!IMPORTANT]
   > Nytt återställningsplanen efter redundansväxlingen för installationen är klar.


## <a name="summary"></a>Sammanfattning
Med Azure Site Recovery kan du skapa en fullständig automatiserad haveriberedskapsplan för en filserver VM har filresurser som finns på StorSimple-lagring. Du kan påbörja redundans på några sekunder från var som helst i händelse av ett avbrott och få programmet igång på några få minuter.
