---
title: 'Självstudiekurs: Hantera säkerhetskopierade SAP HANA DB med CLI'
description: I den här självstudien kan du läsa om hur du hanterar säkerhetskopierade SAP HANA-databaser som körs på en Azure VM med Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 2c088c27a678a4541cbba3c4c43c9cd830c60ff0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238743"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Självstudiekurs: Hantera SAP HANA-databaser i en Virtuell Azure-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller genom skript. Den här dokumentationen beskriver hur du hanterar en säkerhetskopierad SAP HANA-databas på Azure VM – allt med Azure CLI. Du kan också utföra dessa steg med [Azure-portalen](https://docs.microsoft.com/azure/backup/sap-hana-db-manage).

Använd [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) för att köra CLI-kommandon.

I slutet av den här självstudien kan du:

> [!div class="checklist"]
>
> * Övervaka säkerhetskopiering och återställning av jobb
> * Skydda nya databaser som lagts till i en SAP HANA-instans
> * Ändra policyn
> * Sluta skydda
> * Återuppta skydd

Om du har använt [Säkerhetskopiera en SAP HANA-databas i Azure med CLI](tutorial-sap-hana-backup-cli.md) för att säkerhetskopiera DIN SAP HANA-databas använder du följande resurser:

* en resursgrupp med namnet *saphanaResourceGroup*
* ett valv som heter *saphanaVault*
* skyddad behållare med namnet *VMAppContainer; Beräkna;saphanaResourceGroup;saphanaVM*
* säkerhetskopierade databas/objekt med namnet *saphanadatabase;hxe;hxe*
* resurser i *regionen Westus2*

Azure CLI gör det enkelt att hantera en SAP HANA-databas som körs på en Azure VM som säkerhetskopieras med Azure Backup. Den här självstudien beskriver var och en av hanteringsåtgärderna.

## <a name="monitor-backup-and-restore-jobs"></a>Övervaka säkerhetskopiering och återställning av jobb

Om du vill övervaka slutförda eller jobb som körs (säkerhetskopiering eller återställning) använder du cmdlet för [jobblistan för az-säkerhetskopiering.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) CLI kan du också [avbryta ett jobb som körs](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) eller vänta [tills ett jobb har slutförts.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

Utdata ser ut ungefär så här:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Ändra policy

Om du vill ändra principen bakom SAP HANA-säkerhetskopieringskonfigurationen använder du cmdlet för [az-säkerhetskopieringsprincipen.](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-set) Namnparametern i den här cmdleten refererar till det säkerhetskopieringsobjekt vars princip vi vill ändra. För den här guiden kommer vi att ersätta principen för vår SAP HANA databas *saphanadatabase;hxe;hxe* med en ny politik *newsaphanaPolicy*. Nya principer kan skapas med hjälp av [az backup-principen skapa](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet.

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

Utdata bör se ut ungefär så här:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Skydda nya databaser som lagts till i en SAP HANA-instans

[Om du registrerar en SAP HANA-instans med ett återställningstjänstvalv](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) identifieras automatiskt alla databaser i den här instansen.

Men i de fall då nya databaser läggs till i SAP HANA-instansen senare, använd cmdlet för [az backup protectable-item initialisera.](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-initialize) Den här cmdleten identifierar de nya databaserna som lagts till.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Använd sedan cmdleten [för azure backup protectable-item](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) för att lista alla databaser som har upptäckts på din SAP HANA-instans. Den här listan utesluter dock de databaser där säkerhetskopiering redan har konfigurerats. När databasen som ska säkerhetskopieras har identifierats läser du [Aktivera säkerhetskopiering i SAP HANA-databasen](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Den nya databasen som du vill säkerhetskopiera visas i den här listan, som ser ut så här:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Stoppa skydd för en SAP HANA-databas

Du kan sluta skydda en SAP HANA-databas på ett par olika sätt:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter.
* Stoppa alla framtida säkerhetskopieringsjobb och lämna återställningspunkterna intakta.

Om du väljer att lämna återställningspunkter bör du tänka på följande:

* Alla återställningspunkter förblir intakta för alltid, all beskärning ska stanna vid stoppskyddet med lagringsdata.
* Du debiteras för den skyddade instansen och förbrukad lagring.
* Om du tar bort en datakälla utan att stoppa säkerhetskopior misslyckas nya säkerhetskopior.

Låt oss titta på vart och ett av sätten att stoppa skyddet mer i detalj.

### <a name="stop-protection-with-retain-data"></a>Avbryt skyddet med kvarhållning av data

Om du vill stoppa skyddet med lagringsdata använder du [az backup-skyddet inaktivera](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

Utdata bör se ut ungefär så här:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Om du vill kontrollera status för den här åtgärden använder du [jobbet az backup visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retain-data"></a>Stoppa skyddet utan att behålla data

Om du vill stoppa skyddet utan att behålla data använder du [az backup-skyddet inaktivera](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

Utdata bör se ut ungefär så här:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Om du vill kontrollera status för den här åtgärden använder du [jobbet az backup visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="resume-protection"></a>Återuppta skydd

När du stoppar skyddet för SAP HANA-databasen med lagringsdata kan du senare återuppta skyddet. Om du inte behåller säkerhetskopierade data kan du inte återuppta skyddet.

Om du vill återuppta skyddet använder du [cmdlet för az-säkerhetskopieringsskydd.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume)

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

Utdata bör se ut ungefär så här:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Om du vill kontrollera status för den här åtgärden använder du [jobbet az backup visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du säkerhetskopierar en SAP HANA-databas som körs på Azure VM med Azure-portalen finns i [Backup SAP HANA-databaser på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

* Mer information om hur du hanterar en säkerhetskopierad SAP HANA-databas som körs på Azure VM med Azure-portalen finns i [Hantera säkerhetskopierade SAP HANA-databaser på Azure VM](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
