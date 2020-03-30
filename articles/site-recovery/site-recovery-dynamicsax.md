---
title: Haveriberedskap för Dynamics AX med Azure Site Recovery
description: Lär dig hur du konfigurerar haveriberedskap för Dynamics AX med Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 0b32f00374aa8ce6c41415e28f319e3e7d5abddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941595"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Konfigurera haveriberedskap för ett multitiert Dynamics AX-program   




 Dynamics AX är en av de mest populära affärssystemlösningar som används av företag för att standardisera processer på olika platser, hantera resurser och förenkla efterlevnaden. Eftersom programmet är avgörande för en organisation, i händelse av en katastrof, bör programmet vara igång på korttid.

Idag tillhandahåller Dynamics AX inga funktioner för in-the-box-katastrofåterställning. Dynamics AX består av många serverkomponenter, till exempel Windows Application Object Server, Azure Active Directory, Azure SQL Database, SharePoint Server och Reporting Services. Att hantera haveriberedskapen för var och en av dessa komponenter manuellt är inte bara dyrt utan också felbenägna.

I den här artikeln beskrivs hur du kan skapa en katastrofåterställningslösning för ditt Dynamics AX-program med hjälp av [Azure Site Recovery](site-recovery-overview.md). Den omfattar även planerade/oplanerade testundans med hjälp av en återställningsplan med ett klick, konfigurationer och förutsättningar som stöds.



## <a name="prerequisites"></a>Krav

Implementera haveriberedskap för Dynamics AX-program med hjälp av Site Recovery kräver följande förutsättningar:

• Ställ in en lokal Dynamics AX-distribution.

• Skapa ett site recovery-valv i en Azure-prenumeration.

• Om Azure är din återställningsplats kör du verktyget för bedömning av azure virtual machine readiness assessment på de virtuella datorerna. De måste vara kompatibla med Azure Virtual Machines och Site Recovery services.

## <a name="site-recovery-support"></a>Site Recovery-stöd

I den här artikeln använde vi virtuella VMware-datorer med Dynamics AX 2012 R3 på Windows Server 2012 R2 Enterprise. Eftersom site recovery-replikering är programagnostiker förväntar vi oss att rekommendationerna här ska spärras för följande scenarier.

### <a name="source-and-target"></a>Källa och mål

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**Vmware** | Ja | Ja
**Fysisk server** | Ja | Ja

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Aktivera haveriberedskap av Dynamics AX-programmet med hjälp av Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Skydda ditt Dynamics AX-program
För att aktivera fullständig programreplikering och återställning måste varje komponent i Dynamics AX skyddas.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Konfigurera Active Directory- och DNS-replikering

Active Directory krävs på haveriberedskapsplatsen för att Dynamics AX-programmet ska fungera. Vi rekommenderar följande två alternativ baserat på komplexiteten i kundens lokala miljö.

**Alternativ 1**

Kunden har ett litet antal program och en enda domänkontrollant för hela den lokala platsen och planerar att växla över hela webbplatsen tillsammans. Vi rekommenderar att du använder Site Recovery-replikering för att replikera domänkontrollantdatorn till en sekundär plats (gäller för både plats-till-plats- och plats-till-Azure-scenarier).

**Alternativ 2**

Kunden har ett stort antal program och kör en Active Directory-skog och planerar att växla över några program åt gången. Vi rekommenderar att du konfigurerar ytterligare en domänkontrollant på platsen för haveriberedskap (en sekundär plats eller i Azure).

 Mer information finns i [Göra en domänkontrollant tillgänglig på en plats för haveriberedskap](site-recovery-active-directory.md). För resten av det här dokumentet förutsätter vi att en domänkontrollant är tillgänglig på haveriberedskapsplatsen.

### <a name="2-set-up-sql-server-replication"></a>2. Konfigurera SQL Server-replikering
Teknisk vägledning om det rekommenderade alternativet för att skydda SQL-nivån finns i [Replikera program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Aktivera skydd för virtuella dynamics AX-klient- och programobjektserver
Utför relevant konfiguration för platsåterställning baserat på om de virtuella datorerna distribueras på [Hyper-V](site-recovery-hyper-v-site-to-azure.md) eller [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Vi rekommenderar att du konfigurerar den kraschkonsekventa frekvensen till 15 minuter.
>

Följande ögonblicksbild visar skyddsstatus för virtuella dynamics-komponent-datorer i ett VMware-site-to-Azure-skyddsscenario.

![Skyddade objekt](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Konfigurera nätverk
**Konfigurera VM-beräknings- och nätverksinställningar**

Konfigurera nätverksinställningar i Site Recovery för Dynamics AX-klienten och Application Object Server så att VM-nätverken kopplas till rätt katastrofåterställningsnätverk efter redundans. Kontrollera att haveriberedskapsnätverket för dessa nivåer är dirigerbart till SQL-nivån.

Du kan välja den virtuella datorn i de replikerade objekten för att konfigurera nätverksinställningarna, vilket visas i följande ögonblicksbild:

* För Programobjektserverservrar väljer du rätt tillgänglighetsuppsättning.

* Om du använder en statisk IP anger du den IP som du vill att den virtuella datorn ska ta i textrutan **Mål-IP.**

    ![Nätverksinställningar](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Skapa en återhämtningsplan

Du kan skapa en återställningsplan i Site Recovery för att automatisera redundansprocessen. Lägg till en appnivå och en webbnivå i återställningsplanen. Beställ dem i olika grupper så att frontend stängs av före appnivån.

1. Välj valv för webbplatsåterställning i prenumerationen och välj panelen **Återställningsplaner.**

2. Välj **+ Återställningsplan**och ange ett namn.

3. Välj **källa** och **mål**. Målet kan vara Azure eller en sekundär plats. Om du väljer Azure måste du ange distributionsmodellen.

    ![Skapa en återställningsplan](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Välj programobjektservern och klientens virtuella datorer för återställningsplanen och välj ✓.

    ![Markera objekt](./media/site-recovery-dynamics-ax/selectvms.png)

    Exempel på återställningsplan:

    ![Information om återställningsplan](./media/site-recovery-dynamics-ax/recoveryplan.png)

Du kan anpassa återställningsplanen för Dynamics AX-programmet genom att lägga till följande steg. Den föregående ögonblicksbilden visar hela återställningsplanen när du har lagt till alla steg.


* **SQL Server redundanssteg:** Information om återställningssteg som är specifika för SQL-servern finns i [Replikeringsprogram med SQL Server och Azure Site Recovery](site-recovery-sql.md).

* **Redundansgrupp 1:** Växla över virtuella datorer för programobjektservern.
Kontrollera att den valda återställningspunkten är så nära databasen PIT som möjligt, men inte före den.

* **Skript**: Lägg till belastningsutjämnare (endast E-A).
Lägg till ett skript (via Azure Automation) när vm-gruppen Application Object Server har lagt till en belastningsutjämnare i den. Du kan använda ett skript för att utföra den här uppgiften. Mer information finns i [Så här lägger du till en belastningsutjämnare för multitier-programkatastrofåterställning](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Redundansgrupp 2:** Växla över virtuella dynamics AX-klient-klientdatorerna. Växla över virtuella datorer på webbnivå som en del av återställningsplanen.


### <a name="perform-a-test-failover"></a>Utföra en testväxling

Mer information som är specifik för Active Directory under test redundans finns i kompletterande guide för "Active Directory disaster recovery solution".

Mer information som är specifik för SQL-servern under test redundans finns i [Replikera program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

1. Gå till Azure-portalen och välj ditt site recovery-valv.

2. Välj den återställningsplan som skapats för Dynamics AX.

3. Välj **Testa redundans**.

4. Välj det virtuella nätverket för att starta test failover-processen.

5. När den sekundära miljön är kan du utföra dina valideringar.

6. När valideringarna är klara väljer du **Valideringar slutförda** och testundanundansmiljön rensas.

Mer information om hur du utför en testväxling finns i [Testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Utföra en redundans

1. Gå till Azure-portalen och välj ditt site recovery-valv.

2. Välj den återställningsplan som skapats för Dynamics AX.

3. Välj **Redundans**och välj **Redundans**.

4. Välj målnätverket och välj **✓** för att starta redundansprocessen.

Mer information om hur du gör en redundans finns [i Redundans i Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Utföra en återställning efter fel

Om du vill ha några säröverväganden som är specifika för SQL Server under återställning efter fel finns [i Replikera program med SQL Server och Azure Site Recovery](site-recovery-sql.md).

1. Gå till Azure-portalen och välj ditt site recovery-valv.

2. Välj den återställningsplan som skapats för Dynamics AX.

3. Välj **Redundans**och välj **Redundans**.

4. Välj **Ändra riktning**.

5. Välj lämpliga alternativ: datasynkronisering och skapande av virtuella datorer.

6. Välj **✓** om du vill starta återställningen av återställningen.


Mer information om hur du gör en återställning efter fel finns i Virtuella datorer med återställning av [VMware från Azure till lokala](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Sammanfattning
Genom att använda Site Recovery kan du skapa en fullständig automatisk haveriberedskapsplan för ditt Dynamics AX-program. I händelse av ett avbrott kan du initiera redundansen inom några sekunder från var som helst och få igång programmet på några minuter.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du skyddar företagets arbetsbelastningar med Site Recovery finns i [Vilka arbetsbelastningar kan jag skydda?](site-recovery-workload.md)
