---
title: Återställa SAP HANA databaser på virtuella Azure-datorer
description: I den här artikeln får du lära dig hur du återställer SAP HANA databaser som körs på Azure Virtual Machines.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74287923"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Återställa SAP HANA databaser på virtuella Azure-datorer

Den här artikeln beskriver hur du återställer SAP HANA databaser som körs på den virtuella Azure-datorn (VM), att Azure Backup-tjänsten har säkerhetskopierats till ett Azure Backup Recovery Services-valv. Återställningar kan användas för att skapa kopior av data för utvecklings-och test scenarier eller för att återgå till ett tidigare tillstånd.

Mer information om hur du säkerhetskopierar SAP HANA databaser finns i [säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Återställa till en tidpunkt eller till en återställnings punkt

Azure Backup kan återställa SAP HANA databaser som körs på virtuella Azure-datorer på följande sätt:

* Återställ till ett visst datum eller en angiven tidpunkt (till den andra) med hjälp av logg säkerhets kopior. Azure Backup identifierar automatiskt lämpliga fullständiga, differentiella säkerhets kopior och kedja av logg säkerhets kopior som krävs för att återställa baserat på den valda tiden.

* Återställ till en viss fullständig eller differentiell säkerhets kopia för att återställa till en viss återställnings punkt.

## <a name="prerequisites"></a>Krav

Observera följande innan du återställer en databas:

* Du kan bara återställa databasen till en SAP HANA-instans i samma region

* Mål instansen måste registreras med samma valv som källan

* Azure Backup kan inte identifiera två olika SAP HANAs instanser på samma virtuella dator. Därför är det inte möjligt att återställa data från en instans till en annan på samma virtuella dator

* Kontrol lera statusen för **säkerhets kopierings beredskap** för att säkerställa att mål SAP HANAs instansen är redo för återställning:

  * Öppna valvet där mål SAP HANAs instansen är registrerad

  * På instrument panelen för valvet, under **komma igång**, väljer du **säkerhets kopiering**

![Säkerhets kopiering i valvets instrument panel](media/sap-hana-db-restore/getting-started-backup.png)

* Under **säkerhets kopiering**under **vad vill du säkerhetskopiera? väljer du** **SAP HANA i virtuell Azure-dator**

![Välj SAP HANA i virtuell Azure-dator](media/sap-hana-db-restore/sap-hana-backup.png)

* Klicka på **Visa information** under **identifiera databaser i virtuella datorer**

![Visa information](media/sap-hana-db-restore/view-details.png)

* Granska **beredskap för säkerhets kopieringen** för den virtuella mål datorn

![Skyddade servrar](media/sap-hana-db-restore/protected-servers.png)

* Mer information om de återställnings typer som SAP HANA stöder finns i SAP HANA anmärkning [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Återställ en databas

* Öppna valvet där SAP HANA databas som ska återställas har registrerats

* Välj **säkerhets kopierings objekt** under **skyddade objekt**på instrument panelen för valvet

![Säkerhetskopiera objekt](media/sap-hana-db-restore/backup-items.png)

* Under **säkerhets kopierings objekt**, under **säkerhets kopierings hanterings typ** väljer du **SAP HANA i Azure VM**

![Typ av säkerhets kopierings hantering](media/sap-hana-db-restore/backup-management-type.png)

* Välj den databas som ska återställas

 ![Databas som ska återställas](media/sap-hana-db-restore/database-to-restore.png)

* Granska menyn databas. Den innehåller information om säkerhets kopiering av databasen, inklusive:

  * De äldsta och senaste återställnings punkterna

  * Status för logg säkerhets kopiering under de senaste 24 och 72 timmarna för databasen

![Menyn databas](media/sap-hana-db-restore/database-menu.png)

* Välj **Återställ databas**

* Under **Återställ konfiguration**anger du var (eller hur) du vill återställa data:

  * **Alternativ plats**: Återställ databasen till en annan plats och behåll den ursprungliga käll databasen.

  * **Skriv över DB**: Återställ data till samma SAP HANA instans som den ursprungliga källan. Med det här alternativet skrivs den ursprungliga databasen över.

![Återställ konfiguration](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Återställ till en annan plats

* I menyn **Återställ konfiguration** , under **återställnings**läge, väljer du **alternativ plats**.

![Återställ till en annan plats](media/sap-hana-db-restore/restore-alternate-location.png)

* Välj det SAP HANA värddator namn och instans namn som du vill återställa databasen till.
* Kontrol lera om mål SAP HANAs instansen är redo för återställning genom att se till att **säkerhets kopieringen** är klar. Mer information finns i [avsnittet krav](#prerequisites) .
* I rutan **Restored DB Name** (Namn på återställd databas) anger du namnet på måldatabasen.

> [!NOTE]
> Återställningar av Enkel databas container (SDC) måste följa dessa [kontroller](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

* Om det är tillämpligt väljer du **Skriv över om databasen med samma namn redan finns på den valda Hana-instansen**.
* Välj **OK**.

![Återställa konfiguration – slutlig skärm](media/sap-hana-db-restore/restore-configuration-last.png)

* I **Välj återställnings punkt**väljer du **loggar (tidpunkt)** för att [återställa till en viss tidpunkt](#restore-to-a-specific-point-in-time). Eller Välj **fullständig & differentiell** för att [återställa till en viss återställnings punkt](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Återställ och skriv över

* I menyn **Återställ konfiguration** , under **återställningen**, väljer du **Skriv över DB** > **OK**.

![Åsidosätt databas](media/sap-hana-db-restore/overwrite-db.png)

* I **Välj återställnings punkt**väljer du **loggar (tidpunkt)** för att [återställa till en viss tidpunkt](#restore-to-a-specific-point-in-time). Eller Välj **fullständig & differentiell** för att [återställa till en viss återställnings punkt](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Återställ till en viss tidpunkt

Om du har valt **loggar (tidpunkt)** som återställnings typ gör du följande:

* Välj en återställnings punkt i logg diagrammet och välj sedan **OK** för att välja återställnings punkten.

![Återställningspunkt](media/sap-hana-db-restore/restore-point.png)

* På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.

![Välj Återställ](media/sap-hana-db-restore/restore-restore.png)

* Spåra återställnings förloppet i **aviserings** området eller spåra den genom att välja **återställnings jobb** på menyn databas.

![Återställningen har utlösts](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Återställa till en viss återställnings punkt

Om du har valt **fullständig & differentiell** som återställnings typ gör du följande:

* Välj en återställnings punkt i listan och välj sedan **OK** för att välja återställnings punkten.

![Återställa en viss återställnings punkt](media/sap-hana-db-restore/specific-recovery-point.png)

* På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.

![Starta återställnings jobb](media/sap-hana-db-restore/restore-specific.png)

* Spåra återställnings förloppet i **aviserings** området eller spåra den genom att välja **återställnings jobb** på menyn databas.

![Återställnings förlopp](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> I MDC (Multiple Database container) återställs när system databasen har återställts till en mål instansen måste en köra skriptet för för registrering igen. Det går bara att återställa efterföljande klient databas återställningar. Mer information hittar du i [fel sökning – MDC Restore](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur](sap-hana-db-manage.md) du hanterar SAP HANA databaser som har säkerhetskopierats med Azure Backup
