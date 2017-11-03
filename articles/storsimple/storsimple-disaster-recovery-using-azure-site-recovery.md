---
title: Automatisera StorSimple filresursen DR med Azure Site Recovery | Microsoft Docs
description: "Beskriver stegen och bästa praxis för att skapa en lösning för katastrofåterställning för filresurser som finns på Microsoft Azure StorSimple-lagring."
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: vidarmsft
ms.openlocfilehash: e60cc83f49f9e0d0f878d7f49333f1be34ce54a6
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatisk Disaster Recovery-lösning med hjälp av Azure Site Recovery för filresurser som finns på StorSimple
## <a name="overview"></a>Översikt
Microsoft Azure StorSimple är en hybrid cloud lagringslösning som åtgärdar svårigheter av Ostrukturerade data som vanligtvis är kopplad till filresurser. StorSimple använder lagringsutrymmet i molnet som ett tillägg för lösningen lokalt och nivåer data på lokal lagring och lagringsutrymmet i molnet. Inbyggt dataskydd med lokala och molnbaserade ögonblicksbilder, eliminerar behovet av en sprawling lagringsinfrastruktur.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) är en Azure-tjänst som tillhandahåller disaster recovery (DR) funktioner genom att samordna replikering, redundans och återställning av virtuella datorer. Azure Site Recovery stöder ett antal tekniker för replikering av att replikera konsekvent, skydda och växlar sömlöst över virtuella datorer och program till privata och offentliga eller värdbaserade moln.

Med hjälp av Azure Site Recovery replikering av virtuella datorer och StorSimple snapshot molnfunktioner, kan du skydda hela filen server-miljö. Vid ett avbrott måste använda du en enda klickning för att hämta filresurser online i Azure på bara några minuter.

Det här dokumentet beskrivs i detalj hur du kan skapa en lösning för katastrofåterställning för dina filresurser som finns på StorSimple-lagring och utföra planerade, oplanerade och testa redundans med en återställningsplan med ett klick. I princip visar hur du ändrar Recovery planera i Azure Site Recovery-valvet för att aktivera StorSimple växling vid fel under katastrofåterställning scenarier. Dessutom beskrivs förutsättningar och konfigurationer som stöds. Det här dokumentet förutsätter att du är bekant med grunderna i Azure Site Recovery och StorSimple arkitekturerna.

## <a name="supported-azure-site-recovery-deployment-options"></a>Stöds Azure Site Recovery-distributionsalternativ
Kunder kan distribuera filservrar som fysiska servrar eller virtuella datorer (VM) på Hyper-V- eller VMware och skapa filresurser från volymer högg utanför StorSimple lagring. Azure Site Recovery kan skydda både fysiska och virtuella distributioner till en sekundär plats eller till Azure. Det här dokumentet innehåller information om en DR-lösning med Azure som återställningsplatsen för en filserver VM finns på Hyper-V och filresurser på StorSimple-lagring. Andra scenarier där filservern VM är på en VMware-VM eller en fysisk dator kan implementeras på samma sätt.

## <a name="prerequisites"></a>Krav
Implementera en lösning för katastrofåterställning med ett klick som använder Azure Site Recovery för filresurser som finns på StorSimple-lagring har följande krav:

* Lokal fil för Windows Server 2012 R2-servern VM i Hyper-V eller VMware eller en fysisk dator
* StorSimple-enhet lokalt registrerad med Azure StorSimple manager
* StorSimple moln installation som skapats i Azure StorSimple manager. Anordningen kan lagras i ett avställning tillstånd.
* Filresurser som finns på de volymer som konfigurerats på StorSimple-lagringsenhet
* [Azure Site Recovery services-valvet](../site-recovery/site-recovery-vmm-to-vmm.md) skapas i en Microsoft Azure-prenumeration

Dessutom, om Azure är din återställningsplatsen, kör den [Azure virtuella Readiness Assessment tool](http://azure.microsoft.com/downloads/vm-readiness-assessment/) på virtuella datorer så att de är kompatibla med virtuella Azure-datorer och Azure Site Recovery services.

Att undvika fördröjning problem (vilket kan resultera i ökade kostnader), se till att du skapar din StorSimple-enhet för molnet, automation-konto och lagring eller konton i samma region.

## <a name="enable-dr-for-storsimple-file-shares"></a>Aktivera DR för StorSimple-filresurser
Varje komponent i den lokala miljön måste skyddas om du vill aktivera fullständig replikering och återställning. Det här avsnittet beskrivs hur du:

* Konfigurera Active Directory och DNS-replikering (valfritt)
* Använd Azure Site Recovery för att aktivera skydd på filservern VM
* Aktivera skyddet för StorSimple-volymer
* Konfigurera nätverket

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Konfigurera Active Directory och DNS-replikering (valfritt)
Om du vill skydda datorer som kör Active Directory och DNS så att de är tillgängliga på DR-plats måste du uttryckligen skyddar dem (så att filservrarna som är tillgängliga efter växling vid fel med autentisering). Det finns två rekommenderade alternativ baserat på komplexiteten i kundens lokala miljö.

#### <a name="option-1"></a>Alternativ 1
Om kunden har ett litet antal program, en enda domänkontrollant för hela lokal plats och fungerar inte över hela platsen, och vi rekommenderar att du använder Azure Site Recovery replikering replikeras domain controller datorn till en sekundär plats (Detta gäller för både plats-till-plats och platsen till Azure).

#### <a name="option-2"></a>Alternativ 2
Om kunden har ett stort antal program körs på en Active Directory-skog och misslyckas över några program i taget, så rekommenderar vi att ställa in ytterligare en domänkontrollant på DR-plats (en sekundär plats eller i Azure).

Referera till [DR automatisk lösning för Active Directory och DNS med hjälp av Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) anvisningar när du gör en domänkontrollant tillgänglig på DR-plats. I resten av det här dokumentet förutsätter vi att en domänkontrollant är tillgänglig på DR-plats.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Använd Azure Site Recovery för att aktivera skydd på filservern VM
Det här steget måste du förbereda lokala filen server-miljö, skapa och förbereda en Azure Site Recovery-valvet och aktivera filen skyddet för den virtuella datorn.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Om du vill förbereda filen lokalt server-miljö
1. Ange den **User Account Control** till **meddela aldrig**. Detta krävs så att du kan använda Azure automation-skript för att ansluta till iSCSI-mål efter redundans av Azure Site Recovery.

   1. Tryck på Windows-tangenten + Q och Sök efter **UAC**.
   2. Välj **ändra User Account Control inställningar**.
   3. Dra fältet längst ned mot **meddela aldrig**.
   4. Klicka på **OK** och välj sedan **Ja** när du tillfrågas.

      ![Inställningar för kontroll av användarkonto](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png)
2. Installera VM-agenten på varje filserver virtuella datorer. Detta krävs så att du kan köra Azure automation-skript på den över virtuella datorer.

   1. [Ladda ner agenten](http://aka.ms/vmagentwin) till `C:\\Users\\<username>\\Downloads`.
   2. Öppna Windows PowerShell i administratörsläge (Kör som administratör) och ange sedan följande kommando för att navigera till nedladdningsplatsen:

      `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

      > [!NOTE]
      > Filnamnet kan ändras beroende på vilken version.
      >
      >
3. Klicka på **Nästa**.
4. Acceptera den **villkoren i avtalet** och klicka sedan på **nästa**.
5. Klicka på **Slutför**.
6. Skapa filresurser med hjälp av volymer högg utanför StorSimple lagring. Mer information finns i [använda StorSimple Manager-tjänsten för att hantera volymer](storsimple-manage-volumes.md).

   1. Tryck på Windows-tangenten + Q på din lokala virtuella datorer och Sök efter **iSCSI**.
   2. Välj **iSCSI-initieraren**.
   3. Välj den **Configuration** fliken och kopiera namnet på initieraren.
   4. Logga in på [Azure-portalen](https://portal.azure.com/).
   5. Välj den **StorSimple** och markera sedan StorSimple Manager-tjänsten som innehåller den fysiska enheten.
   6. Skapa volymen behållare och sedan skapa volymerna. (Dessa volymer är för filen resurs/er på filservern virtuella datorer). Kopiera initierarnamnet och ge ett lämpligt namn för Access Control-poster när du skapar volymer.
   7. Välj den **konfigurera** och notera ned IP-adressen till enheten.
   8. På din lokala virtuella datorer går du till den **iSCSI-initieraren** igen och ange IP-Adressen i avsnittet Snabbanslut. Klicka på **Snabbanslut** (enheten ska nu ansluten).
   9. Öppna Azure portal och välj den **volymer och enheter** fliken. Klicka på **automatiskt konfigurera**. Den volym som du skapade bör visas.
   10. I portalen, väljer du den **enheter** och markera sedan **skapa en ny virtuell enhet.** (Den här virtuella enheten används om det uppstår redundans). Den här nya virtuella enheten kan lagras i ett offline-tillstånd att undvika extra kostnader. Om du vill koppla från den virtuella enheten, gå till den **virtuella datorer** avsnittet på portalen och stänga av.
   11. Gå tillbaka till de lokala virtuella datorerna och öppna Diskhantering (tryck på Windows-tangenten + X och välj **Diskhantering**).
   12. Du ser några extra diskar (beroende på antalet volymer som du har skapat). Högerklicka på först en, Välj **initiera Disk**, och välj **OK**. Högerklicka på den **inte allokerat** väljer **ny enkel volym**, tilldela den en enhetsbeteckning och slutför guiden.
   13. Upprepa steg l för alla diskar. Du kan nu se alla diskar på **detta PC** i Utforskaren.
   14. Använda rollen fil- och lagringstjänster för att skapa filresurser på dessa volymer.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Att skapa och förbereda ett Azure Site Recovery-valv
Referera till den [dokumentation för Azure Site Recovery](../site-recovery/site-recovery-hyper-v-site-to-azure.md) att komma igång med Azure Site Recovery innan du skyddar filservern VM.

#### <a name="to-enable-protection"></a>Att aktivera skydd
1. Koppla från iSCSI-target(s) från de lokala virtuella datorer som du vill skydda via Azure Site Recovery:

   1. Tryck på Windows-tangenten + Q och Sök efter **iSCSI**.
   2. Välj **Konfigurera iSCSI-initieraren**.
   3. Koppla från den virtuella StorSimple-enheten som du tidigare anslutning. Alternativt kan du växla ut filservern för några minuter när skyddet aktiverades.

   > [!NOTE]
   > Detta innebär att filresurserna blir tillfälligt otillgänglig.
   >
   >
2. [Aktivera skydd för virtuella datorer](../site-recovery/site-recovery-hyper-v-site-to-azure.md) på filservern VM från Azure Site Recovery-portalen.
3. När den första synkroniseringen startar återansluta du målet igen. Gå till iSCSI-initieraren Välj StorSimple-enheten och klicka på **Anslut**.
4. När synkroniseringen har slutförts och status för den virtuella datorn är **skyddade**väljer du den virtuella datorn, väljer den **konfigurera** fliken och uppdateras i nätverket för den virtuella datorn (detta är det nätverk som den redundansväxlade virtuella datorer ska vara en del av). Om nätverket inte visas, betyder det att synkroniseringen fortfarande pågår.

### <a name="enable-protection-of-storsimple-volumes"></a>Aktivera skyddet för StorSimple-volymer
Om du inte har valt den **aktiverar en standard säkerhetskopiering för den här volymen** för StorSimple-volymer, gå till **principer för säkerhetskopiering** i StorSimple Manager-tjänsten och skapa en lämplig säkerhetskopieringsprincip för alla volymer. Vi rekommenderar att du ställer in frekvensen av säkerhetskopieringar återställningspunktmål (RPO) som du skulle vilja se för programmet.

### <a name="configure-the-network"></a>Konfigurera nätverket
Konfigurera nätverksinställningar i Azure Site Recovery för filserver VM så att de Virtuella datornätverk som är kopplade till rätt DR-nätverk efter växling vid fel.

Du kan välja den virtuella datorn i den **replikerade objekt** att konfigurera inställningar för nätverk, som visas i följande bild.

![Beräkning och nätverk](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan
Du kan skapa en återställningsplan i ASR att automatisera redundans av filresurser. Om ett avbrott inträffar kan få du filresurserna upp några minuter med bara en enda klickning. Om du vill aktivera det här automation behöver ett Azure automation-konto.

#### <a name="to-create-an-automation-account"></a>Skapa ett Automation-konto
1. Gå till Azure-portalen &gt; **Automation** avsnitt.
2. Klicka på **+ Lägg till** knappen öppnas under bladet.

   ![Lägga till ett Automation-konto](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)

   * Name - ange ett nytt automation-konto
   * Prenumeration – Välj prenumeration
   * Resource group - skapa nya/välja befintlig resursgrupp
   * Plats – Välj plats, förvara den på samma geo eller region där StorSimple-enhet för molnet och Storage-konton har skapats.
   * Skapa kör som-kontot Azure - Välj **Ja** alternativet.

3. Gå till Automation-kontot **Runbooks** &gt; **Bläddra galleriet** att importera alla runbooks till automation-kontot.
4. Lägg till följande runbooks genom att söka efter **Disaster Recovery** tagg i galleriet:

   * Rensa av StorSimple-volymer efter testa redundans (TFO)
   * Failover StorSimple volymbehållare
   * Montera volymer på StorSimple-enhet efter växling vid fel
   * Avinstallera tillägget för anpassat skript i Azure VM
   * Starta virtuella StorSimple-enhet

     ![Bläddra-galleriet](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)

5. Publicera alla skript genom att välja runbook i automation-kontot och klickar på **redigera** &gt; **publicera** och sedan **Ja** i verifieringsmeddelandet. Efter det här steget i **Runbooks** fliken visas på följande sätt:

    ![Runbooks](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)

6. I automation-konto klickar du på **variabler** &gt; **lägga till en variabel** och Lägg till följande variabler. Du kan välja att kryptera dessa tillgångar. Dessa variabler är särskilda återställningsplan. Om din återställningspunkt planera är som du skapar i nästa steg TestPlan, och sedan variabler ska vara TestPlan-StorSimRegKey TestPlan AzureSubscriptionName och så vidare.

   * **BaseUrl**: den resurs-url för Azure-molnet. Få med **Get-AzureRmEnvironment | Select-Object-namn, ResourceManagerUrl** cmdlet.
   * *RecoveryPlanName***- ResourceGroupName**: I Resource Manager-grupp som har StorSimple-resursen.
   * *RecoveryPlanName***- ManagerName**: I StorSimple-resurs som har StorSimple-enhet.
   * *RecoveryPlanName***- DeviceName**: I StorSimple-enhet som ska växlas.
   * *RecoveryPlanName***- DeviceIpAddress**: IP-adressen till enheten (detta finns i den **enheter** StorSimple Enhetshanteraren på fliken &gt; **Inställningar** &gt; **nätverk** &gt; **DNS-inställningarna** grupp).
   * *RecoveryPlanName***- VolumeContainers**: en kommaavgränsad sträng med volymbehållare finns på enheten som behöver flyttas över, till exempel: volcon1 volcon2, volcon3.
   * *RecoveryPlanName***- TargetDeviceName**: I StorSimple moln enhet där behållarna är att växlas vid fel.
   * *RecoveryPlanName***- TargetDeviceIpAddress**: IP-adress på målenheten (detta finns i den **virtuella** avsnittet &gt; **inställningar**  grupp &gt; **nätverk** fliken).
   * *RecoveryPlanName***- StorageAccountName**: lagringskontonamnet skript (som har att köras på den över VM) kommer att lagras. Detta kan vara storage-konto som har utrymme att lagra skriptet tillfälligt.
   * *RecoveryPlanName***- StorageAccountKey**: åtkomstnyckeln för lagringskontot ovan.
   * *RecoveryPlanName***- VMGUIDS**: när du skyddar en VM Azure Site Recovery tilldelar varje VM ett unikt ID som ger information om den misslyckade över VM. Om du vill hämta VMGUID, Välj den **återställningstjänster** och klicka på **skyddade objekt** &gt; **Skyddsgrupper** &gt; **datorer** &gt; **egenskaper**. Om du har flera virtuella datorer lägger sedan till GUID som en kommaavgränsad sträng.

    Om namnet på återställningsplanen är fileServerpredayRP, till exempel sedan din **variabler**, **anslutningar** och **certifikat** fliken bör se ut så här när du har lagt till alla tillgångar.

    ![Tillgångar](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

7. Överför StorSimple 8000-serien Runbook-modulen i Automation-kontot. Använd den nedanstående steg för att lägga till en modul:

   a. Öppna powershell, skapa en ny mapp och ändra katalogen till mappen.
    
    ```
         mkdir C:\scripts\StorSimpleSDKTools
         cd C:\scripts\StorSimpleSDKTools
    ```
   b. Hämta nuget CLI under samma mapp i steg 1.
      Olika versioner av nuget.exe finns på [nuget hämtar](https://www.nuget.org/downloads). Varje hämtningslänken pekar direkt till en .exe-fil, så se till att högerklicka på och spara filen på datorn i stället körs från webbläsaren.

    ```
         wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

   c. Hämta beroende SDK

    ```
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

   d. Skapa en Azure Automation Runbook-modulen för hantering av enheter StorSimple 8000-serien. Använd den nedan kommandon för att skapa ett Automation-modul zip-filen.

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

     e. Importera Azure Automation-modul zip-filen (Microsoft.Azure.Management.StorSimple8000Series.zip) i ovanstående steg. Detta kan göras genom att välja Automation-kontot, klickar du på **moduler** under delade resurser och klicka sedan på **lägga till en modul**.

    När du har importerat modulen StorSimple 8000-serien i **moduler** fliken bör visas på följande sätt:

    ![Moduler](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

8. Gå till den **återställningstjänster** och välj det Azure Site Recovery-valvet som du skapade tidigare.
9. Välj den **Återställningsplaner (Site Recovery)** alternativet från **hantera** grupp och skapa en ny återställningsplan som följer:

   a.  Klicka på **+ Återställ plan** knappen öppnas under bladet.

      ![Skapa en återställningsplan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)

   b.  Ange ett namn på recovery plan, Välj källa, mål och distribution av modellen värden.

   c.  Välj de virtuella datorerna från den skyddsgrupp som du vill inkludera i återställningsplanen och på **OK** knappen.

   d.  Välj återställningsplan som du skapade tidigare, klicka på **anpassa** för att öppna vyn Recovery plan anpassning.

   e.  Högerklicka på **alla grupper avstängning** och klicka på **lägga till före åtgärden**.

   f.  Öppnar Infoga åtgärd bladet, anger du ett namn, Välj **primära sidan** alternativ i hur du kör alternativet, markera Automation-konto (som du har lagt till runbooks) och välj sedan den **Failover-StorSimple-volym-behållare** runbook.

   g.  Högerklicka på **grupp 1: starta** och på **Lägg till skyddade objekt** alternativet och välj sedan de virtuella datorer som ska skyddas i återställningsplanen och klicka på **Ok** knappen. Valfritt, om det redan valt virtuella datorer.

   h.  Högerklicka på **grupp 1: starta** och klicka på **efter åtgärden** alternativet och lägger till följande skript:

   * Start-StorSimple-virtuell-installation-runbook
   * Växla över-StorSimple-volym-behållare runbook
   * Montera volymer efter redundans runbook
   * Avinstallera –--tillägget för anpassat skript runbook

   Jag.  Lägg till en manuell åtgärd efter ovan 4 skript i samma **grupp 1: efter steg** avsnitt. Den här åtgärden är den punkt där du kan kontrollera att allt fungerar korrekt. Den här åtgärden måste du lägga till som en del av redundanstest (så att bara välja de **testa redundans** kryssrutan).

   j.  När den manuella åtgärden, lägga till den **Rensa** skript på samma sätt som du använde för den andra runbooks. **Spara** återställningsplanen.

    > [!NOTE]
    > När du kör ett redundanstest, bör du kontrollera allt på den manuella åtgärden eftersom StorSimple-volymer som hade varit klonas på målenheten tas bort som en del av rensningen när den manuella åtgärden har slutförts.
    >

    ![Recoery plan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Utför ett redundanstest
Referera till den [Active Directory DR lösning](../site-recovery/site-recovery-active-directory.md) handbok för specifika saker du bör Active Directory när du testar redundansen. Lokal installation påverkas inte alls när du testar redundansen inträffar. StorSimple-volymer som kopplats till den virtuella datorn lokalt klonas till StorSimple-enhet för molnet på Azure. En virtuell dator för testning tas i Azure och de klonade volymerna som är kopplade till den virtuella datorn.

#### <a name="to-perform-the-test-failover"></a>För att testa redundans
1. Välj Site Recovery-valvet i Azure-portalen.
2. Klicka på återställningsplan som skapats för filservern VM.
3. Klicka på **Redundanstestningen**.
4. Välj det virtuella Azure-nätverket som virtuella Azure-datorer ska ansluta till efter redundansväxlingen.

   ![Starta redundans](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
5. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet genom att klicka på den virtuella datorn för att öppna dess egenskaper eller på den **redundans testjobb** i valvnamnet &gt; **jobb** &gt; **Site Recovery-jobb**.
6. När redundansväxlingen är klar bör du även att kunna se Azure datorn visas i Azure portal &gt; **virtuella datorer**. Du kan utföra dina verifieringar.
7. När validering är klar klickar du på **verifieringar fullständig**. Detta tar bort StorSimple-volymer och stänga av StorSimple moln-enhet.
8. När du är klar klickar du på **Rensa redundanstestet** på återställningsplanen. Ange i anteckningar och spara observationer från redundanstestningen. Detta tar bort den virtuella datorn som skapades under testning av redundans.

## <a name="perform-a-planned-failover"></a>Utför en planerad redundansväxling
   Under en planerad redundans filserver lokal virtuell dator är avstängd smidigt och ett moln ögonblicksbild volymer på StorSimple-enheten har tagits. StorSimple-volymer har redundansväxlats till den virtuella enheten, replikerade virtuella datorn tas på Azure och volymerna som är kopplade till den virtuella datorn.

#### <a name="to-perform-a-planned-failover"></a>Utföra en planerad redundans
1. I Azure portal, väljer **återställningstjänster** valvet &gt; **återställningsplaner (Site Recovery)** &gt; **recoveryplan_name** skapas för filservern VM.
2. Klicka på bladet Recovery plan **mer** &gt; **planerad redundans**.

   ![Återställningsplan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
3. På den **bekräfta planerad redundans** bladet Välj käll- och målplatserna och välj målnätverket och klicka på kryssikonen ✓ att starta processen för växling vid fel.
4. När du har skapat virtuella replikdatorer som de är i en bekräftelse av väntande tillstånd. Klicka på **Commit** att genomföra redundans.
5. När replikeringen är klar, virtuella datorer som startas på den sekundära platsen.

## <a name="perform-a-failover"></a>Utför en växling vid fel
StorSimple-volymer har redundansväxlats till den virtuella enheten under en oplanerad redundans, replikerade virtuella datorn kommer på Azure och volymerna som är kopplade till den virtuella datorn.

#### <a name="to-perform-a-failover"></a>Du utför en växling vid fel
1. I Azure portal, väljer **återställningstjänster** valvet &gt; **återställningsplaner (Site Recovery)** &gt; **recoveryplan_name** skapas för filservern VM.
2. Klicka på bladet Recovery plan **mer** &gt; **redundans**.
3. På den **bekräfta redundans** bladet Välj källa och mål-platser.
4. Välj **Stäng virtuella datorer och synkronisera senaste data** att ange att Site Recovery ska försöka stänga av den skyddade virtuella datorn och synkronisera data så att den senaste versionen av data kommer att redundansväxlas.
5. Efter växling vid fel är de virtuella datorerna i en bekräftelse av väntande tillstånd. Klicka på **Commit** att genomföra redundans.


## <a name="perform-a-failback"></a>Utför en återställning efter fel
Under en återställning efter fel misslyckades StorSimple volymbehållare över tillbaka till den fysiska enheten efter en säkerhetskopia görs.

#### <a name="to-perform-a-failback"></a>Utföra en återställning efter fel
1. I Azure portal, väljer **återställningstjänster** valvet &gt; **återställningsplaner (Site Recovery)** &gt; **recoveryplan_name** skapas för filservern VM.
2. Klicka på bladet Recovery plan **mer** &gt; **planerad redundans**.
3. Välj käll- och målplatserna, Välj lämplig datasynkronisering och alternativ för att skapa VM.
4. Klicka på **OK** för att starta processen för återställning efter fel.

   ![Starta återställning efter fel](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Metodtips
### <a name="capacity-planning-and-readiness-assessment"></a>Kapacitet planerings- och readiness assessment
#### <a name="hyper-v-site"></a>Hyper-V-platsen
Använd den [användaren kapacitetsplaneringsverktyget för](http://www.microsoft.com/download/details.aspx?id=39057) att utforma server, lagring och nätverkets infrastruktur för din miljö för Hyper-V-replikering.

#### <a name="azure"></a>Azure
Du kan köra den [Azure virtuella Readiness Assessment tool](http://azure.microsoft.com/downloads/vm-readiness-assessment/) på virtuella datorer så att de är kompatibla med virtuella Azure-datorer och Azure Site Recovery Services. Readiness Assessment Tool kontrollerar VM konfigurationer och varnar när konfigurationer är inte kompatibla med Azure. Till exempel utfärdar den en varning om en enhet C: är större än 127 GB.

Kapacitetsplanering består av minst två viktiga processer:

* Mappning av lokal Hyper-V virtuella datorer till Azure VM-storlekar (till exempel A6, A7, A8 och A9).
* Avgör krävs Internet-bandbredd.

## <a name="limitations"></a>Begränsningar
* För närvarande kan endast 1 StorSimple-enhet flyttas över (för att en enda StorSimple moln-installation). Scenario för en filserver som sträcker sig över flera StorSimple-enheter stöds inte ännu.
* Om du får ett fel medan skyddet aktiveras för en virtuell dator kan du kontrollera att du har kopplat från iSCSI-mål.
* Alla volymbehållare som grupperas tillsammans på grund av principer för säkerhetskopiering över volymbehållare kommer att växlas vid fel tillsammans.
* Alla volymer i volymbehållare som du har valt kommer att redundansväxlas.
* Volymer som lägga till upp till fler än 64 TB går inte att redundansväxla eftersom maximal kapacitet för en enskild StorSimple moln installation är 64 TB.
* Om planerad/oplanerad växling vid fel misslyckas och de virtuella datorerna har skapats i Azure, sedan inte rensa upp de virtuella datorerna. I stället göra en återställning efter fel. Om du tar bort de virtuella datorerna sedan de lokala virtuella datorerna kan inte aktiveras igen.
* Efter en växling, om du inte kan se volymer, gå till de virtuella datorerna, öppna Diskhantering, skanna om diskarna och tar dem online.
* I vissa fall kan enhetsbeteckningar i DR-plats skilja sig från bokstäver lokal. Om detta inträffar kan behöver du åtgärda problemet manuellt när redundansväxlingen är klar.
* Timeout för failover-jobb: I StorSimple-skriptet kommer om redundans för volymbehållare tar längre tid än Azure Site Recovery-gränsen per skript (för närvarande 120 minuter).
* Säkerhetskopieringsjobbet timeout: I StorSimple-skriptet på grund av timeout om säkerhetskopiering av volymer tar längre tid än Azure Site Recovery-gränsen per skript (för närvarande 120 minuter).

  > [!IMPORTANT]
  > Kör säkerhetskopieringen manuellt från Azure-portalen och kör sedan återställningsplanen igen.

* Klona jobbet timeout: I StorSimple-skriptet på grund av timeout om Kloningen av volymer tar längre tid än Azure Site Recovery-gränsen per skript (för närvarande 120 minuter).
* Tid synkroniseringsfel: I StorSimple skript fel säger att säkerhetskopiorna inte genomfördes även om säkerhetskopieringen har slutförts i portalen. En möjlig orsak till detta kan vara att tiden för StorSimple-enhet kan vara synkroniserad med den aktuella tiden i tidszon.

  > [!IMPORTANT]
  > Synkronisera installation tiden med den aktuella tiden i tidszon.

* Installation failover-fel: det StorSimple skript kan misslyckas om det är en installation redundans när återställningsplanen körs.

  > [!IMPORTANT]
  > Kör återställningsplanen när enheten redundansväxlingen är klar.


## <a name="summary"></a>Sammanfattning
Med Azure Site Recovery kan du skapa en fullständig automatiserad haveriberedskapsplan för en virtuell dator för filserver med filresurser som finns på StorSimple-lagring. Du kan påbörja redundans inom några sekunder från valfri plats vid ett avbrott och hämta programmet på några minuter.
