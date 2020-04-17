---
title: Migrera datorer som fysisk server till Azure med Azure Migrate.
description: I den här artikeln beskrivs hur du migrerar fysiska datorer till Azure med Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: MVC
ms.openlocfilehash: 1824fc6c7cbc0fd0390770027f4a15d9130139de
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535391"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Migrera datorer som fysiska servrar till Azure

Den här artikeln visar hur du migrerar datorer som fysiska servrar till Azure med hjälp av verktyget Azure Migrate:Server Migration. Migrera maskiner genom att behandla dem som fysiska servrar är användbart i ett antal scenarier:

- Migrera lokala fysiska servrar.
- Migrera virtuella datorer som virtualiseras av plattformar som Xen, KVM.
- Migrera virtuella datorer med Hyper-V eller VMware om du av någon anledning inte kan använda standardmigreringsprocessen för [Hyper-V](tutorial-migrate-hyper-v.md)eller [VMware-migrering.](server-migrate-overview.md)
- Migrera virtuella datorer som körs i privata moln.
- Migrera virtuella datorer som körs i offentliga moln som Amazon Web Services (AWS) eller Google Cloud Platform (GCP).


Den här självstudien är den tredje i en serie som visar hur du bedömer och migrerar fysiska servrar till Azure. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbered att använda Azure med Azure Migrate:Server Migration.
> * Kontrollera kraven för datorer som du vill migrera och förbered en dator för Azure Migrate-replikeringsverktyget som används för att identifiera och migrera datorer till Azure.
> * Lägg till verktyget För migrera azure-migrera server i Azure Migrate-hubben.
> * Ställ in replikeringsverktyget.
> * Installera mobilitetstjänsten på datorer som du vill migrera.
> * Aktivera replikering.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario så att du snabbt kan ställa in ett proof-of-concept. Självstudier använder standardalternativ där det är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i instruktionserna för Azure Migrate.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.


## <a name="prerequisites"></a>Krav

Innan du börjar de här självstudierna bör du:

[Granska](migrate-architecture.md) migreringsarkitekturen.




## <a name="prepare-azure"></a>Förbereda Azure

Förbered Azure för migrering med servermigrering.

**Aktivitet** | **Detaljer**
--- | ---
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto behöver Contributer- eller ägarbehörigheter för att skapa ett projekt.
**Verifiera behörigheter för ditt Azure-konto** | Ditt Azure-konto behöver behörigheter för att skapa en virtuell dator och skriva till en Azure-hanterad disk.


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. Öppna prenumerationen i Azure-portalen och välj **Åtkomstkontroll (IAM)**.
2. Leta reda på det relevanta kontot i **Check access**och klicka på det för att visa behörigheter.
3. Du bör ha **behörigheten Deltagare** eller **Ägare.**
    - Om du just har skapat ett kostnadsfritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerationsägare arbetar du med ägaren för att tilldela rollen.


### <a name="assign-azure-account-permissions"></a>Tilldela Azure-kontobehörigheter

Tilldela rollen Deltagare i den virtuella datorn till Azure-kontot. Detta ger behörigheter till:

    - Skapa en virtuell dator i den valda resursgruppen.
    - Skapa en virtuell dator i det valda virtuella nätverket.
    - Skriv till en Azure-hanterad disk. 

### <a name="create-an-azure-network"></a>Skapa ett Azure-nätverk

[Konfigurera](../virtual-network/manage-virtual-network.md#create-a-virtual-network) ett virtuellt Azure-nätverk (VNet). När du replikerar till Azure skapas och ansluts virtuella Azure-datorer till Det Virtuella Azure-nätverk som du anger när du konfigurerar migreringen.

## <a name="prepare-for-migration"></a>Förbereda för migrering

Om du vill förbereda för den fysiska servermigreringen måste du verifiera de fysiska serverinställningarna och förbereda distributionen av en replikeringsverktyget.

### <a name="check-machine-requirements-for-migration"></a>Kontrollera maskinkrav för migrering

Se till att datorer uppfyller kraven för migrering till Azure. 

> [!NOTE]
> När Azure Migrate:Server-migreringen migrerar fysiska datorer använder du samma replikeringsarkitektur som agentbaserad haveriberedskap i Azure Site Recovery-tjänsten och vissa komponenter delar samma kodbas. En del innehåll kan länka till dokumentationen för webbplatsåterställning.

1. [Kontrollera](migrate-support-matrix-physical-migration.md#physical-server-requirements) fysiska serverkrav.
2. Verifiera att lokala datorer som du replikerar till Azure uppfyller [Kraven på Azure VM](migrate-support-matrix-physical-migration.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Förbereda en maskin för replikeringsverktyget

Azure Migrate:Server Migration använder en replikeringsinstallation för att replikera datorer till Azure. Replikeringsverktyget kör följande komponenter.

- **Konfigurationsserver:** Konfigurationsservern samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- **Processserver:** Processservern fungerar som en replikeringsgateway. Den tar emot replikeringsdata. optimerar den med cachelagring, komprimering och kryptering och skickar den till ett cachelagringskonto i Azure. 

Förbered för distribution av apparater enligt följande:

- Du förbereder en dator som värd för replikeringsverktyget. [Granska](migrate-replication-appliance.md#appliance-requirements) maskinens krav. Apparaten ska inte installeras på en källdator som du vill replikera.
- Replikeringsverktyget använder MySQL. Läs [alternativen](migrate-replication-appliance.md#mysql-installation) för att installera MySQL på apparaten.
- Granska Azure-url:erna som krävs för replikeringsinstallationen för att komma åt [offentliga](migrate-replication-appliance.md#url-access) moln och [myndighetsmoln.](migrate-replication-appliance.md#azure-government-url-access)
- Granska [port] (migrera-replikering-appliance.md#port-access) åtkomstkrav för replikeringsverktyget.

## <a name="add-the-server-migration-tool"></a>Lägga till verktyget Servermigrering

Konfigurera ett Azure Migrate-projekt och lägg sedan till verktyget Servermigrering i det.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **översikten** klickar du på **Utvärdera och migrera servrar**.
4. Under **Upptäck, utvärdera och migrera servrar**klickar du på Utvärdera och migrera **servrar**.

    ![Upptäck och utvärdera servrar](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. I **Discover, assess and migrate servers** (Identifiera, utvärdera och migrera servrar) klickar du på **Lägg till verktyg**.
6. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.
7. I **Projektinformation** anger du projektnamnet och geografin där du vill skapa projektet och klickar på **Nästa**. Granska geografiska områden som stöds för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [statliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

8. I **Välj bedömningsverktyg**väljer du **Hoppa över att lägga till ett bedömningsverktyg för nu** > **Nästa**.
9. I **Välj migreringsverktyg**väljer du **Azure Migrate: Servermigrering** > **Nästa**.
10. I **Review + add tools** (Granska + lägg till verktyg)
granskar du inställningarna och klickar på **Lägg till verktyg**
11. När du har lagt till verktyget visas det i Azure Migrate-projektet > **servrar** > **migreringsverktyg**.

## <a name="set-up-the-replication-appliance"></a>Konfigurera replikeringsverktyget

Det första steget i migreringen är att ställa in replikeringsverktyget. Om du vill konfigurera apparaten för fysisk servermigrering hämtar du installationsfilen för installationen och kör den sedan på [den dator du förberett](#prepare-a-machine-for-the-replication-appliance). När du har installerat installationen registrerar du den med Azure Migrate Server Migration.


### <a name="download-the-replication-appliance-installer"></a>Hämta installationsprogrammet för replikeringsverktyget

1. Klicka på Identifiera i Azure Migrate-projektet **>-servrar**i **Azure Migrate: Servermigrering**. **Discover**

    ![Identifiera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. **I** **Upptäck-datorer** > **Virtualiseras dina datorer?**
4. I **Målregionen**väljer du den Azure-region som du vill migrera datorerna till.
5. Välj **Bekräfta att målregionen för migrering är regionnamn**.
6. Klicka på **Skapa resurser**. Detta skapar ett Azure Site Recovery-valv i bakgrunden.
    - Om du redan har konfigurerat migreringen med Azure Migrate Server Migration kan målalternativet inte konfigureras eftersom resurser har konfigurerats tidigare.
    - Du kan inte ändra målområdet för det här projektet när du har klickat på den här knappen.
    - Alla efterföljande migreringar är till den här regionen.

7. I **Vill du installera en ny replikeringsinstallation?** väljer du Installera en **replikeringsinstallation**.
9. Ladda ned installationsprogrammet och registreringsnyckeln i **Hämta och installera replikeringsverktyget.** Du måste till nyckeln för att registrera apparaten. Nyckeln är giltig i fem dagar efter att den har hämtats.

    ![Hämta leverantör](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Kopiera installationsfilen och nyckelfilen till den Windows Server 2016-dator som du skapade för installationen.
11. Kör installationsfilen för replikeringsverktyget, enligt beskrivningen i nästa procedur. När installationen är klar startas konfigurationsguiden för apparat automatiskt (Du kan också starta guiden manuellt med hjälp av genvägen cspsconfigtool som skapas på apparatens skrivbord). Använd fliken Hantera konton i guiden om du vill lägga till kontoinformation som ska användas för push-installation av mobilitetstjänsten. I den här självstudien installerar vi mobilitetstjänsten manuellt på datorer som ska replikeras, så skapa ett dummy-konto i det här steget och fortsätt.

12. När apparaten har startats om efter installationen väljer du den nya apparaten i **Välj konfigurationsserver**i **Upptäck maskiner**och klickar på Slutför **registrering**. Slutför registreringen utför ett par sista uppgifter för att förbereda replikeringsverktyget.

    ![Slutför registreringen](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Det kan ta lite tid efter slutförandet av registreringen tills identifierade datorer visas i Azure Migrate Server Migration. När virtuella datorer upptäcks ökar antalet **identifierade servrar.**

![Identifierade servrar](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Installera mobilitetstjänsten

På datorer som du vill migrera måste du installera tjänsten Mobilitet. Agentinstallatörerna är tillgängliga på replikeringsverktyget. Du hittar rätt installationsprogrammet och installerar agenten på varje dator som du vill migrera. Gör på följande sätt:

1. Logga in på replikeringsverktyget.
2. Navigera till **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Hitta installationsprogrammet för datorns operativsystem och version. Granska [operativsystem som stöds](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Kopiera installationsfilen till den dator som du vill migrera.
5. Kontrollera att du har den lösenfras som genererades när du distribuerade apparaten.
    - Lagra filen i en tillfällig textfil på datorn.
    - Du kan hämta lösenfrasen på replikeringsverktyget. Kör **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** för att visa den aktuella lösenfrasen från kommandoraden.
    - Återskapa inte lösenfrasen. Detta kommer att bryta anslutningen och du måste registrera om replikeringsverktyget.


### <a name="install-on-windows"></a>Installera i Windows

1. Extrahera innehållet i installationsfilen till en lokal mapp (till exempel C:\Temp) på datorn enligt följande:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Kör installationsprogrammet för mobilitetstjänster:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Registrera agenten med replikeringsverktyget:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Installera på Linux

1. Extrahera innehållet i installationsprogrammet tarball till en lokal mapp (till exempel /tmp/MobSvcInstaller) på maskinen, enligt följande:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Kör installationsskriptet:
    ```
    sudo ./install -r MS -q
    ```
3. Registrera agenten med replikeringsverktyget:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replikera maskiner

Välj nu maskiner för migrering. 

> [!NOTE]
> Du kan replikera upp till 10 maskiner tillsammans. Om du behöver replikera mer replikeras de samtidigt i batchar om 10.

1. I Azure Migrate-projektet > **Servrar**klickar **Azure Migrate: Servermigrering**, på **Replikera**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. I **Replikera**> **källinställningarNa** > **är dina datorer virtualiserade?** väljer du **Inte virtualiserad/Annan**.
3. I Lokalinstallation väljer du namnet på den Azure **Migrate-installation**som du konfigurerar.
4. Välj namnet på replikeringsverktyget i **Process Server.**
6. I **Gästautentiseringsuppgifter**anger du ett dummy-konto som ska användas för att installera mobilitetstjänsten manuellt (push-installation stöds inte i Fysisk). Klicka sedan på **Nästa: Virtuella datorer**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. I **Inställningar för virtuell dator**i Importera **No, I'll specify the migration settings manually** **migrering från en utvärdering?**
8. Kontrollera varje virtuell dator som du vill migrera. Klicka sedan på **Nästa: Målinställningar**.

    ![Välj virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. I **Målinställningar** väljer du prenumeration och den målregion som du vill migrera till. Ange sedan den resursgrupp där du vill att de virtuella Azure-datorerna ska finnas efter migreringen.
10. I **Virtuellt nätverk** väljer du det Azure VNet/undernät som de virtuella Azure-datorerna ska anslutas till efter migreringen.
11. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka sedan på **Nästa**.

    ![Målinställningar](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. I **Compute** granskar du namnet på den virtuella datorn, storlek, disktyp för operativsystemet och tillgänglighetsuppsättningen. De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **VM-storlek:** Som standard väljer Azure Migrate Server Migration en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**. 
    - **OS-disk:** Ange OS-disken (start) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns. 
    - **Tillgänglighetsuppsättning**: Om den virtuella datorn ska vara i en Azure-tillgänglighetsuppsättning efter migreringen anger du uppsättningen. Uppsättningen måste finnas i målets resursgrupp som du anger för migreringen.

    ![Beräkningsinställningar](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. I **Diskar**anger du om VM-diskarna ska replikeras till Azure och väljer disktypen (standard-SSD/HDD eller premiumhanterade diskar) i Azure. Klicka sedan på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar kommer de inte att synas i den virtuella Azure-datorn efter migreringen. 

    ![Diskinställningar](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. I **Granska och starta replikering** kontrollerar du inställningarna och klickar på **Replikera** för att påbörja den första replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningarna när som helst innan replikeringen startar, **Hantera** > **replikeringsdatorer**. Det går inte att ändra inställningarna efter att replikeringen har startat.



## <a name="track-and-monitor"></a>Spåra och övervaka

- När du klickar på **Replikera** börjar ett Start Replication-jobb. 
- När startreplikeringsjobbet har slutförts börjar datorerna sin första replikering till Azure.
- När den första replikeringen är klar börjar deltareplikeringen. Inkrementella ändringar på lokala diskar replikeras regelbundet till replikdiskarna i Azure.


Du kan spåra jobbstatus i portalmeddelandena.

Du kan övervaka replikeringsstatus genom att klicka på **Replikera servrar** i **Azure Migrate: Servermigrering**.
![Övervaka replikering](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Kör en testmigrering


När deltareplikeringen börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator, innan du migrerar den.

- Köra en testmigrering kontrollerar att migreringen fungerar som förväntat, utan att påverka de lokala datorerna, som förblir i drift, och fortsätta replikera. 
- Testmigrering simulerar migreringen genom att skapa en Virtuell Azure-dator med replikerade data (migrera vanligtvis till ett icke-produktions-virtuellt nätverk i din Azure-prenumeration).
- Du kan använda det replikerade testet Azure VM för att validera migreringen, utföra apptestning och åtgärda eventuella problem innan fullständig migrering.

Gör en testmigrering på följande sätt:


1. I **Migreringsmål** > **Servrar** > Azure**Migrate: Servermigrering**, klicka på Testa **migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har verifierat att testmigrering fungerar som förväntat kan du migrera de lokala datorerna.

1. Klicka på **Replikera servrar**i Azure Migrate-projektet > **Servrar** > **Azure Migrate: Servermigrering**.

    ![Servrarna replikeras](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3.  > Välj **Migrate** >  **Ja****OK**i Migrera Stäng av virtuella datorer och utför en**planerad migrering utan dataförlust**.
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**

    För migrering av fysiska servrar är rekommendationen att sänka programmet som en del av migreringsfönstret (låt inte programmen acceptera några anslutningar) och sedan initiera migreringen (servern måste fortsätta att köras, så att återstående ändringar kan synkroniseras) innan migreringen är klar.

4. Ett migreringsjobb startas för den virtuella datorn. Spåra jobbet i Azure-meddelanden.
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan **Virtual Machines**.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är klar högerklickar du på den virtuella datorn > **Stoppa migreringen**. Det här gör följande:
    - Stoppar replikeringen för den lokala datorn.
    - Tar bort datorn från antalet **replikerande servrar** i Azure Migrate: Servermigrering.
    - Rensar replikeringstillståndsinformation för datorn.
2. Installera Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) eller [Linux-agenten](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) på de migrerade datorerna.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Minska över trafiken till den migrerade Azure VM-instansen.
6. Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
7. Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
8. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 

## <a name="post-migration-best-practices"></a>Bästa metoder för efter migreringen

- För ökat skydd:
    - Skydda data genom att säkerhetskopiera virtuella Azure-datorer med Azure Backup-tjänsten. [Läs mer](../backup/quick-backup-vm-portal.md).
    - Håll arbetsbelastningar i körning och kontinuerligt tillgängliga genom att replikera virtuella Azure-datorer till en sekundär region med Site Recovery. [Läs mer](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- För ökad säkerhet:
    - Lås och begränsa åtkomsten för inkommande trafik med [Azure Security Center - Just in time administration](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Begränsa nätverkstrafik till hanteringsslutpunkter med [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Distribuera [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) för att säkra diskar och skydda data från stöld och obehörig åtkomst.
    - Läs mer om [ att skydda IaaS-resurser](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) och besök [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- För övervakning och hantering:
    - Överväg att distribuera [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) för att övervaka användning och utgifter.


## <a name="next-steps"></a>Nästa steg

Undersök [molnmigreringsresan](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud Adoption Framework.
