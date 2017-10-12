---
title: Replikera virtuella Hyper-V-datorer i VMM-moln till Azure | Microsoft Docs
description: "Dirigera replikering, redundans och återställning av virtuella Hyper-V-datorer hanterade i System Center VMM-moln till Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: 958b61f5de732a882e0a2682b8dd4e18504a6ae7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>Replikera virtuella Hyper-V-datorer i VMM-moln till Azure med hjälp av Site Recovery i Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-azure.md)
> * [Klassiska Azure](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell – Klassisk](site-recovery-deploy-with-powershell.md)


Den här artikeln beskriver hur du replikerar lokala virtuella Hyper-V-datorer som hanteras i System Center VMM-moln till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md)-tjänsten på Azure Portal.

När du har läst den här artikeln kan du lämna kommentarer längst ned på sidan eller på [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Läs mer i [den här artikeln](site-recovery-migrate-to-azure.md) om att migrera datorer till Azure (utan återställning).


## <a name="deployment-steps"></a>Distributionssteg

Följ den här artikeln för att slutföra de här distributionsstegen:


1. [Lär dig mer](site-recovery-components.md) om arkitekturen för den här distributionen. Dessutom kan du [lära dig mer om](site-recovery-hyper-v-azure-architecture.md) hur Hyper-V-replikering fungerar i Site Recovery.
2. Kontrollera krav och begränsningar.
3. Ställa in Azure-nätverk och lagringskonton.
4. Förbered den lokala VMM-servern och Hyper-V-värdar.
5. Skapa ett Recovery Services-valv. Valvet innehåller konfigurationsinställningar och styr replikeringen.
6. Ange inställningar för datakällan. Registrera VMM-servern i valvet. Installera Azure Site Recovery-providern på VMM-servern. Installera Recovery Services-agenten på Hyper-V-värdar.
7. Konfigurera inställningar för mål och replikering.
8. Aktivera replikering för virtuella Hyper-V-datorer.
9. Kör ett redundanstest för att kontrollera att allt fungerar som förväntat.



## <a name="prerequisites"></a>Krav


**Stöd för krav** | **Detaljer**
--- | ---
**Azure** | Lär dig om [Azure-krav](site-recovery-prereq.md#azure-requirements).
**Lokala servrar** | [Lär dig mer](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-azure) om krav för den lokala VMM-servern och Hyper-V-värdar.
**Lokala virtuella Hyper-V-datorer** | Virtuella datorer du vill replikera bör köra ett[operativsystem som stöds](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) och överensstämma med [krav för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**Azure-URL: er** | VMM-servern behöver åtkomst till dessa webbadresser:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Om du har IP-adressbaserade brandväggsregler ontrollerar du att de tillåter kommunikation till Azure.<br/></br> Tillåt [Azure Datacenter IP Ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653) (IP-intervall för Azures datacenter) och HTTPS-port 443.<br/></br> Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra (används för åtkomstkontroll och identitetshantering).


## <a name="prepare-for-deployment"></a>Förbereda för distribution
När du förbereder distributionen måste du:

1. [Skapa ett Azure-nätverk](#set-up-an-azure-network) som de virtuella Azure-datorerna ska anslutas till efter en redundansväxling.
2. [Skapa ett Azure-lagringskonto](#set-up-an-azure-storage-account) för replikerade data.
3. [Förbereda VMM-servern](#prepare-the-vmm-server) för Site Recovery-distribution.
4. Förbereda för nätverksmappning. Konfigurera nätverk så att du kan konfigurera nätverksmappning under Site Recovery-distributionen.

### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk
Du behöver ett Azure-nätverk dit de virtuella Azure-datorerna som skapats kan anslutas efter redundansväxlingen.

* Nätverket måste finnas på samma region som Recovery Services-valvet.
* Beroende på vilken resursmodell du vill använda för redundansväxlade virtuella Azure-datorer konfigurerar du Azure-nätverket i [Resource Manager-läge](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) eller [klassiskt läge](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
* Vi rekommenderar att du konfigurerar ett nätverk innan du börjar. Om du inte gör det måste du göra det under distributionen av Site Recovery.
Azure-nätverk som används av Site Recovery inte kan [flyttas](../azure-resource-manager/resource-group-move-resources.md) inom samma eller mellan olika prenumerationer.

### <a name="set-up-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto
* Du behöver en standard/premium Azure storage-konto för att lagra data som replikeras till Azure. [Premiumlagring](../storage/common/storage-premium-storage.md) används för virtuella datorer som behöver konsekvent hög i/o-prestanda och låg fördröjning till värden för i/o-intensiva arbetsbelastningar. Om du vill använda ett Premium Storage-konto för replikerade data konfigurerar du ytterligare ett standardlagringskonto för att lagra replikeringsloggar som samlar in löpande ändringar i lokala data. Kontot måste finnas i samma region som Recovery Services-valvet.
* Beroende på vilken resursmodell du vill använda för redundansväxlade virtuella Azure-datorer skapar du ett konto i [Resource Manager-läge](../storage/common/storage-create-storage-account.md) eller [klassiskt läge](../storage/common/storage-create-storage-account.md).
* Vi rekommenderar att du skapar ett konto innan du börjar. Om du inte gör det måste du göra det under distributionen av Site Recovery.
- Observera att lagringskonton som används av Site Recovery inte kan [flyttas](../azure-resource-manager/resource-group-move-resources.md) inom samma eller mellan olika prenumerationer.

### <a name="prepare-the-vmm-server"></a>Förbereda VMM-servern
* Kontrollera att VMM-servern uppfyller [kraven](#prerequisites).
* Under distributionen av Site Recovery kan du ange att alla moln på en VMM-server ska vara tillgängliga på Azure-portalen. Om du bara vill att specifika moln ska visas på portalen aktiverar du den inställningen i molnet i VMM-administrationskonsolen.

### <a name="prepare-for-network-mapping"></a>Förbereda för nätverksmappning
Du måste konfigurera nätverksmappning under distributionen av Site Recovery. Nätverksmappningen mappar mellan VM-källnätverken i VMM och Azure-målnätverken för att följande ska vara möjligt:

* Datorer som redundansväxlas i samma nätverk kan ansluta till varandra, även om de inte redundansväxlas på samma sätt eller i samma återställningsplan.
* Om en nätverksgateway har konfigurerats i Azure-målnätverket kan virtuella Azure-datorer ansluta till lokala virtuella datorer.
* När du ska konfigurera nätverksmappnig behöver du följande:

  * Se till att de virtuella datorerna på Hyper-V-källvärdservern är anslutna till ett VM-nätverk i VMM. Nätverket ska kopplas till ett logiskt nätverk som är associerat med molnet.
  * Ett Azure-nätverk så som det beskrivs [ovan](#set-up-an-azure-network)

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **New** > **Monitoring + Management** > **Backup and Site Recovery (OMS)**.

    ![Nytt valv](./media/site-recovery-vmm-to-azure/new-vault3.png)
3. I **Namn** anger du ett eget namn som identifierar valvet. Om du har mer än en prenumeration väljer du en av dem.
4. [Skapa en resursgrupp](../azure-resource-manager/resource-group-template-deploy-portal.md) eller välj en befintlig. Ange en Azure-region. Datorer replikeras till den här regionen. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/)
5. Du kan snabbt komma åt valvet från instrumentpanelen genom att klicka på **Fäst på instrumentpanelen** > **Skapa valv**.

    ![Nytt valv](./media/site-recovery-vmm-to-azure/new-vault.png)

Det nya valvet visas på **instrumentpanelen** > **Alla resurser** och på huvudbladet för **Recovery Services-valv**.


## <a name="select-the-protection-goal"></a>Välj skyddsmål

Välj vad och vart du vill replikera.

1. Välj valvet på **Recovery Services-valv**.
2. Under **Komma igång** klickar du på **Webbplatsåterställning** > **Förbereda infrastrukturen** > **Skyddsmål**.

    ![Välja mål](./media/site-recovery-vmm-to-azure/choose-goals.png)
3. I **Skyddsmål** väljer du **Till Azure** och sedan **Ja, med Hyper-V**. Välj **Ja** för att bekräfta att du hanterar Hyper-V-värdar och återställningsplatsen med hjälp av VMM. Klicka sedan på **OK**.

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Installera Azure Site Recovery-providern på VMM-servern och registrera servern i valvet. Installera Azure Recovery Services-agenten på Hyper-V-värdar.

1. Klicka på **Förbereda infrastrukturen** > **Källa**.

    ![Konfigurera källan](./media/site-recovery-vmm-to-azure/set-source1.png)

2. I **Förbered källa** klickar du på **+ VMM** för att lägga till en VMM-server.

    ![Konfigurera källan](./media/site-recovery-vmm-to-azure/set-source2.png)

3. På **Lägg till server** kontrollerar du att **System Center VMM-server** visas i **Servertyp** och att VMM-servern uppfyller [de allmänna kraven och URL-kraven](#prerequisites).
4. Ladda ned installationsfilen för Azure Site Recovery-providern.
5. Ladda ned registreringsnyckeln. Du behöver den när du kör installationsprogrammet. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/site-recovery-vmm-to-azure/set-source3.png)


## <a name="install-the-provider-on-the-vmm-server"></a>Installera providern på VMM-servern

1. Kör installationsfilen på VMM-servern för providern.
2. I **Microsoft Update** kan du välja uppdateringar så att provideruppdateringarna installeras i enlighet med din Microsoft Update-princip.
3. I **Installation** accepterar du eller ändrar standardinstallationsplatsen för providern och klickar på **Installera**.

    ![Installationsplats](./media/site-recovery-vmm-to-azure/provider2.png)
4. När installationen är klar klickar du på **Registrera** för att registrera VMM-servern i valvet.
5. På sidan **Valvinställningar** klickar du på **Bläddra** och väljer valvnyckelfilen. Ange Azure Site Recovery-prenumerationen och valvnamnet.

    ![Serverregistrering](./media/site-recovery-vmm-to-azure/provider10.PNG)
6. I **Internetanslutning** anger du hur providern som körs på VMM-servern ska ansluta till Site Recovery via internet.

   * Om du vill att providern ska ansluta direkt väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
   * Om din befintliga proxyserver kräver autentisering, eller om du vill använda en anpassad proxyserver, väljer du **Anslut till Azure Site Recovery med proxyserver**.
   * Om du använder en anpassad proxyserver anger du adressen, porten och autentiseringsuppgifterna.
   * Om du använder en proxyserver bör du redan ha tillåtit URL:erna som beskrivs i [krav](#on-premises-prerequisites).
   * Om du använder en anpassad proxyserver skapas ett RunAs-konto (DRAProxyAccount) i VMM automatiskt med de angivna proxyautentiseringsuppgifterna. Konfigurera proxyservern så att det här kontot kan autentiseras. Du kan ändra inställningarna för RunAs-kontot i VMM i VMM-konsolen. I **Inställningar** expanderar du **Säkerhet** > **Kör som-konton** och ändrar sedan lösenordet för DRAProxyAccount. Du måste starta om VMM-tjänsten så att den här inställningen börjar gälla.

     ![Internet](./media/site-recovery-vmm-to-azure/provider13.PNG)
7. Acceptera eller ändra platsen för ett SSL-certifikat som genereras automatiskt för datakryptering. Det här certifikatet används om du aktiverar datakryptering för ett moln som skyddas av Azure på Azure Site Recovery-portalen. Skydda det här certifikatet. När du kör en redundansväxling till Azure måste den dekrypteras om datakryptering är aktiverat.

    > [!NOTE]
    > Vi rekommenderar att du använder krypteringsfunktionen i Azure för att kryptera vilande data, i stället för att använda datakrypteringsalternativet i Azure Site Recovery. Krypteringsfunktionen i Azure kan aktiveras för ett lagringskonto och bidrar till bättre prestanda eftersom krypteringen/dekrypteringen hanteras av Azure Storage.
    > [Lär dig mer om kryptering med Storage-tjänsten från Azure](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption).
    
8. I **Servernamn** anger du ett eget namn som identifierar VMM-servern i valvet. I en klusterkonfiguration anger du namnet på VMM-klusterrollen.
9. Aktivera **Synkronisera molnmetadata** om du vill synkronisera metadata för alla moln på VMM-servern med valvet. Den här åtgärden behöver bara göras en gång på varje server. Om du inte vill synkronisera alla moln kan du lämna den här inställningen avmarkerad och synkronisera varje moln individuellt i molnegenskaperna i VMM-konsolen. Slutför processen genom att klicka på **Registrera**.

    ![Serverregistrering](./media/site-recovery-vmm-to-azure/provider16.PNG)
10. Registreringen startar. När registreringen är klar visas servern på bladet **Site Recovery-infrastruktur** > **VMM-servrar**.


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Installera Azure Recovery Services-agenten på Hyper-V-värdar

1. När du har konfigurerat providern måste du hämta installationsfilen för Azure Recovery Services-agenten. Kör installationsprogrammet på varje Hyper-V-server i VMM-molnet.

    ![Hyper-V-platser](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)
2. I **Kravkontroll**, klicka på **Nästa**. Alla nödvändiga komponenter som saknas installeras automatiskt.

    ![Krav för Recovery Services-agenten](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)
3. Godkänn eller ändra installationsplatsen och cachelagringsplatsen på **Installationsinställningar**. Du kan konfigurera cachen på en enhet som har minst 5 GB tillgängligt utrymme, men vi rekommenderar en cacheenhet med 600 GB eller mer ledigt utrymme. Klicka på **Installera**.
4. När installationen är klar klickar du på **Stäng** för att slutföra.

    ![Registrera MARS-agenten](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

### <a name="command-line-installation"></a>Kommandoradsinstallation
Du kan installera Microsoft Azure Recovery Services-agenten från kommandoraden med följande kommando:

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Konfigurera Internetåtkomst via en proxyserver till Site Recovery från Hyper-V-värdar

Recovery Services-agenten som körs på Hyper-V-värdar behöver Internetåtkomst till Azure för VM-replikering. Om du ansluter till Internet via en proxyserver konfigurerar du den så här:

1. Öppna snapin-modulen Microsoft Azure Backup MMC på Hyper-V-värden. Som standard finns det en genväg till Microsoft Azure Backup på skrivbordet eller i C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Klicka på **Ändra egenskaper** i snapin-modulen.
3. Ange information om proxyservern på fliken **Proxykonfiguration**.

    ![Registrera MARS-agenten](./media/site-recovery-vmm-to-azure/mars-proxy.png)
4. Kontrollera att agenten kan nå URL:erna som beskrivs i [kravavsnittet](#on-premises-prerequisites).

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön
Ange Azure-lagringskontot som ska användas för replikering och det Azure-nätverk som virtuella Azure-datorer ska ansluta till efter en redundansväxling.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den prenumeration och resursgrupp där du vill skapa de redundansväxlade virtuella datorerna. Välj den distributionsmodell som du vill använda i Azure (klassisk eller Resource Manager) för de redundansväxlade virtuella datorerna.

    ![Lagring](./media/site-recovery-vmm-to-azure/enablerep3.png)

2. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

    ![Lagring](./media/site-recovery-vmm-to-azure/compatible-storage.png)

3. Om du inte har skapat ett lagringskonto och vill skapa ett med hjälp av Resource Manager klickar du på **+Lagringskonto** för att göra det direkt.  På bladet **Skapa lagringskonto** anger du kontonamn, typ, prenumeration och plats. Kontot måste finnas på samma plats som Recovery Services-valvet.

   ![Lagring](./media/site-recovery-vmm-to-azure/gs-createstorage.png)


   * Om du vill skapa ett lagringskonto med hjälp av den klassiska modellen gör du det på Azure-portalen. [Läs mer](../storage/common/storage-create-storage-account.md)
   * Om du använder ett Premium Storage-konto för replikerade data konfigurerar du ytterligare ett standardlagringskonto för att lagra replikeringsloggar som samlar in löpande ändringar i lokala data.
5. Om du inte har skapat ett Azure-nätverk och vill skapa ett med hjälp av Resource Manager klickar du på **+Nätverk** för att göra det direkt. På bladet **Skapa virtuellt nätverk** anger du nätverksnamn, adressintervall, information om undernät, prenumeration och plats. Nätverket måste finnas på samma plats som Recovery Services-valvet.

   ![Nätverk](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

   Om du vill skapa ett nätverk med den klassiska modellen gör du det på Azure-portalen. [Läs mer](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>Konfigurera nätverksmappning

* [Läs](#prepare-for-network-mapping) en snabb överblick över vad nätverksmappning gör.
* Kontrollera att virtuella datorer på VMM-servern är anslutna till ett virtuellt datornätverk och att du har skapat minst ett virtuellt Azure-nätverk. Flera virtuella datornätverk kan mappas till ett enda Azure-nätverk.

Konfigurera mappning på följande sätt:

1. Under **Site Recovery-infrastruktur** > **Nätverksmappningar** > **Nätverksmappning** klickar du på ikonen **+Nätverksmappning**.

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

## <a name="configure-replication-settings"></a>Konfigurera replikeringsinställningar
1. Skapa en ny replikeringsprincip genom att klicka på **Förbered infrastruktur** > **Replikeringsinställningar** > **+Skapa och koppla**.

    ![Nätverk](./media/site-recovery-vmm-to-azure/gs-replication.png)
2. I **Princip för att skapa och koppla** anger du ett principnamn.
3. I **Kopieringsfrekvens** anger du hur ofta du vill replikera förändringsdata (delta) efter den första replikeringen (med 30 sekunders mellanrum, var femte minut eller varje kvart).

    > [!NOTE]
    >  En frekvens på 30 sekunder stöds inte när du replikerar till premiumlagring. Begränsningen bestäms av antalet ögonblicksbilder per blob (100) som stöds av premium-lagring. [Läs mer](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)

4. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden i antal timmar för varje återställningspunkt. Skyddade datorer kan återställas till valfri punkt inom en period.
5. I **Appkompatibel ögonblicksbildsfrekvens** anger du hur ofta (1–12 timmar) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas. Hyper-V använder två typer av ögonblicksbilder: en standardögonblicksbild som tillhandahåller en inkrementell ögonblicksbild av hela den virtuella datorn och en programkonsekvent ögonblicksbild som tar en ögonblicksbild vid en viss tidpunkt av programdata på den virtuella datorn. Programkonsekventa ögonblicksbilder använda VSS (Volume Shadow Copy Service) för att säkerställa att programmen är i ett konsekvent tillstånd när ögonblicksbilden tas. Observera att om du aktiverar programkonsekventa ögonblicksbilder så påverkar detta prestanda för program som körs på virtuella källdatorer. Kontrollera att värdet som du anger är mindre än antalet ytterligare återställningspunkter som du konfigurerar.
6. I **Starttid för inledande replikering** anger du när den inledande replikeringen ska börja. Replikeringen sker via Internetbandbredden så du kanske vill schemalägga den utanför kontorstid.
7. I **Kryptera data lagrade på Azure** anger du om du vill kryptera vilande data i Azure-lagring. Klicka sedan på **OK**.

    ![Replikeringsprincip](./media/site-recovery-vmm-to-azure/gs-replication2.png)
8. När du skapar en ny princip associeras den automatiskt med VMM-molnet. Klicka på **OK**. Du kan associera ytterligare VMM-moln (och de virtuella datorerna i dem) med den här replikeringsprincipen i **Replikering** > principnamn > **Associera VMM-moln**.

    ![Replikeringsprincip](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="capacity-planning"></a>Kapacitetsplanering

Nu när du har konfigurerat den grundläggande infrastrukturen är det dags att planera för kapaciteten och tänka igenom om du behöver ytterligare resurser.

Site Recovery tillhandahåller ett kapacitetsplaneringsverktyg som hjälper dig att allokera rätt resurser för din källmiljö, Site Recovery-komponenterna, nätverk och lagring. Du kan köra planeringsverktyget i snabbläge för att få uppskattningar baserat på det genomsnittliga antalet virtuella datorer, diskar och lagring eller i detaljerat läge där du anger siffrorna på arbetsbelastningsnivå. Innan du börjar:

* Samla in information om replikeringsmiljön, inklusive virtuella datorer, diskar per virtuell dator och lagringsutrymme per disk.
* Beräkna den dagliga förändringstakten (omsättningen) för replikerade data. [Kapacitetsplaneringsverktyget för Hyper-V-replikering](https://www.microsoft.com/download/details.aspx?id=39057) kan hjälpa dig med detta.

1. Klicka på **Ladda ned** för att ladda ned verktyget och köra det. [Läs artikeln](site-recovery-capacity-planner.md) som medföljer verktyget.
2. När du är klar väljer du **Ja** i **Har du kört Capacity Planner**?

   ![Kapacitetsplanering](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

   Lär dig mer om att [kontrollera nätverkets bandbredd](#network-bandwidth-considerations)




## <a name="enable-replication"></a>Aktivera replikering

Innan du börjar bör du kontrollera att ditt Azure-konto har de nödvändiga [behörigheterna](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) för att aktivera replikering för en ny virtuell dator till Azure.

Aktivera replikering på följande sätt:

1. Klicka på **Steg 2: Replikera program** > **Källa**. När du har aktiverat replikering för första gången klickar du på **+Replikera** i valvet för att aktivera replikering för ytterligare datorer.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/enable-replication1.png)
2. På bladet **Källa** väljer du den VMM-server och det moln som Hyper-V-värdarna finns i. Klicka sedan på **OK**.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/enable-replication-source.png)
3. I **Mål** väljer du prenumerationen, distributionsmodellen som används efter en redundansväxling och lagringskontot som du använder för replikerade data.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/enable-replication-target.png)
4. Välj lagringskontot som du vill använda. Om du vill använda ett annat lagringskonto än det som du har kan du [skapa ett](#set-up-an-azure-storage-account). Om du använder ett Premium Storage-konto för replikerade data måste du konfigurera ytterligare ett standardlagringskonto för att lagra replikeringsloggar som samlar in löpande ändringar i lokala data. För att skapa ett lagringskonto med resurshanterarmodellen klickar du på **Skapa ny**. Om du vill skapa ett lagringskonto med hjälp av den klassiska modellen gör du det [i Azure-portalen](../storage/common/storage-create-storage-account.md). Klicka sedan på **OK**.
5. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator. Om du vill använda ett annat nätverk än det som du har kan du [skapa ett](#set-up-an-azure-network). Skapa ett nätverk med hjälp av Resource Manager-modellen genom att klicka på **Skapa nytt**. Om du vill skapa ett nätverk med den klassiska modellen gör du det på [Azure-portalen](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Välj ett undernät om det behövs. Klicka sedan på **OK**.
6. I **Virtual Machines** > **Välj virtuella datorer** klickar du på och väljer de datorer som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/enable-replication5.png)

7. I **Egenskaper** > **Konfigurera egenskaper** väljer du operativsystemet för de valda virtuella datorerna och operativsystemdisken.

    - Kontrollera att namnet på den virtuella datorn i Azure (målnamnet) uppfyller [kraven för virtuella datorer i Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).   
    - Som standard markeras alla diskar på den virtuella datorn för replikering men du kan rensa diskar för att exkludera dem.

        - Du kanske vill utesluta diskar för att minska bandbredden för replikering. Till exempel kanske du inte vill replikera diskar med tillfälliga data eller data som uppdateras varje gång en dator eller ett program startar om (till exempel pagefile.sys eller tempdb för Microsoft SQL Server). Du kan undanta en disk från replikering genom att avmarkera den.
        - Endast standarddiskar kan undantas. Du kan inte undanta OS-diskar.
        - Vi rekommenderar att du inte undantar dynamiska diskar. Site Recovery kan inte identifiera om en virtuell hårddisk är en standarddisk eller dynamisk disk på den virtuella gästdatorn. Om alla beroende dynamiska volymdiskar inte undantas identifieras skyddade dynamiska diskar som felaktiga på den virtuella datorn vid redundansväxling, och data på disken kan inte nås.
        - När replikering har aktiverats kan du inte lägga till eller ta bort diskar för replikering. Om du vill lägga till eller undanta en disk måste du inaktivera skyddet för den virtuella datorn och sedan aktivera det igen.
        - Diskar som du skapar manuellt i Azure växlas inte tillbaka igen. Om du till exempel växlar över tre diskar och skapar två direkt på den virtuella datorn i Azure så kommer endast de tre diskarna som växlades över att växlas tillbaka från Azure till Hyper-V. Du kan inte ta med diskar som har skapats manuellt i redundansväxlingar eller omvända replikeringar från Hyper-V till Azure.
        - Om du undantar en disk som behövs för att ett program ska fungera efter redundansväxlingen till Azure måste du skapa den manuellt i Azure så att det replikerade programmet kan köras. Du kan också integrera Azure Automation i en återställningsplan för att skapa disken under en redundansväxling av datorn.

    Spara ändringarna genom att klicka på **OK**. Du kan ange ytterligare egenskaper senare.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)

8. I **Replikeringsinställningar** > **Konfigurera replikeringsinställningar** väljer du den replikeringsprincip som du vill använda för de skyddade virtuella datorerna. Klicka sedan på **OK**. Du kan ändra replikeringsprincipen under **Replikeringsprinciper** > principnamn > **Redigera inställningar**. De ändringar du gör används både för datorer som redan replikeras och för nya datorer.

   ![Aktivera replikering](./media/site-recovery-vmm-to-azure/enable-replication7.png)

Du kan följa förloppet för jobbet **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.

### <a name="view-and-manage-vm-properties"></a>Visa och hantera egenskaper för virtuella datorer

Vi rekommenderar att du kontrollerar egenskaperna för källdatorn. Kom ihåg att namnet på den virtuella Azure-datorn måste uppfylla [kraven för virtuella datorer i Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Gå till **Skyddade objekt**, klicka på **Replikerade objekt** och välj den dator som du vill visa information om.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/vm-essentials.png)
2. I **Egenskaper** kan du visa information om replikering och redundans för den virtuella datorn.

    ![Aktivera replikering](./media/site-recovery-vmm-to-azure/test-failover2.png)
3. I **Beräkning och nätverk** > **Beräkna egenskaper** kan du ange namnet och storleken på den virtuella Azure-datorn. Ändra namnet så att det uppfyller [kraven för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) om det behövs. Du kan också visa och ändra information om målnätverket, undernätet och IP-adressen som ska tilldelas den virtuella Azure-datorn.
Tänk på följande:

   * Du kan ange IP-måladressen. Om du inte anger någon adress använder den redundansväxlade datorn DHCP. Om du anger en adress som inte är tillgänglig under redundansväxlingen, misslyckas växlingen. Samma mål-IP-adress kan användas för att testa redundans om adressen är tillgänglig i nätverket för redundanstestet.
   * Antalet nätverkskort beror på storleken som du anger för den virtuella måldatorn enligt följande:

     * Om antalet nätverkskort på källdatorn är mindre än eller lika med antalet nätverkskort som tillåts för måldatorns storlek så kommer målet att ha samma antal kort som källan.
     * Om antalet nätverkskort för den virtuella källdatorn överskrider det tillåtna antalet för målstorleken så används den högsta målstorleken.
     * Om en källdator exempelvis har två nätverkskort och måldatorn stöder fyra så kommer måldatorn att ha två kort. Om källdatorn har två nätverkskort men målstorleken endast stöder ett så kommer måldatorn bara att ha ett kort.     
     * Om den virtuella datorn har flera nätverkskort ansluts alla till samma nätverk.

     ![Aktivera replikering](./media/site-recovery-vmm-to-azure/test-failover4.png)

4. I **Diskar** kan du se vilket operativsystem och vilka datadiskar på den virtuella datorn som kommer att replikeras.

#### <a name="managed-disks"></a>Hanterade diskar

I **beräknings- och nätverksinställningar** > **beräkna egenskaper**, du kan ange inställningen ”Använd hanterade diskar” till ”Ja” för den virtuella datorn om du vill bifoga hanterade diskar till din dator i samband med migrering till Azure. Hanterade diskar förenklar diskhantering för virtuella Azure IaaS-datorer genom att hantera de lagringskonton som är associerade med de virtuella diskarna. [Mer information om hanterade diskar](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview).

   - Hanterade diskar skapas och kopplas endast till den virtuella datorn vid en redundansväxling till Azure. När du aktiverar skydd fortsätter data från lokala datorer att replikeras till lagringskonton.
   Hanterade diskar kan endast skapas för virtuella datorer som distribueras med Resource manager-distributionsmodellen.  

  > [!NOTE]
  > Återställning från Azure till lokala Hyper-V-miljön stöds inte för datorer med hanterade diskar. Ange endast ”Använd hanterade diskar” som ”Ja” om du planerar att migrera den här datorn till Azure.

   - När du anger ”Använd hanterade diskar” till ”Ja” kommer endast tillgänglighetsuppsättningar i resursgruppen med ”Använd hanterade diskar” inställd på ”Ja” vara tillgänglig för urvalet. Det beror på att virtuella datorer med hanterade diskar endast kan vara en del av tillgänglighetsuppsättningar med värdet ”Ja” som egenskap för ”Använd hanterade diskar”. Se till att du skapar tillgänglighetsuppsättningar med egenskapen ”Använd hanterade diskar” inställd i enlighet med hur du avser använda hanterade diskar vid redundansväxling.  När du anger ”Använd hanterade diskar” som ”Nej” kommer endast tillgänglighetsuppsättningar i resursgruppen med ”Använd hanterade diskar” inställd på ”Nej” vara tillgänglig för urvalet. [Mer information om hanterade diskar och tillgänglighetsuppsättningar](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set).

  > [!NOTE]
  > Om lagringskontot som används för replikering har krypterats med Storage Service-kryptering vid någon tidpunkt misslyckas skapandet av hanterade diskar vid redundansväxling. Du kan antingen ange ”Använd hanterade diskar” som ”Nej” och försöka upprepa redundansväxlingen eller inaktivera skyddet för den virtuella datorn och skydda det till ett lagringskonto som inte har krypterats av lagringstjänstens vid någon tidpunkt.
  > [Lär dig mer om Storage Service Encryption och hanterade diskar](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).


## <a name="test-the-deployment"></a>Testa distributionen

Du kan testa distributionen genom att köra ett redundanstest för en enskild virtuell dator eller genom att köra en återställningsplan som innehåller en eller flera virtuella datorer.

### <a name="before-you-start"></a>Innan du börjar

 - Om du vill ansluta till virtuella Azure-datorer med RDP efter en redundansväxling kan du hitta mer information om att [förbereda en anslutning](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Om du vill testa fullständigt behöver du kopiera Active Directory och DNS i din testmiljö. [Läs mer](site-recovery-active-directory.md#test-failover-considerations).

### <a name="run-a-test-failover"></a>Köra ett redundanstest

1. Om du vill redundansväxla en enstaka virtuell dator klickar du på den virtuella datorn under **Replikerade objekt** > **+Testa redundans**.
2. Om du vill redundansväxla en återställningsplan går du till **Återställningsplaner**, högerklickar på planen > **Testa redundans**. Om du vill skapa en återställningsplan [följer du dessa instruktioner](site-recovery-create-recovery-plans.md).
3. I **Testa redundans** väljer du det Azure-nätverk som de virtuella Azure-datorerna ska ansluta till efter redundansväxlingen.
4. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet genom att klicka på den virtuella datorn för att visa dess egenskaper, eller i jobbet **Testa redundans** under **Site Recovery-jobb**.
5. När redundansväxlingen är klar bör du även kunna se Azure-replikdatorn på Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn har rätt storlek, att den är ansluten till rätt nätverk och körs.
6. Om du förberedde för anslutning efter redundansväxlingen bör du kunna ansluta till den virtuella Azure-datorn.
7. När du är klar klickar du på **Rensa redundanstest** i återställningsplanen. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen. När du gör det tas de virtuella datorerna som skapades under redundanstestningen bort.

Mer information finns i artikeln [Test failover to Azure](site-recovery-test-failover-to-azure.md) (Testa redundans till Azure).

## <a name="monitor-the-deployment"></a>Övervaka distributionen

Så här gör du om du vill övervaka konfigurationsinställningarna, statusen och hälsotillståndet för Site Recovery-distributionen:

1. Klicka på valvnamnet för att få åtkomst till **Essentials**-instrumentpanelen. På den här instrumentpanelen kan du övervaka Site Recovery-jobb, replikeringsstatusen, återställningsplaner, servertillstånd och händelser.  Du kan anpassa **Essentials** och visa de paneler och layouter som är mest användbara för dig, inklusive status för andra Site Recovery- och Backup-valv.

    ![Essentials](./media/site-recovery-vmm-to-azure/essentials.png)
2. Under **Hälsotillstånd** kan du övervaka problem på lokala servrar (VMM- eller konfigurationsservrar) och de händelser som har uppmärksammats av Site Recovery under de senaste 24 timmarna.
3. Du kan hantera och övervaka replikeringen på panelerna **Replikerade objekt**, **Återställningsplaner** och **Site Recovery-jobb**. Du kan visa mer detaljer om jobb under **Jobb** > **Site Recovery-jobb**.

## <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Installation från kommandoraden för Azure Site Recovery-providern

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

    > [!NOTE]
    > Vi rekommenderar att du använder krypteringsfunktionen i Azure för att kryptera vilande data, i stället för att använda krypteringsalternativet (alternativet Kryptering aktiverat) i Azure Site Recovery. Krypteringsfunktionen i Azure kan aktiveras för ett lagringskonto och bidrar till bättre prestanda eftersom krypteringen/dekrypteringen hanteras av Azure  
    > Storage.
    > [Lär dig mer om kryptering med Storage-tjänsten i Azure](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption).
    
* **/proxyAddress**: Valfri parameter som anger adressen till proxyservern.
* **/proxyport**: Valfri parameter som anger porten för proxyservern.
* **/proxyUsername**: Valfri parameter som anger användarnamnet för proxyservern (om proxyservern kräver autentisering).
* **/proxyPassword**: Valfri parameter som anger lösenordet för autentisering med proxyservern (om proxyservern kräver autentisering).


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

## <a name="next-steps"></a>Nästa steg

När den inledande replikeringen är klar och du har testat distributionen kan du starta redundansväxlingar vid behov. [Lär dig mer](site-recovery-failover.md) om olika typer av redundansväxlingar och hur du kör dem.
