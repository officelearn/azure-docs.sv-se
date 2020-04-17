---
title: Migrera virtuella virtuella hyper-v-datorer till Azure med Azure Migrera servermigrering
description: Lär dig hur du migrerar lokala virtuella hyper-V-datorer till Azure med Azure Migrera servermigrering
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 6b9732aab9e3fe0d26b4c572efe87c3a9d3e29f6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535357"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrera virtuella Hyper-V-datorer till Azure 

Den här artikeln visar hur du migrerar lokala virtuella hyper-V-datorer till Azure med hjälp av agentlös migrering med verktyget Azure Migrate: ServerMigrering.

[Azure Migrate](migrate-services-overview.md) är en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbetsbelastningar och privata/offentliga virtuella datorer i molnet till Azure. Hubben tillhandahåller Azure Migrate-verktyg för utvärdering och migrering samt ISV-erbjudanden (Independent Software Vendor) från tredje part.

Den här självstudien är den tredje i en serie som visar hur du bedömer och migrerar Hyper-V till Azure med Azure Migrate Server Assessment and Server Migration. I den här guiden får du lära dig att:


> [!div class="checklist"]
> * Förbereda Azure och din lokala Hyper-V-miljö
> * Ställ in källmiljön.
> * Konfigurera målmiljön.
> * Aktivera replikering.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.


## <a name="prerequisites"></a>Krav

Innan du börjar de här självstudierna bör du:

1. [Granska](hyper-v-migration-architecture.md) Hyper-V-migreringsarkitekturen.
2. [Granska](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Hyper-V-värdkrav och Azure-url:er som Hyper-V-värdarna behöver komma åt.
3. [Granska](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) krav för virtuella hyper-virtuella datorer som du vill migrera. Virtuella datorer med hyper-V måste uppfylla [Kraven för Azure VM](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).
2. Vi rekommenderar att du slutför de tidigare självstudierna i den här serien. Den [första självstudien](tutorial-prepare-hyper-v.md) visar hur du konfigurerar Azure och Hyper-V för migrering. Den andra självstudien visar hur du [bedömer virtuella hyper-virtuella datorer](självstudie-assess-hyper-v.md före migreringen med Azure Migrate:Server Assessment. 
    > [!NOTE]
    > Även om vi rekommenderar att du provar en utvärdering behöver du inte köra en utvärdering innan du migrerar virtuella datorer.
    > För migrera virtuella virtuella hyper-v-datorer körs Azure Migrate:ServerMigrering programvaruagenter (Microsoft Azure Site Recovery provider och Microsoft Azure Recovery Service agent) på Hyper-V-värdar eller klusternoder för att dirigera och replikera data till Azure Migrate. [Azure Migrate-enheten](migrate-appliance.md) används inte för Hyper-V-migrering.

3. Kontrollera att ditt Azure-konto har tilldelats rollen Deltagare i den virtuella datorn, så att du har behörighet att:

    - Skapa en virtuell dator i den valda resursgruppen.
    - Skapa en virtuell dator i det valda virtuella nätverket.
    - Skriv till en Azure-hanterad disk.
4. [Konfigurera ett Azure-nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). När du migrerar till Azure ansluts de skapade virtuella Azure-datorerna till ett Azure-nätverk som du anger när du konfigurerar migreringen.

## <a name="add-the-azure-migrateserver-migration-tool"></a>Lägga till verktyget För migrering av Azure Migrate:Server

Lägg till verktyget Azure Migrate:Server Migration.

- Om du har följt den andra självstudien för att bedöma virtuella datorer med [VMware](/tutorial-assess-hyper-v.md)har du redan konfigurerat ett Azure Migrate-projekt och kan lägga till verktyget nu.
- Om du inte följde den andra självstudien[följer du dessa instruktioner](how-to-add-tool-first-time.md) för att konfigurera ett Azure Migrate-projekt. Du lägger till verktyget Azure Migrate:Server Migration när du skapar projektet.

Om du har konfigurerat ett projekt lägger du till verktyget på följande sätt:

1. Klicka på **Översikt**i Azure Migrate-projektet . 
2. Klicka på Utvärdera och migrera servrar i **identifiera, bedöma och migrera** **servrar.**
3. I **Migreringsverktyg**väljer du **Klicka här om du vill lägga till ett migreringsverktyg när du är redo att migrera**.

    ![Välj ett verktyg](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Välj **Azure Migrate: Servermigreringstillägg** > **Add tool** i verktygslistan

    ![Verktyg för servermigrering](./media/tutorial-migrate-hyper-v/server-migration-tool.png)

## <a name="prepare-hyper-v-hosts"></a>Förbered Hyper-V-värdar


1. Klicka på Identifiera i Azure Migrate-projektet **>-servrar**i **Azure Migrate: Servermigrering**. **Discover**
2. I **Upptäck-datorer** >  **Yes, with Hyper-V****Virtualiseras dina datorer?**
3. I **Målregionen**väljer du den Azure-region som du vill migrera datorerna till.
6. Välj **Bekräfta att målregionen för migrering är regionnamn**.
7. Klicka på **Skapa resurser**. Detta skapar ett Azure Site Recovery-valv i bakgrunden.
    - Om du redan har konfigurerat migreringen med Azure Migrera servermigrering visas inte det här alternativet eftersom resurser har konfigurerats tidigare.
    - Du kan inte ändra målområdet för det här projektet när du har klickat på den här knappen.
    - Alla efterföljande migreringar är till den här regionen.
    
8. I **Förbered Hyper-V-värdservrar**laddar du ned Hyper-V Replication-providern och registreringsnyckelfilen.
    - Registreringsnyckeln behövs för att registrera Hyper-V-värden med Azure Migrate Server Migration.
    - Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Hämta leverantör och nyckel](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Kopiera providerinstallationsfilen och registreringsnyckelfilen till varje Hyper-V-värd (eller klusternod) som kör virtuella datorer som du vill replikera.
5. Kör providerinstallationsfilen på varje värd, enligt beskrivningen i nästa procedur.
6. När du har installerat leverantören på värdar klickar du på **Slutför registrering**i **Upptäck datorer**.

    ![Slutför registreringen](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Det kan ta upp till 15 minuter efter slutförandet av registreringen tills identifierade virtuella datorer visas i Azure Migrate Server Migration. När virtuella datorer upptäcks ökar antalet **identifierade servrar.**

![Identifierade servrar](./media/tutorial-migrate-hyper-v/discovered-servers.png)


## <a name="replicate-hyper-v-vms"></a>Virtuella datorer med replikera hyper-vm

När identifieringen har slutförts kan du börja replikering av virtuella hyper-virtuella datorer till Azure.

> [!NOTE]
> Du kan replikera upp till 10 maskiner tillsammans. Om du behöver replikera mer replikeras de samtidigt i batchar om 10.

1. I Azure Migrate-projektet > **Servrar**klickar **Azure Migrate: Servermigrering**, på **Replikera**.
2. I **Replikera**> **källinställningarNa** > **Är dina datorer virtualiserade?**, välj **Ja, med Hyper-V**. Klicka sedan på **Nästa: Virtuella datorer**.
3. I **Virtuella datorer** väljer du de datorer som du vill replikera.
    - Om du har kört en utvärdering av de virtuella datorerna, kan du tillämpa storleksändring av virtuella datorer och disktypsrekommendationer (premium/standard) från utvärderingsresultaten. Gör detta i **Vill du importera migreringsinställningar från en Azure Migrate-utvärdering?** och välj alternativet **Ja**.
    - Om du inte har kört någon utvärdering, eller om du inte vill använda utvärderingsinställningarna, väljer du alternativet **Nej**.
    - Om du valde att använda utvärderingen väljer du VM-grupp och utvärderingsnamn.

        ![Välj utvärdering](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. I **Virtuella datorer** söker du efter önskade datorer och markerar varje virtuell dator som du vill migrera. Klicka sedan på **Nästa: Målinställningar**.

    ![Välj virtuella datorer](./media/tutorial-migrate-hyper-v/select-vms.png)

5. I **Målinställningar**väljer du den målregion som du ska migrera till, prenumerationen och resursgruppen där virtuella Azure-datorer kommer att finnas kvar efter migreringen.
7. I **Replikeringslagringskonto**väljer du det Azure Storage-konto där replikerade data ska lagras i Azure.
8. **Virtuellt nätverk**väljer du det Azure VNet/-undernät som virtuella Azure-datorer ska anslutas till efter migreringen.
9. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka sedan på **Nästa**.

    ![Målinställningar](./media/tutorial-migrate-hyper-v/target-settings.png)

10. I **Compute** granskar du namnet på den virtuella datorn, storlek, disktyp för operativsystemet och tillgänglighetsuppsättningen. De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **VM-storlek:** Om du använder utvärderingsrekommendationer innehåller listrutan vm-storlek den rekommenderade storleken. Annars väljer Azure Migrate en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**. 
    - **OS-disk:** Ange OS-disken (start) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns. 
    - **Tillgänglighetsuppsättning**: Om den virtuella datorn ska vara i en Azure-tillgänglighetsuppsättning efter migreringen anger du uppsättningen. Uppsättningen måste finnas i målets resursgrupp som du anger för migreringen.

    ![Beräkningsinställningar för virtuella datorer](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. I **Diskar** anger du om VM-diskarna ska replikeras till Azure och disktypen (standard SSD/HDD eller premiumhanterade diskar) i Azure. Klicka sedan på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar kommer de inte att synas i den virtuella Azure-datorn efter migreringen. 

    ![Diskar](./media/tutorial-migrate-hyper-v/disks.png)

10. I **Granska och starta replikering** kontrollerar du inställningarna och klickar på **Replikera** för att påbörja den första replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningarna när som helst innan replikeringen startar i **Hantera** > **replikeringsdatorer**. Det går inte att ändra inställningarna efter att replikeringen har startat.

## <a name="provisioning-for-the-first-time"></a>Etablering för första gången

Om detta är den första virtuella datorn som du replikerar i Azure Migrate-projektet, etablerar Azure Migrate: Servermigrering automatiskt dessa resurser i samma resursgrupp som projektet.

- **Servicebuss**: Azure Migrate: Servermigrering använder Service Bus för att skicka replikeringsorkestreringsmeddelanden till installationen.
- **Gateway storage account**: Azure Migrate: Server Migration använder gateway storage-kontot för att lagra tillståndsinformation om de virtuella datorer som replikeras.
- **Logglagringskonto:** Azure Migrate-installationen överför replikeringsloggar för virtuella datorer till ett logglagringskonto. Azure Migrate tillämpar replikeringsinformationen på de replikhanterade diskarna.
- **Nyckelvalv:** Azure Migrate-enheten använder nyckelvalvet för att hantera anslutningssträngar för servicebussen och komma åt nycklar för lagringskonton som används i replikering. Du bör ha ställt in de behörigheter som nyckelvalvet behöver för att komma åt lagringskontot när du [har förberett Azure](tutorial-prepare-hyper-v.md#prepare-azure) för Hyper-V VM-utvärdering och migrering. 


## <a name="track-and-monitor"></a>Spåra och övervaka


- När du klickar på **Replikera** börjar ett Start Replication-jobb. 
- När startreplikeringsjobbet har slutförts börjar datorerna sin första replikering till Azure.
- När den första replikeringen är klar börjar deltareplikeringen. Inkrementella ändringar på lokala diskar replikeras regelbundet till Azure.

Du kan spåra jobbstatus i portalmeddelandena.

Du kan övervaka replikeringsstatus genom att klicka på **Replikera servrar** i **Azure Migrate: Servermigrering**.
![Övervaka replikering](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Kör en testmigrering


När deltareplikeringen börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator, innan du migrerar den.

- Köra en testmigrering kontrollerar att migreringen fungerar som förväntat, utan att påverka de lokala datorerna, som förblir i drift, och fortsätta replikera. 
- Testmigrering simulerar migreringen genom att skapa en Virtuell Azure-dator med replikerade data (migrera vanligtvis till ett Azure VNet som inte är produktion i din Azure-prenumeration).
- Du kan använda det replikerade testet Azure VM för att validera migreringen, utföra apptestning och åtgärda eventuella problem innan fullständig migrering.

Gör en testmigrering på följande sätt:


1. I **Migreringsmål** > **Servrar** > Azure**Migrate: Servermigrering**, klicka på Testa **migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. I **Testmigrering**väljer du det virtuella Azure-nätverk där Den virtuella Azure-datorn finns efter migreringen. Vi rekommenderar att du använder ett virtuellt nätverk som inte är produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har verifierat att testmigrering fungerar som förväntat kan du migrera de lokala datorerna.

1. Klicka på **Replikera servrar**i Azure Migrate-projektet > **Servrar** > **Azure Migrate: Servermigrering**.

    ![Servrarna replikeras](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3.  > Välj **Migrate** >  **Ja****OK**i Migrera Stäng av virtuella datorer och utför en**planerad migrering utan dataförlust**.
    - Som standard stänger Azure Migrate av den lokala virtuella datorn och kör en replikering på begäran som synkroniserar eventuella VM-ändringar som har inträffat sedan den senaste replikeringen utfördes. Detta säkerställer att ingen dataförlust sker.
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**
4. Ett migreringsjobb startas för den virtuella datorn. Spåra jobbet i Azure-meddelanden.
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan **Virtual Machines**.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är klar högerklickar du på den virtuella datorn > **Stoppa migreringen**. Det här gör följande:
    - Stoppar replikeringen för den lokala datorn.
    - Tar bort datorn från antalet **replikerande servrar** i Azure Migrate: Servermigrering.
    - Rensar replikeringstillståndsinformation för den virtuella datorn.
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
-  Överväg att distribuera [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) för att övervaka användning och utgifter.


## <a name="next-steps"></a>Nästa steg

Undersök [molnmigreringsresan](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud Adoption Framework.
