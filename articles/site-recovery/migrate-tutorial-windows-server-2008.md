---
title: Migrera lokala Windows Server 2008-servrar till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du migrerar lokala Windows Server 2008-datorer till Azure med Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/11/2018
ms.author: bsiva
ms.openlocfilehash: 0d3f28f0a9f1e9862fabb6ce5e96597f1534abd8
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008771"
---
# <a name="migrate-servers-running-windows-server-2008-2008-r2-to-azure"></a>Migrera servrar som kör Windows Server 2008, 2008 R2 till Azure

Den här självstudien visar hur du migrerar lokala servrar som kör Windows Server 2008 eller 2008 R2 till Azure med Azure Site Recovery. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbered din lokala miljö för migrering
> * Konfigurera målmiljön
> * Konfigurerar en replikeringsprincip
> * Aktivera replikering
> * Kör en testmigrering för att kontrollera att allt fungerar som förväntat
> * Redundans till Azure och slutföra migreringen

Avsnittet begränsningar och kända problem, visar några av begränsningar och lösningar för kända problem som du kan stöta på när du migrerar Windows Server 2008-datorer till Azure. 


## <a name="supported-operating-systems-and-environments"></a>Operativsystem och miljöer som stöds


|Operativsystem  | En lokal miljö  |
|---------|---------|
|Windows Server 2008 SP2 - 32-bitars och 64-bitars (IA-32 och x86 64)</br>-Standard</br>-Enterprise</br>-Datacenter   |     Virtuella VMware-datorer, Hyper-V-datorer och fysiska servrar    |
|Windows Server 2008 R2 SP1 - 64-bitars</br>-Standard</br>-Enterprise</br>-Datacenter     |     Virtuella VMware-datorer, Hyper-V-datorer och fysiska servrar|

> [!WARNING]
> - Migrering av servrar som kör Server Core stöds inte.
> - Se till att du har senaste servicepack och Windows-uppdateringar som installerats innan du migrerar.


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar är det bra att granska Azure Site Recovery-arkitekturen för [VMware och fysiska servermigrering](vmware-azure-architecture.md) eller [migrering av Hyper-V virtuell dator](hyper-v-azure-architecture.md) 

Om du vill migrera Hyper-V-datorer som kör Windows Server 2008 eller Windows Server 2008 R2, följer du stegen i den [migrera lokala datorer till Azure](migrate-tutorial-on-premises-azure.md) självstudien.

Resten av den här kursen visar hur du kan migrera lokala virtuella VMware-datorer och fysiska servrar som kör Windows Server 2008 eller 2008 R2.


## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem

- Den konfigurationsservern och ytterligare processervrar mobilitetstjänsten som används för att migrera servrar för Windows Server 2008 SP2 bör köra version 9.18.0.1 av Azure Site Recovery-programvaran. Enhetlig installationen av version 9.18.0.1 av konfigurationsservern och processervern kan laddas ned från [ https://aka.ms/asr-w2k8-migration-setup ](https://aka.ms/asr-w2k8-migration-setup).

- En befintlig konfigurationsservern eller processervern kan inte användas för att migrera servrar som kör Windows Server 2008 SP2. En ny Configuration Server ska etableras med version 9.18.0.1 av Azure Site Recovery-programvaran. Den här konfigurationsservern bör endast användas för migrering av Windows-servrar till Azure.

- Programåterställningspunkter för konsekvent och funktionen konsekvens för flera virtuella datorer stöds inte för replikering av servrar som kör Windows Server 2008 SP2. Windows Server 2008 SP2 servrar ska migreras till en kraschkonsekventa återställningspunkten. Krascher konsekventa återställningspunkter genereras var femte minut som standard. Replikeringshälsa aktivera kritisk på grund av bristande konsekvent programåterställningspunkter genereras om du använder en replikeringsprincip med en frekvens för programkonsekventa ögonblicksbilder av konfigurerade programmet. Ange frekvensen för programkonsekventa ögonblicksbilder i replikeringsprincip på ”av” för att undvika falska positiva identifieringar.

- De servrar som ska migreras bör ha .NET Framework 3.5 Service Pack 1 för mobilitetstjänsten ska fungera.


- Om servern har dynamiska diskar, kanske du märker i vissa konfigurationer, som dessa diskar på den misslyckade över server markeras offline eller visas som externa diskar. Du kan också hända att speglade set-status för speglade volymer mellan diskar är markerad ”misslyckades redundans”. Du kan åtgärda det här problemet från diskmgmt.msc genom att importera de här diskarna och återaktivera dem manuellt.

- De servrar som ska migreras bör ha vmstorfl.sys-drivrutinen. Redundans kan misslyckas om drivrutinen inte finns på servern som ska migreras. 
  > [!TIP]
  >Kontrollera om drivrutinen finns på ”C:\Windows\system32\drivers\vmstorfl.sys”. Om drivrutinen inte hittas kan du lösa problemet genom att skapa en dummy-fil på plats. 
  >
  > Öppna Kommandotolken (kör > cmd) och kör sedan följande: ”kopiera nul c:\Windows\system32\drivers\vmstorfl.sys”

- Du kanske inte att RDP för Windows Server 2008 SP2-servrar som kör 32-bitars operativsystem omedelbart efter att de har redundansväxlats eller testa redundansväxling till Azure. Starta om den virtuella datorn från Azure-portalen och försök ansluta igen. Om du är fortfarande inte kan ansluta kontrollerar du om servern är konfigurerad att tillåta anslutningar till fjärrskrivbord och se till att det finns inga brandväggsregler eller nätverkssäkerhetsgrupper som blockerar anslutningen. 
  > [!TIP]
  > Ett redundanstest rekommenderas starkt innan du migrerar servrar. Se till att minst en lyckad redundanstestning har utförs på varje server som du migrerar. Anslut till testet misslyckades redundansväxlade datorn som en del av redundanstestet, och se till att saker fungerar som förväntat.
  >
  >Redundanstestet är avbrottsfria och hjälper dig att testa migrering genom att skapa virtuella datorer i ett isolerat nätverk du väljer. Till skillnad från Redundansåtgärden under redundanstestet, fortsätter datareplikering att progres. Du kan utföra eftersom många redundanstestningen som du vill, innan du är redo att migrera. 
  >
  >


## <a name="getting-started"></a>Komma igång

Utför följande åtgärder för att förbereda Azure-prenumeration och en lokal VMware/fysiska miljön:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. Förbered lokala [VMware](vmware-azure-tutorial-prepare-on-premises.md)


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Skapa en resurs** > **Övervakning och hantering** > **Backup och Site Recovery**.
3. I **namn**, ange det egna namnet **W2K8 migrering**. Om du har mer än en prenumeration väljer du den lämpligaste.
4. Skapa en resursgrupp **w2k8migrate**.
5. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. För att snabbt komma åt valvet från instrumentpanelen klickar du på **Fäst på instrumentpanelen** och sedan på **Skapa**.

   ![Nytt valv](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

Det nya valvet läggs till på **Instrumentpanelen** under **Alla resurser** och på huvudsidan för **Recovery Services-valv**.


## <a name="prepare-your-on-premises-environment-for-migration"></a>Förbered din lokala miljö för migrering

- Ladda ned installationsprogrammet till konfigurationsserver (enhetligt installationsprogram) från [https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup)
- [Konfigurera](physical-azure-disaster-recovery.md#set-up-the-source-environment) källmiljön använda installationsfilen hämtade i föregående steg.

> [!IMPORTANT]
> Kontrollera att du använder installationsfilen hämtade i det första steget ovan för att installera och registrera konfigurationsservern. Hämta inte installationsfilen från Azure-portalen. Installationsfilen finns på [ https://aka.ms/asr-w2k8-migration-setup ](https://aka.ms/asr-w2k8-migration-setup) är den enda versionen som har stöd för Windows Server 2008-migrering.
>
> Du kan inte använda en befintlig Configuration Server för att migrera datorer som kör Windows Server 2008. Du måste installera en ny konfigurationsservern med hjälp av länken ovan.

 ![Ladda ned valvregistreringsnyckel](media/migrate-tutorial-windows-server-2008/download-vault-credentials.png) 
 

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange Resource Manager-distributionsmodellen.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.


## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

1. Klicka för att skapa en ny replikeringsprincip **Site Recovery-infrastruktur** > **replikeringsprinciper** > **+ replikeringsprincip**.
2. I **skapa replikeringsprincip**, ange ett principnamn.
3. I **tröskelvärde för Replikeringspunktmål**, ange objektiva (RPO) återställningspunktgränsen. En avisering skapas om replikeringen RPO överskrider den här gränsen.
4. I **kvarhållning av återställningspunkt**, ange hur lång tid (i timmar) kvarhållningsperioden är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster. Upp till 24 timmar kvarhållning stöds för datorer som har replikerats till premiumlagring och 72 timmar för standardlagring.
5. I **appkonsekvent ögonblicksbildsfrekvens**, ange **av**. Klicka på **OK** för att skapa principen.

Principen associeras automatiskt med konfigurationsservern.

> [!WARNING]
> Kontrollera att du anger **OFF** i programkonsekvent ögonblicksbild frekvensinställningen för replikeringsprincipen. Bara kraschkonsekventa återställningspunkter stöds vid replikering av servrar som kör Windows Server 2008. Att ange ett annat värde för appkonsekvent ögonblicksbildsfrekvens leder falska aviseringar genom att aktivera replikering hälsotillståndet för servern kritiskt på grund av bristande appkonsekventa återställningspunkter.

   ![Skapa replikeringsprincip](media/migrate-tutorial-windows-server-2008/create-policy.png)

## <a name="enable-replication"></a>Aktivera replikering

[Aktivera replikering](physical-azure-disaster-recovery.md#enable-replication) för Windows Server 2008 SP2 / Windows Server 2008 R2 SP1-server som ska migreras.
   
   ![Lägg till fysisk server](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Aktivera replikering](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

## <a name="run-a-test-migration"></a>Kör en testmigrering

Du kan göra ett redundanstest för att replikera servrar när den inledande replikeringen är klar och serverstatus övergår i **skyddade**.

Kör en [testredundansväxling](tutorial-dr-drill-azure.md) till Azure för att kontrollera att allt fungerar som förväntat.

   ![Testa redundans](media/migrate-tutorial-windows-server-2008/testfailover.png)


## <a name="migrate-to-azure"></a>Migrera till Azure

Kör en redundansväxling för de datorer som du vill migrera.

1. I **Inställningar** > **Replikerade objekt** klickar du på datorn > **Redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Välj den senaste återställningspunkten.
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av servern innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
4. Kontrollera att den virtuella Azure-datorn visas i Azure som förväntat.
5. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Slutför migrering**. Detta avslutar migreringsprocessen, stoppar replikeringen för den virtuella datorn och stoppar Site Recovery-debitering för den virtuella datorn.

   ![Slutföra migrering](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Replikeringen av den virtuella datorn stoppas innan redundansen startas. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.
