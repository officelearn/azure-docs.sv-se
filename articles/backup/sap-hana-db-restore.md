---
title: Återställa SAP HANA-databaser på virtuella Azure-datorer
description: I den här artikeln bör du ta reda på hur du återställer SAP HANA-databaser som körs på Virtuella Azure-datorer.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287923"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Återställa SAP HANA-databaser på virtuella Azure-datorer

I den här artikeln beskrivs hur du återställer SAP HANA-databaser som körs på Azure Virtual Machine (VM), som Azure Backup-tjänsten har säkerhetskopierat till ett Azure Backup Recovery Services-valv. Återställningar kan användas för att skapa kopior av data för utvecklings-/testscenarier eller för att återgå till ett tidigare tillstånd.

Mer information om hur du säkerhetskopierar SAP HANA-databaser finns i [Säkerhetskopiera SAP HANA-databaser på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Återställa till en tidpunkt eller till en återställningspunkt

Azure Backup kan återställa SAP HANA-databaser som körs på virtuella Azure-datorer enligt följande:

* Återställ till ett visst datum eller en viss tid (till det andra) med hjälp av loggsäkerhetskopior. Azure Backup bestämmer automatiskt lämpliga fullständiga, differentiella säkerhetskopior och kedjan av loggsäkerhetskopior som krävs för att återställa baserat på den valda tiden.

* Återställ till en specifik fullständig eller differentiell säkerhetskopiering för att återställa till en viss återställningspunkt.

## <a name="prerequisites"></a>Krav

Observera följande innan du återställer en databas:

* Du kan återställa databasen endast till en SAP HANA-instans som finns i samma region

* Målinstansen måste registreras med samma valv som källan

* Azure Backup kan inte identifiera två olika SAP HANA-instanser på samma virtuella dator. Därför är det inte möjligt att återställa data från en instans till en annan på samma virtuella dator

* Kontrollera statusen för **säkerhetsberedskapen** för säkerhetskopiering för att säkerställa att SAP HANA-instansen är klar för återställning:

  * Öppna valvet där mål-SAP HANA-instansen är registrerad

  * Välj **Säkerhetskopiering**under Komma igång på **instrumentpanelen** för valvet

![Säkerhetskopiering i instrumentpanelen i valvet](media/sap-hana-db-restore/getting-started-backup.png)

* I **Säkerhetskopiering**under Vad vill du **SAP HANA in Azure VM** **säkerhetskopiera?**

![Välj SAP HANA i Azure VM](media/sap-hana-db-restore/sap-hana-backup.png)

* Under **Upptäck DBs i virtuella datorer** klicka på Visa **information**

![Visa information](media/sap-hana-db-restore/view-details.png)

* Granska **säkerhetskopieringsberedskapen** för måldatorn

![Skyddade servrar](media/sap-hana-db-restore/protected-servers.png)

* Mer information om de återställningstyper som SAP HANA stöder finns i SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Återställ en databas

* Öppna valvet där SAP HANA-databasen som ska återställas registreras

* På instrumentpanelen för **valvet,** under Skyddade objekt, väljer du **Säkerhetskopieringsobjekt**

![Säkerhetskopior](media/sap-hana-db-restore/backup-items.png)

* I **Säkerhetskopieringsobjekt**väljer du **SAP HANA under** hantering **av säkerhetskopieringstyper** i Azure VM

![Typ av säkerhetskopieringshantering](media/sap-hana-db-restore/backup-management-type.png)

* Markera den databas som ska återställas

 ![Databas som ska återställas](media/sap-hana-db-restore/database-to-restore.png)

* Granska databasmenyn. Den innehåller information om säkerhetskopiering av databaser, inklusive:

  * De äldsta och senaste återställningspunkterna

  * Loggsäkerhetsstatus för de senaste 24 och 72 timmarna för databasen

![Menyn Databas](media/sap-hana-db-restore/database-menu.png)

* Välj **Återställ DB**

* Under **Återställ konfiguration**anger du var (eller hur) som data ska återställas:

  * **Alternativ plats**: Återställ databasen till en annan plats och behåll den ursprungliga källdatabasen.

  * **Skriv över DB:** Återställ data till samma SAP HANA-instans som den ursprungliga källan. Det här alternativet skriver över den ursprungliga databasen.

![Återställ konfiguration](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Återställa till alternativ plats

* Välj **Alternativ plats**under **Var du återställer**på menyn **Återställ konfiguration** .

![Återställa till alternativ plats](media/sap-hana-db-restore/restore-alternate-location.png)

* Välj det SAP HANA-värdnamn och förekomstnamn som du vill återställa databasen till.
* Kontrollera om mål-SAP HANA-instansen är klar för återställning genom att säkerställa **säkerhetskopieringsberedskapen.** Mer information finns i [avsnittet för förutsättningar.](#prerequisites)
* I rutan **Restored DB Name** (Namn på återställd databas) anger du namnet på måldatabasen.

> [!NOTE]
> SDC-återställningar (Single Database Container) måste följa dessa [kontroller](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

* Om tillämpligt väljer du **Skriv över om db med samma namn redan finns på vald HANA-instans**.
* Välj **OK**.

![Återställ konfiguration - sista skärmen](media/sap-hana-db-restore/restore-configuration-last.png)

* I **Välj återställningspunkt**väljer du **Loggar (Tidpunkt)** för att [återställa till en viss tidpunkt](#restore-to-a-specific-point-in-time). Eller välj **Fullständig & differential** för att återställa till en viss [återställningspunkt](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Återställa och skriva över

* Välj **Skriv över DB** > **OK**under Var **du återställer**på menyn **Återställ konfiguration** .

![Åsidosätt databas](media/sap-hana-db-restore/overwrite-db.png)

* I **Välj återställningspunkt**väljer du **Loggar (Tidpunkt)** för att [återställa till en viss tidpunkt](#restore-to-a-specific-point-in-time). Eller välj **Fullständig & differential** för att återställa till en viss [återställningspunkt](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Återställa till en viss tidpunkt

Om du har valt **Loggar (Point in Time)** som återställningstyp gör du följande:

* Välj en återställningspunkt i loggdiagrammet och välj **OK** för att välja återställningspunkt.

![Återställningspunkt](media/sap-hana-db-restore/restore-point.png)

* På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.

![Välj återställning](media/sap-hana-db-restore/restore-restore.png)

* Spåra återställningsstatusen i området **Meddelanden** eller spåra den genom att välja **Återställ jobb** på databasmenyn.

![Återställningen har utlösts](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Återställa till en viss återställningspunkt

Om du har valt **Fullständig & Differential** som återställningstyp gör du följande:

* Välj en återställningspunkt i listan och välj **OK** för att välja återställningspunkten.

![Återställa specifik återställningspunkt](media/sap-hana-db-restore/specific-recovery-point.png)

* På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.

![Starta återställningsjobb](media/sap-hana-db-restore/restore-specific.png)

* Spåra återställningsstatusen i området **Meddelanden** eller spåra den genom att välja **Återställ jobb** på databasmenyn.

![Återställ förloppet](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> I MDC (Multiple Database Container) återställs efter att system-DB har återställts till en målinstans måste man köra förregistreringsskriptet igen. Först då lyckas de efterföljande klient-DB-återställningarna. Mer information finns i [Felsökning – MDC Restore](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du](sap-hana-db-manage.md) hanterar SAP HANA-databaser som säkerhetskopierats med Azure Backup
