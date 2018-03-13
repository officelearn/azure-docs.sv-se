---
title: "Replikera en flera skikt Dynamics AX-distribution med hjälp av Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du replikera och skydda Dynamics AX med hjälp av Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: asgang
ms.openlocfilehash: b390f6c62a6ddf8c800f79b42a36dac2c4f4c908
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="replicate-a-multitier-dynamics-ax-application-by-using-azure-site-recovery"></a>Replikera en programarkitektur i Dynamics AX med hjälp av Azure Site Recovery

## <a name="overview"></a>Översikt


 Dynamics AX är en av de mest populära ERP-lösningar som används av företag för att standardisera processer i platser, hantera resurser och förenkla efterlevnad. Eftersom programmet är viktiga för en organisation, vid en katastrof måste programmet vara igång i minimitid.

Idag, ger Dynamics AX inte några out box-katastrofåterställning återställningsfunktioner. Dynamics AX består av flera serverkomponenter, till exempel Windows Application Object Server, Azure Active Directory, Azure SQL Database, SharePoint Server och Reporting Services. Om du vill hantera haveriet är återställning av var och en av dessa komponenter manuellt inte bara dyra men även fel felbenägna.

Den här artikeln förklarar hur du kan skapa en lösning för katastrofåterställning för Dynamics AX-program med hjälp av [Azure Site Recovery](site-recovery-overview.md). Den omfattar också planerad/oplanerad redundanstestning med hjälp av en återställningsplan med ett klick, konfigurationer som stöds och förutsättningar.



## <a name="prerequisites"></a>Förutsättningar

Implementera haveriberedskap för Dynamics AX-program med hjälp av Site Recovery kräver följande förutsättningar:

• Konfigurera en lokal Dynamics AX-distribution.

• Skapa ett Site Recovery-valv i en Azure-prenumeration.

• Om Azure är din återställningsplatsen köra verktyget Azure virtuella Readiness Assessment på virtuella datorer. De måste vara kompatibel med virtuella datorer i Azure och Site Recovery services.

## <a name="site-recovery-support"></a>Site Recovery-stöd

För att skapa den här artikeln kan använda vi virtuella VMware-datorer med Dynamics AX 2012 R3 körs på Windows Server 2012 R2 Enterprise. Eftersom site recovery replikering är oberoende av programmet, planerar vi rekommendationerna här för att rymma för följande scenarier.

### <a name="source-and-target"></a>Källa och mål

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja
**Fysisk server** | Ja | Ja

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Aktivera återställning av Dynamics AX-program med hjälp av Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Skydda din Dynamics AX-program
Om du vill aktivera replikering för hela programmet och återställning, måste varje komponent av Dynamics AX skyddas.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Konfigurera Active Directory och DNS-replikering

Active Directory krävs för disaster recovery platsen för Dynamics AX-programmet ska fungera. Vi rekommenderar följande alternativ baserat på komplexiteten i kundens lokala miljö.

**Alternativ 1**

Kunden har ett litet antal program och en enda domänkontrollant för hela lokal plats och planerar att redundansväxla hela webbplatsen. Vi rekommenderar att du använder Site Recovery replikering för att replikera domain controller dator till en sekundär plats (gäller för både plats-till-plats och platsen till Azure).

**Alternativ 2**

Kunden har ett stort antal program och kör en Active Directory-skog och planer för att växla över några program i taget. Vi rekommenderar att du konfigurerar ytterligare en domänkontrollant på webbplatsen disaster recovery (en sekundär plats eller i Azure).

 Mer information finns i [tillgängliggöra en domänkontrollant på en plats för disaster recovery](site-recovery-active-directory.md). För resten av det här dokumentet förutsätter att en domänkontrollant är tillgänglig på disaster recovery-webbplatsen.

### <a name="2-set-up-sql-server-replication"></a>2. Konfigurera SQL Server-replikering
Teknisk information om det rekommenderade alternativet för att skydda SQL-nivå finns [replikera program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Aktivera skydd för Dynamics AX-klienten och programmet objektet Server-datorer
Konfigurera relevanta Site Recovery baserat på om de virtuella datorerna har distribuerats på [Hyper-V](site-recovery-hyper-v-site-to-azure.md) eller [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Vi rekommenderar att du konfigurerar kraschkonsekvent frekvensen till 15 minuter.
>

Följande ögonblicksbilden visar skydd för virtuella datorer Dynamics-komponenten i ett scenario med VMware-platsen till Azure-skydd.

![Skyddade objekt](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Konfigurera nätverk
**Konfigurera VM beräknings- och nätverksinställningar**

Konfigurera nätverksinställningar i Site Recovery för Dynamics AX-klienten och programmet objektet Server virtuella datorer, så att Virtuella datornätverk hämta ansluten till rätt disaster recovery-nätverket efter redundans. Kontrollera att disaster recovery-nätverk för de här nivåerna är dirigerbara till SQL-nivån.

Du kan välja den virtuella datorn i de replikerade objekt du konfigurerar nätverksinställningar, enligt följande ögonblicksbilden:

* Välj rätt tillgänglighetsuppsättningen för AOS-servrar.

* Om du använder en statisk IP-adress, ange IP-adress som du vill att den virtuella datorn i den **mål-IP** textruta.

    ![Nätverksinställningar ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png).


### <a name="5-create-a-recovery-plan"></a>5. Skapa en återställningsplan

Du kan skapa en återställningsplan i Site Recovery för att automatisera processen med växling vid fel. Lägg till en app-nivå och en webbnivå i återställningsplanen. Ordna dem i olika grupper så att frontend stängas av innan app-nivå.

1. Välj Site Recovery-valvet i din prenumeration och välj den **Återställningsplaner** panelen.

2. Välj **+ återställningsplanen**, och ange ett namn.

3. Välj den **källa** och **mål**. Målet kan vara Azure eller en sekundär plats. Om du väljer Azure måste du ange distributionsmodellen.

    ![Skapa en återställningsplan](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Markera objektet programservern och klienten virtuella datorer för återställningsplanen och markera ✓.

    ![Välj objekt](./media/site-recovery-dynamics-ax/selectvms.png)

    Exempel på plan för återställning:

    ![Planera återställningsinformation](./media/site-recovery-dynamics-ax/recoveryplan.png)

Du kan anpassa återställningsplan för Dynamics AX-program genom att lägga till följande steg. Den tidigare ögonblicksbilden visar slutförd återställningsplanen när du har lagt till alla åtgärder.


* **SQL Server-redundans steg**: information om åtgärder för återställning specifika till SQLServer finns [replikering program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

* **Redundans grupp 1**: växla över programmet objektet Server-datorer.
Se till att den valda återställningspunkten är så nära som möjligt till databasen PIT, men inte i den.

* **Skriptet**: Lägg till belastningsutjämnare (endast E-A).
Lägg till ett skript (via Azure Automation) efter programmet objektet Server VM-gruppen visas för att lägga till en belastningsutjämnare. Du kan använda ett skript för att göra detta. Mer information finns i [hur du lägger till en belastningsutjämnare för katastrofåterställning för programarkitektur](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Redundans Grupp2**: växla över Dynamics AX-klienten virtuella datorer. Växla över webbnivå virtuella datorer som en del i återställningsplanen.


### <a name="perform-a-test-failover"></a>Utför ett redundanstest

Mer information om Active Directory under redundanstestningen, finns i handbok ”Active Directory lösning för katastrofåterställning”.

Mer information om SQLServer under redundanstestningen, finns i [replikera program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

1. Gå till Azure-portalen och välj Site Recovery-valvet.

2. Välj återställningsplanen skapat för Dynamics AX.

3. Välj **Redundanstestningen**.

4. Välj det virtuella nätverket för att starta processen testa redundans.

5. Du kan utföra dina verifieringar när den sekundära miljön är upp.

6. Efter att verifieringar har slutförts, Välj **verifieringar slutföra** och redundanstestmiljön har rensats.

Läs mer om hur du utför ett redundanstest [Redundanstestningen till Azure Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Utför en växling vid fel

1. Gå till Azure-portalen och välj Site Recovery-valvet.

2. Välj återställningsplanen skapat för Dynamics AX.

3. Välj **redundans**, och välj **redundans**.

4. Välj målnätverket och **✓** att starta processen för växling vid fel.

Mer information om hur du gör en växling vid fel finns [redundans i Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Utför en återställning efter fel

Överväganden specifika till SQL Server vid återställning finns [replikera program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

1. Gå till Azure-portalen och välj Site Recovery-valvet.

2. Välj återställningsplanen skapat för Dynamics AX.

3. Välj **redundans**, och välj **redundans**.

4. Välj **ändra riktning**.

5. Markera lämpliga alternativ: synkronisering och skapa en virtuell dator.

6. Välj **✓** att starta processen för återställning efter fel.


Mer information om hur du gör en återställning efter fel finns [återställning av virtuella VMware-datorer från Azure till lokala](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Sammanfattning
Genom att använda Site Recovery kan skapa du en fullständig automatiserad haveriberedskapsplan för Dynamics AX-program. Du kan påbörja redundans inom några sekunder från var som helst och hämta programmet körs i minuter vid ett avbrott.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du skyddar arbetsbelastningar på företag med Site Recovery finns [vilka arbetsbelastningar kan jag skydda?](site-recovery-workload.md).
