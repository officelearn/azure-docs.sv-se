---
title: 'Självstudie: hantera säkerhetskopierade SAP HANA DB med CLI'
description: I den här självstudien får du lära dig hur du hanterar säkerhetskopierade SAP HANA databaser som körs på en virtuell Azure-dator med Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 2c088c27a678a4541cbba3c4c43c9cd830c60ff0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393913"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Självstudie: hantera SAP HANA databaser i en virtuell Azure-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommando raden eller genom skript. Den här dokumentationen beskriver hur du hanterar en säkerhetskopierad SAP HANA-databas på Azure VM – allt med Azure CLI. Du kan också utföra dessa steg med hjälp av [Azure Portal](https://docs.microsoft.com/azure/backup/sap-hana-db-manage).

Använd [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) för att köra CLI-kommandon.

I slutet av den här självstudien kommer du att kunna:

> [!div class="checklist"]
>
> * Övervaka säkerhets kopierings-och återställnings jobb
> * Skydda nya databaser som läggs till i en SAP HANA-instans
> * Ändra principen
> * Stoppa skydd
> * Återuppta skydd

Om du har använt [säkerhetskopiera en SAP HANA databas i Azure med hjälp av CLI](tutorial-sap-hana-backup-cli.md) för att säkerhetskopiera SAP HANA-databasen använder du följande resurser:

* en resurs grupp med namnet *saphanaResourceGroup*
* ett valv med namnet *saphanaVault*
* skyddad behållare med namnet *VMAppContainer; Compute; saphanaResourceGroup; saphanaVM*
* säkerhetskopierade databasen/objektet med namnet *saphanadatabase; hxe; hxe*
* resurser i regionen *westus2*

Med Azure CLI är det enkelt att hantera en SAP HANA databas som körs på en virtuell Azure-dator som säkerhets kopie ras med Azure Backup. Den här självstudien beskriver var och en av hanterings åtgärderna.

## <a name="monitor-backup-and-restore-jobs"></a>Övervaka säkerhets kopierings-och återställnings jobb

Om du vill övervaka slutförda eller pågående jobb (säkerhets kopiering eller återställning) använder du cmdleten [AZ backup Job List](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) . Med CLI kan du också [pausa ett pågående jobb](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) eller [vänta tills ett jobb har slutförts](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

Utdata kommer att se ut ungefär så här:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Ändra princip

Om du vill ändra principen underliggande SAP HANA säkerhets kopierings konfiguration använder du cmdleten [AZ backup princip set](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-set) . Namn parametern i den här cmdleten refererar till det säkerhets kopierings objekt vars princip vi vill ändra. I den här självstudien kommer vi att ersätta principen för vår SAP HANA Database *saphanadatabase; hxe; hxe* med en ny princip *newsaphanaPolicy*. Nya principer kan skapas med hjälp av [AZ säkerhets kopierings princip skapa](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet.

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

Utdata bör se ut så här:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Skydda nya databaser som läggs till i en SAP HANA-instans

[När en SAP HANA-instans registreras med ett Recovery Services-valv](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) upptäcks automatiskt alla databaser på den här instansen.

I de fall då nya databaser läggs till i SAP HANA-instansen senare, använder du cmdleten [AZ backup protectable-item Initialize](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-initialize) . Den här cmdleten identifierar de nya databaserna som lagts till.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Använd sedan cmdleten [AZ backup Protected-item List](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) för att visa en lista över alla databaser som har identifierats på din SAP HANA-instans. Den här listan utesluter dock de databaser där säkerhets kopiering redan har kon figurer ATS. När databasen som ska säkerhets kopie ras identifieras, se [Aktivera säkerhets kopiering på SAP HANA databas](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Den nya databasen som du vill säkerhetskopiera visas i den här listan, vilket kommer att se ut så här:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Stoppa skyddet för en SAP HANA databas

Du kan sluta skydda en SAP HANA-databas på ett par olika sätt:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter.
* Stoppa alla framtida säkerhets kopierings jobb och lämna återställnings punkterna intakta.

Om du väljer att lämna återställnings punkter bör du tänka på följande:

* Alla återställnings punkter förblir intakta för alltid och all rensning stoppas vid stopp av skyddet med data kvar.
* Du debiteras för den skyddade instansen och den förbrukade lagringen.
* Om du tar bort en data källa utan att stoppa säkerhets kopieringen kommer nya säkerhets kopieringar att Miss lyckas.

Nu ska vi titta på var och en av metoderna för att stoppa skyddet i mer detalj.

### <a name="stop-protection-with-retain-data"></a>Avbryt skyddet med kvarhållning av data

Om du vill stoppa skyddet med kvarhållna data använder du [AZ backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

Utdata bör se ut så här:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Om du vill kontrol lera status för den här åtgärden använder du [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retain-data"></a>Stoppa skydd utan att behålla data

Om du vill stoppa skyddet utan att behålla data använder du [AZ backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

Utdata bör se ut så här:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Om du vill kontrol lera status för den här åtgärden använder du [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="resume-protection"></a>Återuppta skydd

När du slutar skydda SAP HANA databasen med Behåll data kan du senare återuppta skyddet. Om du inte behåller säkerhetskopierade data kan du inte återuppta skyddet.

Om du vill återuppta skyddet använder du cmdleten [AZ backup Protection Resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) .

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

Utdata bör se ut så här:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Om du vill kontrol lera status för den här åtgärden använder du [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Nästa steg

* Information om hur du säkerhetskopierar en SAP HANA databas som körs på en virtuell Azure-dator med hjälp av Azure Portal finns i [säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

* Information om hur du hanterar en säkerhets kopie rad SAP HANA databas som körs på en virtuell Azure-dator med hjälp av Azure Portal finns i [Hantera Säkerhetskopierade SAP HANA databaser på den virtuella Azure-datorn](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
