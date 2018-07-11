---
title: Replikera en skikt Dynamics AX-distribution med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du replikera och skydda Dynamics AX med hjälp av Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: asgang
manager: rochakm
editor: ''
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: asgang
ms.openlocfilehash: 931cc628dccc77a026791b27a7a8159b37c585d4
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919571"
---
# <a name="replicate-a-multitier-dynamics-ax-application-by-using-azure-site-recovery"></a>Replikera ett skikt Dynamics AX-program med hjälp av Azure Site Recovery

## <a name="overview"></a>Översikt


 Dynamics AX är en av de mest populära ERP-lösningar som används av företag för att standardisera processer över platser, hantera resurser och enklare att uppfylla. Eftersom programmet är viktiga för en organisation, vid katastrofåterställning bör programmet vara igång i den minsta tiden.

Idag, ger Dynamics AX inte några out-of the box-katastrofåterställning återställningsfunktioner. Dynamics AX består av flera serverkomponenter, till exempel Windows Application Object Server, Azure Active Directory, Azure SQL Database, SharePoint Server och Reporting Services. Om du vill hantera katastrofen är återställning av var och en av dessa komponenter manuellt inte bara dyra men även felbenägna.

Den här artikeln förklarar hur du kan skapa en lösning för haveriberedskap för din Dynamics AX-program med hjälp av [Azure Site Recovery](site-recovery-overview.md). Den behandlar också planerad/oplanerad redundanstestning med hjälp av en återställningsplan med ett klick, konfigurationer som stöds och förutsättningar.



## <a name="prerequisites"></a>Förutsättningar

Implementera haveriberedskap för Dynamics AX-program med hjälp av Site Recovery kräver följande förutsättningar:

• Ställa in en lokal Dynamics AX-distribution.

• Skapa ett Site Recovery-valv i en Azure-prenumeration.

• Om du använder Azure som återställningsplats, kör verktyget för diagnostisk utvärdering av för Azure virtuella datorer på de virtuella datorerna. De måste vara kompatibla med Azure Virtual Machines och Site Recovery-tjänster.

## <a name="site-recovery-support"></a>Site Recovery-stöd

I syfte att skapa den här artikeln, använde vi virtuella VMware-datorer med Dynamics AX 2012 R3 på Windows Server 2012 R2 Enterprise. Eftersom site recovery-replikering är oberoende av program, vi förväntar oss rekommendationerna här för att rymma för följande scenarier.

### <a name="source-and-target"></a>Källa och mål

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja
**Fysisk server** | Ja | Ja

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Aktivera återställning av Dynamics AX-program med hjälp av Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Skydda din Dynamics AX-program
Om du vill aktivera hela appen replikeringen och återställningen, måste varje komponent i Dynamics AX skyddas.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Konfigurera Active Directory och DNS-replikering

Active Directory krävs på katastrofåterställningsplatsen för Dynamics AX-program ska fungera. Vi rekommenderar följande alternativ baserat på komplexitet och kundens lokala miljö.

**Alternativ 1**

Kunden har ett litet antal program och en enda domänkontrollant för hela lokal plats och planerar att redundansväxla hela platsen. Vi rekommenderar att du använder Site Recovery-replikering för att replikera den domain controller-datorn till en sekundär plats (gäller för både plats-till-plats-och plats till Azure).

**Alternativ 2**

Kunden har ett stort antal program och kör en Active Directory-skog och planer för att växla över några program i taget. Vi rekommenderar att du konfigurerar ytterligare en domänkontrollant på katastrofåterställningsplatsen (en sekundär plats eller i Azure).

 Mer information finns i [tillgängliggöra en domänkontrollant för en plats för katastrofåterställning](site-recovery-active-directory.md). Under resten av det här dokumentet förutsätter vi att en domänkontrollant är tillgänglig på katastrofåterställningsplatsen.

### <a name="2-set-up-sql-server-replication"></a>2. Konfigurera SQL Server-replikering
Teknisk vägledning om det rekommenderade alternativet för att skydda SQL-nivå finns [replikera program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Aktivera skydd för den Dynamics AX-klienten och programmet objektet Server-datorer
Utföra relevanta Site Recovery configuration baserat på om de virtuella datorerna distribueras på [Hyper-V](site-recovery-hyper-v-site-to-azure.md) eller [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Vi rekommenderar att du konfigurerar kraschkonsekvent frekvens till 15 minuter.
>

Följande ögonblicksbilden visar skydd för virtuella datorer Dynamics-komponenten i ett scenario för VMware-plats-till-Azure-skydd.

![Skyddade objekt](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Konfigurera nätverk
**Konfigurera VM-beräkning och nätverksinställningar**

Konfigurera nätverksinställningar i Site Recovery för Dynamics AX-klienten och programmet objektet Server-datorer, så att de Virtuella datornätverken hämta ansluten till rätt disaster recovery-nätverket efter redundans. Kontrollera att disaster recovery-nätverket för de här nivåerna är dirigerbara till SQL-nivå.

Du kan välja den virtuella datorn i de replikerade objekt som du konfigurerar nätverksinställningar, enligt följande ögonblicksbilden:

* Välj rätt tillgänglighetsuppsättningen för Application Object Server-servrar.

* Om du använder en statisk IP-adress, ange IP-adress som du vill att den virtuella datorn i den **mål-IP** textrutan.

    ![Nätverksinställningar ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png).


### <a name="5-create-a-recovery-plan"></a>5. Skapa en återställningsplan

Du kan skapa en återställningsplan i Site Recovery för att automatisera redundansprocessen. Lägg till en app och en webbnivå i återställningsplanen. Ordna dem i olika grupper så att klientdelen som avslutas innan app-nivå.

1. Välj Site Recovery-valvet i din prenumeration och välj den **Återställningsplaner** panelen.

2. Välj **+ återställningsplan**, och ange ett namn.

3. Välj den **källa** och **Target**. Målet kan vara Azure eller en sekundär plats. Om du väljer Azure måste du ange distributionsmodellen.

    ![Skapa en återställningsplan](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Välj objektet programservern och klient-VM för återställningsplanen och välj ✓.

    ![Välj objekt](./media/site-recovery-dynamics-ax/selectvms.png)

    Recovery plan-exempel:

    ![Information om återställning av plan](./media/site-recovery-dynamics-ax/recoveryplan.png)

Du kan anpassa återställningsplanen för Dynamics AX-program genom att lägga till följande steg. Föregående ögonblicksbild visar slutförd återställningsplanen när du har lagt till alla steg.


* **Steg för SQL Server-redundans**: information om specifika återställningssteg till SQLServer finns i [replikering program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

* **Redundansgruppen 1**: växlar över programmet objektet Server-datorer.
Se till att den valda återställningspunkten är så nära som möjligt till databasen PIT, men inte före den.

* **Skriptet**: Lägg till belastningsutjämnare (endast E-A).
Lägg till ett skript (via Azure Automation) efter program objektet Server VM-gruppen visas för att lägga till en belastningsutjämnare i den. Du kan använda ett skript för att göra detta. Mer information finns i [hur du lägger till en belastningsutjämnare för haveriberedskap för program med flera nivåer](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Redundans Grupp2**: växla över virtuella datorer för Dynamics AX-klienten. Växla över virtuella datorer på webbnivå som en del av återställningsplanen.


### <a name="perform-a-test-failover"></a>Utför ett redundanstest

Mer information om Active Directory under redundanstest, finns i guiden ”Active Directory-haveriberedskapslösning” tillhörande.

Läs mer specifika för SQLServer under redundanstest, i [replikera program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

1. Gå till Azure-portalen och välj din Site Recovery-valvet.

2. Välj återställningsplan som skapats för Dynamics AX.

3. Välj **Redundanstest**.

4. Välj det virtuella nätverket för att börja testa redundans.

5. När den sekundära miljön är igång, kan du utföra dina verifieringar.

6. När verifieringar har slutförts kan du välja **verifieringar slutföra** och redundanstestmiljön har rensats.

Läs mer om hur du utför ett redundanstest [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Utför en redundansväxling

1. Gå till Azure-portalen och välj din Site Recovery-valvet.

2. Välj återställningsplan som skapats för Dynamics AX.

3. Välj **redundans**, och välj **redundans**.

4. Välj målnätverket och **✓** att starta redundansprocessen.

Läs mer om hur du gör en redundansväxling [redundans i Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Utföra en återställning efter fel

Överväganden specifika till SQL Server vid återställning efter fel finns i [replikera program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

1. Gå till Azure-portalen och välj din Site Recovery-valvet.

2. Välj återställningsplan som skapats för Dynamics AX.

3. Välj **redundans**, och välj **redundans**.

4. Välj **ändra riktning**.

5. Välj lämpligt alternativ: datasynkronisering och skapa en virtuell dator.

6. Välj **✓** att starta processen för återställning efter fel.


Mer information om hur du gör en återställning efter fel finns i [återställning efter fel virtuella VMware-datorer från Azure till lokala](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Sammanfattning
Du kan skapa en fullständig automatiserad haveriberedskapsplan för din Dynamics AX-program med hjälp av Site Recovery. I händelse av avbrott, du påbörja redundans på några sekunder från valfri plats och få programmet igång på bara några minuter.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du skyddar arbetsbelastningar med Site Recovery finns [vilka arbetsbelastningar kan jag skydda?](site-recovery-workload.md).
