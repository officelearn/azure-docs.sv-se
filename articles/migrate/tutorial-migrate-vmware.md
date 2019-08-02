---
title: Migrera lokala virtuella VMware-datorer till Azure med agent lös Azure Migrate Server-migrering | Microsoft Docs
description: Beskriver hur du utför och utan agent migrering av lokala virtuella VMware-datorer till Azure med hjälp av Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7fba9cbbaa15359e7e4dd95e66645dd5f4022431
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640780"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrera virtuella VMware-datorer till Azure (utan agent)

Den här artikeln visar hur du migrerar lokala virtuella VMware-datorer till Azure med hjälp av återställning utan agent med Migreringsverktyg för Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och AWS/GCP VM-instanser till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV).

Den här självstudien är den tredje i en serie som visar hur du bedömer och migrerar virtuella VMware-datorer till Azure med hjälp av Azure Migrate Server bedömning och migrering. I den här guiden får du lära dig att:

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

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar de här självstudierna bör du:

1. [Förstå](migrate-architecture.md) VMware-migreringens arkitektur.
2. [Slutför den första](tutorial-prepare-vmware.md) självstudien i den här serien för att konfigurera Azure och VMware för migrering. I den här självstudien måste du särskilt:
    - [Förbered Azure](tutorial-prepare-vmware.md#prepare-azure) för migrering.
    - [Förbered den lokala miljön](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) för migrering.
    
3. Vi rekommenderar att du provar att utvärdera virtuella VMware-datorer med Azure Migrate Server-utvärdering innan du migrerar dem till Azure. Om du vill konfigurera utvärderingen [Slutför du den andra](tutorial-assess-vmware.md) självstudien i den här serien. Om du inte vill utvärdera de virtuella datorerna kan du hoppa över den här självstudien. Även om vi rekommenderar att du testar en utvärdering, men du inte behöver köra en utvärdering innan du försöker utföra en migrering.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Lägg till verktyget för migrering av Azure Migrate Server

Om du inte följer den andra självstudien för att utvärdera virtuella VMware-datorer måste du [följa de här anvisningarna](how-to-add-tool-first-time.md) för att skapa ett Azure Migrate-projekt och välja verktyget för migrering av Azure Migrate Server. 

Om du följde den andra själv studie kursen och redan har ett Azure Migrate-projekt konfigurerat lägger du till Migreringsverktyg för Azure Migrate server på följande sätt:

1. Klicka på **Översikt**i Azure Migrate projektet. 
2. I **identifiera, utvärdera**och migrera servrar klickar du på **utvärdera och migrera servrar**.

     ![Utvärdera och migrera servrar](./media/tutorial-migrate-vmware/assess-migrate.png)

3. I **Migreringsverktyg**väljer **du klicka här för att lägga till ett Migreringsverktyg när du är redo att migrera**.

    ![Välj ett verktyg](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. I listan verktyg väljer **du Azure Migrate: Verktyget Migreringsverktyg**till** Server** > 

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

**Åtgärd** | **Detaljer** | **Problemlösning**
--- | --- | ---
Se till att Windows-volymer i Azure VM använder samma enhets beteckningar som den lokala virtuella datorn. | Konfigurera SAN-principen som online alla. | 1. Logga in på den virtuella datorn med ett administratörs konto och öppna ett kommando fönster.<br/> 2. Skriv **DiskPart** för att köra DiskPart-verktyget.<br/> 3. Typ **San-princip = OnlineAll**<br/> 4. Skriv exit för att lämna DiskPart och Stäng kommando tolken.
Aktivera Azure Serial Access-konsolen för den virtuella Azure-datorn | Detta hjälper till med fel sökning. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn startas med disk avbildningen och motsvarar en omstart för den nya virtuella datorn. | Följ [dessa instruktioner](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) för att aktivera.
Installera Hyper-V-gäst integrering | Om du migrerar datorer som kör Windows Server 2003 installerar du Hyper-V-gäst integrerings tjänster på den virtuella datorns operativ system. | [Läs mer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Fjärrskrivbord | Aktivera fjärr skrivbord på den virtuella datorn och kontrol lera att Windows-brandväggen inte blockerar åtkomst till fjärr skrivbord på någon nätverks profil. | [Läs mer](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Förbered virtuella Linux-datorer

**Åtgärd** | **Detaljer** 
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

När identifieringen är klar kan du påbörja replikering av virtuella VMware-datorer till Azure.

1. Azure Migrate i Azure Migrate Project >- **servrar: Server-** migrering klickar du på **Replikera**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-vmware/select-replicate.png)

2. I **Replikera**, > **käll inställningar** > **att datorerna har virtualiserats?** väljer du **Ja, med VMware vSphere**.
3. I **lokal**installation väljer du namnet på Azure Migrate-installationen som du ställer in > **OK**. 

    ![Käll inställningar](./media/tutorial-migrate-vmware/source-settings.png)

    - Det här steget förutsätter att du redan har konfigurerat en installation när du har slutfört självstudien.
    - Om du inte har konfigurerat en installation följer du anvisningarna i [den här artikeln](how-to-set-up-appliance-vmware.md).

4. I **virtuella datorer**väljer du de datorer som du vill replikera.
    - Om du har kört en utvärdering för de virtuella datorerna kan du tillämpa rekommendationer för VM-storlek och disk typ (Premium/standard) från utvärderings resultatet. Det gör du genom att välja alternativet **Ja** i **Importera inställningar för migrering från en Azure Migrate utvärdering?** .
    - Om du inte har kört en utvärdering eller om du inte vill använda utvärderings inställningarna väljer du **inga** alternativ.
    - Om du har valt att använda utvärderingen väljer du VM-gruppen och bedömnings namnet.

    ![Välj utvärdering](./media/tutorial-migrate-vmware/select-assessment.png)

5. I **virtuella datorer**söker du efter de virtuella datorerna efter behov och kontrollerar varje virtuell dator som du vill migrera. Klicka sedan **på Nästa: Mål inställningar**.

    ![Välj virtuella datorer](./media/tutorial-migrate-vmware/select-vms.png)

6. I **mål inställningar**väljer du den prenumeration och mål region som du ska migrera till och anger resurs gruppen där de virtuella Azure-datorerna ska finnas efter migreringen. I **Virtual Network**väljer du det virtuella Azure-nätverk som de virtuella Azure-datorerna ska kopplas till efter migreringen.
7. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmån. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance-eller Windows Server-prenumerationer och vill använda förmånen för de datorer som du migrerar. Klicka sedan på **Nästa**.

    ![Målinställningar](./media/tutorial-migrate-vmware/target-settings.png)

8. I **Compute**granskar du VM-namn, storlek, typ av operativ system disk och tillgänglighets uppsättning. Virtuella datorer måste uppfylla [kraven för Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **VM-storlek**: Om du använder bedömnings rekommendationer kommer List rutan VM-storlek att innehålla den rekommenderade storleken. Annars Azure Migrate väljer en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Alternativt kan du välja en manuell storlek i **storlek på virtuell Azure-dator**. 
    - **OS-disk**: Ange OS-disken (start) för den virtuella datorn. OS-disken är den disk som har operativ systemets start program och installations program. 
    - **Tillgänglighetsuppsättning**: Om den virtuella datorn ska finnas i en Azure-tillgänglighets uppsättning efter migreringen anger du uppsättningen. Uppsättningen måste finnas i mål resurs gruppen som du anger för migreringen.

    ![Inställningar för VM-beräkning](./media/tutorial-migrate-vmware/compute-settings.png)

9. I **diskar**anger du om de virtuella dator diskarna ska replikeras till Azure och väljer disk typ (standard SSD/HDD eller Premium-Managed Disks) i Azure. Klicka sedan på **Nästa**.
    - Du kan exkludera diskar från replikering.
    - Om du exkluderar diskar finns det inte på den virtuella Azure-datorn efter migreringen. 

    ![Diskar](./media/tutorial-migrate-vmware/disks.png)

10. Granska inställningarna i **Granska och starta replikering**och klicka på **Replikera** för att starta den inledande replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningar varje tid innan replikeringen startar i **Hantera** > **replikerings datorer**. Det går inte att ändra inställningarna när replikeringen har påbörjats.

### <a name="provisioning-for-the-first-time"></a>Etablering för första gången

Om det här är den första virtuella dator som du replikerar i Azure Migrate-projektet, etablerar Azure Migrate Server migrering automatiskt dessa resurser i samma resurs grupp som projektet.

- **Service Bus**: Azure Migrate Server-migrering använder Service Bus för att skicka meddelanden om dirigering av replikering till enheten.
- **Gateway-lagrings konto**: Server-migreringen använder Gateway Storage-kontot för att lagra tillståndsinformation om de virtuella datorer som replikeras.
- **Logg lagrings konto**: Azure Migrate-installationen överför replik loggar för virtuella datorer till ett logg lagrings konto. Azure Migrate tillämpar replikeringsinformation på de replik hanterade diskarna.
- **Nyckel valv**: Azure Migrate-enheten använder nyckel valvet för att hantera anslutnings strängar för Service Bus och åtkomst nycklar för de lagrings konton som används i replikeringen. Du bör ha ställt in de behörigheter som nyckel valvet behöver för att komma åt lagrings kontot när du för beredde. [Granska dessa behörigheter](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Spåra och övervaka


- När du klickar på **Replikera** startar du jobbet starta replikering. 
- När jobbet starta replikeringen har slutförts påbörjar datorerna sin inledande replikering till Azure.
- Under den inledande replikeringen skapas en VM-ögonblicksbild. Disk data från ögonblicks bilden replikeras till replik Managed disks i Azure.
- När den inledande replikeringen har slutförts börjar delta-replikeringen. Stegvisa ändringar av lokala diskar replikeras regelbundet till replik diskarna i Azure.

Du kan spåra jobb status i Portal meddelanden.

Du kan övervaka replikeringsstatus genom att klicka på **Replikera servrar** i **Azure Migrate: Server-** migrering.
![Övervaka replikering](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Kör en testmigrering


När delta-replikering börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- Genom att köra en test-migrering kontrollerar du att migreringen fungerar som förväntat, utan att det påverkar de lokala datorerna, som fortsätter att fungera och fortsätter att replikeras. 
- Testmigrering simulerar migreringen genom att skapa en virtuell Azure-dator med replikerade data (vanligt vis migrera till ett virtuellt nätverk som inte är i Azure-prenumerationen).
- Du kan använda den replikerade virtuella Azure-datorn för att verifiera migreringen, utföra app-testning och åtgärda eventuella problem före fullständig migrering.

Gör en testmigrering enligt följande:


1. I >  **** Serverför > migreringsmålAzureMigrate: **Server-** migrering klickar du på **testa migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som du vill testa och klicka på **testa migrering**.

    ![Testmigrering](./media/tutorial-migrate-vmware/test-migrate.png)

3. I **testa migrering**väljer du det virtuella Azure-nätverk där den virtuella Azure-datorn ska placeras efter migreringen. Vi rekommenderar att du använder ett virtuellt nätverk som inte är för produktion.
4. Testmigreringen startar. Övervaka jobbet i Portal meddelanden.
5. När migreringen är klar kan du Visa den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Dator namnet har ett suffix **-test**.
6. När testet är klar högerklickar du på den virtuella Azure-datorn i **Replikera datorer**och klickar på **Rensa testmigrering**.

    ![Rensa migrering](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigreringen fungerar som förväntat kan du migrera de lokala datorerna.

1. I Azure Migrate Project >- **servrar** > **Azure Migrate: Server-** migrering klickar du på **Replikera servrar**.

    ![Replikerar servrarna](./media/tutorial-migrate-vmware/replicate-servers.png)

2. I **Replikera datorer**, högerklickar du på den virtuella datorn > **migrera**.
3. I **migrera** > **Stäng virtuella datorer och utför en planerad migrering utan data förlust**väljer du **Ja** > **OK**.
    - Som standard stänger Azure Migrate av den lokala virtuella datorn och kör en replikering på begäran för att synkronisera eventuella ändringar i virtuella datorer som inträffade sedan den senaste replikeringen skedde. Detta garanterar ingen data förlust.
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**
4. Ett migreringsjobb startar för den virtuella datorn. Spåra jobbet i Azure-meddelanden.
5. När jobbet har slutförts kan du Visa och hantera den virtuella datorn från sidan **Virtual Machines** .

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

Undersök [resan för migrering](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) i molnet i Azure Cloud adoption Framework.
