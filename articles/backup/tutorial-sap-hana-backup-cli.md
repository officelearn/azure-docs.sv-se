---
title: Självstudiekurs - SAP HANA DB-säkerhetskopiering på Azure med CLI
description: I den här självstudien kan du läsa om hur du säkerhetskopierar SAP HANA-databaser som körs på en Azure VM till ett Azure Backup Recovery Services-valv med Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: cdc8a8fb09a086a2b9212c21d071f267991fa275
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78206630"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Självstudiekurs: Säkerhetskopiera SAP HANA-databaser i en Virtuell Azure-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller genom skript. Den här dokumentationen beskriver hur du säkerhetskopierar en SAP HANA-databas och utlöser säkerhetskopiering på begäran – allt med Azure CLI. Du kan också utföra dessa steg med [Azure-portalen](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

Det här dokumentet förutsätter att du redan har en SAP HANA-databas installerad på en Azure VM.This document assumes that you already have an SAP HANA database installed on an Azure VM. (Du kan också [skapa en virtuell dator med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)). I slutet av den här självstudien kan du:

> [!div class="checklist"]
>
> * Skapa ett Recovery Services-valv
> * Registrera SAP HANA-instans och identifiera databaser på den
> * Aktivera säkerhetskopiering i en SAP HANA-databas
> * Utlösa en säkerhetskopiering på begäran

Kolla in de [scenarier som vi för närvarande stöder](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support) för SAP HANA.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill installera och använda CLI lokalt måste du köra Azure CLI version xx.xxx.x eller senare. Kör `az --version` för att hitta CLI-versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Ett Recovery Services-valv är en logisk behållare som lagrar säkerhetskopieringsdata för varje skyddad resurs, till exempel virtuella Azure-datorer eller arbetsbelastningar som körs på virtuella Azure-datorer – till exempel SQL- eller HANA-databaser. När säkerhetskopieringsjobbet för en skyddad resurs körs, skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

Skapa ett Recovery Services-valv med [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create). Ange samma resursgrupp och plats som den virtuella datorn som du vill skydda. Lär dig hur du skapar en virtuell dator med Azure CLI med den här [snabbstarten för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

För den här guiden kommer vi att använda följande:

* en resursgrupp med namnet *saphanaResourceGroup*
* en virtuell dator med namnet *saphanaVM*
* resurser i *westus2-platsen.*

Vi kommer att skapa ett valv som heter *saphanaVault.*

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Recovery Services-valvet är som standard inställt på geo-redundant lagring. Geo-redundant lagring ger ett extra skydd genom att dina säkerhetskopierade data replikeras till en sekundär Azure-region som ligger hundratals kilometer från den primära regionen. Om lagringsredundansinställningen behöver ändras använder du uppsättningen uppsättning cmdlet [för säkerhetskopior för az-säkerhetskopieringsvalv.](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set)

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Om du vill se om valvet har skapats använder du cmdleten [för az backup vault-listan.](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-list) Följande svar visas:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Registrera och skydda SAP HANA-instansen

För att SAP HANA-instansen (den virtuella datorn med SAP HANA installerad på den) ska identifieras av Azure-tjänsterna måste ett [förregistreringsskript](https://aka.ms/scriptforpermsonhana) köras på SAP HANA-datorn. Kontrollera att alla [förutsättningar](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#prerequisites) uppfylls innan du kör skriptet. Mer information om vad skriptet gör finns i avsnittet [Vad förregistreringsskriptet gör.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

När skriptet körs kan SAP HANA-instansen registreras med återställningstjänsternas valv som vi skapade tidigare. Om du vill registrera instansen använder du cmdlet [för az-reservbehållaren.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-register) *VMResourceId* är resurs-ID för den virtuella datorn som du skapade för att installera SAP HANA.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Om den virtuella datorn inte finns i samma resursgrupp som valvet refererar *saphanaResourceGroup* till resursgruppen där valvet skapades.

Om du registrerar SAP HANA-instansen identifieras alla dess aktuella databaser automatiskt. Men för att upptäcka nya databaser som kan läggas till i framtiden hänvisa till [upptäcka nya databaser som lagts till i den registrerade SAP HANA-instansavsnittet.](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance)

Om du vill kontrollera om SAP HANA-instansen har registrerats med ditt valv använder du cmdlet [för azure backup-behållare.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) Följande svar visas:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> Kolumnen "namn" i ovanstående utdata refererar till behållarnamnet. Det här behållarnamnet används i nästa avsnitt för att aktivera säkerhetskopior och utlösa dem. Vilket i det här fallet är *VMAppContainer; Beräkna;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Aktivera säkerhetskopiering i SAP HANA-databasen

Cmdlet för [azure backup protectable-item](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) list list out all the databases discovered on the SAP HANA instance that you registered in the previous step.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Du bör hitta den databas som du vill säkerhetskopiera i den här listan, som kommer att se ut så här:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Som du kan se av ovanstående utgång är SID för SAP HANA-systemet HXE. I den här självstudien konfigurerar vi säkerhetskopiering för *saphanadatabase;hxe;hxe-databasen* som finns på *hxehost-servern.*

För att skydda och konfigurera säkerhetskopiering på en databas, en i taget, använder vi [az backup skydd enable-for-azurewl](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurewl) cmdlet. Ange namnet på den princip som du vill använda. Om du vill skapa en princip med CLI använder du [az backup-principen create](https://docs.microsoft.com//cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet. För den här självstudien kommer vi att använda *sapahanaPolicy-policyn.*

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Du kan kontrollera om ovanstående säkerhetskopieringskonfiguration är klar med hjälp av cmdlet [för az-jobblistan för säkerhetskopiering.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) Utdata visas på följande sätt:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

Cmdlet [för az-säkerhetskopieringsjobb](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) listar alla säkerhetskopieringsjobb (schemalagda eller på begäran) som har körts eller körs för närvarande på den skyddade databasen, förutom andra åtgärder som register, konfigurera säkerhetskopiering, ta bort säkerhetskopierade data etc.

## <a name="trigger-an-on-demand-backup"></a>Utlösa en säkerhetskopiering på begäran

I avsnittet ovan beskrivs hur du konfigurerar en schemalagd säkerhetskopiering, men i det här avsnittet beskrivs hur du utlöser en säkerhetskopiering på begäran. För att göra detta använder vi [az backup skydd backup-nu](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now) cmdlet.

>[!NOTE]
> Bevarandeprincipen för en säkerhetskopiering på begäran bestäms av den underliggande bevarandeprincipen för databasen.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

Utdata visas på följande sätt:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

Svaret ger dig jobbnamnet. Det här jobbnamnet kan användas för att spåra jobbstatus med hjälp av [az-säkerhetskopieringsjobbet](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

>[!NOTE]
>Förutom att schemalägga en fullständig eller differentiell säkerhetskopiering kan de också utlösas manuellt. Log-säkerhetskopior utlöses och hanteras automatiskt av SAP HANA internt.
>
> Inkrementella säkerhetskopior stöds för närvarande inte av Azure Backup.

## <a name="next-steps"></a>Nästa steg

* Om du vill veta hur du återställer en SAP HANA-databas i Azure VM med CLI fortsätter du till självstudien – [Återställ en SAP HANA-databas i Azure VM med CLI](tutorial-sap-hana-restore-cli.md)

* Mer information om hur du säkerhetskopierar en SAP HANA-databas som körs i Azure VM med Azure-portal finns i [Säkerhetskopiering av en SAP HANA-databaser på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
