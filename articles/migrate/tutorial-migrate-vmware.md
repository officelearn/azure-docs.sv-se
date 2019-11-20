---
title: Migrera virtuella VMware-datorer utan agent Azure Migrate Server migrering
description: Lär dig hur du kör en agent lös migrering av virtuella VMware-datorer med Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2b4aad83abc92170df5a7e7cfa7f7751b49b3424
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196405"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrera virtuella VMware-datorer till Azure (utan agent)

Den här artikeln visar hur du migrerar lokala virtuella VMware-datorer till Azure med hjälp av återställning utan agent med Migreringsverktyg för Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och AWS/GCP VM-instanser till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV).

Den här självstudien är den tredje i en serie som visar hur du bedömer och migrerar virtuella VMware-datorer till Azure med hjälp av Azure Migrate Server bedömning och migrering. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Förbered virtuella datorer för migrering.
> * Lägg till migrerings verktyget för Azure migration Server.
> * Identifiera virtuella datorer som du vill migrera.
> * Starta replikering av virtuella datorer.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering av virtuella datorer.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="migration-methods"></a>Metoder för migrering

Du kan migrera virtuella VMware-datorer till Azure med hjälp av verktyget för migrering av Azure Migrate Server. Det här verktyget erbjuder ett par alternativ för migrering av virtuella VMware-datorer:

- Migrering med hjälp av replikering utan agent. Migrera virtuella datorer utan att behöva installera något på dem.
- Migrering med en agent för replikering. Installera en agent på den virtuella datorn för replikering.

Om du vill bestämma om du vill använda en agent lös eller agent-baserad migrering kan du läsa följande artiklar:

- [Lär dig hur](server-migrate-overview.md) en agent utan migrering fungerar och [Jämför metoder för migrering](server-migrate-overview.md#compare-migration-methods).
- [Läs den här artikeln](tutorial-migrate-vmware-agent.md) om du vill använda den agentbaserade metoden.

## <a name="prerequisites"></a>Krav

Innan du börjar de här självstudierna bör du:

1. [Slutför den första självstudien](tutorial-prepare-vmware.md) i den här serien för att konfigurera Azure och VMware för migrering. I den här självstudien måste du särskilt:
    - [Förbered Azure](tutorial-prepare-vmware.md#prepare-azure) för migrering.
    - [Förbered den lokala miljön](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) för migrering.
    
2. Vi rekommenderar att du provar att utvärdera virtuella VMware-datorer med Azure Migrate Server-utvärdering innan du migrerar dem till Azure. Om du vill konfigurera utvärderingen [Slutför du den andra självstudien](tutorial-assess-vmware.md) i den här serien. Om du inte vill utvärdera de virtuella datorerna kan du hoppa över den här självstudien. Även om vi rekommenderar att du testar en utvärdering, men du inte behöver köra en utvärdering innan du försöker utföra en migrering.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Lägg till verktyget för migrering av Azure Migrate Server

Om du inte följer den andra självstudien för att utvärdera virtuella VMware-datorer måste du [följa de här anvisningarna](how-to-add-tool-first-time.md) för att skapa ett Azure Migrate-projekt och välja verktyget för migrering av Azure Migrate Server. 

Om du följde den andra själv studie kursen och redan har ett Azure Migrate-projekt konfigurerat lägger du till Migreringsverktyg för Azure Migrate server på följande sätt:

1. Klicka på **Översikt**i Azure Migrate projektet. 
2. I **identifiera, utvärdera**och migrera servrar klickar du på **utvärdera och migrera servrar**.

     ![Utvärdera och migrera servrar](./media/tutorial-migrate-vmware/assess-migrate.png)

3. I **Migreringsverktyg**väljer **du klicka här för att lägga till ett Migreringsverktyg när du är redo att migrera**.

    ![Välj ett verktyg](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. I listan verktyg väljer du **Azure Migrate:** verktyget Migreringsverktyg > **Lägg till**

    ![Migreringsverktyg för Server](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurera Azure Migrate-enheten

Azure Migrate Server-migrering kör en förenklad VMware VM-enhet. Installationen utför VM-identifiering och skickar VM-metadata och prestanda data till Azure Migrate Server-migrering. Samma apparat används också av verktyget för Azure Migrate Server bedömning.

Om du följde den andra självstudien för att utvärdera virtuella VMware-datorer, har du redan konfigurerat installationen under den självstudien. Om du inte följer den själv studie kursen måste du konfigurera installationen nu. Gör så här: 

- Hämta en mall för en embryo-fil och importera den till vCenter Server.
- Skapa installationen och kontrol lera att den kan ansluta till Azure Migrate Server utvärdering. 
- Konfigurera enheten för första gången och registrera den med Azure Migrate-projektet.

Följ anvisningarna i [den här artikeln](how-to-set-up-appliance-vmware.md) för att konfigurera installationen.


## <a name="prepare-vms-for-migration"></a>Förbereda virtuella datorer för migrering

Azure Migrate kräver vissa VM-ändringar för att säkerställa att virtuella datorer kan migreras till Azure.

- För vissa operativ system gör Azure Migrate dessa ändringar automatiskt. [Läs mer](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)
- Om du migrerar en virtuell dator som inte har något av dessa operativ system, följer du anvisningarna för att förbereda den virtuella datorn.
- Det är viktigt att du gör dessa ändringar innan du påbörjar migrering. Om du migrerar den virtuella datorn innan du gör ändringen kanske den virtuella datorn inte startar i Azure.
- Konfigurations ändringar som du gör på lokala virtuella datorer replikeras till Azure när replikeringen för den virtuella datorn har Aktiver ATS. Säkerställ att ändringarna replikeras genom att kontrol lera att återställnings punkten du migrerar till är senare än den tid då konfigurations ändringarna gjordes lokalt.


### <a name="prepare-windows-server-vms"></a>Förbereda virtuella Windows Server-datorer

**Åtgärd** | **Information** | **Problemlösning**
--- | --- | ---
Se till att Windows-volymer i Azure VM använder samma enhets beteckningar som den lokala virtuella datorn. | Konfigurera SAN-principen som online alla. | 1. Logga in på den virtuella datorn med ett administratörs konto och öppna ett kommando fönster.<br/> 2. Skriv DiskPart för att **köra DiskPart-** verktyget.<br/> 3. Ange **San-princip = OnlineAll**<br/> 4. Skriv exit för att lämna DiskPart och Stäng kommando tolken.
Aktivera Azure Serial Access-konsolen för den virtuella Azure-datorn | Detta hjälper till med fel sökning. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn startas med disk avbildningen och motsvarar en omstart för den nya virtuella datorn. | Följ [dessa instruktioner](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) för att aktivera.
Installera Hyper-V-gäst integrering | Om du migrerar datorer som kör Windows Server 2003 installerar du Hyper-V-gäst integrerings tjänster på den virtuella datorns operativ system. | [Läs mer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Fjärrskrivbord | Aktivera fjärr skrivbord på den virtuella datorn och kontrol lera att Windows-brandväggen inte blockerar åtkomst till fjärr skrivbord på någon nätverks profil. | [Läs mer](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Förbered virtuella Linux-datorer

**Åtgärd** | **Information** 
--- | --- | ---
Installera Hyper-V Linux Integration Services | De flesta nya versioner av Linux-distributioner har detta inkluderat som standard.
Återskapa Linux init-avbildningen så att den innehåller de nödvändiga Hyper-V-drivrutinerna | Detta säkerställer att den virtuella datorn kommer att starta i Azure och krävs endast för vissa distributioner.
Aktivera loggning av Azures serie konsol | Detta hjälper till med fel sökning. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn startas med disk avbildningen och motsvarar en omstart för den nya virtuella datorn.<br/> Följ [dessa instruktioner](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) för att aktivera.
Uppdatera enhets mappnings fil | Uppdatera enhets mappnings filen som har enhets namnet till volym associationer för att använda beständiga enhets identifierare
Uppdatera fstab-poster | Uppdatera poster för att använda beständiga volym identifierare.
Ta bort udev-regel | Ta bort alla udev-regler som reserverar gränssnitts namn baserat på Mac-adress osv.
Uppdatera nätverks gränssnitt | Uppdatera nätverks gränssnitt för att ta emot IP-adress baserat på DHCP.
Aktivera SSH | Se till att SSH är aktiverat och att sshd-tjänsten är inställd på att starta automatiskt vid omstart.<br/> Se till att inkommande anslutningar för ssh-anslutning inte blockeras av OS-brandväggen eller skript bara regler.

[Följ den här artikeln](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) som beskriver de här stegen för att köra en virtuell Linux-dator på Azure och ta med anvisningar för några av de populära Linux-distributionerna.  


## <a name="replicate-vms"></a>Replikera virtuella datorer

När identifieringen är klar kan du påbörja replikeringen av virtuella VMware-datorer till Azure. 

> [!NOTE]
> Du kan replikera upp till 10 datorer tillsammans. Om du behöver replikera mer replikerar du dem samtidigt i batchar med 10. För migrering utan agent kan du köra upp till 100 samtidiga replikeringar.

1. I Azure Migrate Project >- **servrar** **Azure Migrate: Server-migrering**klickar du på **Replikera**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-vmware/select-replicate.png)

2. I **Replikera** > **Källinställningar** > **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere**.
3. I **Lokal dator** väljer du namnet på den Azure Migrate-dator som du konfigurerar > **OK**. 

    ![Källinställningar](./media/tutorial-migrate-vmware/source-settings.png)

    - Det här steget förutsätter att du redan har konfigurerat en installation när du har slutfört självstudien.
    - Om du inte har konfigurerat en installation följer du anvisningarna i [den här artikeln](how-to-set-up-appliance-vmware.md).

4. I **Virtuella datorer** väljer du de datorer som du vill replikera.
    - Om du har kört en utvärdering av de virtuella datorerna, kan du tillämpa storleksändring av virtuella datorer och disktypsrekommendationer (premium/standard) från utvärderingsresultaten. Gör detta i **Vill du importera migreringsinställningar från en Azure Migrate-utvärdering?** och välj alternativet **Ja**.
    - Om du inte har kört någon utvärdering, eller om du inte vill använda utvärderingsinställningarna, väljer du alternativet **Nej**.
    - Om du valde att använda utvärderingen väljer du VM-grupp och utvärderingsnamn.

    ![Välj utvärdering](./media/tutorial-migrate-vmware/select-assessment.png)

5. I **Virtuella datorer** söker du efter önskade datorer och markerar varje virtuell dator som du vill migrera. Klicka sedan på **Nästa: mål inställningar**.

    ![Välj virtuella datorer](./media/tutorial-migrate-vmware/select-vms.png)

6. I **Målinställningar** väljer du prenumeration och den målregion som du vill migrera till. Ange sedan den resursgrupp där du vill att de virtuella Azure-datorerna ska finnas efter migreringen. I **Virtuellt nätverk** väljer du det Azure VNet/undernät som de virtuella Azure-datorerna ska anslutas till efter migreringen.
7. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka sedan på **Nästa**.

    ![Mål inställningar](./media/tutorial-migrate-vmware/target-settings.png)

8. I **Compute** granskar du namnet på den virtuella datorn, storlek, disktyp för operativsystemet och tillgänglighetsuppsättningen. De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **VM-storlek**: om du använder utvärderings rekommendationer kommer List rutan VM-storlek att innehålla den rekommenderade storleken. Annars väljer Azure Migrate en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**. 
    - **OS-disk**: Ange OS-disken (start) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns. 
    - **Tillgänglighets uppsättning**: om den virtuella datorn ska finnas i en Azure-tillgänglighets uppsättning efter migreringen anger du uppsättningen. Uppsättningen måste finnas i målets resursgrupp som du anger för migreringen.

    ![Inställningar för VM-beräkning](./media/tutorial-migrate-vmware/compute-settings.png)

9. I **Diskar** anger du om VM-diskarna ska replikeras till Azure och disktypen (standard SSD/HDD eller premiumhanterade diskar) i Azure. Klicka sedan på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar kommer de inte att synas i den virtuella Azure-datorn efter migreringen. 

    ![Diskar](./media/tutorial-migrate-vmware/disks.png)

10. I **Granska och starta replikering** kontrollerar du inställningarna och klickar på **Replikera** för att påbörja den första replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningarna när du vill innan replikeringen startar i **Hantera** > **Replikera datorer**. Det går inte att ändra inställningarna efter att replikeringen har startat.

### <a name="provisioning-for-the-first-time"></a>Etablering för första gången

Om det här är den första virtuella dator som du replikerar i Azure Migrate-projektet, etablerar Azure Migrate Server migrering automatiskt dessa resurser i samma resurs grupp som projektet.

- **Service Bus**: Azure Migrate Server-migrering använder Service Bus för att skicka meddelanden om dirigering av replikering till-enheten.
- **Gateway Storage-konto**: Server Migration använder Gateway Storage-kontot för att lagra statusinformation om de virtuella datorer som replikeras.
- **Logg lagrings konto**: Azure Migrate-installationen överför replik loggar för virtuella datorer till ett logg lagrings konto. Azure Migrate tillämpar replikeringsinformation på de replik hanterade diskarna.
- **Nyckel valv**: Azure Migrates enheten använder nyckel valvet för att hantera anslutnings strängar för Service Bus och åtkomst nycklar för de lagrings konton som används i replikeringen. Du bör ha ställt in de behörigheter som nyckel valvet behöver för att komma åt lagrings kontot när du för beredde. [Granska dessa behörigheter](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Spåra och övervaka


- När du klickar på **Replikera** startar du jobbet starta replikering. 
- När jobbet starta replikeringen har slutförts påbörjar datorerna sin inledande replikering till Azure.
- Under den inledande replikeringen skapas en VM-ögonblicksbild. Disk data från ögonblicks bilden replikeras till replik Managed disks i Azure.
- När den inledande replikeringen har slutförts börjar delta-replikeringen. Stegvisa ändringar av lokala diskar replikeras regelbundet till replik diskarna i Azure.

Du kan spåra jobb status i Portal meddelanden.

Du kan övervaka replikeringsstatus genom att klicka på **Replikera servrar** i **Azure Migrate: Server-migrering**.
![övervaka replikering](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Kör en testmigrering


När delta-replikering börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- Genom att köra en test-migrering kontrollerar du att migreringen fungerar som förväntat, utan att det påverkar de lokala datorerna, som fortsätter att fungera och fortsätter att replikeras. 
- Testmigrering simulerar migreringen genom att skapa en virtuell Azure-dator med replikerade data (vanligt vis migrera till ett virtuellt nätverk som inte är i Azure-prenumerationen).
- Du kan använda den replikerade virtuella Azure-datorn för att verifiera migreringen, utföra app-testning och åtgärda eventuella problem före fullständig migrering.

Gör en testmigrering enligt följande:


1. I **mål för migrering** > **servrar** > **Azure Migrate: Server migrering**klickar du på **test migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-vmware/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigreringen fungerar som förväntat kan du migrera de lokala datorerna.

1. I Azure Migrate Project >- **servrar** > **Azure Migrate: Server-migrering**klickar du på **Replikera servrar**.

    ![Servrarna replikeras](./media/tutorial-migrate-vmware/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3. I **Migrera** > **Stäng av virtuella datorer och utför en planerad migrering utan dataförlust** väljer du **Ja** > **OK**.
    - Som standard stänger Azure Migrate av den lokala virtuella datorn och kör en replikering på begäran som synkroniserar eventuella VM-ändringar som har inträffat sedan den senaste replikeringen utfördes. Detta säkerställer att ingen dataförlust sker.
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**
4. Ett migreringsjobb startas för den virtuella datorn. Spåra jobbet i Azure-meddelanden.
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan **Virtual Machines**.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är färdig högerklickar du på den virtuella datorn > **stoppa migreringen**. Detta stoppar replikeringen för den lokala datorn och rensar information om replikeringstillståndet för den virtuella datorn.
2. Installera [Azure VM-](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) eller [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) -agenten på de migrerade datorerna.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Klipp ut över trafik till den migrerade virtuella Azure-instansen.
6. Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
7. Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
8. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 

## <a name="post-migration-best-practices"></a>Metod tips för efter migreringen

- För ökat skydd:
    - Skydda data genom att säkerhetskopiera virtuella Azure-datorer med Azure Backup-tjänsten. [Läs mer](../backup/quick-backup-vm-portal.md).
    - Håll arbetsbelastningar i körning och kontinuerligt tillgängliga genom att replikera virtuella Azure-datorer till en sekundär region med Site Recovery. [Läs mer](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- För ökad säkerhet:
    - Lås och begränsa inkommande trafik åtkomst med [Azure Security Center – just-in-Time-administration](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Begränsa nätverkstrafik till hanteringsslutpunkter med [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Distribuera [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) för att säkra diskar och skydda data från stöld och obehörig åtkomst.
    - Läs mer om [ att skydda IaaS-resurser](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) och besök [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- För övervakning och hantering:
-  Överväg att distribuera [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) för att övervaka användning och utgifter.


## <a name="next-steps"></a>Nästa steg

Undersök [resan för migrering i molnet](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud adoption Framework.
