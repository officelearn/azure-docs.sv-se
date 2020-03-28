---
title: Självstudiekurs - SAP HANA DB-återställning på Azure med CLI
description: I den här självstudien kan du lära dig hur du återställer SAP HANA-databaser som körs på en Azure VM från ett Azure Backup Recovery Services-valv med Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75472074"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Självstudiekurs: Återställ SAP HANA-databaser i en Virtuell Azure-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller genom skript. Den här dokumentationen beskriver hur du återställer en säkerhetskopierad SAP HANA-databas på en Azure VM - med Hjälp av Azure CLI. Du kan också utföra dessa steg med [Azure-portalen](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).

Använd [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) för att köra CLI-kommandon.

I slutet av den här självstudien kommer du att kunna:

> [!div class="checklist"]
>
> * Visa återställningspunkter för en säkerhetskopierad databas
> * Återställ en databas

Den här självstudien förutsätter att du har en SAP HANA-databas som körs på Azure VM som säkerhetskopieras med Azure Backup. Om du har använt [Säkerhetskopiera en SAP HANA-databas i Azure med CLI](tutorial-sap-hana-backup-cli.md) för att säkerhetskopiera DIN SAP HANA-databas använder du följande resurser:

* en resursgrupp med namnet *saphanaResourceGroup*
* ett valv som heter *saphanaVault*
* skyddad behållare med namnet *VMAppContainer; Beräkna;saphanaResourceGroup;saphanaVM*
* säkerhetskopierade databas/objekt med namnet *saphanadatabase;hxe;hxe*
* resurser i *regionen Westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Visa återställningspunkter för en säkerhetskopierad databas

Om du vill visa listan över alla återställningspunkter för en databas använder du cmdlet för [listan för återställningspunkt för az-säkerhetskopiering](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) enligt följande:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

Listan över återställningspunkter kommer att se ut på följande sätt:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Som du kan se innehåller listan ovan tre återställningspunkter: en vardera för fullständig, differentiell och log backup.

>[!NOTE]
>Du kan också visa start- och slutpunkterna för varje obruten log-säkerhetskopieringskedja med hjälp av [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) cmdlet.

## <a name="prerequisites-to-restore-a-database"></a>Förutsättningar för att återställa en databas

Kontrollera att följande förutsättningar uppfylls innan du återställer en databas:

* Du kan återställa databasen endast till en SAP HANA-instans som finns i samma region
* Målinstansen måste registreras med samma valv som källan
* Azure Backup kan inte identifiera två olika SAP HANA-instanser på samma virtuella dator. Därför är det inte möjligt att återställa data från en instans till en annan på samma virtuella dator.

## <a name="restore-a-database"></a>Återställ en databas

Azure Backup kan återställa SAP HANA-databaser som körs på virtuella Azure-datorer enligt följande:

* Återställ till ett visst datum eller en viss tid (till det andra) med hjälp av loggsäkerhetskopior. Azure Backup bestämmer automatiskt lämpliga fullständiga, differentiella säkerhetskopior och kedjan av loggsäkerhetskopior som krävs för att återställa baserat på den valda tiden.
* Återställ till en specifik fullständig eller differentiell säkerhetskopiering för att återställa till en viss återställningspunkt.

Om du vill återställa en databas använder du cmdlet för [az restore-azurewl,](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) vilket kräver ett återställningskonfigurationsobjekt som en av ingångarna. Det här objektet kan genereras med hjälp av [az backup recoveryconfig visa](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) cmdlet. Config-objektet för återställning innehåller alla detaljer för att utföra en återställning. En av dem är återställningsläget – **OriginalWorkloadRestore** eller **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** - Återställ data till samma SAP HANA-instans som den ursprungliga källan. Det här alternativet skriver över den ursprungliga databasen. <br>
> **AlternateWorkloadRestore** - Återställ databasen till en alternativ plats och behåll den ursprungliga källdatabasen.

## <a name="restore-to-alternate-location"></a>Återställa till alternativ plats

Om du vill återställa en databas till en annan plats använder du **AlternateWorkloadRestore** som återställningsläge. Du måste sedan välja återställningspunkt, som antingen kan vara en tidigare point-in-time eller någon av de tidigare återställningspunkterna.

I den här självstudien återställer du till en tidigare återställningspunkt. [Visa listan över återställningspunkter](#view-restore-points-for-a-backed-up-database) för databasen och välj den punkt som du vill återställa till. Den här självstudien kommer att använda återställningspunkten med namnet *7660777527047692711*.

Med hjälp av ovanstående återställningspunkt namn och återställningsläge, låt oss skapa återställning config objekt med hjälp av [az backup recoveryconfig visa](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) cmdlet. Låt oss titta på vad var och en av de återstående parametrarna i denna cmdlet betyder:

* **--mål-objekt-namn** Det här är namnet som den återställda databasen kommer att använda. I det här fallet använde vi namnet *restored_database*.
* **--target-server-namn** Det här är namnet på en SAP HANA-server som har registrerats i ett återhämtningstjänstvalv och ligger i samma region som databasen som ska återställas. För den här guiden återställer vi databasen till samma SAP HANA-server som vi har skyddat, med namnet *hxehost*.
* **--target-server-typ** För återställning av SAP HANA-databaser måste **SapHanaDatabase** användas.

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

Svaret på ovanstående fråga kommer att vara en återställning config objekt som ser ut ungefär så här:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Nu, för att återställa databasen köra [az återställa-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet. För att använda det här kommandot kommer vi att ange ovanstående json-utdata som sparas i en fil med namnet *recoveryconfig.json*.

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

Svaret ger dig jobbnamnet. Det här jobbnamnet kan användas för att spåra jobbstatus med [az backup jobb visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="restore-and-overwrite"></a>Återställa och skriva över

Om du vill återställa till den ursprungliga platsen använder vi **OrignialWorkloadRestore** som återställningsläge. Du måste sedan välja återställningspunkt, som antingen kan vara en tidigare point-in-time eller någon av de tidigare återställningspunkterna.

För den här självstudien väljer vi föregående point-in-time "28-11-2019-09:53:00" att återställa till. Du kan ange den här återställningspunkten i följande format: dd-mm-yyyy, dd-mm-yyyy-hh:mm:ss. Om du vill välja en giltig point-in-time att återställa till använder du [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) cmdlet, som listar intervallen för obrutna log chain-säkerhetskopior.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

Svaret på ovanstående fråga kommer att vara en återställning config objekt som ser ut enligt följande:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Nu, för att återställa databasen köra [az återställa-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet. För att använda det här kommandot kommer vi att ange ovanstående json-utdata som sparas i en fil med namnet *recoveryconfig.json*.

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

Svaret ger dig jobbnamnet. Det här jobbnamnet kan användas för att spåra jobbstatus med hjälp av [az-säkerhetskopieringsjobbet](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Nästa steg

* Om du vill veta hur du hanterar SAP HANA-databaser som säkerhetskopieras med Azure CLI fortsätter du självstudien [Hantera en SAP HANA-databas i Azure VM med CLI](tutorial-sap-hana-backup-cli.md)

* Mer information om hur du återställer en SAP HANA-databas som körs i Azure VM med Azure-portalen finns i [Återställa en SAP HANA-databaser på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
