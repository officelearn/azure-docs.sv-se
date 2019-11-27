---
title: Migrera virtuella Hyper-V-datorer till Azure med Azure Migrate Server-migrering
description: Lär dig hur du migrerar lokala virtuella Hyper-V-datorer till Azure med Azure Migrate Server-migrering
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: be5d519269739f09b4a4264292f578b1d7051d26
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196320"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrera virtuella Hyper-V-datorer till Azure 

Den här artikeln visar hur du migrerar lokala virtuella Hyper-V-datorer till Azure med hjälp av återställning utan agent med Migreringsverktyg för Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och privata/offentliga virtuella moln datorer till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV).

Den här självstudien är den tredje i en serie som visar hur du kan utvärdera och migrera Hyper-V till Azure med hjälp av Azure Migrate Server bedömning och migrering. I den här guiden får du lära dig att:


> [!div class="checklist"]
> * Förbereda Azure och din lokala Hyper-V-miljö
> * Konfigurera käll miljön och distribuera en replikeringsprincip.
> * Konfigurera målmiljön.
> * Aktivera replikering.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar de här självstudierna bör du:

1. [Granska](hyper-v-migration-architecture.md) arkitekturen för Hyper-V-migrering.
2. [Slutför den första självstudien](tutorial-prepare-hyper-v.md) i den här serien för att konfigurera Azure och Hyper-V för migrering. I den första självstudien får du:
    - [Förbered Azure](tutorial-prepare-hyper-v.md#prepare-azure) för migrering.
    - [Förbered den lokala miljön](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) för migrering.
3. Vi rekommenderar att du provar att utvärdera virtuella Hyper-V-datorer med hjälp av Azure Migrate Server utvärdering innan du migrerar dem till Azure. Det gör du genom att [slutföra den andra självstudien](tutorial-assess-hyper-v.md) i den här serien. Även om vi rekommenderar att du testar en utvärdering behöver du inte köra en utvärdering innan du migrerar virtuella datorer.
4. Se till att ditt Azure-konto har tilldelats rollen virtuell dator deltagare, så att du har behörighet att:

    - Skapa en virtuell dator i den valda resursgruppen.
    - Skapa en virtuell dator i det valda virtuella nätverket.
    - Skriv till en Azure-hanterad disk.   
5. [Konfigurera ett Azure-nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). När du migrerar till Azure är de skapade virtuella Azure-datorerna anslutna till ett Azure-nätverk som du anger när du konfigurerar migrering.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Lägg till verktyget för migrering av Azure Migrate Server

Om du inte följer den andra självstudien för att utvärdera virtuella Hyper-V-datorer, måste du [följa de här anvisningarna](how-to-add-tool-first-time.md) för att konfigurera ett Azure Migrate projekt och lägga till verktyget för migrering av Azure Migrate server till projektet.

Om du har följt den andra själv studie kursen och redan har en Azure Migrate projekt konfiguration lägger du till Migreringsverktyg för Azure Migrate server på följande sätt:

1. Klicka på **Översikt**i Azure Migrate projektet. 
2. I **identifiera, utvärdera**och migrera servrar klickar du på **utvärdera och migrera servrar**.
3. I **Migreringsverktyg**väljer **du klicka här för att lägga till ett Migreringsverktyg när du är redo att migrera**.

    ![Välj ett verktyg](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. I listan verktyg väljer du **Azure Migrate:** verktyget Migreringsverktyg > **Lägg till**

    ![Migreringsverktyg för Server](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurera Azure Migrate-enheten

Azure Migrate Server-migrering kör en förenklad Hyper-V VM-enhet.

- Installationen utför VM-identifiering och skickar VM-metadata och prestanda data till Azure Migrate Server-migrering.
- Samma apparat används också av verktyget för Azure Migrate Server bedömning.

Så här konfigurerar du installationen av enheten:
- Om du följde den andra självstudien för att utvärdera virtuella Hyper-V-datorer, har du redan konfigurerat installationen under den självstudien.
- Om du inte följer den själv studie kursen måste du konfigurera installationen nu. Gör så här: 

    - Ladda ned en komprimerad virtuell Hyper-V-hårddisk från Azure Portal.
    - Skapa installationen och kontrol lera att den kan ansluta till Azure Migrate Server utvärdering. 
    - Konfigurera enheten för första gången och registrera den med Azure Migrate-projektet.

    Följ anvisningarna i [den här artikeln](how-to-set-up-appliance-hyper-v.md) för att konfigurera installationen.

## <a name="prepare-hyper-v-hosts"></a>Förbereda Hyper-V-värdar

1. I **Azure Migrate: Server-migrering**i Azure Migrate Project >- **servrar**klickar du på **identifiera**.
2. I **identifiera datorer** > **dina datorer virtualiserade? väljer du** **Ja, med Hyper-V**.
3. I **mål region**väljer du den Azure-region som du vill migrera datorerna till.
6. Välj **Bekräfta att mål regionen för migrering är regions namn**.
7. Klicka på **Skapa resurser**. Detta skapar ett Azure Site Recovery valv i bakgrunden.
    - Om du redan har konfigurerat migrering med Azure Migrate Server-migreringen visas inte det här alternativet eftersom resurserna tidigare har kon figurer ATS.
    - Du kan inte ändra mål region för projektet när du har klickat på den här knappen.
    - Alla efterföljande migreringar är till den här regionen.
    
8. I **förbereda Hyper-v-värdservern**laddar du ned providern för Hyper-v-replikering och registrerings nyckel filen.
    - Registrerings nyckeln krävs för att registrera Hyper-V-värden med Azure Migrate Server-migrering.
    - Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Hämta Provider och nyckel](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Kopiera installations filen för providern och registrerings nyckel filen till varje Hyper-V-värd (eller klusternod) som kör virtuella datorer som du vill replikera.
5. Kör installations filen för providern på varje värd, enligt beskrivningen i nästa procedur.
6. När du har installerat providern på värdarna i **identifiera datorer**klickar du på **Slutför registrering**.

    ![Slutför registrering](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Det kan ta upp till 15 minuter att slutföra registreringen tills identifierade virtuella datorer visas i Azure Migrate Server-migrering. När virtuella datorer identifieras ökar antalet **identifierade servrar** .

![Identifierade servrar](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Registrera Hyper-V-värdar

Installera den nedladdade installations filen (AzureSiteRecoveryProvider. exe) på varje relevant Hyper-V-värd.

1. Kör installations filen för providern på varje värd eller klusternod.
2. I guiden Konfigurera Provider > **Microsoft Update**väljer du att använda Microsoft Update för att söka efter leverantörs uppdateringar.
3. I **installationen**accepterar du standard installations platsen för providern och agenten och väljer **Installera**.
4. Efter installationen, i registrerings guiden > **valv inställningar**, väljer du **Bläddra**och i **nyckel fil**väljer du den valv nyckel fil som du laddade ned.
5. I **proxyinställningar**anger du hur providern som körs på värden ansluter till Internet.
    - Om enheten finns bakom en proxyserver måste du ange proxyinställningar.
    - Ange namnet på proxyn som **http://ip-address** eller **http://FQDN** . HTTPS-proxyservrar stöds inte.
   

6. Kontrol lera att providern kan komma åt de [nödvändiga URL: erna](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access).
7. Klicka på **Slutför**när värden har registrerats i **registreringen**.

## <a name="replicate-hyper-v-vms"></a>Replikera virtuella Hyper-V-datorer

När identifieringen är klar kan du påbörja replikering av virtuella Hyper-V-datorer till Azure.

> [!NOTE]
> Du kan replikera upp till 10 datorer tillsammans. Om du behöver replikera mer replikerar du dem samtidigt i batchar med 10.

1. I Azure Migrate Project >- **servrar** **Azure Migrate: Server-migrering**klickar du på **Replikera**.
2. I **Replikera**, > **käll inställningar** > **att datorerna har virtualiserats?** väljer du **Ja, med Hyper-V**. Klicka sedan på **Nästa: virtuella datorer**.
3. I **Virtuella datorer** väljer du de datorer som du vill replikera.
    - Om du har kört en utvärdering av de virtuella datorerna, kan du tillämpa storleksändring av virtuella datorer och disktypsrekommendationer (premium/standard) från utvärderingsresultaten. Gör detta i **Vill du importera migreringsinställningar från en Azure Migrate-utvärdering?** och välj alternativet **Ja**.
    - Om du inte har kört någon utvärdering, eller om du inte vill använda utvärderingsinställningarna, väljer du alternativet **Nej**.
    - Om du valde att använda utvärderingen väljer du VM-grupp och utvärderingsnamn.

        ![Välj utvärdering](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. I **Virtuella datorer** söker du efter önskade datorer och markerar varje virtuell dator som du vill migrera. Klicka sedan på **Nästa: mål inställningar**.

    ![Välj virtuella datorer](./media/tutorial-migrate-hyper-v/select-vms.png)

5. I **mål inställningar**väljer du den mål region dit du vill migrera, prenumerationen och resurs gruppen där de virtuella Azure-datorerna ska finnas efter migreringen.
7. I **lagrings konto för replikering**väljer du det Azure Storage-konto där replikerade data ska lagras i Azure.
8. **Virtual Network**väljer du det virtuella Azure-nätverk som de virtuella Azure-datorerna ska kopplas till efter migreringen.
9. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka sedan på **Nästa**.

    ![Mål inställningar](./media/tutorial-migrate-hyper-v/target-settings.png)

10. I **Compute** granskar du namnet på den virtuella datorn, storlek, disktyp för operativsystemet och tillgänglighetsuppsättningen. De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **VM-storlek**: om du använder utvärderings rekommendationer kommer List rutan VM-storlek att innehålla den rekommenderade storleken. Annars väljer Azure Migrate en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**. 
    - **OS-disk**: Ange OS-disken (start) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns. 
    - **Tillgänglighets uppsättning**: om den virtuella datorn ska finnas i en Azure-tillgänglighets uppsättning efter migreringen anger du uppsättningen. Uppsättningen måste finnas i målets resursgrupp som du anger för migreringen.

    ![Inställningar för VM-beräkning](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. I **Diskar** anger du om VM-diskarna ska replikeras till Azure och disktypen (standard SSD/HDD eller premiumhanterade diskar) i Azure. Klicka sedan på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar kommer de inte att synas i den virtuella Azure-datorn efter migreringen. 

    ![Diskar](./media/tutorial-migrate-hyper-v/disks.png)

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
- När den inledande replikeringen har slutförts börjar delta-replikeringen. Stegvisa ändringar av lokala diskar replikeras regelbundet till Azure.

Du kan spåra jobb status i Portal meddelanden.

Du kan övervaka replikeringsstatus genom att klicka på **Replikera servrar** i **Azure Migrate: Server-migrering**.
![övervaka replikering](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Kör en testmigrering


När delta-replikering börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- Genom att köra en test-migrering kontrollerar du att migreringen fungerar som förväntat, utan att det påverkar de lokala datorerna, som fortsätter att fungera och fortsätter att replikeras. 
- Testmigrering simulerar migreringen genom att skapa en virtuell Azure-dator med replikerade data (vanligt vis migrera till ett virtuellt nätverk som inte är i Azure-prenumerationen).
- Du kan använda den replikerade virtuella Azure-datorn för att verifiera migreringen, utföra app-testning och åtgärda eventuella problem före fullständig migrering.

Gör en testmigrering enligt följande:


1. I **mål för migrering** > **servrar** > **Azure Migrate: Server migrering**klickar du på **test migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigreringen fungerar som förväntat kan du migrera de lokala datorerna.

1. I Azure Migrate Project >- **servrar** > **Azure Migrate: Server-migrering**klickar du på **Replikera servrar**.

    ![Servrarna replikeras](./media/tutorial-migrate-hyper-v/replicate-servers.png)

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
