---
title: Replikera virtuella Hyper-V-datorer i VMM-moln till Azure | Microsoft Docs
description: "Den här artikeln beskriver hur du replikerar virtuella Hyper-V-datorer på Hyper-V-värdar som finns i System Center VMM-moln till Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 9d526a1f-0d8e-46ec-83eb-7ea762271db5
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ed97f2aeb19566b12342e5194ac8a01293f453bf
ms.lasthandoff: 04/03/2017


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>Replikera virtuella Hyper-V-datorer i VMM-moln till Azure
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-azure.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [Klassisk portal](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell – Klassisk](site-recovery-deploy-with-powershell.md)
>
>

Azure Site Recovery-tjänsten bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att samordna replikering, redundans och återställning av virtuella datorer och fysiska servrar. Datorer kan replikeras till Azure eller till ett sekundärt lokalt datacenter. En snabb översikt finns i [Vad är Azure Site Recovery?](site-recovery-overview.md)

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du distribuerar Site Recovery för att replikera virtuella Hyper-V-datorer på Hyper-V-värdservrar som finns i privata VMM-moln till Azure.

Artikeln innehåller kraven för scenariot och visar hur du konfigurerar ett Site Recovery-valv, skaffar Azure Site Recovery-providern som installeras på VMM-servern, registrerar servern i valvet, lägger till ett Azure-lagringskonto, installerar Azure Recovery Services-agenten på Hyper-V-värdservrar, konfigurerar skyddsinställningar för VMM-moln som tillämpas på alla skyddade virtuella datorer och hur du sedan aktiverar skydd för dessa virtuella datorer. Avsluta med att testa redundansen för att se till att allt fungerar som förväntat.

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Arkitektur
![Arkitektur](./media/site-recovery-vmm-to-azure-classic/topology.png)

* Azure Site Recovery-providern installeras på VMM under distributionen av Site Recovery och VMM-servern registreras i Site Recovery-valvet. Providern kommunicerar med Site Recovery för att samordna replikeringen.
* Azure Recovery Services-agenten installeras på Hyper-V-värdservrar under distributionen av Site Recovery. Den hanterar datareplikeringen till Azure-lagring.

## <a name="azure-prerequisites"></a>Krav för Azure
Det här behöver du i Azure.

| **Krav** | **Detaljer** |
| --- | --- |
| **Azure-konto** |Du behöver ett [Microsoft Azure](https://azure.microsoft.com/)-konto. Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/). [Lär dig mer](https://azure.microsoft.com/pricing/details/site-recovery/) om priserna för Site Recovery. |
| **Azure Storage** |Du behöver ett Azure-lagringskonto för att lagra replikerade data. Replikerade data lagras i Azure och virtuella Azure-datorer skapas i samband med redundansväxlingen. <br/><br/>Du behöver ett [geo-redundant standardlagringskonto](../storage/storage-redundancy.md#geo-redundant-storage). Kontot måste finnas i samma region som Site Recovery-tjänsten och vara associerat med samma prenumeration. Observera att replikeringen till Premium-lagringskonton inte stöds för närvarande och därför inte bör användas.<br/><br/>[Läs om](../storage/storage-introduction.md) Azure-lagring. |
| **Azure-nätverk** |Du behöver ett virtuellt Azure-nätverk som virtuella Azure-datorer ansluter till vid en redundansväxling. Det virtuella Azure-nätverket måste finnas i samma region som Site Recovery-valvet. |

## <a name="on-premises-prerequisites"></a>Krav för det lokala systemet
Det här behöver du lokalt.

| **Krav** | **Detaljer** |
| --- | --- |
| **VMM** |Du behöver minst en VMM-server som distribuerats som en fristående fysisk eller virtuell server eller som ett virtuellt kluster. <br/><br/>VMM-servern måste köra System Center 2012 R2 med de senaste kumulativa uppdateringarna.<br/><br/>Du behöver minst ett moln som konfigurerats på VMM-servern.<br/><br/>Källmolnet som du vill skydda måste innehålla en eller flera VMM-värdgrupper.<br/><br/>Läs mer om hur du konfigurerar VMM-moln i [Genomgång: Skapa privata moln med System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) i Keith Mayers blogg. |
| **Hyper-V** |Du behöver en eller flera Hyper-V-värdservrar eller Hyper-V-kluster i VMM-molnet. Värdservern bör ha en eller flera virtuella datorer. <br/><br/>Hyper-V-servrar måste köras på minst **Windows Server 2012 R2** med Hyper-V-rollen eller **Microsoft Hyper-V Server 2012 R2** och ha de senaste uppdateringarna installerade.<br/><br/>Hyper-V-servrar som innehåller virtuella datorer som du vill skydda måste finnas i ett VMM-moln.<br/><br/>Om du kör Hyper-V i ett kluster bör du vara medveten om att klusterutjämning inte skapas automatiskt om du har ett statiskt IP-adressbaserat kluster. Du måste konfigurera klusterutjämningen manuellt. [Lär dig mer](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) i Aidan Finns blogginlägg. |
| **Skyddade datorer** | Virtuella datorer som du vill skydda måste uppfylla [kraven för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). |

## <a name="network-mapping-prerequisites"></a>Krav för nätverksmappning
När du skyddar virtuella datorer i Azure mappar nätverksmappningen mellan virtuella datornätverk på VMM-källservern och Azure-målnätverken för att följande ska vara möjligt:

* Alla datorer som redundansväxlar i samma nätverk kan ansluta till varandra, oavsett vilken återställningsplan de finns i.
* Om en nätverksgateway har konfigurerats i Azure-målnätverket kan virtuella datorer ansluta till andra lokala virtuella datorer.
* Om du inte konfigurerar nätverksmappning kan endast virtuella datorer som växlar över i samma återställningsplan ansluta till varandra efter en redundansväxling till Azure.

Om du vill distribuera nätverksmappning behöver du följande:

* De virtuella datorerna som du vill skydda på VMM-källservern måste vara anslutna till ett virtuellt datornätverk. Nätverket ska kopplas till ett logiskt nätverk som är associerat med molnet.
* Ett Azure-nätverk som de replikerade virtuella datorerna kan ansluta till efter redundansväxlingen. Du väljer det här nätverket vid tidpunkten för redundansväxlingen. Nätverket måste finnas i samma region som din Azure Site Recovery-prenumeration.


Förbereda nätverk i VMM:

   * [Konfigurera logiska nätverk](https://technet.microsoft.com/library/jj721568.aspx).
   * [Konfigurera virtuella datornätverk](https://technet.microsoft.com/library/jj721575.aspx).


## <a name="step-1-create-a-site-recovery-vault"></a>Steg 1: Skapa ett Site Recovery-valv
1. Logga in på [hanteringsportalen](https://portal.azure.com) från den VMM-server som du vill registrera.
2. Klicka på **Datatjänster** > **Återställningstjänster** > **Site Recovery-valv**.
3. Klicka på **Skapa nytt** > **Snabbregistrering**.
4. I **Namn** anger du ett eget namn som identifierar valvet.
5. I **Region** väljer du ett geografiskt område för valvet. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Klicka på **Skapa valv**.

    ![Nytt valv](./media/site-recovery-vmm-to-azure-classic/create-vault.png)

Kontrollera att valvet har skapats genom att titta i statusfältet. Valvet visas som **Aktivt** på Recovery Services-huvudsidan.

## <a name="step-2-generate-a-vault-registration-key"></a>Steg 2: Skapa en valvregistreringsnyckel
Generera en registreringsnyckel i valvet. När du har laddat ned Azure Site Recovery-providern och installerat den på VMM-servern använder du den här nyckeln för att registrera VMM-servern i valvet.

1. På sidan **Recovery Services** klickar du på valvet för att öppna sidan Snabbstart. Du kan också öppna Snabbstart när du vill med hjälp av ikonen.

    ![Snabbstartsikon](./media/site-recovery-vmm-to-azure-classic/qs-icon.png)
2. Välj **Mellan en lokal VMM-plats och Microsoft Azure**.
3. Klicka på **Generera registreringsnyckel** i **Förbered VMM-servrar**. Nyckelfilen genereras automatiskt och är giltig i fem dagar efter att den har skapats. Om du inte kommer åt Azure-portalen från VMM-servern måste du kopiera den här filen till servern.

    ![Registreringsnyckel](./media/site-recovery-vmm-to-azure-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Steg 3: Installera Azure Site Recovery-providern
1. I **Snabbstart** > **Förbered VMM-servrar** klickar du på **Ladda ned Microsoft Azure Site Recovery-providern för installation på VMM-servrar** för att ladda ned den senaste versionen av providerinstallationsfilen.
2. Kör den här filen på VMM-källservern.

   > [!NOTE]
   > Om VMM distribueras i ett kluster och du installerar providern för första gången installerar du den på en aktiv nod och slutför installationen för att registrera VMM-servern i valvet. Installera sedan providern på de andra noderna. Observera att om du uppgraderar providern så måste du uppgradera den på alla noder eftersom de måste köra samma providerversion.
   >
   >
3. Installationsprogrammet kör en kravkontroll och begär om tillstånd att stoppa VMM-tjänsten för att starta installationen av providern. VMM-tjänsten startas om automatiskt när installationen är klar. Om du installerar i ett VMM-kluster uppmanas du att stoppa klusterrollen.
4. I **Microsoft Update** kan du välja att installera relevanta uppdateringar. Om den här inställningen är aktiverad installeras provideruppdateringar baserat på din Microsoft Update-princip.

    ![Microsoft-uppdateringar](./media/site-recovery-vmm-to-azure-classic/updates.png)
5. Installationsplatsen för providern är **<SystemDrive>\Program\Microsoft System Center 2012 R2\Virtual Machine Manager\bin**. Klicka på **Installera**.

   ![InstallLocation](./media/site-recovery-vmm-to-azure-classic/install-location.png)
6. När providern har installerats klickar du på **Registrera** för att registrera servern i valvet.

    ![InstallComplete](./media/site-recovery-vmm-to-azure-classic/install-complete.png)
7. I **Valvnamn** kontrollerar du namnet på valvet som servern ska registreras i. Klicka på *Next*.

    ![Serverregistrering](./media/site-recovery-vmm-to-azure-classic/vaultcred.PNG)
8. I **Internetanslutning** anger du hur providern som körs på VMM-servern ansluter till Internet. Välj **Anslut med befintliga proxyinställningar** för att använda standardinställningarna för Internet som konfigurerats på servern.

    ![Internetinställningar](./media/site-recovery-vmm-to-azure-classic/proxydetails.PNG)

   * Om du vill använda en anpassad proxy konfigurerar du den innan du installerar providern. När du konfigurerar anpassade proxyinställningar körs ett test för att kontrollera proxyanslutningen.
   * Om du använder en anpassad proxy eller om din standardproxy kräver autentisering måste du ange proxyinformationen, inklusive proxyadress och port.
   * Följande URL:er måste vara tillgängliga från VMM-servern och Hyper-v-värdar
     * *.hypervrecoverymanager.windowsazure.com
     * *.accesscontrol.windows.net
     * *.backup.windowsazure.com
     * *.blob.core.windows.net
     * *.store.core.windows.net
   * Tillåt IP-adresserna som beskrivs i [IP-intervall för Azure-datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653) och HTTPS-protokollet (443). Du bör också vitlista IP-intervall för den Azure-region som du planerar att använda samt för regionen USA, västra.
   * Om du använder en anpassad proxyserver skapas ett RunAs-konto (DRAProxyAccount) i VMM automatiskt med de angivna proxyautentiseringsuppgifterna. Konfigurera proxyservern så att det här kontot kan autentiseras. Du kan ändra inställningarna för RunAs-kontot i VMM i VMM-konsolen. Du gör det genom att öppna arbetsytan **Inställningar**, expandera **Säkerhet**, klicka på **Kör som-konton** och sedan ändra lösenordet för DRAProxyAccount. Du måste starta om VMM-tjänsten så att den här inställningen börjar gälla.
9. I **Registreringsnyckel** väljer du den nyckel som du laddade ned från Azure Site Recovery och kopierade till VMM-servern.
10. Krypteringsinställningen används bara när du replikerar virtuella Hyper-V-datorer i VMM-moln till Azure. Om du replikerar till en sekundär plats används den inte.
11. I **Servernamn** anger du ett eget namn som identifierar VMM-servern i valvet. I en klusterkonfiguration anger du namnet på VMM-klusterrollen.
12. I **Synkronisera molnmetadata** väljer du om du vill synkronisera metadata för alla moln på VMM-servern med valvet. Den här åtgärden behöver bara göras en gång på varje server. Om du inte vill synkronisera alla moln kan du lämna den här inställningen avmarkerad och synkronisera varje moln individuellt i molnegenskaperna i VMM-konsolen.
13. Slutför processen genom att klicka på **Nästa**. Efter registreringen hämtas metadata från VMM-servern av Azure Site Recovery. Servern visas på fliken **VMM-servrar** på sidan **Servrar** i valvet.

    ![Sista sidan](./media/site-recovery-vmm-to-azure-classic/provider13.PNG)

Efter registreringen hämtas metadata från VMM-servern av Azure Site Recovery. Servern visas på fliken **VMM-servrar** på sidan **Servrar** i valvet.

### <a name="command-line-installation"></a>Kommandoradsinstallation
Azure Site Recovery-providern kan även installeras med följande kommandorad. Den här metoden kan användas för att installera providern på Server Core för Windows Server 2012 R2.

1. Ladda ned installationsfilen och registreringsnyckeln för providern till en mapp. Till exempel C:\ASR.
2. Stoppa tjänsten System Center Virtual Machine Manager
3. Extrahera installationsprogrammet för providern genom att köra följande kommandon från en upphöjd kommandotolk:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. Installera providern så här:

        C:\ASR> setupdr.exe /i
5. Registrera providern så här:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Med följande parametrar:

* **/Credentials**: Obligatorisk parameter som anger den plats där registreringsnyckelfilen finns.  
* **/FriendlyName**: Obligatorisk parameter för namnet på Hyper-V-värdservern som visas på Azure Site Recovery-portalen.
* **/EncryptionEnabled**: Valfri parameter för att ange om du vill kryptera dina virtuella datorer i Azure (kryptering av vilande data). Filnamnet måste ha filnamnstillägget **.pfx**.
* **/proxyAddress**: Valfri parameter som anger adressen till proxyservern.
* **/proxyport**: Valfri parameter som anger porten för proxyservern.
* **/proxyUsername**: Valfri parameter som anger proxyserverns användarnamn.
* **/proxyPassword**: Valfri parameter som anger lösenordet för proxyservern.  

## <a name="step-4-create-an-azure-storage-account"></a>Steg 4: Skapa ett Azure-lagringskonto
1. Om du inte har något Azure-konto skapar du ett genom att klicka på **Lägg till ett Azure Storage-konto**.
2. Skapa ett konto med geo-replikering aktiverat. Kontot måste finnas i samma region som Azure Site Recovery-tjänsten och vara associerat med samma prenumeration.

    ![Lagringskonto](./media/site-recovery-vmm-to-azure-classic/storage.png)

> [!NOTE]
> [Migrering av lagringskonton](../azure-resource-manager/resource-group-move-resources.md) mellan resursgrupper i samma prenumeration eller mellan prenumerationer stöds inte för lagringskonton som används för att distribuera Site Recovery.
>
>

## <a name="step-5-install-the-azure-recovery-services-agent"></a>Steg 5: Installera Azure Recovery Services-agenten
Installera Azure Recovery Services-agenten på varje Hyper-V-värdserver i VMM-molnet.

1. Klicka på **Snabbstart** > **Ladda ned Azure Site Recovery Services-agenten och installera på värdar** för att hämta den senaste versionen av agentinstallationsfilen.

    ![Installera Recovery Services-agenten](./media/site-recovery-vmm-to-azure-classic/install-agent.png)
2. Kör installationsfilen på varje Hyper-V-värdserver.
3. Klicka på **Nästa** på sidan **Kravkontroll**. Alla nödvändiga komponenter som saknas installeras automatiskt.

    ![Krav för Recovery Services-agenten](./media/site-recovery-vmm-to-azure-classic/agent-prereqs.png)
4. På sidan **Installationsinställningar** anger du var du vill installera agenten och väljer cachelagringsplatsen där säkerhetskopierade metadata ska installeras. Klicka på **Installera**.
5. När installationen är klar klickar du på **Stäng** för att slutföra guiden.

    ![Registrera MARS-agenten](./media/site-recovery-vmm-to-azure-classic/agent-register.png)

### <a name="command-line-installation"></a>Kommandoradsinstallation
Du kan även installera Microsoft Azure Recovery Services-agenten från kommandoraden genom att köra följande kommando:

    marsagentinstaller.exe /q /nu

## <a name="step-6-configure-cloud-protection-settings"></a>Steg 6: Konfigurera skyddsinställningar för molnet
När VMM-servern har registrerats kan du konfigurera skyddsinställningar för molnet. Eftersom du aktiverade alternativet **Synkronisera molndata med valvet** när du installerade providern visas alla moln på VMM-servern på fliken <b>Skyddade objekt</b> i valvet.

![Publicerat moln](./media/site-recovery-vmm-to-azure-classic/clouds-list.png)

1. På sidan Snabbstart klickar du på **Konfigurera skydd för VMM-moln**.
2. På fliken **Skyddade objekt** klickar du på det moln som du vill konfigurera och går till fliken **Konfiguration**.
3. I **Mål** väljer du **Azure**.
4. I **Lagringskonto** väljer du det Azure-lagringskonto som du använder för replikering.
5. Välj **Av** för **Kryptera lagrade data**. Den här inställningen anger att data ska krypteras när de replikeras mellan den lokala platsen och Azure.
6. Lämna standardinställningen i **Kopieringsfrekvens**. Det här värdet anger hur ofta data ska synkroniseras mellan käll- och målplatserna.
7. Lämna standardinställningen i **Spara återställningspunkter i**. Med standardvärdet noll lagras bara den senaste återställningspunkten för en primär virtuell dator på en replikvärdserver.
8. Lämna standardinställningen i **Frekvens för programkonsekventa ögonblicksbilder**. Det här värdet anger hur ofta ögonblicksbilder ska skapas. Ögonblicksbilder använder VSS (Volume Shadow Copy Service) för att säkerställa att programmen är i ett konsekvent tillstånd när ögonblicksbilden tas.  Om du anger ett värde ser du till att det är mindre än antalet ytterligare återställningspunkter som du konfigurerar.
9. I **Starttid för replikering** anger du när den inledande replikeringen av data till Azure ska starta. Tidszonen på Hyper-V-värdservern används. Vi rekommenderar att du schemalägger den inledande replikeringen vid låg belastning.

    ![Inställningar för molnreplikering](./media/site-recovery-vmm-to-azure-classic/cloud-settings.png)

När du har sparat inställningarna skapas ett jobb som kan övervakas på fliken **Jobb**. Alla Hyper-V-värdservrar i VMM-källmolnet konfigureras för replikering.

När du har sparat kan molninställningarna ändras på fliken **Konfigurera**. Om du vill ändra målplatsen eller mållagringskontot måste du ta bort molnkonfigurationen och sedan konfigurera om molnet. Observera att om du ändrar lagringskontot så tillämpas ändringarna endast för virtuella datorer som du aktiverar skydd för efter ändringen av lagringskontot. Befintliga virtuella datorer migreras inte till det nya lagringskontot.

## <a name="step-7-configure-network-mapping"></a>Steg 7: Konfigurera nätverksmappning
Innan du börjar använda nätverksmappning kontrollerar du att de virtuella datorerna på VMM-källservern är anslutna till ett virtuellt datornätverk. Skapa också ett eller flera virtuella Azure-nätverk. Observera att flera virtuella datornätverk kan mappas till ett enda Azure-nätverk.

1. Klicka på **Mappa nätverk** på sidan Snabbstart.
2. Välj VMM-källservern i **Källplats** på fliken **Nätverk**. Välj Azure i **Målplats**.
3. I **Källnätverk** visas en lista med virtuella datornätverk som är associerade med VMM-servern. I **Målnätverk** visas de Azure-nätverk som är associerade med prenumerationen.
4. Välj VM-källnätverket och klicka på **Mappa**.
5. Välj det Azure-målnätverk som du vill använda på sidan **Välj ett målnätverk**.
6. Slutför mappningen genom att klicka på bockmarkeringen.

    ![Inställningar för molnreplikering](./media/site-recovery-vmm-to-azure-classic/map-networks.png)

När du har sparat inställningarna börjar ett jobb spåra mappningsförloppet. Du kan övervaka jobbet på fliken Jobb. Befintliga virtuella replikdatorer som hör till VM-källnätverket ansluts till Azure-målnätverket. Nya virtuella datorer som är anslutna till källnätverket ansluts till det mappade Azure-nätverket efter replikeringen. Om du ändrar en befintlig mappning med ett nytt nätverk ansluts virtuella replikdatorer med de nya inställningarna.

Observera att om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns så ansluts den virtuella replikdatorn till det målundernätverket efter en redundansväxling. Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.

> [!NOTE]
> [Migrering av nätverk](../azure-resource-manager/resource-group-move-resources.md) mellan resursgrupper i samma prenumeration eller mellan prenumerationer stöds inte för nätverk som används för att distribuera Site Recovery.
>
>

## <a name="step-8-enable-protection-for-virtual-machines"></a>Steg 8: Aktivera skydd för virtuella datorer
När du har konfigurerar servrar, moln och nätverk kan du aktivera skydd för virtuella datorer i molnet. Observera följande:

* Virtuella datorer måste uppfylla [kraven för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
* För att kunna aktivera skydd måste du ange egenskaperna för operativsystemet och operativsystemdisken för den virtuella datorn. Du kan ange egenskapen när du skapar en virtuell dator i VMM med hjälp av en mall för virtuella datorer. Du kan också ange dessa egenskaper för befintliga virtuella datorer på flikarna **Allmänt** och **Maskinvarukonfiguration** i egenskaperna för de virtuella datorerna. Om du inte anger dessa egenskaper i VMM kan du konfigurera dem på Azure Site Recovery-portalen.

    ![Skapa en virtuell dator](./media/site-recovery-vmm-to-azure-classic/enable-new.png)

    ![Ändra egenskaper för en virtuell dator](./media/site-recovery-vmm-to-azure-classic/enable-existing.png)

1. Om du vill aktivera skydd klickar du på **Aktivera skydd** > **Lägg till virtuella datorer** på fliken **Virtual Machines** i molnet som den virtuella datorn finns i.
2. I listan över virtuella datorer i molnet väljer du den virtuella dator som du vill skydda.

    ![Aktivera skydd för en virtuell dator](./media/site-recovery-vmm-to-azure-classic/select-vm.png)

    Spåra förloppet för åtgärden **Aktivera skydd** på fliken **Jobb**, inklusive den inledande replikeringen. När jobbet **Slutför skydd** har körts är den virtuella datorn redo för redundans. När du har aktiverat skydd och de virtuella datorerna har replikerats kan du visa dem i Azure.

    ![Jobb för att skydda virtuella datorer](./media/site-recovery-vmm-to-azure-classic/vm-jobs.png)

1. Kontrollera egenskaperna för de virtuella datorerna och ändra dem vid behov.

    ![Verifiera virtuella datorer](./media/site-recovery-vmm-to-azure-classic/vm-properties.png)
2. Du kan ändra följande nätverksegenskaper på fliken **Konfigurera** i egenskaperna för virtuella datorer.

* **Antal nätverkskort på den virtuella måldatorn** – Antalet nätverkskort beror på storleken som du anger för den virtuella måldatorn. Kontrollera [storleksspecifikationerna för virtuella datorer](../virtual-machines/linux/sizes.md) och se hur många nätverkskort som stöds med storleken på den virtuella datorn. När du ändrar storleken för en virtuell dator och sparar inställningarna ändras antalet nätverkskort nästa gång du öppnar sidan **Konfigurera**. Antalet nätverkskort för virtuella måldatorer är det minsta antalet nätverkskort på den virtuella källdatorn och det högsta antalet nätverkskort som stöds av storleken för den valda virtuella datorn enligt följande:

  * Om antalet nätverkskort på källdatorn är mindre än eller lika med antalet nätverkskort som tillåts för måldatorns storlek så kommer målet att ha samma antal kort som källan.
  * Om antalet nätverkskort för den virtuella källdatorn överskrider det tillåtna antalet för målstorleken så används den högsta målstorleken.
  * Om en källdator exempelvis har två nätverkskort och måldatorn stöder fyra så kommer måldatorn att ha två kort. Om källdatorn har två nätverkskort men målstorleken endast stöder ett så kommer måldatorn bara att ha ett kort.     
* **Nätverk för den virtuella måldatorn** – Vilket nätverk som den virtuella datorn ansluter till beror på nätverksmappningen för den virtuella källdatorns nätverk. Om den virtuella källdatorn har mer än ett nätverkskort och källnätverken mappas till olika nätverk på målet så måste du välja ett av målnätverken.
* **Undernät för varje nätverkskort** –För varje nätverkskort kan du välja det undernät som den redundansväxlade virtuella datorn ska ansluta till.
* **Mål-IP-adress** – Om nätverkskortet på den virtuella källdatorn är konfigurerat att använda en statisk IP-adress kan du ange IP-adressen för den virtuella måldatorn. Med den här funktionen behålls IP-adressen för en virtuell källdator efter en redundansväxling. Om ingen IP-adress anges tilldelas en tillgänglig IP-adress till nätverkskortet i samband med redundansväxlingen. Om mål-IP-adressen har angetts men redan används av en annan virtuell dator som körs i Azure så misslyckas redundansväxlingen.  

    ![Ändra egenskaper för nätverk](./media/site-recovery-vmm-to-azure-classic/multi-nic.png)

> [!NOTE]
> Virtuella Linux-datorer med en statisk IP-adress stöds inte.
>
>

## <a name="test-the-deployment"></a>Testa distributionen
Om du vill testa distributionen kan du köra ett redundanstest för en enskild virtuell dator eller skapa en återställningsplan med flera virtuella datorer och köra ett redundanstest för planen.  

Ett redundanstest simulerar redundans- och återställningsmekanismen i ett isolerat nätverk. Tänk på följande:

* Om du vill ansluta till den virtuella datorn i Azure med hjälp av Fjärrskrivbord efter redundansväxlingen aktiverar du Anslutning till fjärrskrivbord på den virtuella datorn innan du kör redundanstestet.
* Efter redundansväxlingen använder du en offentlig IP-adress för att ansluta till den virtuella Azure-datorn med hjälp av Fjärrskrivbord. Om du vill göra det ser du till att det inte finns några domänprinciper som hindrar dig från att ansluta till en virtuell dator med en offentlig adress.

> [!NOTE]
> För att få bästa möjliga prestanda när du redundansväxlar till Azure kan du kontrollera att du installerat Azure-agenten på den virtuella datorn. Den gör att starten går snabbare och underlättar felsökning. Hämta [Linux-agenten](https://github.com/Azure/WALinuxAgent) eller [Windows-agenten](http://go.microsoft.com/fwlink/?LinkID=394789).
>
>

### <a name="create-a-recovery-plan"></a>Skapa en återställningsplan
1. Lägg till en ny plan på fliken **Återställningsplaner**. Ange ett namn, **VMM** i **Källtyp** och VMM-källservern i **Källa**. Målet är Azure.

    ![Skapa en återställningsplan](./media/site-recovery-vmm-to-azure-classic/recovery-plan1.png)
2. På sidan **Välj virtuella datorer** väljer du de virtuella datorer som du vill lägga till i återställningsplanen. Dessa virtuella datorer läggs till i standardgruppen för återställningsplaner – Grupp 1. Upp till högst 100 virtuella datorer i en enda återställningsplan testas.

* Om du vill kontrollera egenskaperna för virtuella datorer innan du lägger till dem i planen klickar du på den virtuella datorn på egenskapssidan för det moln som den finns i. Du kan också konfigurera egenskaper för virtuella datorer i VMM-konsolen.
* Skydd har aktiverats för alla virtuella datorer som visas. Listan innehåller både virtuella datorer som skydd har aktiverats för och vars inledande replikering har slutförts, och virtuella datorer som skydd har aktiverats för men vars inledande replikering har väntande status. Endast virtuella datorer vars inledande replikering har slutförts kan redundansväxlas som en del av en återställningsplan.

    ![Skapa en återställningsplan](./media/site-recovery-vmm-to-azure-classic/select-rp.png)

När en återställningsplan har skapats visas den på fliken **Återställningsplaner**. Du kan också lägga till [Azure Automation Runbook-rutiner](site-recovery-runbook-automation.md) till återställningsplanen om du vill automatisera åtgärder under en redundansväxling.

### <a name="run-a-test-failover"></a>Köra ett redundanstest
Du kan köra ett redundanstest mot Azure på två sätt.

* **Testa redundans utan ett Azure-nätverk** – Den här typen av redundanstest kontrollerar att den virtuella datorn visas korrekt i Azure. Den virtuella datorn ansluts inte till något Azure-nätverk efter redundansväxlingen.
* **Testa redundans med ett Azure-nätverk** – Den här typen av redundanstest kontroller att hela replikeringsmiljön visas som förväntat och att redundansväxlade virtuella datorer ansluts korrekt till det angivna Azure-målnätverket. Vad gäller hanteringen av undernät i samband med ett redundanstest så identifierar testet undernätet för de virtuella datorerna baserat på undernätet för den virtuella replikdatorn. Detta skiljer sig från vanlig replikering då undernätet för en virtuell replikdator baseras på undernätet för den virtuella källdatorn.

Om du vill köra ett redundanstest för en virtuell dator som du har aktiverat skydd för till Azure utan att ange något Azure-målnätverk behöver du inte förbereda något. Om du vill köra ett redundanstest med ett Azure-målnätverk måste du skapa ett nytt Azure-nätverk som är isolerat från Azure-produktionsnätverket (standardbeteendet när du skapar ett nytt nätverk i Azure). Mer information finns i [Köra ett redundanstest](site-recovery-failover.md).

Du måste också konfigurera infrastrukturen för att den replikerade virtuella datorn ska fungera som förväntat. Exempelvis kan en virtuell dator med en domänkontrollant och DNS replikeras till Azure med hjälp av Azure Site Recovery och kan skapas i testnätverket med hjälp av funktionen Testa redundans. Mer information finns i avsnittet [Saker att tänka på vid redundanstestning för Active Directory](site-recovery-active-directory.md#test-failover-considerations).

Gör följande om du vill köra ett redundanstest:

1. På fliken **Återställningsplaner** väljer du planen och klickar på **Testa redundans**.
2. På sidan **Bekräfta redundanstest** väljer du **Inget** eller ett specifikt Azure-nätverk.  Observera att om du väljer Inget så kontrollerar redundanstestet att den virtuella datorn replikeras korrekt till Azure, men kontrollerar inte nätverkskonfigurationen för replikeringen.

    ![Inget nätverk](./media/site-recovery-vmm-to-azure-classic/test-no-network.png)
3. Om datakryptering är aktiverat för molnet går du till **Krypteringsnyckel** och väljer det certifikat som utfärdades under installationen av providern på VMM-servern när du aktiverade alternativet för att använda datakryptering för ett moln.
4. Du kan följa redundansförloppet på fliken **Jobb**. Du bör även kunna se den virtuella testreplikdatorn på Azure-portalen. Om du har åtkomst till de virtuella datorerna från ditt lokala nätverk kan du initiera en fjärrskrivbordsanslutning till den virtuella datorn.
5. När redundansväxlingen kommer till fasen **Slutför testning** slutför du det genom att klicka på **Slutför test**. Om du vill visa mer information går du till fliken **Jobb** där du kan övervaka förloppet och statusen för redundansväxlingen, samt utföra eventuella nödvändiga åtgärder.
6. Efter redundansväxlingen visas den virtuella testreplikdatorn i Azure Portal. Om du har åtkomst till de virtuella datorerna från ditt lokala nätverk kan du initiera en fjärrskrivbordsanslutning till den virtuella datorn. Gör följande:

   1. Kontrollera att de virtuella datorerna startas.
   2. Om du vill ansluta till den virtuella datorn i Azure med hjälp av Fjärrskrivbord efter redundansväxlingen aktiverar du Anslutning till fjärrskrivbord på den virtuella datorn innan du kör redundanstestet. Du måste också lägga till en RDP-slutpunkt på den virtuella datorn. Du kan använda [Azure Automation Runbook-rutiner](site-recovery-runbook-automation.md) för att göra det.
   3. Om du efter redundansväxlingen använder en offentlig IP-adress för att ansluta till den virtuella datorn i Azure med hjälp av Fjärrskrivbord kontrollerar du att det inte finns några domänprinciper som hindrar dig från att ansluta till en virtuell dator med en offentlig adress.
7. När testet är klart gör du följande:

   * Klicka på **Redundanstestet är klart**. Rensa testmiljön så att de virtuella testdatorerna stängs av och tas bort.
   * Klicka på **Kommentarer** om du vill skriva ner och spara observationer från redundanstestningen.


## <a name="next-steps"></a>Nästa steg
Lär dig mer om hur du [konfigurerar återställningsplaner](site-recovery-create-recovery-plans.md) och [redundans](site-recovery-failover.md).

