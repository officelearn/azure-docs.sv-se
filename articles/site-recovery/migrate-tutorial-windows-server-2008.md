---
title: Migrera lokala Windows Server 2008-servrar till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du migrerar lokala Windows Server 2008-datorer till Azure med Azure Site Recovery.
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 09/22/2018
ms.author: bsiva
ms.custom: MVC
ms.openlocfilehash: 68a1367eec5392036797612e631a438b076b2cfc
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210473"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Migrera servrar som kör Windows Server 2008 till Azure

Den här självstudiekursen visar hur du migrerar lokala servrar med Windows Server 2008 eller 2008 R2 till Azure med hjälp av Azure Site Recovery. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbereda din lokala miljö för migrering
> * Konfigurera målmiljön
> * Konfigurerar en replikeringsprincip
> * Aktivera replikering
> * Kör en testmigrering för att kontrollera att allt fungerar som förväntat
> * Redundansväxla till Azure och slutföra migreringen

Avsnittet med begränsningar och kända problem listar några begränsningar och lösningar som du kan stöta på när du migrerar Windows Server 2008-datorer till Azure. 


## <a name="supported-operating-systems-and-environments"></a>Operativsystem och miljöer som stöds


|Operativsystem  | Lokal miljö  |
|---------|---------|
|Windows Server 2008 SP2 – 32- och 64-bitars (IA-32 och x86-64)</br>– Standard</br>– Enterprise</br>– Datacenter   |     VMware-VM, Hyper-V-VM och fysiska servrar    |
|Windows Server 2008 R2 SP1 – 64-bitars</br>– Standard</br>– Enterprise</br>– Datacenter     |     VMware-VM, Hyper-V-VM och fysiska servrar|

> [!WARNING]
> - Migrering av servrar som kör Server Core stöds inte.
> - Se till att du har senaste service pack och Windows-uppdateringar installerade innan du migrerar.


## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar är det bra att granska Azure Site Recovery-arkitekturen för [migrering av VMware och fysisk server](vmware-azure-architecture.md) eller [migrering av virtuell Hyper-V-dator](hyper-v-azure-architecture.md) 

Om du vill migrera virtuella Hyper-V-datorer som kör Windows Server 2008 eller Windows Server 2008 R2 följer du stegen i självstudiekursen om [migrering av lokala datorer till Azure](migrate-tutorial-on-premises-azure.md).

Resten av den här självstudiekursen visar hur du kan migrera lokala virtuella VMware-datorer och fysiska servrar som kör Windows Server 2008 eller 2008 R2.


## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem

- Konfigurationsservern, ytterligare processervrar och mobilitetstjänsten som används för att migrera Windows Server 2008 SP2-servrar ska köra version 9.19.0.0 eller senare av Azure Site Recovery-programvaran.

- Programkonsekventa återställningspunkter och konsekvensfunktionen för flera virtuella datorer stöds inte replikering av servrar som kör Windows Server 2008 SP2. Windows Server 2008 SP2-servrar ska migreras till en kraschkonsekvent återställningspunkt. Kraschkonsekventa återställningspunkter genereras var 5:e minut som standard. Om du använder en replikeringsprincip med ett konfigurerad programkonsekvent ögonblicksbildsfrekvens gör så att replikeringsstatusen blir kritisk på grund av bristen på programkonsekventa återställningspunkter. Om du vill undvika falska positiva identifieringar ställer du in den programkonsekventa ögonblicksbildfrekvensen i replikeringsprincipen på ”Av”.

- Servrarna som migreras ska ha .NET Framework 3.5 Service Pack 1 för at mobilitetstjänsten ska fungera.

- Om servern har dynamiska diskar kan du märka i vissa konfigurationer att dessa diskar på den rendundansväxlade servern är markerade offline eller visas som externa diskar. Du kan även märka att speglade set-statusen för speglade volymer på dynamiska diskar markeras ”Redundansåtgärden misslyckades”. Du kan åtgärda problemet från diskmgmt.msc genom att manuellt importera dessa diskar och återaktivera dem.

- Servrarna som migreras ska ha drivrutinen vmstorfl.sys. Redundansen kan misslyckas om drivrutin inte finns på servern som migreras. 
  > [!TIP]
  >Kontrollera om drivrutinen finns på "C:\Windows\system32\drivers\vmstorfl.sys" . Om drivrutinen inte hittas kan du lösa problemet genom att skapa en dummy-fil på plats. 
  >
  > Öppna kommandotolken (run > cmd) och kör följande: "copy nul c:\Windows\system32\drivers\vmstorfl.sys"

- Du kanske inte kan RDP-ansluta till Windows Server 2008 SP2-servrar som kör 32-bitars operativsystemet när de har redundansväxlat eller testredundansväxlat över till Azure. Starta om den redundansväxlade virtuella datorn via Azure-portalen och försök ansluta igen. Om du fortfarande inte kan ansluta kontrollerar du om servern har konfigurerats att tillåta fjärrskrivbordsanslutningar och att det inte finns några brandväggsregler eller nätverkssäkerhetsgrupper som blockerar anslutningen. 
  > [!TIP]
  > Ett redundanstest rekommenderas starkt innan du migrerar servrar. Se till att du har utfört minst ett lyckat redundanstest på varje server du migrerar. Som en del av redundanstestet ansluter du till den testredundansväxlade datorn och kontrollera att allt fungerar som förväntat.
  >
  >Redundanstestet är avbrottsfritt och hjälper dig att testa migreringar genom att skapa virtuella datorer på ett valfritt isolerat nätverk. Till skillnad från redundansåtgärden fortsätter datareplikeringen under ett redundanstest. Du kan utföra så många redundanstest du vill innan du är redo att migrera. 
  >
  >


## <a name="getting-started"></a>Komma igång

Utför följande uppgifter för att förbereda Azure-prenumerationen och den lokala VMware/fysiska miljön:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. Förbereda [lokal VMware](vmware-azure-tutorial-prepare-on-premises.md)


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Skapa en resurs** > **Övervakning och hantering** > **Backup och Site Recovery**.
3. I **Namn** anger du det egna namnet **W2K8-migrering**. Om du har mer än en prenumeration väljer du den lämpligaste.
4. Skapa en resursgrupp, **w2k8migrate**.
5. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. För att snabbt komma åt valvet från instrumentpanelen klickar du på **Fäst på instrumentpanelen** och sedan på **Skapa**.

   ![Nytt valv](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

Det nya valvet läggs till på **Instrumentpanelen** under **Alla resurser** och på huvudsidan för **Recovery Services-valv**.


## <a name="prepare-your-on-premises-environment-for-migration"></a>Förbereda din lokala miljö för migrering

- Om du vill migrera virtuella Windows Server 2008-datorer som körs på VMware [konfigurera du den lokala konfigurationsservern på VMware](vmware-azure-tutorial.md#set-up-the-source-environment).
- Om konfigurationsservern inte kan konfigureras som en virtuell VMware-dator [konfigurerar du konfigurationsservern på en lokal fysisk server eller virtuell dator](physical-azure-disaster-recovery.md#set-up-the-source-environment).

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange Resource Manager-distributionsmodellen.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.


## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

1. Om du vill skapa en ny replikeringsprincip klickar du på **Site Recovery-infrastruktur** > **Replikeringsprinciper** > **+Replikeringsprincip**.
2. I **Skapa replikeringsprincip** anger du ett principnamn.
3. I **Tröskelvärde för replikeringspunktmål** anger du gränsen för replikeringspunktmålet (RPO). En avisering skapas om replikeringens RPO överskrider gränsen.
4. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden (i antal timmar) för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster. Upp till 24 timmars kvarhållning stöds för datorer replikerade till premiumlagring och 72 timmar för standardlagring.
5. I **Frekvens för appkonsekvent ögonblicksbild** anger du **Av**. Klicka på **OK** för att skapa principen.

Principen associeras automatiskt med konfigurationsservern.

> [!WARNING]
> Se till att du anger **AV** i inställningen Frekvens för appkonsekvent ögonblicksbild för replikeringsprincipen. Endast kraschkonsekventa återställningspunkter stöds vid replikering av servrar som kör Windows Server 2008. Om du anger ett annat värde i Frekvens för appkonsekvent ögonblicksbild resulterar i falska aviseringar genom replikeringsstatusen för servern blir kritisk på grund av bristen på appkonsekventa återställningspunkter.

   ![Skapa replikeringsprincip](media/migrate-tutorial-windows-server-2008/create-policy.png)

## <a name="enable-replication"></a>Aktivera replikering

[Aktivera replikering](physical-azure-disaster-recovery.md#enable-replication) för Windows Server 2008 SP2-/Windows Server 2008 R2 SP1-servern som ska migreras.
   
   ![Lägga till fysisk server](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Aktivera replikering](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

## <a name="run-a-test-migration"></a>Kör en testmigrering

Du kan utföra ett redundanstest för servrar som ska replikeras när den inledande replikeringen slutförs och serverstatusen övergår till **Skyddad**.

Kör en [testredundansväxling](tutorial-dr-drill-azure.md) till Azure för att kontrollera att allt fungerar som förväntat.

   ![Redundanstest](media/migrate-tutorial-windows-server-2008/testfailover.png)


## <a name="migrate-to-azure"></a>Migrera till Azure

Kör en redundansväxling för de datorer som du vill migrera.

1. I **Inställningar** > **Replikerade objekt** klickar du på datorn > **Redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Välj den senaste återställningspunkten.
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av servern innan redundansväxlingen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
4. Kontrollera att den virtuella Azure-datorn visas i Azure som förväntat.
5. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Slutför migrering**. Detta avslutar migreringsprocessen, stoppar replikeringen för den virtuella datorn och stoppar Site Recovery-debitering för den virtuella datorn.

   ![Slutföra migrering](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Replikeringen av den virtuella datorn stoppas innan redundansen startas. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.
