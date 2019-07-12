---
title: Migrera lokala virtuella Hyper-V-datorer till Azure med Azure Migrate servermigrering | Microsoft Docs
description: Den här artikeln beskriver hur du migrerar lokala virtuella Hyper-V-datorer till Azure med Azure Migrate-servermigrering
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4a88e9dddd492d5c24698bcde8c3a1fd942eaf66
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854199"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrera virtuella Hyper-V-datorer till Azure 

Den här artikeln visar hur du migrerar lokala virtuella Hyper-V-datorer till Azure, med verktyget Azure Migrate servermigrering migrering utan agenter.

[Azure Migrate](migrate-services-overview.md) ger en central knutpunkt för att spåra identifiering, bedömning och migrering av dina lokala appar och arbetsbelastningar och privata/offentliga virtuella datorer till Azure. Hubben innehåller Azure Migrate verktyg för bedömning och migrering, samt tredjepartsprogram oberoende programvaruleverantörer (ISV)-erbjudanden.

Den här självstudien är den tredje delen i en serie som visar hur du utvärdera och migrera Hyper-V till Azure med hjälp av Azure Migrate Server bedömning och migrering. I den här guiden får du lära dig att:


> [!div class="checklist"]
> * Förbereda Azure och lokala Hyper-V-miljön
> * Konfigurera källmiljön och distribuera en installation för replikering.
> * Konfigurera mål-miljö...
> * Aktivera replikering.
> * Kör en testmigrering för att kontrollera att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar de här självstudierna bör du:

1. [Granska](migrate-architecture.md) arkitektur för Hyper-V-migrering.
2. [I första självstudiekursen](tutorial-prepare-hyper-v.md) i den här serien du ställer in Azure och Hyper-V för migrering. I den första kursen du:
    - [Förbereda Azure](tutorial-prepare-hyper-v.md#prepare-azure) för migrering.
    - [Förbered den lokala miljön](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) för migrering.
3. Vi rekommenderar att du försöker utvärdera Hyper-V-datorer med hjälp av Azure Migrate Server-utvärdering, innan du migrerar dem till Azure. Gör [i andra självstudiekursen](tutorial-assess-hyper-v.md) i den här serien. Vi rekommenderar att du kan prova att använda en utvärdering har du inte kör en utvärdering innan du migrerar virtuella datorer.
4. Se till att ditt Azure-konto har tilldelats rollen virtuell Datordeltagare så att du har behörighet att:

    - Skapa en virtuell dator i den valda resursgruppen.
    - Skapa en virtuell dator i det valda virtuella nätverket.
    - Skriva till en Azure hanterade diskar.   
5. [Konfigurera ett virtuellt Azure nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). När du migrerar till Azure måste är den skapade virtuella Azure-datorer anslutna till ett virtuellt Azure nätverk du anger när du ställer in migreringen.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Lägg till verktyget Azure Migrate-servermigrering

Om du inte följer den andra självstudien för att utvärdera Hyper-V-datorer, måste du [följer du dessa instruktioner](how-to-add-tool-first-time.md) ställa in ett Azure Migrate-projekt och lägga till Azure Migrate servermigrering tool i projektet.

Om du har följt den andra självstudien och redan har ett Azure Migrate-projekt installation, Lägg till Migreringsverktyget Azure Migrate Server enligt följande:

1. I Azure Migrate-projektet klickar du på **översikt**. 
2. I **Upptäck, utvärdera, och migrering servrar**, klickar du på **utvärdera och migrera servrar för**.
3. I **Migreringsverktyg**väljer **Klicka här för att lägga till ett migrationsverktyg när du är redo att migrera**.

    ![Välj ett verktyg](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Välj i verktygslistan över, **Azure Migrate: Servermigrering** > **Lägg till verktyget**

    ![Server-Migreringsverktyget](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurera Azure Migrate-installation

Azure Migrate servermigrering kör en enkel installation av Hyper-V-dator.

- Enheten som utför identifieringen av virtuella datorer och skickar VM metadata och prestanda till Azure Migrate-servermigrering.
- I samma installation används också av verktyget Azure Migrate Server-utvärdering.

Så här skapar installationen:
- Om du har följt den andra självstudien för att utvärdera Hyper-V-datorer bör har du redan konfigurerat installationen under självstudien.
- Om du inte gå igenom den självstudien, måste du konfigurera installationen nu. Gör du: 

    - Ladda ned en komprimerad Hyper-V virtuell Hårddisk från Azure-portalen.
    - Skapa och kontrollera att den kan ansluta till Azure Migrate Server-utvärdering. 
    - Konfigurera programmet för första gången och registrera den med Azure Migrate-projektet.

    Följ instruktionerna i [i den här artikeln](how-to-set-up-appliance-hyper-v.md) att konfigurera installationen.

## <a name="prepare-hyper-v-hosts"></a>Förbereda Hyper-V-värdar

1. I Azure Migrate-projektet > **servrar**i **Azure Migrate: Servermigrering**, klickar du på **identifiera**.
2. I **identifiera datorer** > **är dina datorer virtualiserade?** väljer **Ja, med Hyper-V**.
3. I **målregionen**, Välj den Azure-region som du vill migrera datorerna.
6. Välj **bekräftar du att målregionen för migrering är Regionsnamn**.
7. Klicka på **skapa resurser**. Detta skapar ett Azure Site Recovery-valv i bakgrunden.
    - Om du redan har konfigurerat migrering med Azure Migrate servermigrering visas inte det här alternativet eftersom resurser har ställts in tidigare.
    - Du kan inte ändra målregion för det här projektet när du klickar på den här knappen.
    - Alla efterföljande migreringar är till den här regionen.
    
8. I **förbereda Hyper-V-värdservrar**, ladda ned provider för Hyper-V-replikering och registreringsnyckelfilen.
    - Nyckel för tjänstregistrering behövs för att registrera Hyper-V-värden med Azure Migrate servermigrering.
    - Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Ladda ned providern och nyckel](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Kopiera installationsfilen för providern och registreringsnyckelfilen till varje Hyper-V-värd (eller klusternod) som kör virtuella datorer du vill replikera.
5. Kör providern installationsfilen på varje värd enligt beskrivningen i nästa procedur.
6. När du har installerat providern på värdar i **identifiera datorer**, klickar du på **Slutför registreringen**.

    ![Slutför registreringen](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Det kan ta upp till 15 minuter efter Slutför registreringen tills identifierade virtuella datorer visas i Azure Migrate servermigrering. Eftersom virtuella datorer har identifierats i **identifierade servrar** räkna ökar.

![Identifierade servrar](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Registrera Hyper-V-värdar

Installera den hämta installationsfilen (AzureSiteRecoveryProvider.exe) på varje relevant Hyper-V-värd.

1. Kör installationsfilen på varje värd eller ett kluster nod för providern.
2. I installationsguiden för providern > **Microsoft Update**, välja för att använda Microsoft Update för att söka efter uppdateringar för providern.
3. I **Installation**accepterar du standardinstallationsplatsen för providern och agenten och välj **installera**.
4. Efter installationen: i Registreringsguiden > **Valvinställningar**väljer **Bläddra**, och i **nyckelfilen**, väljer du valvnyckelfilen som du laddade ned.
5. I **proxyinställningar**, anger du hur providern som körs på värden ska ansluta till internet.
    - Om installationen finns bakom en proxyserver, måste du ange proxyinställningar.
    - Ange proxyserverns namn som **http://ip-address** , eller **http://FQDN** . HTTPS-proxyservrar stöds inte.
   

6. Se till att providern kan nå den [krävs URL: er](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access).
7. I **registrering**, när värden har registrerats klickar du på **Slutför**.

## <a name="replicate-hyper-v-vms"></a>Replikera virtuella Hyper-V-datorer

Med identifieringen har slutförts, kan du börja replikering av Hyper-V-datorer till Azure.

1. I Azure Migrate-projektet > **servrar**, **Azure Migrate: Servermigrering**, klickar du på **replikera**.
2. I **replikera**, > **datakällans inställningar** > **är dina datorer virtualiserade?** väljer **Ja, med Hyper-V**. Klicka sedan på **nästa: Virtuella datorer**.
3. I **virtuella datorer**, Välj de datorer som du vill replikera.
    - Om du har kört en utvärdering för de virtuella datorerna, kan du använda VM-storlek och disk typ (premium/standard) rekommendationerna från resultaten. Att göra detta i **importera inställningar från en Azure Migrate-utvärdering?** väljer den **Ja** alternativet.
    - Om du inte kör en utvärdering, eller om du inte vill använda inställningar för utvärdering, väljer den **nr** alternativ.
    - Om du har valt för att använda utvärderingen, Välj grupp virtuella datorer och namn på utvärdering.

        ![Välj utvärdering](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. I **virtuella datorer**, Sök efter virtuella datorer efter behov och kontrollera varje virtuell dator som du vill migrera. Klicka sedan på **nästa: Rikta inställningar**.

    ![Välj virtuella datorer](./media/tutorial-migrate-hyper-v/select-vms.png)

5. I **rikta inställningar**, väljer du den målregion som ska migreras, prenumerationen och resursgruppen där virtuella Azure-datorer kommer att finnas efter migreringen.
7. I **Replikeringslagringskonto**, Välj Azure storage-kontot som replikerade data lagras i Azure.
8. **Virtuellt nätverk**, Välj Azure virtuellt nätverk/undernät som virtuella Azure-datorer ska anslutas efter migreringen.
9. In **Azure Hybrid Benefit**:

    - Välj **nr** om du inte vill använda Azure Hybrid-förmånen. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas med aktiv Software Assurance eller Windows Server-prenumerationer och du vill använda förmånen med de datorer som du migrerar. Klicka sedan på **Nästa**.

    ![Målinställningar](./media/tutorial-migrate-hyper-v/target-settings.png)

10. I **Compute**, granska virtuella datorns namn, storlek, typ av OS-disk och tillgänglighetsuppsättning. Virtuella datorer måste överensstämma med [krav för Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **VM-storlek**: Om du använder rekommendationerna för innehåller VM-storlek listrutan den rekommenderade storleken. Annars hämtar Azure Migrate en storlek baserat på matchande poster i Azure-prenumeration. Du kan också välja en manuell storlek i **Azure VM-storlek**. 
    - **OS-disken**: Ange operativsystemdisken (Start) för den virtuella datorn. OS-disken är en disk som har operativsystemet startprogrammet och installationsprogrammet. 
    - **Tillgänglighetsuppsättning**: Om den virtuella datorn ska vara i en Azure tillgänglighetsuppsättning efter migreringen, ange en uppsättning. Uppsättningen måste finnas i målresursgruppen som du anger för migreringen.

    ![Inställningarna för beräkning av virtuella datorer](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. I **diskar**, anger du om VM-diskarna ska replikeras till Azure och välj disktyp (standard SSD-Hårddisk eller premium-hanterade diskar) i Azure. Klicka sedan på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar, inte på den virtuella Azure-datorn efter migreringen. 

    ![Diskar](./media/tutorial-migrate-hyper-v/disks.png)

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
- När den inledande replikeringen är klar startar deltareplikeringen. Inkrementella ändringar till lokala diskar replikeras med jämna mellanrum till Azure.

Du kan spåra status för återställningsjobb i portalen meddelanden.

Du kan övervaka replikeringsstatus genom att klicka på **replikera servrar** i **Azure Migrate: Servermigrering**.
![Övervakare för replikering](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Kör en testmigrering


När deltareplikeringen börjar kör du en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- Kör en testmigrering kontrollerar att migreringen fungerar som förväntat, utan att påverka de lokala datorerna som fortsätta att fungera och fortsätta med replikeringen. 
- Testmigrering simulerar migreringen genom att skapa en Azure-dator med hjälp av replikerade data (vanligtvis migrera till ett icke-produktion virtuellt nätverk i Azure-prenumerationen).
- Du kan använda testet replikerade virtuella Azure-datorer att validera migreringen testa appen och åtgärda eventuella problem innan fullständig migrering.

Gör en testmigrering på följande sätt:


1. I **migrering mål** > **servrar** > **Azure Migrate: Servermigrering**, klickar du på **Test migrera servrar**.

     ![Testa migrerade servrar](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Högerklicka på den virtuella datorn för att testa och klicka på **Test migrera**.

    ![Testmigrering](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. I **testmigrering**, Välj den virtuella Azure-nätverket där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett icke-produktion virtuellt nätverk.
4. Den **testa migrering** jobbet startar. Övervaka jobb i portalen meddelanden.
5. När migreringen är klar kan du visa den migrerade virtuella Azure-datorer i **virtuella datorer** i Azure-portalen. Namnet på datorn har ett suffix **-Test**.
6. När testet är klart, högerklickar du på Azure VM i **datorer replikeras**, och klicka på **Rensa testmigrering**.

    ![Rensa migrering](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigreringen fungerar som förväntat, kan du migrera lokala datorer.

1. I Azure Migrate-projektet > **servrar** > **Azure Migrate: Servermigrering**, klickar du på **replikera servrar**.

    ![Replikera servrar](./media/tutorial-migrate-hyper-v/replicate-servers.png)

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
