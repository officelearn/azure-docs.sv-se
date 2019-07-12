---
title: Migrera lokala virtuella VMware-datorer till Azure med utan Agent Azure Migrate servermigrering | Microsoft Docs
description: Beskriver hur du utför och utan Agent migrering av lokala virtuella VMware-datorer till Azure med Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3510c0505a5a3c1353642baf5060a83d13fdd43a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809129"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrera virtuella VMware-datorer till Azure (utan Agent)

Den här artikeln visar hur du migrerar lokala virtuella VMware-datorer till Azure med verktyget Azure Migrate servermigrering migrering utan agenter.

[Azure Migrate](migrate-services-overview.md) ger en central knutpunkt för att spåra identifiering, bedömning och migrering av dina lokala appar och arbetsbelastningar och AWS/GCP VM-instanser till Azure. Hubben innehåller Azure Migrate verktyg för bedömning och migrering, samt tredjepartsprogram oberoende programvaruleverantörer (ISV)-erbjudanden.

Den här självstudien är den tredje delen i en serie som visar hur du utvärdera och migrera virtuella VMware-datorer till Azure med hjälp av Azure Migrate Server bedömning och migrering. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbereda virtuella datorer för migrering.
> * Lägg till Server Migreringsverktyget för Azure-migrering.
> * Identifiera virtuella datorer du vill migrera.
> * Börja replikera virtuella datorer.
> * Kör en testmigrering för att kontrollera att allt fungerar som förväntat.
> * Kör en fullständig VM-migrering.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="migration-methods"></a>Metoder

Du kan migrera virtuella VMware-datorer till Azure med hjälp av verktyget Azure Migrate servermigrering. Det här verktyget erbjuder ett par alternativ för VMware-VM-migrering:

- Migrering med utan Agent replikering. Migrera virtuella datorer utan att behöva installera något på dem.
- Migrering med en agent för replikering. Installera en agent på den virtuella datorn för replikering.

När du ska välja om du vill använda utan Agent eller agentbaserad migreringen, granska de här artiklarna:

- [Lär dig hur](server-migrate-overview.md) migrering utan agenter fungerar och [granska begränsningarna](server-migrate-overview.md#agentless-migration-limitations).
- [Den här artikeln](tutorial-migrate-vmware-agent.md) om du vill använda metoden agentbaserad.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar de här självstudierna bör du:

1. [Förstå](migrate-architecture.md) arkitektur för VMware-migrering.
2. [I första självstudiekursen](tutorial-prepare-vmware.md) i den här serien du ställer in Azure och VMware för migrering. Mer specifikt i den här självstudien måste du:
    - [Förbereda Azure](tutorial-prepare-vmware.md#prepare-azure) för migrering.
    - [Förbered den lokala miljön](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) för migrering.
    
3. Vi rekommenderar att du försöker utvärdera virtuella VMware-datorer med Azure Migrate Server-utvärdering innan du migrerar dem till Azure. Du ställer in utvärdering av [i andra självstudiekursen](tutorial-assess-vmware.md) i den här serien. Om du inte vill utvärdera virtuella datorer kan du hoppa över den här självstudien. Vi rekommenderar att du kan prova att använda en utvärdering, men du inte behöver köra en utvärdering innan du försöker en migrering.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Lägg till verktyget Azure Migrate-servermigrering

Om du inte följer den andra självstudien för att utvärdera virtuella VMware-datorer, måste du [följer du dessa instruktioner](how-to-add-tool-first-time.md) ställer in ett Azure Migrate-projekt och väljer verktyget Azure Migrate servermigrering. 

Om du har följt den andra självstudien och redan har ett Azure Migrate-projekt som ställer in, lägger du till Migreringsverktyget Azure Migrate Server enligt följande:

1. I Azure Migrate-projektet klickar du på **översikt**. 
2. I **Upptäck, utvärdera, och migrering servrar**, klickar du på **utvärdera och migrera servrar för**.

     ![Utvärdera och migrera servrar](./media/tutorial-migrate-vmware/assess-migrate.png)

3. I **Migreringsverktyg**väljer **Klicka här för att lägga till ett migrationsverktyg när du är redo att migrera**.

    ![Välj ett verktyg](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Välj i verktygslistan över, **Azure Migrate: Servermigrering** > **Lägg till verktyget**

    ![Server-Migreringsverktyget](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurera Azure Migrate-installation

Azure Migrate servermigrering kör en enkel VMware VM-installation. Enheten som utför identifieringen av virtuella datorer och skickar VM metadata och prestanda till Azure Migrate-servermigrering. I samma installation används också av verktyget Azure Migrate Server-utvärdering.

Om du har följt den andra självstudien för att utvärdera virtuella VMware-datorer bör har du redan konfigurerat installationen under självstudien. Om du inte gå igenom den självstudien, måste du konfigurera installationen nu. Gör du: 

- Ladda ned en mall för OVA-filen och importera dem till vCenter-servern.
- Skapa och kontrollera att den kan ansluta till Azure Migrate Server-utvärdering. 
- Konfigurera programmet för första gången och registrera den med Azure Migrate-projektet.

Följ instruktionerna i [i den här artikeln](how-to-set-up-appliance-vmware.md) att konfigurera installationen.


## <a name="prepare-vms-for-migration"></a>Förbereda virtuella datorer för migrering

Azure Migrate kräver vissa ändringar för virtuell dator så att virtuella datorer kan migreras till Azure.

- För vissa [operativsystem](server-migrate-overview.md#agentless-migration-limitations), Azure Migrate gör dessa ändringar automatiskt.
- Om du migrerar en virtuell dator som inte har något av dessa operativsystem, följer du anvisningarna för att förbereda den virtuella datorn.
- Det är viktigt att göra ändringarna innan du påbörjar migreringen. Om du migrerar den virtuella datorn innan du ändrar eventuellt den virtuella datorn startar inte upp i Azure.
- Konfigurationsändringar du gör på lokala virtuella datorer replikeras till Azure när replikering för den virtuella datorn har aktiverats. För att säkerställa att ändringarna replikeras, se till att den återställningspunkt som du migrerar till är senare än den tid då konfigurationsändringarna gjorts på plats.


### <a name="prepare-windows-server-vms"></a>Förbereda Windows Server-datorer

**Åtgärd** | **Detaljer** | **Instruktioner**
--- | --- | ---
Kontrollera att Windows-volymer i virtuella Azure-datorer använder samma enhetsbeteckningar som den lokala virtuella datorn. | Konfigurera SAN-princip som alla Online. | 1. Logga in på den virtuella datorn med ett administratörskonto och öppna ett kommandofönster.<br/> 2. Typ **diskpart** att köra verktyget Diskpart.<br/> 3. Typ **SAN-princip = OnlineAll**<br/> 4. Ange Avsluta om du vill lämna Diskpart och Stäng kommandotolken.
Aktivera seriell åtkomst i Azure-konsolen för Azure VM | Detta underlättar felsökning. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn startas med hjälp av disk image och detta motsvarar en omstart för den nya virtuella datorn. | Följ [instruktionerna](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console#enable-serial-console-in-custom-or-older-images) att aktivera.
Installera Hyper-V-gäst-integrering | Om du migrerar datorer som kör Windows Server 2003, installera integreringstjänsterna för Hyper-V-gäst på VM-operativsystem. | [Läs mer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Fjärrskrivbord | Aktivera Fjärrskrivbord på den virtuella datorn och kontrollera att Windows-brandväggen inte blockerar åtkomst för fjärrskrivbord på alla nätverksprofiler. | [Läs mer](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Förbereda virtuella Linux-datorer

**Åtgärd** | **Detaljer** 
--- | --- | ---
Installera Hyper-V Linux Integration-tjänster | De flesta nya versioner av Linux-distributioner har det här ingår som standard.
Återskapa Linux init avbildningen ska innehålla nödvändiga drivrutiner för Hyper-V | Detta säkerställer att den virtuella datorn kommer att starta i Azure och bara installeras på vissa distributioner.
Aktivera loggning för Azure seriekonsol | Detta underlättar felsökning. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn startas med hjälp av disk image och detta motsvarar en omstart för den nya virtuella datorn.<br/> Följ [instruktionerna](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) att aktivera.
Uppdatera enheten av mappningsfilen | Uppdatera filen enheten karta som har enhetsnamnet för att volymen kopplingar, till att använda beständiga enhetsidentifierare
Uppdatera fstab-poster | Uppdatera posterna för att använda permanent volym-ID: n.
Ta bort udev-regel | Ta bort alla udev-regler som reserverar gränssnittsnamn baserat på mac-adress osv.
Uppdatera nätverksgränssnitt | Uppdatera nätverksgränssnitt för att ta emot IP-adress baserat på DHCP.
Aktivera ssh | Se till att ssh är aktiverat och sshd-tjänsten är konfigurerad att starta automatiskt vid omstart.<br/> Se till att inkommande ssh anslutningsförfrågningar inte blockeras av OS-brandvägg eller skriptbara regler.

[Följ den här artikeln](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) som beskriver de här stegen för att köra en Linux-VM på Azure och innehåller anvisningar för några av de populära Linux-distributionerna.  


## <a name="replicate-vms"></a>Replikera virtuella datorer

Med identifieringen har slutförts, kan du börja replikering av virtuella VMware-datorer till Azure.

1. I Azure Migrate-projektet > **servrar**, **Azure Migrate: Servermigrering**, klickar du på **replikera**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-vmware/select-replicate.png)

2. I **replikera**, > **datakällans inställningar** > **är dina datorer virtualiserade?** väljer **Ja, med VMware vSphere**.
3. I **lokala installationen**, Välj namnet på den Azure Migrate-installationen som du har konfigurerat > **OK**. 

    ![Inställningar för datakälla](./media/tutorial-migrate-vmware/source-settings.png)

    - Det här steget förutsätter att du har ställt in en installation när du har slutfört kursen.
    - Om du inte har konfigurerat en installation, och följ sedan instruktionerna i [i den här artikeln](how-to-set-up-appliance-vmware.md).

4. I **virtuella datorer**, Välj de datorer som du vill replikera.
    - Om du har kört en utvärdering för de virtuella datorerna, kan du använda VM-storlek och disk typ (premium/standard) rekommendationerna från resultaten. Att göra detta i **importera inställningar från en Azure Migrate-utvärdering?** väljer den **Ja** alternativet.
    - Om du inte kör en utvärdering, eller om du inte vill använda inställningar för utvärdering, väljer den **nr** alternativ.
    - Om du har valt för att använda utvärderingen, Välj grupp virtuella datorer och namn på utvärdering.

    ![Välj utvärdering](./media/tutorial-migrate-vmware/select-assessment.png)

5. I **virtuella datorer**, Sök efter virtuella datorer efter behov och kontrollera varje virtuell dator som du vill migrera. Klicka sedan på **nästa: Rikta inställningar**.

    ![Välj virtuella datorer](./media/tutorial-migrate-vmware/select-vms.png)

6. I **rikta inställningar**, Välj prenumerationen och målregion som du migrerar och ange den resursgrupp där virtuella Azure-datorer kommer att finnas efter migreringen. I **virtuellt nätverk**, Välj Azure virtuellt nätverk/undernät som virtuella Azure-datorer ska anslutas efter migreringen.
7. In **Azure Hybrid Benefit**:

    - Välj **nr** om du inte vill använda Azure Hybrid-förmånen. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas med aktiv Software Assurance eller Windows Server-prenumerationer och du vill använda förmånen med de datorer som du migrerar. Klicka sedan på **Nästa**.

    ![Målinställningar](./media/tutorial-migrate-vmware/target-settings.png)

8. I **Compute**, granska virtuella datorns namn, storlek, typ av OS-disk och tillgänglighetsuppsättning. Virtuella datorer måste överensstämma med [krav för Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **VM-storlek**: Om du använder rekommendationerna för innehåller VM-storlek listrutan den rekommenderade storleken. Annars hämtar Azure Migrate en storlek baserat på matchande poster i Azure-prenumeration. Du kan också välja en manuell storlek i **Azure VM-storlek**. 
    - **OS-disken**: Ange operativsystemdisken (Start) för den virtuella datorn. OS-disken är en disk som har operativsystemet startprogrammet och installationsprogrammet. 
    - **Tillgänglighetsuppsättning**: Om den virtuella datorn ska vara i en Azure tillgänglighetsuppsättning efter migreringen, ange en uppsättning. Uppsättningen måste finnas i målresursgruppen som du anger för migreringen.

    ![Inställningarna för beräkning av virtuella datorer](./media/tutorial-migrate-vmware/compute-settings.png)

9. I **diskar**, anger du om VM-diskarna ska replikeras till Azure och välj disktyp (standard SSD-Hårddisk eller premium-hanterade diskar) i Azure. Klicka sedan på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar, inte på den virtuella Azure-datorn efter migreringen. 

    ![Diskar](./media/tutorial-migrate-vmware/disks.png)

10. I **granska och startar replikeringen**, granskar du inställningarna och klicka på **replikera** att starta den inledande replikeringen för servrar.

> [!NOTE]
> Du kan uppdatera replikeringsinställningarna som helst innan replikeringen startar i **hantera** > **datorer replikeras**. Inställningarna kan inte ändras när replikeringen startar.

### <a name="provisioning-for-the-first-time"></a>Etablering för första gången

Om detta är den första virtuella datorn du replikerar i Azure Migrate-projektet, etablerar Azure Migrate servermigrering automatiskt dessa resurser i samma resursgrupp som projektet.

- **Service bus**: Azure Migrate servermigrering använder service bus för att skicka replikering orchestration-meddelanden till installationen.
- **Gateway-lagringskonto**: Servermigrering använder gateway-storage-konto för att lagra statusinformation om de virtuella datorerna replikeras.
- **Logglagringskonto**: Azure Migrate-installationen laddar upp replikeringsloggar för virtuella datorer till en logglagringskontot. Azure Migrate gäller replikeringsinformationen om för de hanterade replikeringsdiskar.
- **Nyckelvalv**: Azure Migrate-installationen använder nyckelvalvet för att hantera anslutningssträngar för service bus och snabbtangenterna för lagringskonton som används i replikeringen. Du bör ha ställa in de behörigheter som nyckelvalvet behöver åtkomst till lagringskontot när du har förberett. [Granska dessa behörigheter](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Spåra och övervaka


- När du klickar på **replikera** ett jobb startar replikeringen börjar. 
- När starta replikeringen har slutförts startas datorerna sina inledande replikering till Azure.
- Under den inledande replikeringen skapa en ögonblicksbild av virtuell dator. Diskdata från ögonblicksbilden replikeras till hanterade replikeringsdiskar i Azure.
- När den inledande replikeringen är klar startar deltareplikeringen. Inkrementella ändringar till lokala diskar replikeras med jämna mellanrum till replikeringsdiskar i Azure.

Du kan spåra status för återställningsjobb i portalen meddelanden.

Du kan övervaka replikeringsstatus genom att klicka på **replikera servrar** i **Azure Migrate: Servermigrering**.
![Övervakare för replikering](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Kör en testmigrering


När deltareplikeringen börjar kör du en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- Kör en testmigrering kontrollerar att migreringen fungerar som förväntat, utan att påverka de lokala datorerna som fortsätta att fungera och fortsätta med replikeringen. 
- Testmigrering simulerar migreringen genom att skapa en Azure-dator med hjälp av replikerade data (vanligtvis migrera till ett icke-produktion virtuellt nätverk i Azure-prenumerationen).
- Du kan använda testet replikerade virtuella Azure-datorer att validera migreringen testa appen och åtgärda eventuella problem innan fullständig migrering.

Gör en testmigrering på följande sätt:


1. I **migrering mål** > **servrar** > **Azure Migrate: Servermigrering**, klickar du på **Test migrera servrar**.

     ![Testa migrerade servrar](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Högerklicka på den virtuella datorn för att testa och klicka på **Test migrera**.

    ![Testmigrering](./media/tutorial-migrate-vmware/test-migrate.png)

3. I **testmigrering**, Välj den virtuella Azure-nätverket där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett icke-produktion virtuellt nätverk.
4. Den **testa migrering** jobbet startar. Övervaka jobb i portalen meddelanden.
5. När migreringen är klar kan du visa den migrerade virtuella Azure-datorer i **virtuella datorer** i Azure-portalen. Namnet på datorn har ett suffix **-Test**.
6. När testet är klart, högerklickar du på Azure VM i **datorer replikeras**, och klicka på **Rensa testmigrering**.

    ![Rensa migrering](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigreringen fungerar som förväntat, kan du migrera lokala datorer.

1. I Azure Migrate-projektet > **servrar** > **Azure Migrate: Servermigrering**, klickar du på **replikera servrar**.

    ![Replikera servrar](./media/tutorial-migrate-vmware/replicate-servers.png)

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
-  Överväg att distribuera [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) för att övervaka användning och utgifter.


## <a name="next-steps"></a>Nästa steg

Undersöka den [migrering molnresa](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud Adoption Framework.
