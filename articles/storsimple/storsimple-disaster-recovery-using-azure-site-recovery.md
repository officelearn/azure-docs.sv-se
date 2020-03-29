---
title: Automatisera StorSimple fileshare DR med Azure Site Recovery | Microsoft-dokument
description: I artikeln beskrivs de steg och metodtips som används för att skapa en lösning för haveriberedskap för filresurser som finns på Microsoft Azure StorSimple-lagring.
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
ms.author: alkohli
ms.openlocfilehash: 650798fdb884e6494990efb533335a1dd8b4d89f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67875399"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatisk katastrofåterställningslösning med Azure Site Recovery för filresurser som finns på StorSimple

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
Microsoft Azure StorSimple är en hybridmolnlagringslösning som åtgärdar komplexiteten i ostrukturerade data som vanligen associeras med filresurser. StorSimple använder molnlagring som ett tillägg av den lokala lösningen och automatiskt nivåer data över lokal lagring och molnlagring. Integrerat dataskydd, med lokala ögonblicksbilder och ögonblicksbilder i molnet, eliminerar behovet av en utbredd lagringsinfrastruktur.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) är en Azure-baserad tjänst som tillhandahåller DR-funktioner (Disaster Recovery) genom att dirigera replikering, redundans och återställning av virtuella datorer. Azure Site Recovery stöder ett antal replikeringstekniker för att konsekvent replikera, skydda och sömlöst växla över virtuella datorer och program till privata/offentliga eller värdbaserade moln.

Med azure site recovery, virtual machine replication och StorSimple cloud snapshot capabilities kan du skydda den fullständiga filservermiljön. I händelse av ett avbrott kan du använda ett enda klick för att få dina filresurser online i Azure på bara några minuter.

I det här dokumentet beskrivs i detalj hur du kan skapa en katastrofåterställningslösning för filresurserna som finns på StorSimple-lagring och utföra planerade, oplanerade och testa redundans med hjälp av en återställningsplan med ett klick. I huvudsak visar den hur du kan ändra återställningsplanen i ditt Azure Site Recovery-valv för att aktivera StorSimple-redundans under katastrofscenarier. Dessutom beskrivs konfigurationer och förutsättningar som stöds. Det här dokumentet förutsätter att du är bekant med grunderna i Azure Site Recovery och StorSimple-arkitekturer.

## <a name="supported-azure-site-recovery-deployment-options"></a>Distributionsalternativ för Azure Site Recovery som stöds
Kunder kan distribuera filservrar som fysiska servrar eller virtuella datorer som körs på Hyper-V eller VMware och sedan skapa filresurser från volymer som har skurits ut från StorSimple-lagring. Azure Site Recovery kan skydda både fysiska och virtuella distributioner till antingen en sekundär plats eller till Azure. Det här dokumentet innehåller information om en DR-lösning med Azure som återställningsplats för en virtuell filserver som finns på Hyper-V och med filresurser på StorSimple-lagring. Andra scenarier där den virtuella filservern är på en virtuell virtuell VMware-dator eller en fysisk dator kan implementeras på samma sätt.

## <a name="prerequisites"></a>Krav
Att implementera en katastrofåterställningslösning med ett klick som använder Azure Site Recovery för filresurser som finns på StorSimple-lagring har följande förutsättningar:

   - Lokal windows server 2012 R2-filserver vm som finns på Hyper-V eller VMware eller en fysisk dator
   - StorSimple-lagringsenhet lokalt registrerad hos Azure StorSimple-hanteraren
   - StorSimple Cloud Appliance som skapats i Azure StorSimple-hanteraren. Apparaten kan hållas i avstängningsläge.
   - Filresurser som finns på de volymer som konfigurerats på StorSimple-lagringsenheten
   - [Azure Site Recovery services-valv](../site-recovery/site-recovery-vmm-to-vmm.md) som skapats i en Microsoft Azure-prenumeration

Om Azure är din återställningsplats kör du dessutom verktyget för utvärdering av [Azure Virtual Machine Readiness Assessment](https://azure.microsoft.com/downloads/vm-readiness-assessment/) på virtuella datorer för att säkerställa att de är kompatibla med virtuella Azure-datorer och Azure Site Recovery-tjänster.

Undvik latensproblem (vilket kan leda till högre kostnader) och se till att du skapar din StorSimple Cloud Appliance, automationskonto och lagringskonto i samma region.

## <a name="enable-dr-for-storsimple-file-shares"></a>Aktivera DR för StorSimple-filresurser
Varje komponent i den lokala miljön måste skyddas för att möjliggöra fullständig replikering och återställning. I det här avsnittet beskrivs hur du:
    
   - Konfigurera Active Directory- och DNS-replikering (valfritt)
   - Använd Azure Site Recovery för att aktivera skydd av den virtuella filservern
   - Aktivera skydd av StorSimple-volymer
   - Konfigurera nätverket

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Konfigurera Active Directory- och DNS-replikering (valfritt)
Om du vill skydda datorerna som kör Active Directory och DNS så att de är tillgängliga på DR-platsen måste du uttryckligen skydda dem (så att filservrarna är tillgängliga efter redundans med autentisering). Det finns två rekommenderade alternativ baserat på komplexiteten i kundens lokala miljö.

#### <a name="option-1"></a>Alternativ 1
Om kunden har ett litet antal program, en enda domänkontrollant för hela den lokala platsen och kommer att misslyckas över hela platsen, rekommenderar vi att du använder Azure Site Recovery-replikering för att replikera domänkontrollantdatorn till en sekundär (detta gäller för både plats-till-plats och plats-till-Azure).

#### <a name="option-2"></a>Alternativ 2
Om kunden har ett stort antal program, kör en Active Directory-skog och misslyckas med några program åt gången, rekommenderar vi att du konfigurerar ytterligare en domänkontrollant på DR-platsen (antingen en sekundär plats eller i Azure).

Se [Automatisk DR-lösning för Active Directory och DNS med Hjälp av Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) för instruktioner när du gör en domänkontrollant tillgänglig på DR-platsen. För resten av det här dokumentet förutsätter vi att en domänkontrollant är tillgänglig på DR-platsen.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Använd Azure Site Recovery för att aktivera skydd av den virtuella filservern
Det här steget kräver att du förbereder den lokala filservermiljön, skapar och förbereder ett Azure Site Recovery-valv och aktiverar filskydd för den virtuella datorn.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Så här förbereder du den lokala filservermiljön
1. Ange att kontroll för **användarkonto** **aldrig ska meddela**. Detta krävs så att du kan använda Azure automation-skript för att ansluta iSCSI-målen efter redundans av Azure Site Recovery.
   
   1. Tryck på Windows-tangenten +Q och sök efter **UAC**.  
   1. Välj **Ändra inställningar för kontroll av användarkonto**.  
   1. Dra fältet längst ned mot **Meddela aldrig**.  
   1. Klicka på **OK** och välj sedan **Ja** när du uppmanas att göra det.  
   
      ![Inställningar för kontroll av användarkonto](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Installera VM-agenten på var och en av virtuella filserverns virtuella datorer. Detta krävs så att du kan köra Azure automation-skript på misslyckade över virtuella datorer.
   
   1. [Ladda ner agenten](https://aka.ms/vmagentwin) till `C:\\Users\\<username>\\Downloads`.
   1. Öppna Windows PowerShell i administratörsläge (Kör som administratör) och ange sedan följande kommando för att navigera till hämtningsplatsen:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > Filnamnet kan ändras beroende på version.
      
1. Klicka på **Nästa**.
1. Acceptera **avtalsvillkoren** och klicka sedan på **Nästa**.
1. Klicka på **Slutför**.
1. Skapa filresurser med hjälp av volymer som snidats ut från StorSimple-lagring. Mer information finns i [Använda Tjänsten StorSimple Manager för att hantera volymer](storsimple-manage-volumes.md).
   
   1. På dina lokala virtuella datorer trycker du på Windows-tangenten +Q och söker efter **iSCSI**.
   1. Välj **iSCSI-initierare**.
   1. Välj fliken **Konfiguration** och kopiera initierarnamnet.
   1. Logga in på [Azure-portalen](https://portal.azure.com/).
   1. Välj fliken **StorSimple** och välj sedan den StorSimple Manager-tjänst som innehåller den fysiska enheten.
   1. Skapa volymbehållare och skapa sedan volymer. (Dessa volymer är för filresurserna på virtuella filserverns datorer. Kopiera initierarnamnet och ange ett lämpligt namn för åtkomstkontrollposterna när du skapar volymerna.
   1. Välj fliken **Konfigurera** och anteckna enhetens IP-adress.
   1. På dina lokala virtuella datorer går du till **iSCSI-initieraren** igen och anger IP-adressen i avsnittet Snabbanslutning. Klicka på **Snabbanslutning** (enheten ska nu anslutas).
   1. Öppna Azure-portalen och välj fliken **Auto Configure** **Volymer och enheter.** Volymen som du skapade ska visas.
   1. Välj fliken **Enheter** i portalen och välj sedan **Skapa en ny virtuell enhet.** (Den här virtuella enheten används om en redundans inträffar). Den här nya virtuella enheten kan hållas offline för att undvika extra kostnader. Om du vill koppla från den virtuella enheten går du till avsnittet **Virtuella datorer** på portalen och stänger av den.
   1. Gå tillbaka till de lokala virtuella datorerna och öppna Diskhantering (tryck på Windows -tangenten + X och välj **Diskhantering**).
   1. Du kommer att märka några extra diskar (beroende på antalet volymer du har skapat). Högerklicka på den första, välj **Initiera disk**och välj **OK**. Högerklicka på avsnittet **Ej allokerat,** välj **Ny enkel volym,** tilldela den en enhetsbeteckning och avsluta guiden.
   1. Upprepa steg i för alla diskar. Du kan nu se alla diskar på den **här datorn** i Utforskaren.
   1. Använd rollen Fil- och lagringstjänster för att skapa filresurser på dessa volymer.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Så här skapar och förbereder du ett Azure Site Recovery-valv
Se [Azure Site Recovery-dokumentationen](../site-recovery/site-recovery-hyper-v-site-to-azure.md) för att komma igång med Azure Site Recovery innan du skyddar filservern VM.

#### <a name="to-enable-protection"></a>Så här aktiverar du skydd
1. Koppla från iSCSI-mål från de lokala virtuella datorer som du vill skydda via Azure Site Recovery:
   
   1. Tryck på Windows -tangenten + Q och sök efter **iSCSI**.
   1. Välj **Ställ in iSCSI-initierare**.
   1. Koppla bort StorSimple-enheten som du anslöt tidigare. Du kan också stänga av filservern i några minuter när du aktiverar skyddet.
      
   > [!NOTE]
   > Detta medför att filresurserna inte är tillgängliga för tillfället.
   
1. [Aktivera skydd](../site-recovery/site-recovery-hyper-v-site-to-azure.md) för virtuella datorer för den virtuella datorn från Azure Site Recovery-portalen.
1. När den första synkroniseringen börjar kan du återansluta målet igen. Gå till iSCSI-initieraren, välj StorSimple-enheten och klicka på **Anslut**.
1. När synkroniseringen är klar och statusen för den virtuella datorn är **skyddad**väljer du den virtuella datorn, väljer fliken **Konfigurera** och uppdaterar nätverket för den virtuella datorn i enlighet med detta (det här är det nätverk som misslyckades över virtuella datorer kommer att vara en del av). Om nätverket inte visas betyder det att synkroniseringen fortfarande pågår.

### <a name="enable-protection-of-storsimple-volumes"></a>Aktivera skydd av StorSimple-volymer
Om du inte har valt alternativet **Aktivera en standardsäkerhetskopiering för** den här volymen för StorSimple-volymerna går du till Principer för **säkerhetskopiering** i Tjänsten StorSimple Manager och skapar en lämplig säkerhetskopieringsprincip för alla volymer. Vi rekommenderar att du ställer in frekvensen för säkerhetskopior till återställningspunktsmålet (RPO) som du vill se för programmet.

### <a name="configure-the-network"></a>Konfigurera nätverket
Konfigurera nätverksinställningarna i Azure Site Recovery för filservern så att VM-nätverken är anslutna till rätt DR-nätverk efter redundans.

Du kan välja den virtuella datorn på fliken **Replikerade objekt** för att konfigurera nätverksinställningarna, vilket visas i följande bild.

![Beräkning och nätverk](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan
Du kan skapa en återställningsplan i ASR för att automatisera redundansprocessen för filresurserna. Om ett avbrott inträffar kan du ta upp filresurserna på några minuter med bara ett enda klick. För att aktivera den här automatiseringen behöver du ett Azure automation-konto.

#### <a name="to-create-an-automation-account"></a>Så här skapar du ett Automation-konto
1. Gå till avsnittet &gt; Azure portal **Automation.**
1. Klicka på **+ Lägg till** knappen, öppnas under bladet.
   
   ![Lägga till ett Automation-konto](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Namn - Ange ett nytt automationskonto
   - Prenumeration - Välj prenumeration
   - Resursgrupp - Skapa ny/välj befintlig resursgrupp
   - Plats - Välj plats, förvara den i samma geo/region där StorSimple Cloud Appliance och Storage Accounts skapades.
   - Skapa Azure Run As-konto - Välj **ja-alternativ.**
   
1. Gå till Automation-kontot, klicka på **Runbooks** &gt; **Bläddra i galleri** för att importera alla nödvändiga runbooks till automationskontot.
1. Lägg till följande runbooks genom att hitta taggen **Haveriberedskap** i galleriet:
   
   - Rensa Upp i StorSimple-volymer efter testundans (TFO)
   - StorSimple-volymbehållare med redundans
   - Montera volymer på StorSimple-enheten efter redundans
   - Avinstallera anpassat skripttillägg i Azure VM
   - Starta StorSimple Virtual Appliance
   
      ![Bläddra i galleri](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Publicera alla skript genom att välja runbook i automationskontot och klicka på **Redigera** &gt; **publicera** och sedan **Ja** till verifieringsmeddelandet. Efter det här steget visas fliken **Runbooks** på följande sätt:
   
   ![Runbooks](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Klicka på Variabler **Lägg till en variabel** i **automatiseringskontot** &gt; och lägg till följande variabler. Du kan välja att kryptera dessa tillgångar. Dessa variabler är specifika för återställningsplanen. Om återställningsplanen, som du ska skapa i nästa steg, är namnet TestPlan, bör dina variabler vara TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName och så vidare.

   - **BaseUrl**: Url:en för Resource Manager för Azure-molnet. Få använda **Get-AzEnvironment | Välj objektnamn, ResourceManagerUrl** cmdlet.
   - _RecoveryPlanName_**-ResourceGroupName**: Resurshanteraren-gruppen som har StorSimple-resursen.
   - _RecoveryPlanName_**-ManagerName**: StorSimple-resursen som har StorSimple-enheten.
   - _RecoveryPlanName_**-DeviceName**: Den StorSimple-enhet som måste skickas över.
   - _RecoveryPlanName_**-DeviceIpAddress**: Enhetens IP-adress (detta finns på fliken **Enheter** under &gt; **avsnittet** &gt; Inställningar för **Nätverks-DNS-inställningar** **Network** &gt; i StorSimple-
   - _RecoveryPlanName_**-VolumeContainers**: En kommaavgränsad sträng med volymbehållare som finns på enheten som måste skickas över. till exempel: volcon1, volcon2, volcon3.
   - _RecoveryPlanName_**-TargetDeviceName**: StorSimple Cloud Appliance som behållarna ska skickas över på.
   - _RecoveryPlanName_**-TargetDeviceIpAddress**: MÅLenhetens IP-adress (detta finns i &gt; avsnittet &gt; **Inställningar** för **virtuell dator** **Nätverk).**
   - _RecoveryPlanName_**-StorageAccountName**: Det lagringskontonamn där skriptet (som måste köras på den misslyckade över den virtuella datorn) lagras. Detta kan vara alla lagringskonto som har visst utrymme för att lagra skriptet tillfälligt.
   - _RecoveryPlanName_**-StorageAccountKey**: Åtkomstnyckeln för det ovannämnda lagringskontot.
   - _RecoveryPlanName_**-VMGUIDS**: När en virtuell dator skyddas tilldelar Azure Site Recovery varje virtuell dator ett unikt ID som ger information om den misslyckade över den virtuella datorn. Om du vill hämta VMGUID markerar du fliken **Återställningstjänster** och klickar på &gt; **Egenskaper**för skyddade &gt; **objektskyddsgrupper** &gt; **maskiner** . **Protected Item** Om du har flera virtuella datorer lägger du till GUID:erna som en kommaavgränsad sträng.

     Om namnet på återställningsplanen till exempel är fileServerpredayRP ska fliken **Variabler,** **Anslutningar** och **Certifikat** visas på följande sätt när du har lagt till alla tillgångar.

      ![Tillgångar](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Ladda upp StorSimple 8000-seriens Runbook-modul i ditt Automation-konto. Använd stegen nedan för att lägga till en modul:
   
   1. Öppna powershell, skapa en ny mapp & ändra katalogen till mappen.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Ladda ner nuget CLI under samma mapp i steg1.
      Olika versioner av nuget.exe finns på [nuget nedladdningar](https://www.nuget.org/downloads). Varje nedladdningslänk pekar direkt på en EXE-fil, så se till att högerklicka och spara filen på datorn i stället för att köra den från webbläsaren.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Ladda ner den beroende SDK
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Skapa en Azure Automation Runbook-modul för enhetshantering i StorSimple 8000-serien. Använd kommandona nedan för att skapa en zip-fil för automationsmodul.
         
      ```powershell
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
         
   1. Importera zip-filen för Azure Automation-modulen (Microsoft.Azure.Management.StorSimple8000Series.zip) som skapats i ovanstående steg. Detta kan göras genom att välja Automation-konto, klicka på **Moduler** under DELADE RESURSER och sedan klicka på **Lägg till en modul**.
   
   När du har importerat modulen StorSimple 8000-serien ska fliken **Moduler** visas på följande sätt:
   
      ![Moduler](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Gå till avsnittet **Återställningstjänster** och välj det Azure Site Recovery-valv som du skapade tidigare.
1. Välj alternativet **Återställningsplaner (Site Recovery)** **från** hantera gruppen och skapa en ny återställningsplan enligt följande:
   
   - Klicka på **+ Återställ plan** knapp, öppnas under bladet.
      
      ![Skapa en återställningsplan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Ange ett namn på återställningsplanen, välj Källa, & & distributionsmodellvärden.
   
   - Välj de virtuella datorerna från den skyddsgrupp som du vill inkludera i återställningsplanen och klicka på **OK.**
   
   - Välj Återställningsplan som du skapade tidigare, klicka på **Knappen Anpassa** för att öppna anpassningsvyn för återställningsplanen.
   
   - Högerklicka på **Alla grupper avstängning** och klicka på Lägg till pre **åtgärd**.
   
   - Öppnar Infoga åtgärdsblad, ange ett namn, välj **Primärt sidoalternativ** i Alternativet Var du vill köra, välj Automation-konto (där du har lagt till runbooks) och välj sedan runbooken **Redundansksimple-volymbehållare.**
   
   - Högerklicka på **grupp 1: Starta** och klicka på Lägg till skyddade **objekt** alternativ välj sedan de virtuella datorer som ska skyddas i återställningsplanen och klicka på **Ok** knappen. Valfritt, om det redan är valt virtuella datorer.
   
   - Högerklicka på **grupp 1: Starta** och klicka på Post **åtgärd** alternativ sedan lägga till alla följande skript:  
      
      - Start-StorSimple-Virtual-Appliance runbook  
      - Växla över-StorSimple-volym-behållare runbook  
      - Mount-volymer-efter-redundans runbook  
      - Avinstallera-anpassade-script-tillägg runbook  
        
   - Lägg till en manuell åtgärd efter ovanstående 4 skript i samma **grupp 1: Eftersteg** avsnitt. Den här åtgärden är den punkt där du kan kontrollera att allt fungerar som det ska. Den här åtgärden behöver bara läggas till som en del av Test redundans (så markera bara kryssrutan **Testa redundans).**
    
   - Efter den manuella åtgärden lägger du till **rensningsskriptet** med samma procedur som du använde för de andra runbooksna. **Spara** återhämtningsplanen.
    
   > [!NOTE]
   > När du kör en testväxling bör du kontrollera allt i åtgärdssteget för manuella åtgärder eftersom StorSimple-volymerna som har klonats på målenheten tas bort som en del av rensningen när den manuella åtgärden har slutförts.
       
      ![Återställningsplan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Utföra en testväxling
I kompletterande guide för [Active Directory DR-lösning](../site-recovery/site-recovery-active-directory.md) finns det några som vill ta hänsyn till Active Directory under testundansen. Den lokala inställningen störs inte alls när testmissen sker. StorSimple-volymerna som kopplades till den lokala virtuella datorn klonas till StorSimple Cloud Appliance på Azure. En virtuell dator för teständamål tas upp i Azure och de klonade volymerna är kopplade till den virtuella datorn.

#### <a name="to-perform-the-test-failover"></a>Så här utför du test failover
1. Välj ditt site recovery-valv i Azure-portalen.
1. Klicka på den återställningsplan som skapats för den virtuella filservern.
1. Klicka på **Testa redundans**.
1. Välj det virtuella Azure-nätverk som virtuella Azure-datorer ska anslutas till när redundans inträffar.
   
   ![Starta redundans](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Starta redundansväxlingen genom att klicka på **OK**. Du kan spåra förloppet genom att klicka på den virtuella datorn för &gt; att öppna dess egenskaper eller på **test redundansjobbet** i **jobbplatsåterställningsjobben**för valvjobb. **Jobs** &gt;
1. När redundansen är klar bör du också kunna se att &gt; den replik-Azure-datorn visas i virtuella Azure-portaldatorer . **Virtual Machines** Du kan utföra dina valideringar.
1. När valideringarna är klara klickar du på **Slutför valideringar**. Detta tar bort StorSimple-volymerna och stänger av StorSimple Cloud Appliance.
1. När du är klar klickar du på **Rensa testundans** på återställningsplanen. I Anteckningar post och spara alla observationer som är associerade med testet redundans. Detta tar bort den virtuella datorn som skapades under test redundans.

## <a name="perform-a-planned-failover"></a>Utföra en planerad redundans
   Under en planerad redundans stängs den lokala filservern VM av graciöst och en ögonblicksbild av volymerna på StorSimple-enheten tas. StorSimple-volymerna kan inte komma över till den virtuella enheten, en replik-VM tas upp på Azure och volymerna är kopplade till den virtuella datorn.

#### <a name="to-perform-a-planned-failover"></a>Så här utför du en planerad redundans
1. I Azure-portalen väljer &gt; du &gt; **Återställningstjänstvalvsåterställningsplaner (Site Recovery)** **recoveryplan_name** som skapats för filservern VM. **Recovery services**
1. Klicka på **Mer** &gt; **planerad redundans**på bladet Återställningsplan .  

   ![Återställningsplan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. På bladet **Bekräfta planerad redundans** väljer du käll- och målplatser och väljer målnätverk och klickar på kontrollikonen ✓ för att starta redundansprocessen.
1. När replik virtuella datorer har skapats är de i ett väntande tillstånd. Klicka på **Commit** för att genomföra redundansen.
1. När replikeringen är klar startar de virtuella datorerna på den sekundära platsen.

## <a name="perform-a-failover"></a>Utföra en redundans
Under en oplanerad redundans växlas StorSimple-volymerna över till den virtuella enheten, en replik-VM tas upp på Azure och volymerna är kopplade till den virtuella datorn.

#### <a name="to-perform-a-failover"></a>Så här utför du en redundans
1. I Azure-portalen väljer &gt; du &gt; **Återställningstjänstvalvsåterställningsplaner (Site Recovery)** **recoveryplan_name** som skapats för filservern VM. **Recovery services**
1. Klicka på **Mer** &gt; **redundans**på bladet återhämtningsplan .  
1. På bladet **Bekräfta redundans** väljer du käll- och målplatser.
1. Välj **Stäng av virtuella datorer och synkronisera de senaste data** för att ange att Site Recovery ska försöka stänga av den skyddade virtuella datorn och synkronisera data så att den senaste versionen av data kommer att misslyckas över.
1. Efter redundansen är de virtuella datorerna i ett väntande tillstånd. Klicka på **Commit** för att genomföra redundansen.


## <a name="perform-a-failback"></a>Utföra en återställning efter fel
Under en återställning av fel misslyckas StorSimple-volymbehållare tillbaka till den fysiska enheten när en säkerhetskopia har gjorts.

#### <a name="to-perform-a-failback"></a>Så här utför du en återställning efter fel
1. I Azure-portalen väljer &gt; du &gt; **Återställningstjänstvalvsåterställningsplaner (Site Recovery)** **recoveryplan_name** som skapats för filservern VM. **Recovery services**
1. Klicka på **Mer** &gt; **planerad redundans**på bladet Återställningsplan .  
1. Välj käll- och målplatser, välj lämpliga alternativ för datasynkronisering och skapande av virtuella datorer.
1. Klicka på **OK** för att starta återställningen av återställningen.
   
   ![Starta återställning efter fel](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Metodtips
### <a name="capacity-planning-and-readiness-assessment"></a>Kapacitetsplanering och beredskapsbedömning
#### <a name="hyper-v-site"></a>Hyper-V-plats
Använd [verktyget Användarkapacitetsplanerare](https://www.microsoft.com/download/details.aspx?id=39057) för att utforma server-, lagrings- och nätverksinfrastrukturen för din Hyper-V-replikmiljö.

#### <a name="azure"></a>Azure
Du kan köra verktyget utvärdering av [Azure Virtual Machine Readiness Assessment](https://azure.microsoft.com/downloads/vm-readiness-assessment/) på virtuella datorer för att säkerställa att de är kompatibla med virtuella Azure-datorer och Azure Site Recovery Services. Verktyget för bedömning av beredskap kontrollerar VM-konfigurationer och varnar när konfigurationer är inkompatibla med Azure. Det utfärdar till exempel en varning om en C:-enhet är större än 127 GB.

Kapacitetsplanering består av minst två viktiga processer:

   - Mappa lokala virtuella hyper-virtuella datorer till Azure VM-storlekar (till exempel A6, A7, A8 och A9).
   - Bestämma önskad internetbandbredd.

## <a name="limitations"></a>Begränsningar
- För närvarande kan endast 1 StorSimple-enhet skickas över (till en enda StorSimple Cloud Appliance). Scenariot för en filserver som sträcker sig över flera StorSimple-enheter stöds ännu inte.
- Om du får ett felmeddelande när du aktiverar skydd för en virtuell dator kontrollerar du att du har kopplat från iSCSI-målen.
- Alla volymbehållare som har grupperats tillsammans på grund av säkerhetskopieringsprinciper som sträcker sig över volymbehållare kommer att misslyckas över tillsammans.
- Alla volymer i volymbehållarna som du har valt kommer att skickas över.
- Volymer som lägger till upp till mer än 64 TB kan inte överföras eftersom den maximala kapaciteten för en enda StorSimple Cloud Appliance är 64 TB.
- Om den planerade/oplanerade redundansen misslyckas och de virtuella datorerna skapas i Azure ska du inte rensa de virtuella datorerna. Gör i stället en återställning. Om du tar bort de virtuella datorerna kan de lokala virtuella datorerna inte aktiveras igen.
- Efter en redundans, om du inte kan se volymerna, gå till de virtuella datorerna, öppna Diskhantering, skanna diskarna igen och sedan ansluta dem.
- I vissa fall kan enhetsbeteckningarna på DR-platsen skilja sig från de lokala bokstäverna. Om detta inträffar måste du korrigera problemet manuellt när redundansen är klar.
- Timeout för redundansjobb: StorSimple-skriptet tar timeout om redundansen för volymbehållare tar längre tid än Azure Site Recovery-gränsen per skript (för närvarande 120 minuter).
- Timeout för säkerhetskopiering av jobb: StorSimple-skriptet timeout om säkerhetskopieringen av volymer tar längre tid än Azure Site Recovery-gränsen per skript (för närvarande 120 minuter).
   
  > [!IMPORTANT]
  > Kör säkerhetskopian manuellt från Azure-portalen och kör sedan återställningsplanen igen.
   
- Klona tidsgräns för jobb: StorSimple-skriptet timeout om kloning av volymer tar längre tid än Azure Site Recovery-gränsen per skript (för närvarande 120 minuter).
- Tidssynkroniseringsfel: StorSimple-skripten fel ut säger att säkerhetskopiorna misslyckades trots att säkerhetskopian lyckas i portalen. En möjlig orsak till detta kan vara att StorSimple-apparatens tid kan vara osynkroniserad med den aktuella tiden i tidszonen.
   
  > [!IMPORTANT]
  > Synkronisera apparatens tid med aktuell tid i tidszonen.
   
- Fel vid växling av apparat: StorSimple-skriptet kan misslyckas om det finns en felrapportering för en apparat när återställningsplanen körs.
   
  > [!IMPORTANT]
  > Kör återställningsplanen igen när apparaten har redundansen är klar.


## <a name="summary"></a>Sammanfattning
Med hjälp av Azure Site Recovery kan du skapa en komplett automatisk katastrofåterställningsplan för en virtuell filserver med filresurser som finns på StorSimple-lagring. Du kan initiera redundansen inom några sekunder från var som helst i händelse av ett avbrott och få igång programmet på några minuter.
