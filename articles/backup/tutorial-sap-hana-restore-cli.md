---
title: Självstudie – SAP HANA DB-återställning på Azure med hjälp av CLI
description: I den här självstudien får du lära dig hur du återställer SAP HANA databaser som körs på en virtuell Azure-dator från ett Azure Backup Recovery Services valv med Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472074"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Självstudie: återställa SAP HANA-databaser på en virtuell Azure-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommando raden eller genom skript. Den här dokumentationen innehåller information om hur du återställer en säkerhets kopie rad SAP HANA-databas på en virtuell Azure-dator – med Azure CLI. Du kan också utföra dessa steg med hjälp av [Azure Portal](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).

Använd [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) för att köra CLI-kommandon.

I slutet av den här självstudien kommer du att kunna:

> [!div class="checklist"]
>
> * Visa återställnings punkter för en säkerhets kopie rad databas
> * Återställ en databas

Den här självstudien förutsätter att du har en SAP HANA databas som körs på den virtuella Azure-datorn som säkerhets kopie ras med Azure Backup. Om du har använt [säkerhetskopiera en SAP HANA databas i Azure med hjälp av CLI](tutorial-sap-hana-backup-cli.md) för att säkerhetskopiera SAP HANA-databasen använder du följande resurser:

* en resurs grupp med namnet *saphanaResourceGroup*
* ett valv med namnet *saphanaVault*
* skyddad behållare med namnet *VMAppContainer; Compute; saphanaResourceGroup; saphanaVM*
* säkerhetskopierade databasen/objektet med namnet *saphanadatabase; hxe; hxe*
* resurser i regionen *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Visa återställnings punkter för en säkerhets kopie rad databas

Om du vill visa en lista över alla återställnings punkter för en databas använder du [AZ backup recoverypoint List](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) cmdlet enligt följande:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

Listan över återställnings punkter kommer att se ut så här:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Som du kan se innehåller listan ovan tre återställnings punkter: en för fullständig, differentiell och logg säkerhets kopiering.

>[!NOTE]
>Du kan också visa start-och slut punkterna för varje skadad logg säkerhets kopierings kedja med hjälp av [AZ backup recoverypoint show-log-kedje](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) cmdlet.

## <a name="prerequisites-to-restore-a-database"></a>Krav för att återställa en databas

Kontrol lera att följande krav är uppfyllda innan du återställer en databas:

* Du kan bara återställa databasen till en SAP HANA-instans i samma region
* Mål instansen måste registreras med samma valv som källan
* Azure Backup kan inte identifiera två olika SAP HANAs instanser på samma virtuella dator. Därför går det inte att återställa data från en instans till en annan på samma virtuella dator.

## <a name="restore-a-database"></a>Återställ en databas

Azure Backup kan återställa SAP HANA databaser som körs på virtuella Azure-datorer på följande sätt:

* Återställ till ett visst datum eller en angiven tidpunkt (till den andra) med hjälp av logg säkerhets kopior. Azure Backup identifierar automatiskt lämpliga fullständiga, differentiella säkerhets kopior och kedja av logg säkerhets kopior som krävs för att återställa baserat på den valda tiden.
* Återställ till en viss fullständig eller differentiell säkerhets kopia för att återställa till en viss återställnings punkt.

Om du vill återställa en databas använder du cmdleten AZ Restore [-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) , som kräver ett återställnings konfigurations objekt som en av indata. Det här objektet kan genereras med [AZ backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) cmdlet. Återställnings konfigurations objekt innehåller all information för att utföra en återställning. En av dem är återställnings läge – **OriginalWorkloadRestore** eller **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** – Återställ data till samma SAP HANA instans som den ursprungliga källan. Med det här alternativet skrivs den ursprungliga databasen över. <br>
> **AlternateWorkloadRestore** – Återställ databasen till en annan plats och behåll den ursprungliga käll databasen.

## <a name="restore-to-alternate-location"></a>Återställ till en annan plats

Om du vill återställa en databas till en annan plats använder du **AlternateWorkloadRestore** som återställnings läge. Du måste sedan välja återställnings punkten, som antingen kan vara en tidigare tidpunkt eller någon av de tidigare återställnings punkterna.

I den här självstudien kommer du att återställa till en tidigare återställnings punkt. [Visa listan över återställnings punkter](#view-restore-points-for-a-backed-up-database) för databasen och välj den punkt som du vill återställa till. I den här självstudien används återställnings punkten med namnet *7660777527047692711*.

Med hjälp av återställnings punkt namnet ovan och återställnings läget ska vi skapa återställnings konfigurations objekt med hjälp av [AZ backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) cmdlet. Nu ska vi titta på vad var och en av de återstående parametrarna i denna cmdlet innebär:

* **--mål objekt namn** Detta är det namn som den återställda databasen kommer att använda. I det här fallet använde vi namnet *restored_database*.
* **--mål-server namn** Detta är namnet på en SAP HANA-server som har registrerats till ett Recovery Services-valv och som finns i samma region som databasen som ska återställas. I den här självstudien kommer vi att återställa databasen till samma SAP HANA-server som vi har skyddat, med namnet *hxehost*.
* **--mål-server-typ** **SapHanaDatabase** måste användas för att återställa SAP HANA-databaser.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

Svaret på ovanstående fråga är ett återställnings konfigurations objekt som ser ut ungefär så här:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

För att återställa databasen kör [AZ Restore-azurewl-](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdleten. För att kunna använda det här kommandot kommer vi att ange ovanstående JSON-utdata som sparas i en fil med namnet *recoveryconfig. JSON*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Utdata kommer att se ut så här:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Med svaret får du jobb namnet. Det här jobb namnet kan användas för att spåra jobb statusen med [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="restore-and-overwrite"></a>Återställ och skriv över

För att återställa till den ursprungliga platsen använder vi **OrignialWorkloadRestore** som återställnings läge. Du måste sedan välja återställnings punkten, som antingen kan vara en tidigare tidpunkt eller någon av de tidigare återställnings punkterna.

I den här självstudien väljer vi den föregående tidpunkten "28-11-2019-09:53:00" för att återställa till. Du kan tillhandahålla den här återställnings punkten i följande format: dd-mm-åååå, dd-mm-åååå-hh: mm: SS. Du kan välja en giltig tidpunkt för återställning till genom att använda cmdleten [AZ backup recoverypoint show-log-kedje](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) , som visar intervallen med brutna logg säkerhets kopior.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

Svaret på ovanstående fråga är ett återställnings konfigurations objekt som ser ut så här:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

För att återställa databasen kör [AZ Restore-azurewl-](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdleten. För att kunna använda det här kommandot kommer vi att ange ovanstående JSON-utdata som sparas i en fil med namnet *recoveryconfig. JSON*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Utdata kommer att se ut så här:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Med svaret får du jobb namnet. Det här jobb namnet kan användas för att spåra jobb status med hjälp av [AZ säkerhets kopierings jobb show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om hur du hanterar SAP HANA databaser som säkerhets kopie ras med Azure CLI fortsätter du till självstudien [hantera en SAP HANA databas i virtuell Azure-dator med CLI](tutorial-sap-hana-backup-cli.md)

* Information om hur du återställer en SAP HANA databas som körs på en virtuell Azure-dator med hjälp av Azure Portal finns i [återställa en SAP HANA-databas på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
