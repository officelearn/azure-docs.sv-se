---
title: Migrera lokala fysiska datorer eller virtualiserade datorer till Azure med Azure Migrate servermigrering | Microsoft Docs
description: Den här artikeln beskriver hur du migrerar lokala fysiska datorer eller virtualiserade datorer till Azure med Azure Migrate servermigrering.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2554501ecf6d4ef115e3283fa635c24510b8c797
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249594"
---
# <a name="migrate-physical-or-virtualized-servers-to-azure"></a>Migrera fysiska eller virtualiserade servrar till Azure 

Den här artikeln visar hur du migrerar fysiska eller virtualiserade servrar till Azure. Migreringsverktyget Azure Migrate Server erbjuder migrering av fysiska och virtuella servrar, med hjälp av agent-baserad replikering. Med det här verktyget kan migrerar du ett stort antal datorer till Azure:

- Migrera lokala fysiska servrar.
- Migrera virtuella datorer virtualiseras av plattformar, till exempel Xen, KVM.
- Migrera Hyper-V eller VMware-datorer. Detta är användbart om du av någon anledning det inte går att använda standardmigrering flödet som Azure Migrate servermigrering erbjuder för [Hyper-V](tutorial-migrate-hyper-v.md), [VMware utan Agent](tutorial-migrate-vmware.md) migrering, eller [VMware agentbaserad](tutorial-migrate-vmware-agent.md) migrering.
- Migrera virtuella datorer som körs i privata moln.
- Migrera virtuella datorer som körs i offentliga moln, till exempel Amazon Web Services (AWS) eller Google Cloud Platform (GCP).


[Azure Migrate](migrate-services-overview.md) ger en central knutpunkt för att spåra identifiering, bedömning och migrering av dina lokala appar och arbetsbelastningar och virtuella datorer i molnet instanser till Azure. Hubben innehåller Azure Migrate verktyg för bedömning och migrering, samt tredjepartsprogram oberoende programvaruleverantörer (ISV)-erbjudanden.


I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Förbered Azure för migrering med verktyget Azure Migrate servermigrering.
> * Kontrollera kraven för datorer som du vill migrera och Förbered en dator för Azure Migrate replikering produkten som används för att identifiera och migrera datorer till Azure.
> * Lägg till Migreringsverktyget Azure Migrate Server i Azure Migrate-hubben.
> * Ställ in installationen för replikering.
> * Installera mobilitetstjänsten på datorer som du vill migrera.
> * Aktivera replikering.
> * Kör en testmigrering för att kontrollera att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

> [!NOTE]
> Självstudiekurser visar den enklaste distribution sökvägen för ett scenario så att du snabbt kan ställa in proof-of-concept. Använd standardalternativen där det är möjligt självstudier och visas inte alla möjliga inställningar och sökvägar. Granska de detaljerade förklaringar för Azure Migrate detaljerade anvisningar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar de här självstudierna bör du:

1. [Granska](migrate-architecture.md) migreringsarkitektur för.
2. Se till att ditt Azure-konto har tilldelats rollen virtuell Datordeltagare så att du har behörighet att:

    - Skapa en virtuell dator i den valda resursgruppen.
    - Skapa en virtuell dator i det valda virtuella nätverket.
    - Skriva till en Azure hanterade diskar. 

3. [Konfigurera ett virtuellt Azure nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). När du replikerar till Azure, Azure virtuella datorer skapas, och ansluten till ett virtuellt Azure nätverk du anger när du ställer in migreringen.


## <a name="prepare-azure"></a>Förbereda Azure

Konfigurera Azure-behörigheter innan du kan migrera med Azure Migrate servermigrering.

- **Skapa ett projekt**: Azure-kontot måste ha behörighet att skapa ett Azure Migrate-projekt. 
- **Registrera Azure Migrate replikering installationen**: Replikering installationen skapar och registrerar en app i Azure Active Directory i ditt Azure-konto. Delegera behörigheter för den här.
- **Skapa Key Vault**: För att migrera datorer, skapar Azure Migrate ett Nyckelvalv i resursgruppen, som ska hantera åtkomstnycklar till lagringskontot replikering i din prenumeration. Om du vill skapa valvet behöver du behörigheter för tilldelning på resursgruppen där Azure Migrate-projektet finns. 


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter att skapa projekt

1. Ange prenumerationen i Azure-portalen och välj **åtkomstkontroll (IAM)** .
2. I **Kontrollera åtkomst**, hitta det aktuella kontot och klicka om du vill visa behörigheter.
3. Du bör ha **deltagare** eller **ägare** behörigheter.
    - Om du just har skapat ett kostnadsfritt Azure-konto är du ägare av din prenumeration.
    - Om du inte är prenumerationsägaren, tillsammans med ägare att tilldela rollen.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Tilldela behörighet för att registrera installationen för replikering

Delegera behörigheter för Azure Migrate servermigrering att skapa och registrera en Azure AD-app i ditt konto för agent-baserad migrering. Du kan tilldela behörigheter med hjälp av någon av följande metoder:

- En klient/global administratör kan ge behörigheter till användare i klienten, skapa och registrera Azure AD-appar.
- En klient/global administratör kan tilldela rollen programutvecklare (som har behörigheter) till kontot.

Det är värt som:

- Apparna har inte några andra åtkomstbehörigheter för prenumerationen än de som beskrivs ovan.
- Du behöver bara dessa behörigheter när du registrerar en ny installation av replikering. Du kan ta bort behörigheter när installationen för replikering har konfigurerats. 


#### <a name="grant-account-permissions"></a>Bevilja behörighet

Klient/global administratör kan bevilja behörigheter på följande sätt

1. I Azure AD-klient/globala administratören bör gå till **Azure Active Directory** > **användare** > **användarinställningar**.
2. Administratören bör ange **appregistreringar** till **Ja**.

    ![Azure AD-behörigheter](./media/tutorial-migrate-physical-virtual-machines/aad.png)

> [!NOTE]
> Det här är en standardinställning som inte är känsliga. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Tilldela programutvecklare roll 

Klient/global administratör kan tilldela rollen programutvecklare till ett konto. [Läs mer](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Tilldela behörigheter för att skapa Key Vault

Tilldela behörigheter för tilldelning på resursgruppen där Azure Migrate-projektet finns, enligt följande:

1. I resursgrupp i Azure portal, Välj **åtkomstkontroll (IAM)** .
2. I **Kontrollera åtkomst**, hitta det aktuella kontot och klicka om du vill visa behörigheter. Du behöver **ägare** (eller **deltagare** och **administratör för användaråtkomst**) behörigheter.
3. Om du inte har behörigheterna som krävs, begär du dem från resursgruppägare. 

## <a name="prepare-for-migration"></a>Förbereda för migrering

### <a name="check-machine-requirements-for-migration"></a>Kontrollera kraven för datorn för migrering

Se till att datorer som uppfyller kraven för migrering till Azure. 

> [!NOTE]
> Agentbaserad migrering med Azure Migrate-servermigrering är baserad på funktioner i Azure Site Recovery-tjänsten. Vissa krav länka till dokumentation om Site Recovery.

1. [Kontrollera](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) VMware-serverkraven.
2. [Kontrollera](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) VM stödja krav för migrering.
3. Kontrollera inställningarna för virtuella datorer. Lokala virtuella datorer som du replikerar till Azure måste uppfylla [kraven för Azure virtuella datorer](migrate-support-matrix-vmware.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Förbered en dator för installation för replikering

Azure Migrate servermigrering använder en replikering-installation för att replikera datorer till Azure. Replikering installationen körs följande komponenter.

- **Konfigurationsservern**: Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
- **Processerver**: Processervern fungerar som en replikeringsgateway. Den tar emot replikeringsdata; optimerar dem med cachelagring, komprimering och kryptering och skickar det till ett cachelagringskonto i Azure. 

Innan du börjar måste du förbereda en Windows Server 2016-dator som värd för installationen för replikering. Datorn måste uppfylla [dessa krav](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements).


## <a name="add-the-azure-migrate-server-migration-tool"></a>Lägg till verktyget Azure Migrate-servermigrering

Ställ in ett Azure Migrate-projekt och Lägg sedan till Migreringsverktyget Azure Migrate Server till den.

1. I Azure portal > **alla tjänster**, Sök efter **Azure Migrate**.
2. Under **Services**väljer **Azure Migrate**.
3. I **översikt**, klickar du på **utvärdera och migrera servrar för**.
4. Under **Upptäck, utvärdera och migrera servrar för**, klickar du på **utvärdera och migrera servrar för**.

    ![Identifiera och utvärdera servrar](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. I **Upptäck, utvärdera och migrera servrar för**, klickar du på **lägga till verktyg**.
6. I **migrera projektet**, Välj din Azure-prenumeration och skapa en resursgrupp om du inte har något.
7. I **projektinformation**, ange projektnamn och geografisk plats där du vill skapa projektet och klicka på **nästa**

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    Du kan skapa ett Azure Migrate-projekt i någon av dessa områden.

    **Geografi** | **Region**
    --- | ---
    Asien | Sydostasien
    Europa | Europa, norra eller Europa, västra
    USA | USA, östra eller USA, västra centrala

    Den angivna geografiska platsen för projektet används bara för att lagra de metadata som samlats in från lokala virtuella datorer. Du kan välja en målregion för den faktiska migreringen.
8. I **väljer utvärderingsvertyget**väljer **hoppa över att lägga till en utvärderingsvertyget för tillfället** > **nästa**.
9. I **väljer Migreringsverktyget**väljer **Azure Migrate: Servermigrering** > **nästa**.
10. I **granska + Lägg till verktyg**, granskar du inställningarna och klicka på **lägga till verktyg**
11. När du lägger till verktyget, som det visas i Azure Migrate-projektet > **servrar** > **Migreringsverktyg**.

## <a name="set-up-the-replication-appliance"></a>Konfigurera replikering-installation

Det första steget i migrering är att konfigurera replikering-installation. Du hämta installationsfilen för installationen och kör den på den [datorn som du har förberett](#prepare-a-machine-for-the-replication-appliance). När du har installerat enheten som registrera du den med Azure Migrate servermigrering.


### <a name="download-the-replication-appliance-installer"></a>Ladda ned installationsprogrammet till installationen replikering

1. I Azure Migrate-projektet > **servrar**i ***Azure Migrate: Servermigrering**, klickar du på **identifiera**.

    ![Identifiera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. I **identifiera datorer** > **är dina datorer virtualiserade?** , klickar du på **inte virtualiserad/övrigt**.
4. I **målregionen**, Välj den Azure-region som du vill migrera datorerna.
5. Välj **bekräftar du att målregionen för migrering är Regionsnamn**.
6. Klicka på **skapa resurser**. Detta skapar ett Azure Site Recovery-valv i bakgrunden.
    - Om du redan har konfigurerat migrering med Azure Migrate servermigrering kan alternativet målet inte konfigureras, eftersom resurser har ställts in tidigare.
    - Du kan inte ändra målregion för det här projektet när du klickar på den här knappen.
    - Alla efterföljande migreringar är till den här regionen.

7. I **vill du installera en ny installation av replikering?** väljer **installera en replikering installation**.
9. I **ladda ned och installera installation replikeringsprogram**, ladda ned installationsprogrammet för installation och nyckel för tjänstregistrering. Du behöver till nyckeln för att registrera installationen. Nyckeln är giltig i fem dagar efter den hämtas.

    ![Ladda ned providern](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Kopiera installationsfilen för installation och nyckelfilen till den Windows Server 2016-dator du skapade för installationen.
11. Kör installationsfilen för replikering installation som beskrivs i nästa procedur.
12. När installationen har startats om efter installationen, i **identifiera datorer**, Välj den nya installationen i **Välj konfigurationsservern**, och klicka på **Slutför registreringen**. Slutför registreringen utför några slutliga uppgifter för att förbereda installationen för replikering.

    ![Slutför registreringen](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Det kan ta upp till 15 minuter efter Slutför registreringen tills identifierade datorer visas i Azure Migrate servermigrering. Eftersom virtuella datorer har identifierats i **identifierade servrar** räkna ökar.

![Identifierade servrar](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Installera mobilitetstjänsten

Du måste installera mobilitetstjänstagenten på datorer som du vill migrera. Installationsprogram för agenten är tillgängliga på installationen för replikering. Du hittar rätt installationsprogrammet och installera agenten på varje dator som du vill migrera. Gör detta på följande sätt:

1. Logga in på installationen för replikering.
2. Gå till **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Hitta installationsprogrammet för datorns operativsystem och version. Granska [operativsystem som stöds](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Kopiera installationsfilen till den dator du vill migrera.
5. Se till att du har lösenfrasen som skapades när du distribuerade installationen.
    - Store filen i en tillfällig textfil på datorn.
    - Du kan hämta lösenfrasen på replikering-installationen. Från kommandoraden, kör **C:\ProgramData\ASR\svsystems\bin\genpassphrase.exe - v** att visa aktuell lösenfras.
    - Skapa inte nya lösenfrasen. Detta bryter anslutningen och du måste registrera om installationen för replikering.


### <a name="install-on-windows"></a>Installera i Windows

1. Extrahera innehållet i installer-fil till en lokal mapp (exempelvis C:\Temp) på datorn enligt följande:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Kör Mobilitetstjänstens installationsprogram:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Registrera agenten med replikering installation:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Installera på Linux

1. Extrahera innehållet i installationsprogrammet tarball till en lokal mapp (till exempel /tmp/MobSvcInstaller) på datorn enligt följande:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Kör installationsprogrammet för skriptet:
    ```
    sudo ./install -r MS -q
    ```
3. Registrera agenten med replikering installation:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replikera virtuella datorer

1. I Azure Migrate-projektet > **servrar**, **Azure Migrate: Servermigrering**, klickar du på **replikera**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. I **replikera**, > **datakällans inställningar** > **är dina datorer virtualiserade?** väljer **Ja, med VMware vSphere**.
3. I **lokala installationen**, Välj namnet på den Azure Migrate-installationen som du har konfigurerat.
4. I **vCenter-servern**, ange namnet på den vCenter-server som hanterar de virtuella datorerna eller vSphere-servern där de virtuella datorerna finns.
5. I **Processervern**, Välj namnet på installationen för replikering.
6. I **gäst autentiseringsuppgifter**, du anger ett VM-administratörskonto som ska användas för push-installation av mobilitetstjänsten. I den här självstudien installerar vi mobilitetstjänsten manuellt, så du kan lägga till några dummy-kontot. Klicka sedan på **nästa: Virtuella datorer**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. I **virtuella datorer**i **importera inställningar från en utvärdering?** , lämnar du standardinställningen **Nej, jag anger migreringsinställningarna manuellt**.
8. Kontrollera varje virtuell dator som du vill migrera. Klicka sedan på **nästa: Rikta inställningar**.

    ![Välj virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. I **rikta inställningar**, Välj prenumerationen och målregion som du migrerar och ange den resursgrupp där virtuella Azure-datorer kommer att finnas efter migreringen.
10. I **virtuellt nätverk**, Välj Azure virtuellt nätverk/undernät som virtuella Azure-datorer ska anslutas efter migreringen.
11. I **Azure Hybrid-förmånen**:

    - Välj **nr** om du inte vill använda Azure Hybrid-förmånen. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas med aktiv Software Assurance eller Windows Server-prenumerationer och du vill använda förmånen med de datorer som du migrerar. Klicka sedan på **Nästa**.

    ![Målinställningar](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. I **Compute**, granska virtuella datorns namn, storlek, typ av OS-disk och tillgänglighetsuppsättning. Virtuella datorer måste överensstämma med [krav för Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).

    - **VM-storlek**: Som standard hämtar Azure Migrate servermigrering en storlek baserat på matchande poster i Azure-prenumeration. Du kan också välja en manuell storlek i **Azure VM-storlek**. 
    - **OS-disken**: Ange operativsystemdisken (Start) för den virtuella datorn. OS-disken är en disk som har operativsystemet startprogrammet och installationsprogrammet. 
    - **Tillgänglighetsuppsättning**: Om den virtuella datorn ska vara i en Azure tillgänglighetsuppsättning efter migreringen, ange en uppsättning. Uppsättningen måste finnas i målresursgruppen som du anger för migreringen.

    ![Compute-inställningar](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. I **diskar**, anger du om VM-diskarna ska replikeras till Azure och välj disktyp (standard SSD/HDD- eller premium hanterade diskar) i Azure. Klicka sedan på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar, inte på den virtuella Azure-datorn efter migreringen. 

    ![Diskinställningar](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. I **granska och startar replikeringen**, granskar du inställningarna och klicka på **replikera** att starta den inledande replikeringen för servrar.

> [!NOTE]
> Du kan uppdatera replikeringsinställningarna som helst innan replikeringen startar **hantera** > **datorer replikeras**. Inställningarna kan inte ändras när replikeringen startar.



## <a name="track-and-monitor"></a>Spåra och övervaka

- När du klickar på **replikera** ett jobb startar replikeringen börjar. 
- När starta replikeringen har slutförts startas datorerna sina inledande replikering till Azure.
- När den inledande replikeringen är klar startar deltareplikeringen. Inkrementella ändringar till lokala diskar replikeras med jämna mellanrum till replikeringsdiskar i Azure.


Du kan spåra status för återställningsjobb i portalen meddelanden.

Du kan övervaka replikeringsstatus genom att klicka på **replikera servrar** i **Azure Migrate: Servermigrering**.
![Övervakare för replikering](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Kör en testmigrering


När deltareplikeringen börjar kör du en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- Kör en testmigrering kontrollerar att migreringen fungerar som förväntat, utan att påverka de lokala datorerna som fortsätta att fungera och fortsätta med replikeringen. 
- Testmigrering simulerar migreringen genom att skapa en Azure-dator med hjälp av replikerade data (vanligtvis migrera till ett icke-produktion virtuellt nätverk i Azure-prenumerationen).
- Du kan använda testet replikerade virtuella Azure-datorer att validera migreringen testa appen och åtgärda eventuella problem innan fullständig migrering.

Gör en testmigrering på följande sätt:


1. I **migrering mål** > **servrar** > **Azure Migrate: Servermigrering**, klickar du på **Test migrera servrar**.

     ![Testa migrerade servrar](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Högerklicka på den virtuella datorn för att testa och klicka på **Test migrera**.

    ![Testmigrering](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. I **testmigrering**, Välj den virtuella Azure-nätverket där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett icke-produktion virtuellt nätverk.
4. Den **testa migrering** jobbet startar. Övervaka jobb i portalen meddelanden.
5. När migreringen är klar kan du visa den migrerade virtuella Azure-datorer i **virtuella datorer** i Azure-portalen. Namnet på datorn har ett suffix **-Test**.
6. När testet är klart, högerklickar du på Azure VM i **datorer replikeras**, och klicka på **Rensa testmigrering**.

    ![Rensa migrering](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigreringen fungerar som förväntat, kan du migrera lokala datorer.

1. I Azure Migrate-projektet > **servrar** > **Azure Migrate: Servermigrering**, klickar du på **replikera servrar**.

    ![Replikera servrar](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. I **datorer replikeras**, högerklicka på den virtuella datorn > **migrera**.
3. I **migrera** > **stänga av virtuella datorer och utföra en planerad migrering utan att förlora data**väljer **Ja** > **OK** .
    - Som standard Azure Migrate stänger av den lokala virtuella datorn och kör en på begäran-replikering om du vill synkronisera alla VM-ändringar som gjorts sedan den senaste replikeringen som utfördes. Detta säkerställer att inga data går förlorade.
    - Om du inte vill att stänga av den virtuella datorn, Välj **Nej**
4. Det startar ett migreringsjobb för den virtuella datorn. Spåra jobbet i Azure-aviseringar.
5. När jobbet har slutförts kan du visa och hantera den virtuella datorn från den **virtuella datorer** sidan.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är klar högerklickar du på den virtuella datorn > **stoppa migrering**. Detta stoppar replikeringen för den lokala datorn och rensar replikering statusinformation för den virtuella datorn.
2. Installera Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) eller [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) agenten på de migrerade datorerna.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Klipp ut över trafik till de migrerade Azure VM-instansen.
6. Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
7. Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
8. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 

## <a name="post-migration-best-practices"></a>Metodtips för efter migrering

- För ökat skydd:
    - Skydda data genom att säkerhetskopiera virtuella Azure-datorer med Azure Backup-tjänsten. [Läs mer](../backup/quick-backup-vm-portal.md).
    - Håll arbetsbelastningar i körning och kontinuerligt tillgängliga genom att replikera virtuella Azure-datorer till en sekundär region med Site Recovery. [Läs mer](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- För ökad säkerhet:
    - Låsa och begränsa åtkomsten för inkommande trafik med [Azure Security Center – Just-in-time-administration](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Begränsa nätverkstrafik till hanteringsslutpunkter med [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Distribuera [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) för att säkra diskar och skydda data från stöld och obehörig åtkomst.
    - Läs mer om [ att skydda IaaS-resurser](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) och besök [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- För övervakning och hantering:
    - Överväg att distribuera [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) för att övervaka användning och utgifter.


## <a name="next-steps"></a>Nästa steg

Undersöka den [migrering molnresa](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud Adoption Framework.
