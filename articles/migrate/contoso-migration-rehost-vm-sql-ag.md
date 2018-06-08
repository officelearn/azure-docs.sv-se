---
title: Rehost en Contoso-app genom att migrera den virtuella Azure-datorer och SQL Server AlwaysOn-tillgänglighetsgruppen | Microsoft Docs
description: Lär dig hur Contoso rehost appen lokalt genom att migrera den virtuella Azure-datorer och SQL Server AlwaysOn-tillgänglighetsgruppen
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: raynew
ms.openlocfilehash: 97c8430ab5d4e08e52790b898051d5985c3df03c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839902"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-sql-server-alwayson-availability-group"></a>Contoso-migrering: Rehost en lokal app på Azure Virtual Machines och SQL Server AlwaysOn-tillgänglighetsgrupp

Den här artikeln visar hur Contoso rehost SmartHotel appen i Azure. De migrera app-klientdel VM till en virtuell dator i Azure och app-databasen till en Azure SQL Server-VM, körs i ett redundanskluster för Windows Server med SQL Server AlwaysOn-Tillgänglighetsgrupper.

Det här dokumentet är i en serie artiklar som visar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller bakgrundsinformation och scenarier som illustrerar hur du konfigurerar en infrastruktur för migrering, bedömning av lokala resurser för migrering och kör olika typer av migreringar. Scenarier växa i komplexitet och vi lägger till ytterligare artiklar över tid.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | En översikt över Contosos migreringsstrategi, artikelserien och exempelappar som vi använder. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Beskriver hur Contoso förbereder dess lokalt och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla artiklar om migrering. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser](contoso-migration-assessment.md)  | Visar hur Contoso körs en utvärdering av en lokal två nivåer SmartHotel app som körs på VMware. Contoso utvärderar app virtuella datorer med den [Azure migrera](migrate-overview.md) tjänsten och SQL Server-databasen app med den [databasen Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
[Artikel 4: Rehost en app på Azure VM och en hanterad SQL-instans](contoso-migration-rehost-vm-sql-managed-instance.md) | Visar hur Contoso körs en lift och SKIFT-migrering till Azure för SmartHotel appen. Contoso migrerar appen klientdel VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och app-databas till en SQL hanteras instans, med hjälp av den [Azure migrering databastjänsten](https://docs.microsoft.com/azure/dms/dms-overview). | Tillgängligt
[Artikel 5: Rehost en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Visar hur Contoso migrera appen SmartHotel VMs endast med Site Recovery.
Artikel 6: Rehost en app på Azure Virtual Machines och SQL Server alltid på Availability Group (den här artikeln) | Visar hur Contoso migrerar SmartHotel appen. Contoso använder Site Recovery för att migrera appen virtuella datorer och tjänsten Databasmigrering för att migrera app-databas till en SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgruppen. | Tillgängligt
Artikel 7: Rehost en Linux-app till Azure virtuella datorer och Azure MySQL-Server | Visar hur Contoso migrerar osTicket Linux appen. De migrerar klientdel virtuella datorn med Site Recovery och migrerar (säkerhetskopiering och återställning) databasen till en Azure MySQL-Server-instans med MySQL arbetsstationen | Planerad
Artikel 8: Rehost en Linux-app på virtuella Azure-datorer | Visar hur Contoso har en lift och SKIFT migrering av fins osTicket app virtuella datorer till Azure med Site Recovery | Planerad


Contoso migrera Windows två nivåer i den här artikeln. NET SmartHotel app som körs på virtuella VMware-datorer till Azure. Om du vill använda den här appen, den är öppen källkod och du kan ladda ned det från [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har arbetat tillsammans med deras affärspartner att förstå vad de vill nå med migreringen:

- **Åtgärda tillväxt**: Contoso växer och det finns därför trycket på sina lokala datorer och infrastruktur.
- **Öka effektiviteten**: Contoso måste ta bort onödiga procedurer och förenkla processer för utvecklare och användare.  Affärsbehov IT att snabbt och inte avfall tid eller pengar, vilket leverera snabbare på kunders behov.
- **Öka flexibilitet**: Contoso IT måste kunna snabbare efter behov för företaget. Måste kunna reagera snabbare än ändringarna i marketplace om du vill aktivera framgången i en global ekonomi.  Det får inte vara i vägen eller bli en business-blockerare.
- **Skala**: takt med att företaget växer har Contoso IT måste ange system som kan växa i samma takt.

## <a name="migration-goals"></a>Mål för migrering

Contoso molnet team har fäst ned mål för den här migreringen. Dessa mål som användes för att avgöra den bästa migreringsmetoden:

- Efter migreringen ska app i Azure ha samma prestanda som idag i VMWare.  Appen kommer att finnas kvar som kritiskt i molnet som lokalt.
- Contoso vill inte investera i den här appen.  Det är viktigt att verksamheten, men i sin nuvarande form de vill flytta på ett säkert sätt till molnet.
- Den lokala databasen för appen har haft problem med tillgänglighet. De vill se den distribuerats i Azure som ett kluster med hög tillgänglighet, med funktioner för redundans.
- Contoso vill uppgradera från deras aktuella SQL Server 2008 R2-plattformen, till SQL Server 2017.
- Contoso inte vill använda en Azure SQL Database för den här appen och och är ute efter alternativ.

## <a name="proposed-architecture"></a>Föreslagna arkitektur

I det här scenariot:

- Appen nivåer över två virtuella datorer (WEBVM och SQLVM).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5)
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), körs på en virtuell dator.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).
- Lokala virtuella datorer i Contoso-datacenter ska inaktiveras när migreringen är klar.

![Scenariots arkitektur](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Management-tjänsten för databasen](https://docs.microsoft.com/azure/dms/dms-overview) | Contoso migreras app datanivån med DMS. DMS ansluta till lokal SQLVM dator via ett plats-till-plats-VPN och migrera DMS möjliggör sömlös migreringar från flera databaskällor till Azure data plattformar, med minimal avbrottstid. | Lär dig mer om [regioner som stöds](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) för DMS och get [prisinformationen](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Contoso använder Site Recovery för en lift och SKIFT migrering av app-klientdel VM. Site Recovery samordnar hanterar migrering och katastrofåterställning för virtuella datorer i Azure och lokala virtuella datorer och fysiska servrar.  | Azure Storage avgifter tas ut vid replikering till Azure.  Virtuella Azure-datorer skapas och debiteras när växling vid fel. [Lär dig mer](https://azure.microsoft.com/pricing/details/site-recovery/) om kostnader och priser.

 

## <a name="migration-process"></a>migreringsprocessen

- Contoso migreras appen virtuella datorer till Azure.
- De ska migrera klientdel VM till virtuella Azure-datorn med hjälp av Site Recovery:
    - Som ett första steg ska de förbereda skapa Azure komponenter och förbereda den lokala VMware-infrastrukturen.
    - De kan starta replikering av den virtuella datorn med allt förberedd.
    - När replikeringen är aktiverad och fungerar de migrera den virtuella datorn genom att växla över till Azure.
- De ska migrera databasen till SQL Server-kluster i Azure med Data migrering Service (DMS).
    - Som ett första steg kan de behöver att etablera SQL Server-datorer i Azure, konfigurera klustret och en intern belastningsutjämnare och konfigurera AlwaysOn-Tillgänglighetsgrupper.
    - Med den här på plats, kan de migrera databasen
- Efter migreringen, kommer de aktivera AlwaysOn skyddet för databasen.

![migreringsprocessen](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>Förutsättningar

Här är vad du (och Contoso) behöver för att köra det här scenariot:

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Du bör redan har skapat en prenumeration när du har utfört bedömningen i den första artikeln i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du är inte administratör, måste du arbeta med administratören att tilldela behörigheter för ägare eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan granska [i den här artikeln](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastrukturen** | [Lär dig hur](contoso-migration-infrastructure.md) Contoso ställa in en Azure-infrastruktur.<br/><br/> Mer information om specifika [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [lagring](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) krav för Site Recovery.
**Site recovery (lokalt)** | Din lokala vCenter-servern måste köra version 5.5, 6.0 eller 6.5<br/><br/> En ESXi-värd som kör version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer som körs på ESXi-värd.<br/><br/> Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Stöd för [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [lagring](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfiguration.<br/><br/> Virtuella datorer du vill replikera måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).
**DMS** | För DMS måste en [kompatibel lokala VPN-enhet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Du måste kunna konfigurera den lokala VPN-enheten. Den måste ha ett externt Internetriktade offentlig IPv4-adress och adressen får inte finnas bakom en NAT-enhet.<br/><br/> Kontrollera att du har åtkomst till dina lokala SQL Server-databas.<br/><br/> Windows-brandväggen ska kunna komma åt datakällan databasmotorn. [Läs mer](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Lägg till regler för att tillåta åtkomst till databasen och till filer med hjälp av SMB-port 445 om en brandvägg framför datorn databasen.<br/><br/> De autentiseringsuppgifter som används för att ansluta till datakällan SQL Server och hanterade målinstans måste vara medlemmar i serverrollen sysadmin.<br/><br/> Du behöver ett nätverk delar i din lokala databas som DMS kan använda för att säkerhetskopiera källdatabasen.<br/><br/> Kontrollera att tjänstkontot som kör SQL Server-instansen källa har skrivbehörighet på nätverksresursen.<br/><br/> Anteckna en Windows-användare (och lösenord) som har behörigheten Fullständig behörighet på nätverksresursen. Tjänsten Azure Database migrering personifierar dessa autentiseringsuppgifter för att överföra säkerhetskopieringsfiler till Azure storage-behållare.<br/><br/> Installationen av SQL Server Express anger TCP/IP-protokollet till **inaktiverad** som standard. Kontrollera att den är aktiverad.


## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Contoso kommer att köras migreringen:

> [!div class="checklist"]
> * **Steg 1: Skapa SQL Server-datorer i Azure**: Contoso vill distribuera en klustrad databas i Azure för hög tillgänglighet. De distribuera två virtuella SQL Server-datorer och en Azure intern belastningsutjämnare.
> * **Steg 2: Distribuera klustret**: när du distribuerar SQL Server-VM: ar, de förbereder ett Azure SQL Server-kluster.  De ska migrera sina databasen i förväg skapade klustret.
> * **Steg 3: Förbered DMS**: för att förbereda DMS som de registrerar providern Databasmigrering, skapa en DMS-instans och ett projekt. De har konfigurerat en signatur för delad åtkomst (SAS) identifierare URI (Uniform Resource). DMS använder SA-URI för att komma åt kontot lagringsbehållare som tjänsten Överför filer för SQL Server-säkerhetskopiering.
> * **Steg 4: Förbered Azure Site Recovery**: de skapar ett Azure storage-konto för att lagra replikerade data och Recovery Services-valvet.
> * **Steg 5: Förbered lokal VMware för Site Recovery**: de förbereda konton för VM identifieringen och agentinstallationen på nytt och förbereda lokala virtuella datorer så att de kan ansluta till virtuella Azure-datorer efter redundans.
> * **Steg 6: Replikera VMs**: de konfigurera replikeringsinställningar och aktivera replikering av Virtuella.
> * **Steg 7: Migrera databasen med DMS**: de migrerar-databasen.
> * **Steg 8: Migrera de virtuella datorerna med Site Recovery**: de först köra ett redundanstest för att kontrollera att allt fungerar, följt av en fullständig växling för att migrera den virtuella datorn.


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>Steg 1: Förbered ett kluster med SQL Server AlwaysOn tillgänglighet grupp

Contoso vill planera genom att distribuera databasen i ett kluster i Azure. De kan sedan använda det här klustret för andra databaser. 

- SQL Server-datorer ska distribueras i resursgruppen ContosoRG (används för produktionsresurser).
- Förutom unika namn använda både virtuella datorer samma inställningar.
- Den interna belastningsutjämnaren ska distribueras i ContosoNetworkingRG (används för nätverksresurser).
- Virtuella datorer körs Windows Server 2016 med SQL Server 2017 Enterprise Edition. Contoso har licenser för operativsystemet, så de kommer att använda en avbildning i Azure Marketplace som tillhandahåller licensen som ett tillägg till sina Azure EA åtagande.

Här är hur Contoso konfigurera klustret:

1. De kan skapa två virtuella SQL Server-datorer genom att välja SQL Server 2017 Enterprise Windows Server 2016 avbildning i Azure Marketplace. 

    ![SQL VM SKU](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. I den **Skapa virtuell dator med guiden** > **grunderna**, de konfigurera:

    - Namn för de virtuella datorerna: **SQLAOG1** och **SQLAOG2**.
    - Eftersom datorer är verksamhetskritiska, aktivera de SSD för typ av VM-disk.
    - De ange autentiseringsuppgifter för datorn.
    - De distribuera de virtuella datorerna i den primära ÖSTRA USA 2 region i resursgruppen ContosoRG.

3. I **storlek**, de börjar med D2s_V3 SKU för både virtuella datorer. De kommer senare som de behöver för att skala.
4. I **inställningar**, de gör du följande:

    - Eftersom dessa virtuella datorer är kritiska databaser för appen, använder de hanterade diskar.
    - De placerar datorerna i produktionsnätverket av ÖSTRA USA 2 primära region (**VNET-PRODUKTPRENUMERATION-EUS2**), i databasen undernät (**PRODUKTPRENUMERATION-DB-EUS2**).
    - De skapar en ny tillgänglighetsuppsättning: **SQLAOGAVSET**, med två feldomäner och fem update-domäner.

    ![VIRTUELL DATOR MED SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. I **SQL Server-inställningar**, de begränsa SQL-anslutning till det virtuella nätverket (privat) på standardporten 1433. För autentisering de använder samma autentiseringsuppgifter som de använder på plats (**contosoadmin**).

    ![VIRTUELL DATOR MED SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**Behöver du mer hjälp?**

- [Få hjälp](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings) etablering av en SQL Server-VM.
- [Lär dig mer om](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms) Konfigurera virtuella datorer för olika SQL Server SKU: er.

## <a name="step-2-deploy-the-failover-cluster"></a>Steg 2: Distribuera failover-kluster

Här är hur Contoso konfigurera klustret:

1. De har konfigurerat ett Azure storage-konto ska fungera som vittne molnet.
2. De lägga till SQL Server-datorer i Active Directory-domän i Contoso lokala datacenter.
3. Klustret skapas i Azure.
4. De konfigurera molnet vittne.
5. Slutligen kan SQL Always On-Tillgänglighetsgrupper.

### <a name="set-up-a-storage-account-as-cloud-witness"></a>Ställa in ett lagringskonto som molntjänster vittne

Om du vill konfigurera ett vittne molnet måste Contoso ett Azure Storage-konto som innehåller blob-fil som används för klustret skiljedom. Samma lagringskonto kan användas för att konfigurera molnet vittne för flera kluster. 

Contoso skapar ett lagringskonto på följande sätt:

1. De ange ett beskrivande namn för kontot (**contosocloudwitness**).
2. De distribuera ett allmänt alla syften konto med LRS.
3. De placera kontot i en tredje region - södra centrala USA. De placeras utanför den primära och sekundära regionen så att det finns kvar om regionala fel uppstår.
4. De placera den i deras resursgruppen som innehåller infrastrukturresurser - **ContosoInfraRG**.

    ![Molnet vittne](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. När de skapar lagring för primära och sekundära nycklar skapas för den. De måste den primära åtkomstnyckeln för att skapa moln vittne. Nyckeln visas under lagringskontonamn > **åtkomstnycklar**.

    ![Åtkomstnyckel](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Lägg till virtuella SQL Server-datorer i Contoso-domänen

1. Contoso lägger till SQLAOG1 och SQLAOG2 till domänen contoso.com.
2. På varje virtuell dator installera de sedan Windows Failover Cluster funktioner och verktyg.

## <a name="set-up-the-cluster"></a>Konfigurera klustret

Innan du skapar klustret tar en ögonblicksbild av OS-disken på varje dator i Contoso.

![Ögonblicksbild](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

2. Sedan kan de köra ett skript som de har gjort tillsammans för att skapa Windows-redundanskluster.

    ![Skapa kluster](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

3. När de har skapat klustret kan kontrollera de att de virtuella datorerna visas som klusternoder.

     ![Skapa kluster](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>Konfigurera cloud vittne

1. Contoso konfigurera molnet vittne med hjälp av den **kvorum konfigurationsguiden** i hanteraren för redundanskluster.
2. I guiden kan de välja för att skapa ett moln vittne med storage-konto.
3. När vittne molnet har konfigurerats visas i i snapin-modulen Klusterhanterare för växling vid fel.

    ![Molnet vittne](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

## <a name="enable-sql-server-always-on-availability-groups"></a>Aktivera SQL Server Always On-Tillgänglighetsgrupper

Contoso kan nu aktivera Always On:

1. Konfigurationshanteraren för SQL Server aktiveras **AlwaysOn Availability Groups** för den **SQL Server (MSSQLSERVER)** service.

    ![Aktivera AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. De starta om tjänsten för att ändringarna ska börja gälla.

Contoso kan ställa in AlwaysOn-tillgänglighetsgrupp som skyddar SmartHotel databasen med AlwaysOn aktivera.

**Behöver du mer hjälp?**

- [Läs mer om](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) molnet vittne och skapa ett lagringskonto för den.
- [Hämta instruktioner](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial) för att konfigurera ett kluster och skapa en tillgänglighetsgrupp.

## <a name="step-3-deploy-the-azure-load-balancer"></a>Steg 3: Distribuera Azure belastningsutjämnare

Contoso vill nu distribuera en intern belastningsutjämnare placerad framför klusternoderna. Belastningsutjämnaren lyssnar efter trafik och leder till lämplig nod.

![Belastningsutjämning](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

De skapar belastningsutjämnaren på följande sätt:

1. I Azure-portalen > **nätverk** > **belastningsutjämnare**, de har konfigurerat en intern belastningsutjämnare: **ILB-PRODUKTPRENUMERATION-DB-EUS2-SQLAOG**.
2. De placera belastningsutjämnaren i produktionsnätverket **VNET-PRODUKTPRENUMERATION-EUS2**, i databasen undernät **PRODUKTPRENUMERATION-DB-EUS2**.
3. De tilldela en statisk IP-adress: 10.245.40.100.
4. Som ett nätverk element de distribuerar belastningsutjämning i nätverk resursgruppen **ContosoNetworkingRG**.

    ![Belastningsutjämning](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

När den interna belastningsutjämnaren distribueras måste Contoso ställa in den. De skapar en backend-adresspool, ställa in en hälsoavsökningen och konfigurera en regel för belastningsutjämning.

### <a name="add-a-backend-pool"></a>Lägg till en serverdelspool

Om du vill distribuera trafik till de virtuella datorerna i klustret ställa Contoso in en backend-adresspool som innehåller IP-adresserna för nätverkskorten för virtuella datorer som ska ta emot trafik från belastningsutjämnaren.

1. Belastningen belastningsutjämnaren inställningarna i portalen Contoso lägga till en serverdelspool: **ILB-PROD-DB-EUS-SQLAOG-BEPOOL**.
2. De associera poolen med tillgänglighetsuppsättning SQLAOGAVSET. De virtuella datorerna i uppsättningen (**SQLAOG1** och **SQLAOG2**) har lagts till i poolen.

    ![Serverdelspool](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Contoso skapar en hälsoavsökningen så att belastningsutjämnaren kan övervaka hälsotillståndet för appen. Avsökningen dynamiskt lägger till eller tar bort virtuella datorer från belastningen belastningsutjämnaren rotation, baserat på hur de ska svara hälsokontroller.

De skapa avsökningen enligt följande: 

1. I belastningsutjämnaren belastningen inställningarna i portalen, Contoso skapar en hälsoavsökningen: **SQLAlwaysOnEndPointProbe**.
2. De ange avsökningen att övervaka virtuella datorer på TCP-port 59999.
3. De ange ett intervall på 5 sekunder mellan avsökningar och ett tröskelvärde för 2. Om två avsökningar misslyckas, betraktas den virtuella datorn inte felfri.

    ![Avsökningen](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>Konfigurera belastningsutjämnare för att ta emot trafik


Nu Contoso måste en regel för belastningsutjämnare till defins hur trafiken distribueras till de virtuella datorerna.

- Frontend IP-adressen hanterar inkommande trafik.
- Backend-IP-adresspool som tar emot trafiken.

De skapa regeln enligt följande:

1. I belastningsutjämnaren belastningen inställningarna i portalen, de lägger till en ny regel för belastningsutjämning: **SQLAlwaysOnEndPointListener**.
2. Contoso anger en frontend-lyssnare för att ta emot inkommande SQL trafik på TCP 1433.
3. De ange serverdelspoolen till vilken trafik vidarebefordras och porten där virtuella datorer lyssna efter trafik.
4. Contoso gör flytande IP (direkt serverreturnering). Detta krävs alltid för SQL AlwaysOn.

    ![Avsökningen](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**Behöver du mer hjälp?**

- [Få en översikt](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) av Azure belastningsutjämnare.
- [Lär dig mer om](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal) skapar en belastningsutjämnare.



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>Steg 4: Förbered Azure för Site Recovery-tjänsten

Här är Contoso behöver distribuera Site Recovery på Azure komponenter:

- Ett virtuellt nätverk som virtuella datorer kommer att finnas när de skapar under växling vid fel.
- Ett Azure storage-konto för att lagra replikerade data. 
- Ett Recovery Services-valv i Azure.

De har konfigurerat dessa på följande sätt:

1.  Contoso har redan skapat ett undernät eller det nätverk de kan använda Site Recovery när de [distribuerat Azure-infrastrukturen](contoso-migration-rehost-vm-sql-ag.md).

    - SmartHotel appen är en produktionsapp och WEBVM kommer att migreras till Azure-produktionsnätverket (VNET-PRODUKTPRENUMERATION-EUS2) i den primära regionen östra US2.
    - WEBVM placeras i resursgruppen ContosoRG som används för produktionsresurser och undernät för produktion (PROD-FE-EUS2).

2. Contoso skapar ett Azure storage-konto (contosovmsacc20180528) i den primära regionen.

    - De använder ett allmänt konto med standardlagring och LRS replikering.
    - Kontot måste vara i samma region som valvet.

    ![Site Recovery-lagring](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. Med kontot nätverk och lagring på plats, de nu skapa ett Recovery Services-valv (**ContosoMigrationVault**), och placera den i den **ContosoFailoverRG** resursgrupp i den primära regionen östra USA 2 .

    ![Recovery Services-valv](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) ställa in Azure Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Steg 4: Förbered lokal VMware för Site Recovery

Här är vad Contoso förbereds lokalt:

- Ett konto på vCenter server eller vSphere ESXi värden, att automatisera VM-identifiering.
- Ett konto som ger automatisk installation av mobilitetstjänsten på virtuella VMware-datorer som du vill replikera.
- Lokalt för Virtuella datorer, så att Contoso kan ansluta till den replikerade virtuella Azure-datorn efter redundans.


### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Automatiskt identifiera virtuella datorer. 
- Samordna replikering, redundans och återställning efter fel.
- Minst ett skrivskyddat konto krävs. Du behöver ett konto som kan köra åtgärder som till exempel att skapa och ta bort diskar och aktivering av virtuella datorer.

Contoso har konfigurerat kontot på följande sätt:

1. Contoso skapar en roll på vCenter-nivå.
2. Contoso sedan tilldelar rollen behörigheterna som krävs.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på varje virtuell dator.

- Site Recovery kan göra en automatisk push-installation av den här komponenten när replikering har aktiverats för den virtuella datorn.
- Du behöver ett konto som Site Recovery kan använda för att få åtkomst till den virtuella datorn för push-installation. Du kan ange det här kontot när du konfigurerar replikering i Azure-konsolen.
- Kontot kan vara domän eller lokalt med behörigheter för att installera på den virtuella datorn.




### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Contoso vill ansluta till virtuella Azure-datorer efter redundans. Om du vill göra detta måste göra de följande innan migreringen:

1. För åtkomst via internet de:

 - Aktiverar du RDP på den lokala virtuella datorn innan redundans
 - Kontrollera att TCP och UDP-regler har lagts till för den **offentliga** profil.
 - Kontrollera att RDP tillåts i **Windows-brandväggen** > **tillåtna appar** för alla profiler.
 
2. För åtkomst via plats-till-plats-VPN, de:

 - Aktiverar du RDP på den lokala datorn.
 - Tillåt RDP i den **Windows-brandväggen** -> **tillåtna appar och funktioner**, för **domän och privat** nätverk.
 - Ange operativsystemets SAN-principen på den lokala virtuella datorn till **OnlineAll**.

Dessutom, när de kör en redundansväxling behöver de för att kontrollera följande:

- Det ska vara ingen Windows-uppdateringar som väntar på den virtuella datorn när utlösa en växling vid fel. Om det finns, kan de inte logga in på den virtuella datorn förrän uppdateringen är klar.
- Efter växling vid fel, kan de kontrollera **starta diagnostik** att visa en skärmbild av den virtuella datorn. Om det inte fungerar de bör kontrollera att den virtuella datorn körs och granska dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) skapa och tilldela en roll för automatisk identifiering.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) skapar ett konto för push-installation av mobilitetstjänsten.


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Steg 5: Replikera lokala virtuella datorer till Azure med Site Recovery

Innan de kan köra en migrering till Azure, måste Contoso att konfigurera och aktivera replikering.

### <a name="set-a-replication-goal"></a>Ange ett mål för replikering

1. I valvet, under valvnamnet (ContosoVMVault) de väljer ett mål för replikering (**komma igång** > **Site Recovery** > **Förbered infrastruktur** .
2. De ange som finns lokalt, körs på VMware och replikerar till Azure på sina datorer.

    ![Replikeringsmål](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bekräfta att planera distribution

Om du vill fortsätta, de behöver för att bekräfta att de har slutfört planera distribution genom att välja **Ja, jag har gjort det**. I det här scenariot Contoso bara migrera en virtuell dator, och behöver inte planera distribution.

### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Contoso måste konfigurera sina källmiljön. Om du vill göra detta måste de hämtar ett OVF-mall och distribuera Site Recovery konfigurationsservern med hög tillgänglighet, lokal VMware VM. När konfigurationsservern är igång kan registrera den i den här valvet.

Konfigurationsservern körs ett antal komponenter:

- Konfiguration av serverkomponent som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processerver som fungerar som en replikerings-gateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.
- Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.

Contoso utför de här stegen på följande sätt:


1. De hämtar OVF mallen från i valvet, **Förbered infrastrukturen** > **källa** > **konfigurationsservern**.
    
    ![Hämta OVF](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. De importera mallen till VMware för att skapa och distribuera den virtuella datorn.

    ![OVF-mall](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. När de aktiverar på den virtuella datorn för första gången startar den i en Windows Server 2016-installationsproceduren. De acceptera licensavtalet och ange ett administratörslösenord.
4. När installationen är klar kan logga de in på den virtuella datorn som administratör. Vid första inloggningen körs konfigurationsverktyget för Azure Site Recovery som standard.
5. I verktyget Ange de ett namn som ska användas för att registrera konfigurationsservern i valvet.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har etablerats kan de logga in på Azure-prenumerationen. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.

    ![Registrera konfigurationsservern](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. De loggar in på datorn igen och guiden Konfigurera Server Management startar automatiskt.
9. I guiden Välj de nätverkskort för att ta emot replikeringstrafik. Den här inställningen kan inte ändras när den har konfigurerats.
10. De Välj prenumerationen, resursgruppen och valvet att registrera konfigurationsservern.
        ![Valvet](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. De och sedan ladda ned och installera MySQL-Server och VMWare PowerCLI. 
11. När valideringen ange de FQDN eller IP-adressen för vCenter-server eller vSphere-värd. De lämnar standardporten och anger ett eget namn för vCenter-servern.
12. De kan ange det konto som de har skapats för automatisk upptäckt och de autentiseringsuppgifter som används för att automatiskt installera Mobilitetstjänsten. För Windows-datorer måste kontot behörighet som lokal administratör på virtuella datorer.

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. När registreringen är klar i Azure-portalen Contoso dubbla kontrollerar att konfigurationsservern och VMware-server visas på den **källa** i valvet. Identifieringen kan ta 15 minuter eller mer. 
8. Site Recovery ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

### <a name="set-up-the-target"></a>Konfigurera mål

Contoso anger nu Målinställningar för replikering.

1. I **Förbered infrastruktur** > **mål**, de Välj inställningar för målet.
2. Site Recovery kontrollerar att det finns ett Azure storage-konto och nätverk i det angivna målet.

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

Nej, Contoso kan skapa en replikeringsprincip.

1. I **Förbered infrastruktur** > **replikeringsinställningarna** > **Replikeringsprincipen** >  **skapa och Associera**, de skapar en princip **ContosoMigrationPolicy**.
2. De använder standardinställningarna:
    - **Tröskelvärde för Återställningspunktmål**: standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
    - **Kvarhållningstid för återställningspunkten**. Standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperiod är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
    - **Frekvens av programkonsekventa ögonblicksbilder**. Standardvärdet för en timme. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas.
 
        ![Skapa replikeringsprincip](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. Principen associeras automatiskt med konfigurationsservern. 

    ![Koppla replikeringsprincipen](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>Aktivera replikering

Contoso kan nu starta replikering av WebVM.

1. I **replikera program** > **källa** > **+ replikera** de välja inställningar för datakälla.
2. De anger att de vill aktivera virtuella datorer, Välj vCenter-servern och konfigurationsservern.

    ![Aktivera replikering](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. Nu kan ange de Målinställningar, inklusive resursgruppen och virtuella nätverk och lagringskontot som replikerade data ska sparas.

     ![Aktivera replikering](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. Contoso väljer WebVM för replikering, kontrollerar replikeringsprinciper och aktiverar replikering. Site Recovery installerar Mobilitetstjänsten på den virtuella datorn när replikeringen är aktiverad.
 
    ![Aktivera replikering](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. De spåra replikeringsförloppet på **jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.
5. I **Essentials** i Azure-portalen Contoso kan se strukturen för de virtuella datorerna som replikerar till Azure.

    ![Visa infrastruktur](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**Behöver du mer hjälp?**

- Du kan läsa en fullständig genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurerar replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Du kan lära dig mer om [replikering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-install-the-database-migration-assistant-dma"></a>Steg 5: Installera databasen Migration Assistant (DMA)

Contoso migreras SmartHotel databasen till Azure VM **SQLAOG1** med DMA. De konfigurera DMA enligt följande:

1. De kan hämta verktyget från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) till den lokala SQL Server-VM (**SQLVM**).
2. De kör installationsprogrammet (DownloadMigrationAssistant.msi) på den virtuella datorn.
3. På den **Slutför** kan de välja **starta Microsoft Data Migration Assistant** innan du avslutar guiden.

## <a name="step-6-migrate-the-database-with-dma"></a>Steg 6: Migrera databasen med DMA

1. I DMA de kör en ny migrering - **SmartHotel**.
2. De väljer den **mål servertyp** som **SQL Server på Azure Virtual Machines**. 

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. I informationen migrering de lägger till **SQLVM** som källservern och **SQLAOG1** som mål. De ange autentiseringsuppgifter för varje dator.

     ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. De skapar en lokal resurs för information om databas och konfiguration. Den måste vara tillgänglig med skrivbehörighet av SQL-tjänstkontot på SQLVM och SQLAOG1.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso väljer inloggningar som ska migreras och startar migreringen. När den är klar visar DMA migrering som lyckas.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. De bekräftar att databasen är igång på **SQLAOG1**.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

DMS ansluter till lokala SQL Server-VM via ett plats-till-plats VPN-anslutning mellan Contoso datacenter och Azure och migrerar sedan databasen.

## <a name="step-7-protect-the-database"></a>Steg 7: Skydda databasen

Med app-databas som körs på **SQLAOG1**, Contoso kan nu skydda den med hjälp av AlwaysOn-Tillgänglighetsgrupper. De konfigurera Alwayson med SQL Management Studio och sedan tilldela en lyssnare med hjälp av Windows-kluster. 

### <a name="create-an-alwayson-availability-group"></a>Skapa en AlwaysOn-tillgänglighetsgrupp

1. I SQL Management Studio de högerklickar du på **alltid på hög tillgänglighet** att starta den **guiden Ny tillgänglighetsgrupp**.
2. I **ange alternativ för**, de namnet tillgänglighetsgruppen **SHAOG**. I **Välj databaser**, de väljer SmartHotel-databasen.

    ![AlwaysOn-tillgänglighetsgrupp](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. I **ange repliker**, de lägga till två SQL-noder som tillgänglighetsrepliker och konfigurera dem för att tillhandahålla automatisk redundans med synkront genomförande.

     ![AlwaysOn-tillgänglighetsgrupp](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. De konfigurerar en lyssnare för gruppen (**SHAOG**) och port. IP-adressen för den interna belastningsutjämnaren läggs till som en statisk IP-adress (10.245.40.100).

    ![AlwaysOn-tillgänglighetsgrupp](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. I **Välj datasynkronisering**, aktiveras automatisk seeding. Med det här alternativet skapar SQL Server automatiskt de sekundära replikerna för varje databas i gruppen så Contoso inte behöver säkerhetskopiera och återställa dessa manuellt. Efter verifiering skapas tillgänglighetsgruppen.

    ![AlwaysOn-tillgänglighetsgrupp](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso råkade ut för ett problem när du skapar gruppen. De använder inte Active Directory Windows-integrerad säkerhet och därför måste du bevilja behörighet till SQL-inloggning för att skapa roller för redundanskluster i Windows.

    ![AlwaysOn-tillgänglighetsgrupp](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. När gruppen skapas Se Contoso den i SQL Management Studio.

### <a name="configure-a-listener-on-the-cluster"></a>Konfigurera en lyssnare på klustret

Som ett sista steg i hur du konfigurerar SQL-distribuering Contoso konfigurerar den interna belastningsutjämnaren som en lyssnare på klustret och ger lyssnaren online. De kan använda ett skript för att göra detta.

![Kluster-lyssnare](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>Kontrollera att konfigurationen

Med allt konfigurera Contoso nu har en fungerande availability group i Azure som använder migrerade databasen. De kan kontrollera detta genom att ansluta till den interna belastningsutjämnaren i SQL Management Studio.

![ILB ansluta](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**Behöver du mer hjälp?**
- Lär dig mer om hur du skapar en [tillgänglighetsgruppen](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group) och [lyssnare](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener).
- Manuellt [konfigurera klustret att använda IP-adressen för belastningsutjämnaren](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address).
- [Lär dig mer](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-2) om att skapa och med hjälp av SAS.


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>Steg 8: Migrera den virtuella datorn med Site Recovery

Kör en snabb Contoso testa redundans och sedan migrera den virtuella datorn.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

Kör ett redundanstest säkerställer att allt fungerar som förväntat innan migreringen. 

1. Contoso kör ett redundanstest till den senaste punkten i tid (**senaste bearbetas**).
2. De väljer **Stäng datorn innan du påbörjar redundans**, så att Site Recovery försöker stänga av den Virtuella källdatorn innan växling vid fel. Redundansväxlingen fortsätter även om avstängningen misslyckas. 
3. Testa redundans körs: 

    - En kravkontroll körs se till att alla villkor krävs för migrering på plats.
    - Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten skapas en återställningspunkt utifrån tillgängliga data.
    - En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.

3. När redundansväxlingen är klar visas repliken Azure VM i Azure-portalen. Contoso kontrollerar att den virtuella datorn är rätt storlek, att den är ansluten till rätt nätverk och att den körs. 
4. När du har verifierat, rensa växling vid fel, Contoso och registrera och spara synpunkter. 

### <a name="run-a-failover"></a>Köra en redundansväxling

1. Contoso när du har verifierat att du testar redundansen fungerade som förväntat, skapa en återställningsplan för migrering och Lägg till WEBVM planen.

     ![Återställningsplan](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. De kör en växling vid fel på planen. De markerar du den senaste återställningspunkten och ange Site Recovery ska försöka stänga av den lokala virtuella datorn innan växling vid fel.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. Efter växling vid fel kontrollera de att Azure VM visas som förväntat i Azure-portalen.

    ![Återställningsplan](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. När du har verifierat att den virtuella datorn i Azure, slutföra de migreringen för att slutföra migreringen, stoppa replikering för den virtuella datorn och stoppa Site Recovery-faktureringen för den virtuella datorn.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>Uppdatera anslutningssträngen

Som det sista steget i migreringen uppdatera Contoso anslutningssträngen för att peka på den migrerade databas som körs på SHAOG lyssnaren. Den här konfigurationen ändras på WEBVM nu körs i Azure.  Den här konfigurationen finns i filen web.config för ASP-programmet. 

1. Leta upp filen på C:\inetpub\SmartHotelWeb\web.config.  Ändra namnet på servern motsvarar det fullständiga Domännamnet för AOG: shaog.contoso.com.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. De starta om IIS på WEBVM när uppdaterar filen och spara filen. De göra detta med hjälp av IISRESET /RESTART från en kommandotolk.
2. När IIS har startats, kan nu använda den databas som körs på SQL-MI.


**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) kör ett redundanstest. 
- [Läs](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) så här skapar du en återställningsplan.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) redundansväxlar till Azure.

## <a name="clean-up-after-migration"></a>Rensa efter migreringen

Efter migreringen SmartHotel appen körs på en virtuell dator i Azure och SmartHotel-databas finns i Azure SQL-klustret.

Nu behöver Contoso Rensa stegen:  

- Ta bort de lokala virtuella datorerna från vCenter-lagret.
- Ta bort de virtuella datorerna från lokala säkerhetskopieringsjobb.
- Uppdatera interna dokumentation om du vill visa nya platser och IP-adresser för virtuella datorer.
- Granska alla resurser som interagerar med inaktiverade virtuella datorer och uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.
- Lägg till de två nya virtuella datorerna (SQLAOG1 och SQLAOG2) ska läggas till produktion övervakningssystem.

## <a name="review-the-deployment"></a>Granska distributionen

Med de migrerade resurserna i Azure måste Contoso helt operationalisera och skydda den nya infrastrukturen.

### <a name="security"></a>Säkerhet

Contoso security-teamet har granskat Azure virtuella datorer WEBVM, SQLAOG1 och SQLAOG2 för att fastställa eventuella säkerhetsproblem. 

- De granska Nätverkssäkerhetsgrupper (NSG: er) för den virtuella datorn för åtkomstkontroll. NSG: er används för att se till att endast trafik som tillåts att programmet kan passera.
- De överväger att skydda data på disken med hjälp av Azure Disk Encryption och KeyVault.
- De bör utvärdera transparent datakryptering (TDE) och aktivera det på SmartHotel-databas som körs på den nya SQL AOG. [Läs mer](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017).

[Läs mer](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) om säkerhetsåtgärder för virtuella datorer.

### <a name="backups"></a>Säkerhetskopior

Contoso kommer att säkerhetskopiera data på WEBVM och SQLAOG1 SQLAOG2 med hjälp av Azure Backup-tjänsten. [Läs mer](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licens- och optimering

1. Contoso har befintliga licensiering för sina WEBVM och använder förmån för Azure-Hybrid.  De kommer att konvertera befintliga virtuella Azure-datorer för att dra nytta av den här prisnivån.
2. Contoso aktiverar Azure kostnaden Management licensierade av Cloudyn, ett Microsoft-kontor. Det är en lösning för flera molnkostnad som hjälper dig att använda och hantera Azure och andra molnresurser.  [Lär dig mer](https://docs.microsoft.com/azure/cost-management/overview) om hantering av Azure kostnaden. 

## <a name="conclusion"></a>Sammanfattning

I den här artikeln rehosted Contoso SmartHotel app i Azure genom att migrera app-klientdel VM till Azure med Site Recovery-tjänsten. De migrerade appen databasen till en SQL Server-kluster som etablerats i Azure och skyddas i en SQL Server AlwaysOn-tillgänglighetsgrupp.

## <a name="next-steps"></a>Nästa steg

I nästa artikel i serien vi ska visa hur Contoso rehost service supportavdelningen osTicket appen körs på Linux och distribueras med en MySQL-databas.


