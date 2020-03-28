---
title: Migrera virtuella virtuella datorer med VMware-agentlösa Azure Migrerad servermigrering
description: Lär dig hur du kör en agentlös migrering av virtuella datorer med Virtuella datorer med Azure Migrate.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 825d6ff16a1f51fa476541ee10fea5f8a1c2972e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78304216"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrera virtuella virtuella datorer med VMware till Azure (agentless)

Den här artikeln visar hur du migrerar lokala virtuella datorer med VMware till Azure med hjälp av agentlös migrering med verktyget Migrera server för Azure.This article shows how to migrate on-premises VMware VMs to Azure, using agentless migration with the Azure Migrate Server Migration tool.

[Azure Migrate](migrate-services-overview.md) är en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbetsbelastningar samt AWS/GCP VM-instanser till Azure. Hubben tillhandahåller Azure Migrate-verktyg för utvärdering och migrering samt ISV-erbjudanden (Independent Software Vendor) från tredje part.

Den här självstudien är den tredje i en serie som visar hur du bedömer och migrerar virtuella virtuella datorer med VMware till Azure med Azure Migrate Server Assessment and Migration. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Förbered virtuella datorer för migrering.
> * Lägg till verktyget för migrering av Azure-migreringsserver.
> * Upptäck virtuella datorer som du vill migrera.
> * Börja replikera virtuella datorer.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig vm-migrering.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.

## <a name="migration-methods"></a>Migreringsmetoder

Du kan migrera virtuella virtuella datorer med VMware till Azure med hjälp av verktyget Migrera servermigrering av Azure.You can migrate VMware VMs to Azure using the Azure Migrate Server Migration tool. Det här verktyget erbjuder ett par alternativ för VMware VM-migrering:

- Migrering med hjälp av agentlös replikering. Migrera virtuella datorer utan att behöva installera något på dem.
- Migrering med en agent för replikering. Installera en agent på den virtuella datorn för replikering.

Om du vill bestämma om du vill använda agentless eller agent-baserad migrering läser du följande artiklar:

- [Lär dig hur](server-migrate-overview.md) agentless migrering fungerar och [jämför migreringsmetoder](server-migrate-overview.md#compare-migration-methods).
- [Läs den här artikeln](tutorial-migrate-vmware-agent.md) om du vill använda den agentbaserade metoden.

## <a name="prerequisites"></a>Krav

Innan du börjar de här självstudierna bör du:

1. [Slutför den första självstudien](tutorial-prepare-vmware.md) i den här serien för att konfigurera Azure och VMware för migrering. I den här guiden måste du:
    - [Förbered Azure](tutorial-prepare-vmware.md#prepare-azure) för migrering.
    - [Förbered den lokala miljön](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) för migrering.
    
2. Vi rekommenderar att du försöker bedöma virtuella virtuella datorer med Azure Migrate Server Assessment innan du migrerar dem till Azure. Om du vill ställa in bedömning [slutför du den andra självstudien](tutorial-assess-vmware.md) i den här serien. Om du inte vill bedöma virtuella datorer kan du hoppa över den här självstudien. Även om vi rekommenderar att du provar en utvärdering, men du behöver inte köra en utvärdering innan du försöker en migrering.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Lägga till verktyget migrera server för Azure-migrera server

Om du inte följde den andra självstudien för att bedöma virtuella datorer med VMware måste du [följa dessa instruktioner](how-to-add-tool-first-time.md) som konfigurerar ett Azure Migrate-projekt och välja verktyget Azure Migrate Server Migration. 

Om du har följt den andra självstudien och redan har konfigurerat ett Azure Migrate-projekt lägger du till verktyget För migrera server på följande sätt:

1. Klicka på **Översikt**i Azure Migrate-projektet . 
2. Klicka på Utvärdera och migrera servrar i **identifiera, bedöma och migrera** **servrar.**

     ![Bedöma och migrera servrar](./media/tutorial-migrate-vmware/assess-migrate.png)

3. I **Migreringsverktyg**väljer du **Klicka här om du vill lägga till ett migreringsverktyg när du är redo att migrera**.

    ![Välj ett verktyg](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Välj **Azure Migrate: Servermigreringstillägg** > **Add tool** i verktygslistan

    ![Verktyget Servermigrering](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurera Azure Migrate-enheten

Azure Migrate Server Migration kör en lätt VMware VM-apparat. Installationen av virtuella datorer utför vm-identifiering och skickar VM-metadata och prestandadata till Azure Migrate Server Migration. Samma installation används också av verktyget Azure Migrate Server Assessment.

Om du följde den andra självstudien för att bedöma virtuella datorer med VMware har du redan ställt in apparaten under den självstudien. Om du inte följde den självstudien måste du konfigurera apparaten nu. För att göra detta, du: 

- Hämta en OVA-mallfil och importera den till vCenter Server.
- Skapa installationen och kontrollera att den kan ansluta till Azure Migrate Server Assessment. 
- Konfigurera installationen för första gången och registrera den med Azure Migrate-projektet.

Följ instruktionerna i [den här artikeln](how-to-set-up-appliance-vmware.md) för att ställa in apparaten.


## <a name="prepare-vms-for-migration"></a>Förbereda virtuella datorer för migrering

Azure Migrate kräver vissa vm-ändringar för att säkerställa att virtuella datorer kan migreras till Azure.

- För vissa operativsystem gör Azure Migrate dessa ändringar automatiskt. [Läs mer](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)
- Om du migrerar en virtuell dator som inte har något av dessa operativsystem följer du instruktionerna för att förbereda den virtuella datorn.
- Det är viktigt att du gör dessa ändringar innan du påbörjar migreringen. Om du migrerar den virtuella datorn innan du gör ändringen kanske den virtuella datorn inte startar upp i Azure.
- Konfigurationsändringar som du gör på lokala virtuella datorer replikeras till Azure när replikeringen för den virtuella datorn har aktiverats. Se till att ändringarna replikeras genom att se till att återställningspunkten som du migrerar till är senare än den tidpunkt då konfigurationsändringarna gjordes lokalt.


### <a name="prepare-windows-server-vms"></a>Förbereda virtuella windows server-datorer

**Åtgärd** | **Detaljer** | **Instruktioner**
--- | --- | ---
Kontrollera att Windows-volymer i Azure VM använder samma enhetsbeteckningstilldelningar som den lokala virtuella datorn. | Konfigurera SAN-principen som online alla. | 1. Logga in på den virtuella datorn med ett administratörskonto och öppna ett kommandofönster.<br/> 2. Skriv **diskdel** för att köra diskpartverktyget.<br/> 3. Skriv **SAN POLICY=OnlineAll**<br/> 4. Skriv Avsluta för att lämna Diskpart och stäng kommandotolken.
Aktivera Azure serial access-konsol för Azure VM | Detta hjälper till med felsökning. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn startar med diskavbildningen och det motsvarar en omstart för den nya virtuella datorn. | Följ [dessa instruktioner](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) för att aktivera.
Installera Hyper-V-gästintegrering | Om du migrerar datorer som kör Windows Server 2003 installerar du Hyper-V Guest Integration Services på operativsystemet VM. | [Läs mer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Fjärrskrivbord | Aktivera Fjärrskrivbord på den virtuella datorn och kontrollera att Windows-brandväggen inte blockerar åtkomst till fjärrskrivbord i några nätverksprofiler. | [Läs mer](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Förbereda virtuella Linux-datorer

**Åtgärd** | **Detaljer** 
--- | --- | ---
Installera Hyper-V Linux Integration Services | De flesta nya versioner av Linux-distributioner har detta inkluderat som standard.
Återskapa Linux-init-avbildningen så att den innehåller de nödvändiga Hyper-V-drivrutinerna | Detta säkerställer att den virtuella datorn startar i Azure och krävs endast på vissa distributioner.
Aktivera loggning av Azure-seriekonsol | Detta hjälper till med felsökning. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn startar med diskavbildningen och det motsvarar en omstart för den nya virtuella datorn.<br/> Följ [dessa instruktioner](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) för att aktivera.
Uppdatera enhetsmappningsfil | Uppdatera enhetsmappningsfilen som har enhetsnamnet till volymassociationer för att använda beständiga enhetsidentifierare
Uppdatera fstab-poster | Uppdatera poster om du vill använda beständiga volymidentifierare.
Ta bort udev-regeln | Ta bort alla udev regler som reserverar gränssnittsnamn baserat på Mac-adress etc.
Uppdatera nätverksgränssnitt | Uppdatera nätverksgränssnitt för att ta emot IP-adress baserat på DHCP.
Aktivera ssh | Se till att Ssh är aktiverat och sshd-tjänsten är inställd på att starta automatiskt vid omstart.<br/> Kontrollera att inkommande ssh-anslutningsbegäranden inte blockeras av OS-brandväggen eller skriptbara regler.

[Följ den här artikeln](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) som beskriver dessa steg för att köra en Virtuell Linux-dator på Azure och inkludera instruktioner för några av de populära Linux-distributionerna.  


## <a name="replicate-vms"></a>Replikera virtuella datorer

När identifieringen är klar kan du påbörja replikeringen av virtuella VMware-datorer till Azure. 

> [!NOTE]
> Du kan replikera upp till 10 maskiner tillsammans. Om du behöver replikera mer replikeras de samtidigt i batchar om 10. För agentlös migrering kan du köra upp till 100 samtidiga replikeringar.

1. I Azure Migrate-projektet > **Servrar**klickar **Azure Migrate: Servermigrering**, på **Replikera**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-vmware/select-replicate.png)

2. I **Replikera** > **Källinställningar** > **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere**.
3. I **Lokal dator** väljer du namnet på den Azure Migrate-dator som du konfigurerar > **OK**. 

    ![Källinställningar](./media/tutorial-migrate-vmware/source-settings.png)

    - Det här steget förutsätter att du redan har ställt in en apparat när du har slutfört självstudien.
    - Om du inte har konfigurerat en apparat följer du instruktionerna i den [här artikeln](how-to-set-up-appliance-vmware.md).

4. I **Virtuella datorer** väljer du de datorer som du vill replikera.
    - Om du har kört en utvärdering av de virtuella datorerna, kan du tillämpa storleksändring av virtuella datorer och disktypsrekommendationer (premium/standard) från utvärderingsresultaten. Gör detta i **Vill du importera migreringsinställningar från en Azure Migrate-utvärdering?** och välj alternativet **Ja**.
    - Om du inte har kört någon utvärdering, eller om du inte vill använda utvärderingsinställningarna, väljer du alternativet **Nej**.
    - Om du valde att använda utvärderingen väljer du VM-grupp och utvärderingsnamn.

    ![Välj utvärdering](./media/tutorial-migrate-vmware/select-assessment.png)

5. I **Virtuella datorer** söker du efter önskade datorer och markerar varje virtuell dator som du vill migrera. Klicka sedan på **Nästa: Målinställningar**.

    ![Välj virtuella datorer](./media/tutorial-migrate-vmware/select-vms.png)

6. I **Målinställningar** väljer du prenumeration och den målregion som du vill migrera till. Ange sedan den resursgrupp där du vill att de virtuella Azure-datorerna ska finnas efter migreringen. I **Virtuellt nätverk** väljer du det Azure VNet/undernät som de virtuella Azure-datorerna ska anslutas till efter migreringen.
7. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka sedan på **Nästa**.

    ![Målinställningar](./media/tutorial-migrate-vmware/target-settings.png)

8. I **Compute** granskar du namnet på den virtuella datorn, storlek, disktyp för operativsystemet och tillgänglighetsuppsättningen. De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **VM-storlek:** Om du använder utvärderingsrekommendationer innehåller listrutan vm-storlek den rekommenderade storleken. Annars väljer Azure Migrate en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**. 
    - **OS-disk:** Ange OS-disken (start) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns. 
    - **Tillgänglighetsuppsättning**: Om den virtuella datorn ska vara i en Azure-tillgänglighetsuppsättning efter migreringen anger du uppsättningen. Uppsättningen måste finnas i målets resursgrupp som du anger för migreringen.

    ![Beräkningsinställningar för virtuella datorer](./media/tutorial-migrate-vmware/compute-settings.png)

9. I **Diskar** anger du om VM-diskarna ska replikeras till Azure och disktypen (standard SSD/HDD eller premiumhanterade diskar) i Azure. Klicka sedan på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar kommer de inte att synas i den virtuella Azure-datorn efter migreringen. 

    ![Diskar](./media/tutorial-migrate-vmware/disks.png)

10. I **Granska och starta replikering** kontrollerar du inställningarna och klickar på **Replikera** för att påbörja den första replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningarna när som helst innan replikeringen startar i **Hantera** > **replikeringsdatorer**. Det går inte att ändra inställningarna efter att replikeringen har startat.

### <a name="provisioning-for-the-first-time"></a>Etablering för första gången

Om detta är den första virtuella datorn som du replikerar i Azure Migrate-projektet, etablerar Azure Migrate Server Migration automatiskt dessa resurser i samma resursgrupp som projektet.

- **Servicebuss**: Azure Migrera servermigrering använder servicebussen för att skicka replikeringsorkestreringsmeddelanden till installationen.
- **Gateway storage account**: Server Migration använder gateway storage-kontot för att lagra tillståndsinformation om de virtuella datorer som replikeras.
- **Logglagringskonto:** Azure Migrate-installationen överför replikeringsloggar för virtuella datorer till ett logglagringskonto. Azure Migrate tillämpar replikeringsinformationen på replikhanterade diskar.
- **Nyckelvalv:** Azure Migrate-enheten använder nyckelvalvet för att hantera anslutningssträngar för servicebussen och komma åt nycklar för lagringskonton som används i replikering. Du bör ha ställt in de behörigheter som nyckelvalvet behöver för att komma åt lagringskontot när du förberedde. [Granska dessa behörigheter](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault).   


## <a name="track-and-monitor"></a>Spåra och övervaka


- När du klickar på **Replikera** börjar ett Start Replication-jobb. 
- När startreplikeringsjobbet har slutförts börjar datorerna sin första replikering till Azure.
- Under den första replikeringen skapas en ögonblicksbild av den virtuella datorn. Diskdata från ögonblicksbilden replikeras till replikhanterade diskar i Azure.
- När den första replikeringen är klar börjar deltareplikeringen. Inkrementella ändringar på lokala diskar replikeras regelbundet till replikdiskarna i Azure.

Du kan spåra jobbstatus i portalmeddelandena.

Du kan övervaka replikeringsstatus genom att klicka på **Replikera servrar** i **Azure Migrate: Servermigrering**.
![Övervaka replikering](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Kör en testmigrering


När deltareplikeringen börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator, innan du migrerar den.

- Köra en testmigrering kontrollerar att migreringen fungerar som förväntat, utan att påverka de lokala datorerna, som förblir i drift, och fortsätta replikera. 
- Testmigrering simulerar migreringen genom att skapa en Virtuell Azure-dator med replikerade data (migrera vanligtvis till ett icke-produktions-virtuellt nätverk i din Azure-prenumeration).
- Du kan använda det replikerade testet Azure VM för att validera migreringen, utföra apptestning och åtgärda eventuella problem innan fullständig migrering.

Gör en testmigrering på följande sätt:


1. I **Migreringsmål** > **Servrar** > Azure**Migrate: Servermigrering**, klicka på Testa **migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-vmware/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har verifierat att testmigrering fungerar som förväntat kan du migrera de lokala datorerna.

1. Klicka på **Replikera servrar**i Azure Migrate-projektet > **Servrar** > **Azure Migrate: Servermigrering**.

    ![Servrarna replikeras](./media/tutorial-migrate-vmware/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3.  > Välj **Migrate** >  **Ja****OK**i Migrera Stäng av virtuella datorer och utför en**planerad migrering utan dataförlust**.
    - Som standard stänger Azure Migrate av den lokala virtuella datorn och kör en replikering på begäran som synkroniserar eventuella VM-ändringar som har inträffat sedan den senaste replikeringen utfördes. Detta säkerställer att ingen dataförlust sker.
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**
4. Ett migreringsjobb startas för den virtuella datorn. Spåra jobbet i Azure-meddelanden.
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan **Virtual Machines**.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är klar högerklickar du på den virtuella datorn > **Stoppa replikering**. Detta stoppar replikering för den lokala datorn och rensar upp replikeringstillståndsinformation för den virtuella datorn.
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
