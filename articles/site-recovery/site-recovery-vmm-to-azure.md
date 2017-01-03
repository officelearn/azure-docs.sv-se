---
title: "Replikera virtuella Hyper-V-datorer i VMM-moln till Azure med hjälp av Azure-portalen | Microsoft Docs"
description: "Beskriver hur du distribuerar Site Recovery för att dirigera replikering, redundans och återställning av virtuella Hyper-V-datorer i VMM-moln till Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/23/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 1268d29b0d9c4368f62918758836a73c757c0c8d
ms.openlocfilehash: 3727972c544bb8c2724e9f38953882a7f2251a60


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-the-azure-portal"></a>Replikera virtuella Hyper-V-datorer i VMM-moln till Azure med hjälp av Azure-portalen
> * [Azure Portal](site-recovery-vmm-to-azure.md)
> * [Klassiska Azure](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell – Klassisk](site-recovery-deploy-with-powershell.md)
>
>

Välkommen till Azure Site Recovery-tjänsten!

Site Recovery är en Azure-tjänst som bidrar till din strategi för affärsstabilitet och haveriberedskap (BCDR). Site Recovery arrangerar replikeringen av lokala fysiska servrar och virtuella datorer till molnet (Azure) eller till ett sekundärt datacenter. Vid driftstopp på den primära platsen växlar du över till den sekundära platsen så att program och arbetsbelastningar fortsätter att vara tillgängliga. Du växlar tillbaka till den primära platsen när den har återgått till normal drift. Läs mer i [Vad är Azure Site Recovery?](site-recovery-overview.md)

Den här artikeln beskriver hur du replikerar lokala virtuella Hyper-V-datorer som hanteras i System Center VMM-moln till Azure med hjälp av Azure Site Recovery på Azure-portalen.

När du har läst den här artikeln kan du skriva eventuella kommentarer längst ned. Om du har tekniska frågor kan du ställa dem i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="quick-reference"></a>Snabbreferens
För en fullständig distribution rekommenderar vi att du följer alla stegen i artikeln. Men om du har ont om tid har du en snabb sammanfattning här.

| **Område** | **Detaljer** |
| --- | --- |
| **Scenario för distribution** |Replikera virtuella Hyper-V-datorer i VMM-moln till Azure med hjälp av Azure-portalen |
| **Lokala krav** |En eller flera VMM-servrar som körs i System Center 2012 R2 med ett eller flera moln.<br/><br/> Moln bör innehålla en eller flera värdgrupper för VMM.<br/><br/> Minst en Hyper-V-server i molnet som kör minst Windows Server 2012 R2 med Hyper-V-rollen eller Microsoft Hyper-V Server 2012 R2 med de senaste uppdateringarna installerade.<br/><br/> VMM-servrar och Hyper-V-värdar behöver internetåtkomst och måste kunna komma åt specifika URL:er direkt eller via en proxyserver. [Fullständig information](#on-premises-prerequisites). |
| **Lokala begränsningar** |HTTPS-baserade proxyservrar stöds inte |
| **Provider/agent** |Replikerade virtuella datorer behöver Azure Site Recovery-providern.<br/><br/> Hyper-V-värdar behöver Recovery Services-agenten.<br/><br/> Du installerar dessa under distributionen. |
|  **Krav för Azure** |Azure-konto<br/><br/> Recovery Services-valv<br/><br/> LRS- eller GRS-lagringskontot i valvregionen<br/><br/> Standardlagringskonto<br/><br/> Virtuellt Azure-nätverk i valvregionen. [Fullständig information](#azure-prerequisites). |
|  **Begränsningar för Azure** |Om du använder GRS behöver du ett annat LRS-konto för loggning<br/><br/> Lagringskonton som skapats på Azure-portalen kan inte flyttas mellan resursgrupper i samma, eller olika, prenumerationer. <br/><br/> Premium-lagring stöds inte.<br/><br/> Azure-nätverk som används för Site Recovery kan inte flyttas mellan resursgrupper i samma, eller olika, prenumerationer. 
|  **VM-replikering** |[Virtuella datorer måste uppfylla kraven för Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)<br/><br/>
|  **Begränsningar för replikering** |Du kan inte replikera virtuella datorer som kör Linux med en statisk IP-adress.<br/><br/> Du kan utesluta specifika diskar från replikeringen, men inte OS-disken.
| **Distributionssteg** |1) Förbered Azure (prenumeration, lagring, nätverk) -> 2) Förbered lokalt (VMM och nätverksmappning) -> 3) Skapa Recovery Services-valv > 4) Konfigurera VMM och Hyper-V-värdar -> 5) Konfigurera replikeringsinställningar -> 6) Aktivera replikering -> 7) Testa replikering och redundans. |

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery på Azure Portal

Azure har två olika [distributionsmodeller](../resource-manager-deployment-model.md) för att skapa och arbeta med resurser – Azure Resource Manager och den klassiska distributionsmodellen. Azure har också två portaler – den klassiska Azure-portalen och Azure-portalen. Den här artikeln beskriver hur du distribuerar i Azure-portalen.


Den här artikeln beskriver hur du distribuerar på Azure-portalen, som tillhandahåller en smidig distributionsmiljö. Den klassiska portalen kan användas för att underhålla befintliga valv. Du kan inte skapa nya valv med hjälp av den klassiska portalen.


## <a name="site-recovery-in-your-business"></a>Site Recovery i ditt företag

Organisationer behöver en BCDR-strategi som beskriver hur appar och data fungerar och är tillgängliga under planerade och oplanerade driftavbrott och som ser till att systemets normala drifttillstånd återställs så fort som möjligt. Här är vad Site Recovery kan göra:

* Offsiteskydd för affärsappar som körs på virtuella Hyper-V datorer.
* En enda plats för att installera, hantera och övervaka replikering, redundans och återställning.
* Enkel redundans till Azure och återställning efter fel (återställning) från Azure Hyper-V-värdservrar på din lokala plats.
* Återställningsplaner som innehåller flera virtuella datorer, så att nivåbaserade programbelastningar redundansväxlar tillsammans.

## <a name="scenario-architecture"></a>Scenariots arkitektur
Dessa är komponenterna i scenariot:

* **VMM-server**: En lokal VMM-server med ett eller flera moln.
* **Hyper-V-värd eller Hyper-V-kluster**: Hyper-V-värdservrar eller Hyper-V-kluster som hanteras i VMM-moln.
* **Azure Site Recovery-provider och Recovery Services-agent**: Under distributionen installerar du Azure Site Recovery-providern på VMM-servern och Microsoft Azure Recovery Services-agenten på Hyper-V-värdservrar. Providern på VMM-servern kommunicerar med Site Recovery via HTTPS 443 för att samordna replikeringen. Agenten på Hyper-V-värdservern replikerar data till Azure via HTTPS 443 som standard.
* **Azure**: Du behöver en Azure-prenumeration och ett Azure Storage-konto för att lagra replikerade data samt ett virtuellt Azure-nätverk så att virtuella datorer i Azure ansluts till ett nätverk efter en redundansväxling.

![E2A-topologi](./media/site-recovery-vmm-to-azure/architecture.png)

## <a name="azure-prerequisites"></a>Krav för Azure
Det här behöver du i Azure.

| **Krav** | **Detaljer** |
| --- | --- |
| **Azure-konto** |Du behöver ett [Microsoft Azure](http://azure.microsoft.com/)-konto. Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/). [Lär dig mer](https://azure.microsoft.com/pricing/details/site-recovery/) om priserna för Site Recovery. |
| **Azure Storage** |Du behöver ett Azure Storage-standardkonto för att lagra replikerade data. Du kan använda ett LRS- eller GRS-lagringskonto. Vi rekommenderar GRS så att dina data är flexibla i händelse av ett regionalt strömavbrott, eller om det inte går att återställa den primära regionen. [Läs mer](../storage/storage-redundancy.md). Kontot måste finnas i samma region som Recovery Services-valvet.<br/><br/>Premium-lagring stöds inte.<br/><br/> Replikerade data lagras i Azure och virtuella Azure-datorer skapas när redundansväxlingen sker. <br/><br/> [Läs om](../storage/storage-introduction.md) Azure-lagring. |
| **Azure-nätverk** |Du behöver ett virtuellt Azure-nätverk som virtuella Azure-datorer ansluter till vid en redundansväxling. Nätverket måste finnas i samma region som Recovery Services-valvet. |

## <a name="on-premises-prerequisites"></a>Krav för det lokala systemet
Här är vad du behöver lokalt

| **Krav** | **Detaljer** |
| --- | --- |
| **VMM** |En eller flera VMM-servrar som körs på System Center 2012 R2. Ett eller flera moln bör vara konfigurerade för varje VMM-server. Ett moln bör innehålla:<br/><br/> En eller flera VMM-värdgrupper.<br/><br/> En eller flera Hyper-V-värdservrar eller Hyper-V-kluster i varje värdgrupp.<br/><br/>[Lär dig mer](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx) om hur du konfigurerar VMM-moln. |
| **Hyper-V** |Hyper-V-värdservrar måste köra minst **Windows Server 2012 R2** med Hyper-V-rollen eller **Microsoft Hyper-V Server 2012 R2** och ha de senaste uppdateringarna installerade.<br/><br/> En Hyper-V-server måste innehålla en eller flera virtuella datorer.<br/><br/> En Hyper-V-värdserver eller ett Hyper-V-kluster som innehåller virtuella datorer som du vill replikera måste hanteras i ett VMM-moln.<br/><br/>Hyper-V-servrar måste vara anslutna till Internet, antingen direkt eller via en proxyserver.<br/><br/>De korrigeringar som anges i artikeln [2961977](https://support.microsoft.com/kb/2961977) måste vara installerade på Hyper-V-servrarna.<br/><br/>Hyper-V-värdservrar behöver Internetåtkomst för datareplikering till Azure. |
| **Provider och agent** |Under distributionen av Azure Site Recovery installerar du Azure Site Recovery-providern på VMM-servern och Recovery Services-agenten på Hyper-V-värdar. Providern och agenten måste ansluta till Azure via Internet, direkt eller via en proxyserver. HTTPS-baserade proxyservrar stöds inte. Proxyservern på VMM-servern och Hyper-V-värdar måste ge åtkomst till: <br/><br/> ``*.accesscontrol.windows.net``<br/><br/> ``*.backup.windowsazure.com``<br/><br/> ``*.hypervrecoverymanager.windowsazure.com``<br/><br/> ``*.store.core.windows.net``<br/><br/> ``*.blob.core.windows.net``<br/><br/> ``https://www.msftncsi.com/ncsi.txt``<br/><br/> ``time.windows.com``<br/><br/> ``time.nist.gov``<br/><br/> Om du har IP-adressbaserade brandväggsregler på VMM-servern kontrollerar du att reglerna tillåter kommunikation till Azure.<br/><br/> Tillåt [Azure Datacenter IP Ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653) (IP-intervall för Azures datacenter) och HTTPS-port 443.<br/><br/> Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra.<br/><br/> |

## <a name="protected-machine-prerequisites"></a>Krav för skyddade datorer
| **Krav** | **Detaljer** |
| --- | --- |
| **Skyddade virtuella datorer** |Innan du växlar över en virtuell dator kontrollerar du att namnet som tilldelats den virtuella Azure-datorn uppfyller [kraven för Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Du kan ändra namnet efter att du har aktiverat replikering för den virtuella datorn. <br/><br/> Utrymmet på en enskild disk på skyddade datorer får inte vara över 1 023 GB. En virtuell dator kan ha upp till 64 diskar (alltså upp till 64 TB).<br/><br/> Gästkluster med delade diskar stöds inte.<br/><br/> Start via UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface) stöds inte.<br/><br/> Om NIC-teamindelning används på den virtuella källdatorn konverteras det till ett enda nätverkskort efter en redundansväxling till Azure.<br/><br/>Du kan inte skydda virtuella Hyper-V-datorer som kör Linux med en statisk IP-adress. |

## <a name="prepare-for-deployment"></a>Förbereda för distribution
När du förbereder distributionen måste du:

1. [Skapa ett Azure-nätverk](#set-up-an-azure-network) som de virtuella Azure-datorerna ska anslutas till efter en redundansväxling.
2. [Skapa ett Azure-lagringskonto](#set-up-an-azure-storage-account) för replikerade data.
3. [Förbereda VMM-servern](#prepare-the-vmm-server) för Site Recovery-distribution.
4. [Förbereda för nätverksmappning](#prepare-for-network-mapping). Konfigurera nätverk så att du kan konfigurera nätverksmappning under Site Recovery-distributionen.

### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk
Du behöver ett Azure-nätverk dit de virtuella Azure-datorerna som skapats kan anslutas efter redundansväxlingen.

* Nätverket måste finnas på samma region som Recovery Services-valvet.
* Beroende på vilken resursmodell du vill använda för redundansväxlade virtuella Azure-datorer konfigurerar du Azure-nätverket i [Resource Manager-läge](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) eller [klassiskt läge](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
* Vi rekommenderar att du konfigurerar ett nätverk innan du börjar. Om du inte gör det måste du göra det under distributionen av Site Recovery.
Observera att Azure-nätverk som används av Site Recovery inte kan [flyttas](../resource-group-move-resources.md) inom samma eller mellan olika prenumerationer.

### <a name="set-up-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto
* Du behöver ett Azure-standardlagringskonto som ska lagra data som replikeras till Azure. Kontot måste finnas i samma region som Recovery Services-valvet.
* Beroende på vilken resursmodell du vill använda för redundansväxlade virtuella Azure-datorer skapar du ett konto i [Resource Manager-läge](../storage/storage-create-storage-account.md) eller [klassiskt läge](../storage/storage-create-storage-account-classic-portal.md).
* Vi rekommenderar att du skapar ett konto innan du börjar. Om du inte gör det måste du göra det under distributionen av Site Recovery.
- Observera att lagringskonton som används av Site Recovery inte kan [flyttas](../resource-group-move-resources.md) inom samma eller mellan olika prenumerationer.

### <a name="prepare-the-vmm-server"></a>Förbereda VMM-servern
* Kontrollera att VMM-servern uppfyller [kraven](#on-premises-prerequisites).
* Under distributionen av Site Recovery kan du ange att alla moln på en VMM-server ska vara tillgängliga på Azure-portalen. Om du bara vill att specifika moln ska visas på portalen aktiverar du den inställningen i molnet i VMM-administrationskonsolen.

### <a name="prepare-for-network-mapping"></a>Förbereda för nätverksmappning
Du måste konfigurera nätverksmappning under distributionen av Site Recovery. Nätverksmappningen mappar mellan VM-källnätverken i VMM och Azure-målnätverken för att följande ska vara möjligt:

* Datorer som redundansväxlas i samma nätverk kan ansluta till varandra, även om de inte redundansväxlas på samma sätt eller i samma återställningsplan.
* Om en nätverksgateway har konfigurerats i Azure-målnätverket kan virtuella Azure-datorer ansluta till lokala virtuella datorer.
* När du ska konfigurera nätverksmappnig behöver du följande:

  * Se till att de virtuella datorerna på Hyper-V-källvärdservern är anslutna till ett VM-nätverk i VMM. Nätverket ska kopplas till ett logiskt nätverk som är associerat med molnet.
  * Ett Azure-nätverk så som det beskrivs [ovan](#set-up-an-azure-network)
* [Lär dig mer](site-recovery-network-mapping.md) om hur nätverksmappning fungerar.

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Nytt** > **Hantering** > **Recovery Services**. Du kan också klicka på **Bläddra** > **Recovery Services**-valv > **Lägg till**.

    ![Nytt valv](./media/site-recovery-vmm-to-azure/new-vault3.png)
3. I **Namn** anger du ett eget namn som identifierar valvet. Om du har mer än en prenumeration väljer du en av dem.
4. [Skapa en resursgrupp](../resource-group-template-deploy-portal.md) eller välj en befintlig. Ange en Azure-region. Datorer replikeras till den här regionen. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/)
5. Du kan snabbt komma åt valvet från instrumentpanelen genom att klicka på **Fäst på instrumentpanelen** > **Skapa valv**.

    ![Nytt valv](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

Det nya valvet visas på **instrumentpanelen** > **Alla resurser** och på huvudbladet för **Recovery Services-valv**.

## <a name="get-started"></a>Kom igång

Site Recovery har en Komma igång-guide som hjälper dig att distribuera så snabbt som möjligt. Komma igång-guiden kontrollerar kraven och vägleder dig genom Site Recovery-distributionsstegen i rätt ordning.

Du väljer vilken typ av datorer som du vill replikera och vart du vill replikera. Du konfigurerar lokala servrar, Azure-lagringskonton och nätverk. Du skapar replikeringsprinciper och utför kapacitetsplanering. När infrastrukturen är på plats kan aktiverar du replikering för virtuella datorer. Du kan köra redundansväxlingar för specifika datorer eller skapa återställningsplaner för att redundansväxla flera datorer.

Starta Komma igång-guiden genom att välja hur du vill distribuera Site Recovery. Komma igång-flödet varierar något beroende på dina replikeringskrav.

## <a name="step-1-choose-your-protection-goals"></a>Steg 1: Välja skyddsmål
Välj vad och vart du vill replikera.

1. På bladet **Recovery Services-valv** väljer du ditt valv och klickar på **Inställningar**.
2. I **Komma igång** klickar du på **Site Recovery** > **Steg 1: Förbereda infrastrukturen** > **Skyddsmål**.

    ![Välja mål](./media/site-recovery-vmm-to-azure/choose-goals.png)
3. I **Skyddsmål** väljer du **Till Azure** och sedan **Ja, med Hyper-V**. Välj **Ja** för att bekräfta att du hanterar Hyper-V-värdar och återställningsplatsen med hjälp av VMM. Klicka på **OK**.

    ![Välja mål](./media/site-recovery-vmm-to-azure/choose-goals2.png)

## <a name="step-2-set-up-the-source-environment"></a>Steg 2: Konfigurera källmiljön
Installera Azure Site Recovery-providern på VMM-servern och registrera servern i valvet. Installera Azure Recovery Services-agenten på Hyper-V-värdar.

1. Klicka på **Steg 2: Förbereda infrastrukturen** > **Källa**.

    ![Konfigurera källan](./media/site-recovery-vmm-to-azure/set-source1.png)
2. I **Förbered källa** klickar du på **+ VMM** för att lägga till en VMM-server.

    ![Konfigurera källan](./media/site-recovery-vmm-to-azure/set-source2.png)
3. På bladet **Lägg till server** kontrollerar du att **System Center VMM-server** visas i **Servertyp** och att VMM-servern uppfyller [de allmänna kraven och URL-kraven](#on-premises-prerequisites).
4. Ladda ned installationsfilen för Azure Site Recovery-providern.
5. Ladda ned registreringsnyckeln. Du behöver den när du kör installationsprogrammet. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/site-recovery-vmm-to-azure/set-source3.png)
6. Installera Azure Site Recovery-providern på VMM-servern.

### <a name="set-up-the-azure-site-recovery-provider"></a>Konfigurera Azure Site Recovery-providern
1. Kör installationsfilen för providern.
2. I **Microsoft Update** kan du välja uppdateringar så att provideruppdateringarna installeras i enlighet med din Microsoft Update-princip.
3. I **Installation** accepterar du eller ändrar standardinstallationsplatsen för providern och klickar på **Installera**.

    ![Installationsplats](./media/site-recovery-vmm-to-azure/provider2.png)
4. När installationen är klar klickar du på **Registrera** för att registrera VMM-servern i valvet.
5. I **Valvinställningar** klickar du på **Bläddra** för att välja valvnyckelfilen. Ange Azure Site Recovery-prenumerationen och valvnamnet.

    ![Serverregistrering](./media/site-recovery-vmm-to-azure/provider10.PNG)
6. I **Internetanslutning** anger du hur providern som körs på VMM-servern ska ansluta till Site Recovery via internet.

   * Om du vill att providern ska ansluta direkt väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
   * Om din befintliga proxyserver kräver autentisering, eller om du vill använda en anpassad proxyserver, väljer du **Anslut till Azure Site Recovery med proxyserver**.
   * Om du använder en anpassad proxyserver anger du adressen, porten och autentiseringsuppgifterna.
   * Om du använder en proxyserver bör du redan ha tillåtit URL:erna som beskrivs i [krav](#on-premises-prerequisites).
   * Om du använder en anpassad proxyserver skapas ett RunAs-konto (DRAProxyAccount) i VMM automatiskt med de angivna proxyautentiseringsuppgifterna. Konfigurera proxyservern så att det här kontot kan autentiseras. Du kan ändra inställningarna för RunAs-kontot i VMM i VMM-konsolen. I **Inställningar** expanderar du **Säkerhet** > **Kör som-konton** och ändrar sedan lösenordet för DRAProxyAccount. Du måste starta om VMM-tjänsten så att den här inställningen börjar gälla.

     ![Internet](./media/site-recovery-vmm-to-azure/provider13.PNG)
7. Acceptera eller ändra platsen för ett SSL-certifikat som genereras automatiskt för datakryptering. Det här certifikatet används om du aktiverar datakryptering för ett moln som skyddas av Azure på Azure Site Recovery-portalen. Skydda det här certifikatet. När du kör en redundansväxling till Azure måste den dekrypteras om datakryptering är aktiverat.
8. I **Servernamn** anger du ett eget namn som identifierar VMM-servern i valvet. I en klusterkonfiguration anger du namnet på VMM-klusterrollen.
9. Aktivera **Synkronisera molnmetadata** om du vill synkronisera metadata för alla moln på VMM-servern med valvet. Den här åtgärden behöver bara göras en gång på varje server. Om du inte vill synkronisera alla moln kan du lämna den här inställningen avmarkerad och synkronisera varje moln individuellt i molnegenskaperna i VMM-konsolen. Slutför processen genom att klicka på **Registrera**.

    ![Serverregistrering](./media/site-recovery-vmm-to-azure/provider16.PNG)
10. Registreringen startar. När registreringen är klar visas servern på bladet **Inställningar** > **Servrar** i valvet.

#### <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Installation från kommandoraden för Azure Site Recovery-providern
Azure Site Recovery-providern kan installeras från kommandoraden. Den här metoden kan användas för att installera providern på Server Core för Windows Server 2012 R2.

1. Ladda ned installationsfilen och registreringsnyckeln för providern till en mapp. Till exempel C:\ASR.
2. Extrahera installationsprogrammet för providern genom att köra dessa kommandon från en upphöjd kommandotolk:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Installera komponenterna genom att köra detta kommando:

            C:\ASR> setupdr.exe /i
4. Kör sedan följande kommandon för att registrera servern i valvet:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Där:

* **/Credentials**: Obligatorisk parameter som anger var registreringsnyckelfilen finns.  
* **/FriendlyName**: Obligatorisk parameter för namnet på Hyper-V-värdservern som visas på Azure Site Recovery-portalen.
* * **/EncryptionEnabled**: Valfri parameter när du replikerar virtuella Hyper-V-datorer i VMM-moln till Azure. Ange om du vill kryptera virtuella datorer i Azure (kryptering av vilande data). Kontrollera att namnet på filen har filnamnstillägget **.pfx**. Kryptering är inaktiverat som standard.
* **/proxyAddress**: Valfri parameter som anger adressen till proxyservern.
* **/proxyport**: Valfri parameter som anger porten för proxyservern.
* **/proxyUsername**: Valfri parameter som anger användarnamnet för proxyservern (om proxyservern kräver autentisering).
* **/proxyPassword**: Valfri parameter som anger lösenordet för autentisering med proxyservern (om proxyservern kräver autentisering).

### <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Installera Azure Recovery Services-agenten på Hyper-V-värdar
1. När du har konfigurerat providern måste du hämta installationsfilen för Azure Recovery Services-agenten. Kör installationsprogrammet på varje Hyper-V-server i VMM-molnet.

    ![Hyper-V-platser](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)
2. I **Kravkontroll**, klicka på **Nästa**. Alla nödvändiga komponenter som saknas installeras automatiskt.

    ![Krav för Recovery Services-agenten](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)
3. Godkänn eller ändra installationsplatsen och cachelagringsplatsen på **Installationsinställningar**. Du kan konfigurera cachen på en enhet som har minst 5 GB tillgängligt utrymme, men vi rekommenderar en cacheenhet med 600 GB eller mer ledigt utrymme. Klicka på **Installera**.
4. När installationen är klar klickar du på **Stäng** för att slutföra.

    ![Registrera MARS-agenten](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### <a name="command-line-installation-for-azure-site-recovery-services-agent"></a>Installation från kommandoraden av Azure Site Recovery Services-agenten
Du kan installera Microsoft Azure Recovery Services-agenten från kommandoraden med följande kommando:

     marsagentinstaller.exe /q /nu

#### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Konfigurera Internetåtkomst via en proxyserver till Site Recovery från Hyper-V-värdar
Recovery Services-agenten som körs på Hyper-V-värdar behöver Internetåtkomst till Azure för VM-replikering. Om du ansluter till Internet via en proxyserver konfigurerar du den så här:

1. Öppna snapin-modulen Microsoft Azure Backup MMC på Hyper-V-värden. Som standard finns det en genväg till Microsoft Azure Backup på skrivbordet eller i C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Klicka på **Ändra egenskaper** i snapin-modulen.
3. Ange information om proxyservern på fliken **Proxykonfiguration**.

    ![Registrera MARS-agenten](./media/site-recovery-vmm-to-azure/mars-proxy.png)
4. Kontrollera att agenten kan nå URL:erna som beskrivs i [kravavsnittet](#on-premises-prerequisites).

## <a name="step-3-set-up-the-target-environment"></a>Steg 3: Konfigurera målmiljön
Ange Azure-lagringskontot som ska användas för replikering och det Azure-nätverk som virtuella Azure-datorer ska ansluta till efter en redundansväxling.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den prenumeration och resursgrupp där du vill skapa de redundansväxlade virtuella datorerna. Välj den distributionsmodell som du vill använda i Azure (klassisk eller Resource Manager) för de redundansväxlade virtuella datorerna.

    ![Lagring](./media/site-recovery-vmm-to-azure/enablerep3.png)

2. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.
    ![Storage](./media/site-recovery-vmm-to-azure/compatible-storage.png)

4. Om du inte har skapat ett lagringskonto och vill skapa ett med hjälp av Resource Manager klickar du på **+Lagringskonto** för att göra det direkt.  På bladet **Skapa lagringskonto** anger du kontonamn, typ, prenumeration och plats. Kontot måste finnas på samma plats som Recovery Services-valvet.

   ![Lagring](./media/site-recovery-vmm-to-azure/gs-createstorage.png)

   Tänk på följande:

   * Om du vill skapa ett lagringskonto med hjälp av den klassiska modellen gör du det på Azure-portalen. [Läs mer](../storage/storage-create-storage-account-classic-portal.md)
   * Om du använder ett Premium Storage-konto för replikerade data konfigurerar du ytterligare ett standardlagringskonto för att lagra replikeringsloggar som samlar in löpande ändringar i lokala data.
5. Om du inte har skapat ett Azure-nätverk och vill skapa ett med hjälp av Resource Manager klickar du på **+Nätverk** för att göra det direkt. På bladet **Skapa virtuellt nätverk** anger du nätverksnamn, adressintervall, information om undernät, prenumeration och plats. Nätverket måste finnas på samma plats som Recovery Services-valvet.

   ![Nätverk](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

   Om du vill skapa ett nätverk med den klassiska modellen gör du det på Azure-portalen. [Läs mer](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>Konfigurera nätverksmappning
* [Läs](#prepare-for-network-mapping) en snabb överblick över vad nätverksmappning gör. [Läs detta](site-recovery-network-mapping.md) om du vill ha en mer detaljerad förklaring.
* Kontrollera att virtuella datorer på VMM-servern är anslutna till ett virtuellt datornätverk och att du har skapat minst ett virtuellt Azure-nätverk. Flera virtuella datornätverk kan mappas till ett enda Azure-nätverk.

Konfigurera mappning på följande sätt:

1. I **Inställningar** > **Site Recovery-infrastruktur** > **Nätverksmappningar** > **Nätverksmappning** klickar du på ikonen **+Nätverksmappning**.

    ![Nätverksmappning](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. På **Lägg till nätverksmappning** väljer du VMM-källservern och **Azure** som mål.
3. Kontrollera prenumerationen och distributionsmodellen efter redundansväxling.
4. I **Källnätverk** väljer du det lokala VM-källnätverk som du vill mappa från listan som är associerad med VMM-servern.
5. I **Målnätverk** väljer du det Azure-nätverk som de virtuella Azure-replikdatorerna ska anslutas till när de skapas. Klicka sedan på **OK**.

    ![Nätverksmappning](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Det här händer när nätverksmappningen börjar:

* Befintliga virtuella datorerna i VM-källnätverket ansluts till målnätverket när mappningen börjar. Nya virtuella datorer som är anslutna till VM-källnätverket ansluts till det mappade Azure-nätverket när replikeringen sker.
* Om du ändrar en befintlig nätverksmappning ansluts virtuella replikdatorer med hjälp av de nya inställningarna.
* Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns så ansluts den virtuella replikdatorn till det målundernätverket efter en redundansväxling.
* Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.

## <a name="step-4-set-up-replication-settings"></a>Steg 4: Konfigurera replikeringsinställningar
1. Skapa en ny replikeringsprincip genom att klicka på **Förbered infrastruktur** > **Replikeringsinställningar** > **+Skapa och koppla**.

    ![Nätverk](./media/site-recovery-vmm-to-azure/gs-replication.png)
2. I **Princip för att skapa och koppla** anger du ett principnamn.
3. I **Kopieringsfrekvens** anger du hur ofta du vill replikera förändringsdata (delta) efter den första replikeringen (med 30 sekunders mellanrum, var femte minut eller varje kvart).
4. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden i antal timmar för varje återställningspunkt. Skyddade datorer kan återställas till valfri punkt inom en period.
5. I **Appkompatibel ögonblicksbildsfrekvens** anger du hur ofta (1–12 timmar) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas. Hyper-V använder två typer av ögonblicksbilder: en standardögonblicksbild som tillhandahåller en inkrementell ögonblicksbild av hela den virtuella datorn och en programkonsekvent ögonblicksbild som tar en ögonblicksbild vid en viss tidpunkt av programdata på den virtuella datorn. Programkonsekventa ögonblicksbilder använda VSS (Volume Shadow Copy Service) för att säkerställa att programmen är i ett konsekvent tillstånd när ögonblicksbilden tas. Observera att om du aktiverar programkonsekventa ögonblicksbilder så påverkar detta prestanda för program som körs på virtuella källdatorer. Kontrollera att värdet som du anger är mindre än antalet ytterligare återställningspunkter som du konfigurerar.
6. I **Starttid för inledande replikering** anger du när den inledande replikeringen ska börja. Replikeringen sker via Internetbandbredden så du kanske vill schemalägga den utanför kontorstid.
7. I **Kryptera data lagrade på Azure** anger du om du vill kryptera vilande data i Azure-lagring. Klicka sedan på **OK**.

    ![Replikeringsprincip](./media/site-recovery-vmm-to-azure/gs-replication2.png)
8. När du skapar en ny princip associeras den automatiskt med VMM-molnet. Klicka på **OK**. Du kan associera ytterligare VMM-moln (och de virtuella datorerna i dem) med den här replikeringsprincipen i **Inställningar** > **Replikering** > Principnamn > **Associera VMM-moln**.

    ![Replikeringsprincip](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="step-5-capacity-planning"></a>Steg 5: Kapacitetsplanering
Nu när du har konfigurerat den grundläggande infrastrukturen är det dags att planera för kapaciteten och tänka igenom om du behöver ytterligare resurser.

Site Recovery tillhandahåller ett kapacitetsplaneringsverktyg som hjälper dig att allokera rätt resurser för din källmiljö, Site Recovery-komponenterna, nätverk och lagring. Du kan köra planeringsverktyget i snabbläge för att få uppskattningar baserat på det genomsnittliga antalet virtuella datorer, diskar och lagring eller i detaljerat läge där du anger siffrorna på arbetsbelastningsnivå. Innan du börjar:

* Samla in information om replikeringsmiljön, inklusive virtuella datorer, diskar per virtuell dator och lagringsutrymme per disk.
* Beräkna den dagliga förändringstakten (omsättningen) för replikerade data. [Kapacitetsplaneringsverktyget för Hyper-V-replikering](https://www.microsoft.com/download/details.aspx?id=39057) kan hjälpa dig med detta.

1. Klicka på **Ladda ned** för att ladda ned verktyget och köra det. [Läs artikeln](site-recovery-capacity-planner.md) som medföljer verktyget.
2. När du är klar väljer du **Ja** i **Har du kört Capacity Planner**?

   ![Kapacitetsplanering](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Att tänka på när det gäller nätverksbandbredden
Du kan använda kapacitetsplaneringsverktyget för att beräkna den bandbredd som du behöver för replikering (inledande replikering och sedan delta). Om du vill styra bandbreddsanvändningen för en replikering kan du välja mellan några olika alternativ:

* **Begränsa bandbredden**: Hyper-V-trafik som replikeras till en sekundär plats går igenom en specifik Hyper-V-värd. Du kan begränsa bandbredden på värdservern.
* **Justera bandbredden**: Du kan påverka den bandbredd som används för replikering med hjälp av några registernycklar.

#### <a name="throttle-bandwidth"></a>Begränsa bandbredden
1. Öppna snapin-modulen Microsoft Azure Backup MMC på Hyper-V-värdservern. Som standard finns det en genväg till Microsoft Azure Backup på skrivbordet eller i C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Klicka på **Ändra egenskaper** i snapin-modulen.
3. På fliken **Begränsning** väljer du **Aktivera användningsbegränsning för Internetbandbredd för säkerhetskopieringsåtgärder** och ange begränsningarna för arbetstid och övrig tid. Giltiga intervall är från 512 kbit/s till 102 Mbit/s.

    ![Begränsa bandbredden](./media/site-recovery-vmm-to-azure/throttle2.png)

Du kan också ange begränsningar med hjälp av cmdleten [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx). Här är ett exempel:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** anger att ingen begränsning krävs.

#### <a name="influence-network-bandwidth"></a>Påverka nätverkets bandbredd
Registervärdet **UploadThreadsPerVM** styr antalet trådar som används för att överföra data (inledande replikering eller delta) på en disk. Ett högre värde ökar nätverksbandbredden som används för replikering. Registervärdet **DownloadThreadsPerVM** anger antalet trådar som används för att överföra data under en redundansväxling.

1. Gå till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication** i registret.

   * Ändra värdet **UploadThreadsPerVM** (eller skapa nyckeln om den inte finns) om du vill styra trådarna som används för diskreplikering.
   * Ändra värdet **DownloadThreadsPerVM** (eller skapa nyckeln om den inte finns) om du vill styra trådarna som används för redundanstrafik från Azure.
2. Standardvärdet är 4. I ett ”överetablerat” nätverk bör du ändra registernycklarnas standardvärden. Det högsta antalet är 32. Övervaka trafiken för att optimera värdet.

## <a name="step-6-enable-replication"></a>Steg 6: Aktivera replikering

Aktivera replikering på följande sätt:

1. Klicka på **Steg 2: Replikera program** > **Källa**. När du har aktiverat replikering för första gången klickar du på **+Replikera** i valvet för att aktivera replikering för ytterligare datorer.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/enable-replication1.png)
2. På bladet **Källa** väljer du den VMM-server och det moln som Hyper-V-värdarna finns i. Klicka sedan på **OK**.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/enable-replication-source.png)
3. I **Mål** väljer du prenumerationen, distributionsmodellen som används efter en redundansväxling och lagringskontot som du använder för replikerade data.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/enable-replication-target.png)
4. Välj lagringskontot som du vill använda. Om du vill använda ett annat lagringskonto än det som du har kan du [skapa ett](#set-up-an-azure-storage-account). Skapa ett lagringskonto med hjälp av Resource Manager-modellen genom att klicka på **Skapa nytt**. Om du vill skapa ett lagringskonto med hjälp av den klassiska modellen gör du det [i Azure-portalen](../storage/storage-create-storage-account-classic-portal.md). Klicka sedan på **OK**.
5. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator. Om du vill använda ett annat nätverk än det som du har kan du [skapa ett](#set-up-an-azure-network). Skapa ett nätverk med hjälp av Resource Manager-modellen genom att klicka på **Skapa nytt**. Om du vill skapa ett nätverk med den klassiska modellen gör du det på [Azure-portalen](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Välj ett undernät om det behövs. Klicka sedan på **OK**.
6. I **Virtual Machines** > **Välj virtuella datorer** klickar du på och väljer de datorer som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/enable-replication5.png)
7. I **Egenskaper** > **Konfigurera egenskaper** väljer du operativsystemet för de valda virtuella datorerna och operativsystemdisken. Som standard markeras alla diskar på den virtuella datorn för replikering. Du kanske vill undanta diskar från replikeringen och därigenom begränsa användningen av bandbredd för att replikera onödiga data till Azure. Till exempel kanske du inte vill replikera diskar med tillfälliga data eller data som uppdateras varje gång en dator eller ett program startar om (till exempel pagefile.sys eller tempdb för Microsoft SQL Server). Du kan undanta en disk från replikering genom att avmarkera den. Kontrollera att namnet på den virtuella datorn i Azure (målnamnet) uppfyller [kraven för virtuella datorer i Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) och ändra det om det behövs. Klicka sedan på **OK**. Du kan ange ytterligare egenskaper senare.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)
    
    >[!NOTE]
    > 
    > * Endast standarddiskar kan undantas från replikering. Du kan inte utesluta OS-disken och vi rekommenderar att du inte undantar dynamiska diskar. ASR kan inte identifiera vilken virtuell hårddisk som är en standarddisk eller dynamisk disk på den virtuella gästdatorn.  Om beroende dynamiska volymdiskar inte undantas identifieras en skyddad dynamisk disk som felaktig på den virtuella datorn vid redundansväxlingen och data på disken kan inte nås.   
    > * När replikering har aktiverats kan du inte lägga till eller ta bort diskar för replikering. Om du vill lägga till eller undanta en disk måste du inaktivera skyddet för den virtuella datorn och sedan aktivera det igen.
    > * Om du undantar en disk som behövs för att ett program ska fungera efter redundansväxlingen till Azure måste du skapa den manuellt i Azure så att det replikerade programmet kan köras. Du kan också integrera Azure Automation i en återställningsplan för att skapa disken under en redundansväxling av datorn.
    > * Diskar som du skapar manuellt i Azure växlas inte tillbaka igen. Om du till exempel växlar över tre diskar och skapar två direkt på den virtuella datorn i Azure kommer endast tre diskar som växlades över att växlas tillbaka från Azure till Hyper-V. Du kan inte ta med diskar som har skapats manuellt i redundansväxlingar eller omvända replikeringar från Hyper-V till Azure.
    >
    >
    

8. I **Replikeringsinställningar** > **Konfigurera replikeringsinställningar** väljer du den replikeringsprincip som du vill använda för de skyddade virtuella datorerna. Klicka sedan på **OK**. Du kan ändra replikeringsprincipen i **Inställningar** > **Replikeringsprinciper** > Principnamn > **Redigera inställningar**. De ändringar du gör används både för datorer som redan replikeras och för nya datorer.

   ![Aktivera replikering](./media/site-recovery-vmm-to-azure/enable-replication7.png)

Du kan följa förloppet för jobbet **Aktivera skydd** i **Inställningar** > **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.

### <a name="view-and-manage-vm-properties"></a>Visa och hantera egenskaper för virtuella datorer
Vi rekommenderar att du kontrollerar egenskaperna för källdatorn. Kom ihåg att namnet på den virtuella Azure-datorn måste uppfylla [kraven för virtuella datorer i Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Klicka på **Inställningar** > **Skyddade objekt** > **Replikerade objekt** och välj den dator som du vill visa information om.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/vm-essentials.png)
2. I **Egenskaper** kan du visa information om replikering och redundans för den virtuella datorn.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/test-failover2.png)
3. I **Beräkning och nätverk** > **Beräkna egenskaper** kan du ange namnet och storleken på den virtuella Azure-datorn. Ändra namnet så att det uppfyller [kraven för Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) om det behövs. Du kan också visa och ändra information om målnätverket, undernätet och IP-adressen som ska tilldelas den virtuella Azure-datorn. Tänk på följande:

   * Du kan ange IP-måladressen. Om du inte anger någon adress använder den redundansväxlade datorn DHCP. Om du anger en adress som inte är tillgänglig under redundansväxlingen, misslyckas växlingen. Samma mål-IP-adress kan användas för att testa redundans om adressen är tillgänglig i nätverket för redundanstestet.
   * Antalet nätverkskort beror på storleken som du anger för den virtuella måldatorn enligt följande:

     * Om antalet nätverkskort på källdatorn är mindre än eller lika med antalet nätverkskort som tillåts för måldatorns storlek så kommer målet att ha samma antal kort som källan.
     * Om antalet nätverkskort för den virtuella källdatorn överskrider det tillåtna antalet för målstorleken så används den högsta målstorleken.
     * Om en källdator exempelvis har två nätverkskort och måldatorn stöder fyra så kommer måldatorn att ha två kort. Om källdatorn har två nätverkskort men målstorleken endast stöder ett så kommer måldatorn bara att ha ett kort.     
     * Om den virtuella datorn har flera nätverkskort ansluts alla till samma nätverk.

     ![Aktivera replikering](./media/site-recovery-vmm-to-azure/test-failover4.png)
4. I **Diskar** kan du se vilket operativsystem och vilka datadiskar på den virtuella datorn som kommer att replikeras.

## <a name="step-7-test-your-deployment"></a>Steg 7: Testa distributionen
Du kan testa distributionen genom att köra ett redundanstest för en enskild virtuell dator eller genom att köra en återställningsplan som innehåller en eller flera virtuella datorer.

### <a name="prepare-for-failover"></a>Förbereda för redundansväxling
* Om du vill köra ett redundanstest, rekommenderar vi att du skapar ett nytt Azure-nätverk som har isolerats från Azure-driftsnätverket. Detta är standardfunktionen när du skapar ett nytt nätverk i Azure. [Lär dig mer](site-recovery-failover.md#run-a-test-failover) om hur du kör redundanstestning.
* För att få bästa möjliga prestanda när du redundansväxlar till Azure kan du installera Azure-agenten på den skyddade datorn. Det gör att starten går snabbare och underlättar felsökning. Installera [Linux](https://github.com/Azure/WALinuxAgent)- eller [Windows](http://go.microsoft.com/fwlink/?LinkID=394789)-agenten.
* För att testa distributionen fullt ut behöver du en infrastruktur för den replikerade datorn som fungerar som förväntat. Om du vill testa Active Directory och DNS kan du skapa en virtuell dator som en domänkontrollant med DNS och replikera den till Azure med hjälp av Azure Site Recovery. Läs mer i [saker att tänka på när du testar redundans för Active Directory](site-recovery-active-directory.md#test-failover-considerations).
* Om du har tagit bort diskar från replikering kan du behöva skapa dem manuellt i Azure efter redundansväxlingen så att programmet körs som förväntat.
* Tänk på följande om du vill köra en oplanerad redundansväxling i stället för ett redundanstest:

  * Om möjligt bör du stänga av primära datorer innan du kör en oplanerad redundansväxling. På så sätt är du säker på att inte både käll- och replikdatorerna körs samtidigt.
  * När du kör en oplanerad redundansväxling stoppas datareplikeringen från primära datorer så att eventuella dataförändringar (delta) inte överförs när en oplanerad redundansväxling har startat. Om du kör en oplanerad redundansväxling med en återställningsplan körs den dessutom tills den är klar, även om ett fel inträffar.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling
Om du vill ansluta till virtuella Azure-datorer med RDP efter en redundansväxling gör du följande:

**På den lokala datorn före redundansväxlingen**:

* För åtkomst via Internet aktiverar du RDP, ser till att TCP- och UDP-regler lagts till för **Offentliga** och ser till att RDP tillåts i **Windows-brandväggen** -> **Tillåtna appar och funktioner** för alla profiler.
* För åtkomst via en plats-till-plats-anslutning aktiverar du RDP på datorn och ser till att RDP tillåts i **Windows-brandväggen** -> **Tillåtna appar och funktioner** för **Domännätverk** och **Privata nätverk**.
* Installera den [virtuella Azure-datoragenten](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) på den lokala datorn.
* Kontrollera att operativsystemets SAN-princip har angetts till OnlineAll. [Läs mer](https://support.microsoft.com/kb/3031135)
* Stäng av IPSec-tjänsten innan du kör redundansväxlingen.

**På den virtuella Azure-datorn efter redundansväxlingen**:

* Lägg till en offentlig slutpunkt för RDP-protokollet (port 3389) och ange autentiseringsuppgifter för inloggning.
* Se till att det inte finns några domänprinciper som hindrar dig från att ansluta till en virtuell dator med en offentlig adress.
* Prova att ansluta. Om du inte kan ansluta kontrollerar du att den virtuella datorn körs. Mer felsökningstips finns i den här [artikeln](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Om du vill komma åt en virtuell Azure-dator som kör Linux efter en redundansväxling med hjälp av en Secure Shell-klient (ssh) gör du följande:

**På den lokala datorn före redundansväxlingen**:

* Se till att Secure Shell-tjänsten på den virtuella Azure-datorn är inställd så att den startar automatiskt när datorn startar.
* Kontrollera att brandväggsreglerna tillåter en SSH-anslutning till tjänsten.

**På den virtuella Azure-datorn efter redundansväxlingen**:

* Reglerna för nätverkssäkerhetsgrupper på den redundansväxlade virtuella datorn och Azure-undernätet som den är ansluten till måste tillåta inkommande anslutningar till SSH-porten.
* En offentlig slutpunkt måste skapas för att tillåta inkommande anslutningar på SSH-porten (TCP-port 22 som standard).
* Om den virtuella datorn nås via en VPN-anslutning (Express Route eller plats-till-plats-VPN) kan klienten användas för att ansluta direkt till den virtuella datorn via SSH.

### <a name="run-a-test-failover"></a>Köra ett redundanstest
1. Om du vill redundansväxla en enstaka virtuell dator klickar du på den virtuella datorn > **+Testa redundans** i **Inställningar** > **Replikerade objekt**.
2. Om du vill redundansväxla en återställningsplan går du till **Inställningar** > **Återställningsplaner**, högerklickar på planen > **Testa redundans**. Om du vill skapa en återställningsplan [följer du dessa instruktioner](site-recovery-create-recovery-plans.md).
3. I **Testa redundans** väljer du det Azure-nätverk som de virtuella Azure-datorerna ska ansluta till efter redundansväxlingen.
4. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet genom att klicka på den virtuella datorn för att visa dess egenskaper, eller i jobbet **Testa redundans** i **Inställningar** > **Site Recovery-jobb**.
5. När redundansväxlingen når fasen **Slutför testning** gör du följande:

   1. Visa den virtuella replikdatorn på Azure-portalen. Kontrollera att den virtuella datorn startar.
   2. Om du har åtkomst till de virtuella datorerna från ditt lokala nätverk kan du initiera en fjärrskrivbordsanslutning till den virtuella datorn.
   3. Slutför testet genom att klicka på **Slutför testet**.
   4. Klicka på **Kommentarer** om du vill skriva ner och spara observationer från redundanstestningen.
   5. Klicka på **Redundanstestet är klart**. Rensa testmiljön så att den virtuella testdatorn stängs av och tas bort.
   6. I det här skedet raderas alla element eller virtuella datorer som skapades automatiskt av Site Recovery under redundanstestningen. Övriga element som du har skapat för att testa redundansväxlingen tas inte bort.

      > [!NOTE]
      > Om ett redundanstest fortsätter längre än två veckor slutförs det automatiskt.
      >
      >
6. När redundansväxlingen är klar bör du även kunna se Azure-replikdatorn på Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn har rätt storlek, att den är ansluten till rätt nätverk och körs.
7. Om du [förberedde för anslutning efter redundansväxlingen](#prepare-to-connect-to-Azure-VMs-after-failover) bör du kunna ansluta till den virtuella Azure-datorn.

## <a name="monitor-your-deployment"></a>Övervaka distributionen
Så här gör du om du vill övervaka konfigurationsinställningarna, statusen och hälsotillståndet för Site Recovery-distributionen:

1. Klicka på valvnamnet för att få åtkomst till **Essentials**-instrumentpanelen. På den här instrumentpanelen kan du övervaka Site Recovery-jobb, replikeringsstatusen, återställningsplaner, servertillstånd och händelser.  Du kan anpassa **Essentials** och visa de paneler och layouter som är mest användbara för dig, inklusive status för andra Site Recovery- och Backup-valv.

    ![Essentials](./media/site-recovery-vmm-to-azure/essentials.png)
2. På ikonen **Hälsa** kan du övervaka problem på platsservrar (VMM- eller konfigurationsservrar) och de händelser som har uppmärksammats av Site Recovery under de senaste 24 timmarna.
3. Du kan hantera och övervaka replikeringen på panelerna **Replikerade objekt**, **Återställningsplaner** och **Site Recovery-jobb**. Du kan visa mer detaljer om jobb i **Inställningar** > **Jobb** > **Site Recovery-jobb**.

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat och fått igång distributionen kan du [läsa mer](site-recovery-failover.md) om olika typer av redundansväxlingar.



<!--HONumber=Dec16_HO2-->


