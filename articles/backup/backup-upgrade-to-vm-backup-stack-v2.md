---
title: Uppgradera till den Azure VM-Säkerhetskopieringsstack V2
description: Uppgradera processen och vanliga frågor och svar för säkerhetskopieringsstack för virtuell dator, Resource Manager-distributionsmodellen
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 10/3/2018
ms.author: trinadhk
ms.openlocfilehash: 8882970471b554f6d05a9cf3028e7be572292ef6
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582708"
---
# <a name="upgrade-to-azure-vm-backup-stack-v2"></a>Uppgradera till säkerhetskopiering för Azure stack V2

Resource Manager-distributionsmodellen för uppgradering till säkerhetskopieringsstack för virtuell dator (VM) innehåller följande funktionsförbättringar:

* Kan visa ögonblicksbilder som tas som en del av ett säkerhetskopieringsjobb som är tillgängliga för återställning utan att behöva vänta dataöverföring ska slutföras. Det minskar väntetiden för ögonblicksbilder att kopiera till valvet innan återställningen. Denna funktion eliminerar också den ytterligare lagringsutrymme som krävs för säkerhetskopiering av virtuella datorer i premium, förutom den första säkerhetskopieringen.  

* Minskar tider för säkerhetskopiering och återställning genom att behålla ögonblicksbilder lokalt, i sju dagar.

* Stöd för storlekar upp till 4 TB.

* Möjlighet att använda en ohanterad virtuell dator ursprungliga lagringskonton, när du återställer. Denna möjlighet finns även när den virtuella datorn har diskar som är fördelade på storage-konton. Det går snabbare återställningsåtgärder för en mängd olika VM-konfigurationer.
    > [!NOTE]
    > Den här möjligheten är inte samma som ersätter den virtuella datorn diskar med data från återställningspunkten.


## <a name="whats-changing-in-the-new-stack"></a>Vad ändras om den nya stacken?
För närvarande består säkerhetskopieringsjobbet av två faser:
1.  Ta en ögonblicksbild för virtuell dator.
2.  Överföra en VM-ögonblicksbild till Azure Backup-valvet.

En återställningspunkt anses skapas endast när faser 1 och 2 är klar. Som en del av den nya stacken skapas en återställningspunkt när ögonblicksbilden är klar. Du kan även återställa från denna återställningspunkt med hjälp av samma flöde för återställning. Du kan identifiera den här återställningspunkten i Azure portal genom att använda ”ögonblicksbild” som typ av återställning. När ögonblicksbilden överförs till valvet, ändras typ av återställning till ”ögonblicksbild och valv”.

![Säkerhetskopieringsjobbet på VM säkerhetskopieringsstack distributionsmodellen Resource Manager – lagring och valv](./media/backup-azure-vms/instant-rp-flow.jpg)

Som standard hålls ögonblicksbilder i sju dagar. Den här funktionen möjliggör återställning Slutför snabbare från dessa ögonblicksbilder. Det minskar den tid som krävs för att kopiera data från valvet till kundens lagringskonto.

## <a name="considerations-before-upgrade"></a>Att tänka på innan uppgraderingen

* Uppgraderingen av säkerhetskopieringsstack för virtuell dator är en riktad, förlöper säkerhetskopieringarna som ingår i det här flödet. Eftersom ändringen inträffar på prenumerationsnivån går alla virtuella datorer i det här flödet. Alla nya funktionen tillägg är baserade på samma stack. För närvarande inte kan du styra stack på principnivån för.

* Ögonblicksbilder lagras lokalt att öka skapa en återställningspunkt och påskynda återställningsåtgärder. Därför visas lagringskostnader för ögonblicksbilder som tas under sju dagar.

* Inkrementella ögonblicksbilder lagras som sidblobar. Alla kunder som använder ohanterade diskar debiteras för de sju dagarna ögonblicksbilder lagras i kundens lokala storage-konto. Eftersom återställningspunkt samlingar som används av säkerhetskopieringar för hanterade virtuella datorn använder blob-ögonblicksbilder på underliggande lagringsnivå, hanterade diskar visas kostnaderna för [blob-ögonblicksbild priser](https://docs.microsoft.com/rest/api/storageservices/understanding-how-snapshots-accrue-charges) och de är inkrementell.

* Om du återställer en premium virtuell dator från en återställningspunkt för ögonblicksbild används en temporär lagringsplats medan den virtuella datorn skapas.

* Ögonblicksbilder som tas för omedelbar återställningspunkter count mot gränsen på 10 TB allokerat utrymme för premium storage-konton.

> [!NOTE]
> Uppgradera till säkerhetskopiering för Azure stack V2 kan få support för Azure Backup för den [Standard SSD Managed Disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

## <a name="upgrade"></a>Uppgradera
### <a name="the-azure-portal"></a>Azure Portal
Om du använder Azure-portalen kan se du ett meddelande på instrumentpanelen för valvet. Det här meddelandet relaterar till stöd för stora diskar och förbättringar för säkerhetskopiering och återställning hastighet. Du kan också gå till egenskapssidan för valvet för att hämta uppgraderingsalternativet.

![Säkerhetskopieringsjobbet på VM säkerhetskopieringsstack Resource Manager-modellen – stöd för meddelande](./media/backup-azure-vms/instant-rp-banner.png)

Välj banderollen för att öppna en skärm för att uppgradera till den nya stacken.

![Säkerhetskopieringsjobbet på säkerhetskopieringsstack för virtuell dator distributionsmodellen för Resource Manager – uppgradera](./media/backup-azure-vms/instant-rp.png)

### <a name="powershell"></a>PowerShell
Kör följande cmdlets från en upphöjd PowerShell-terminal:
1.  Logga in på ditt Azure-konto:

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Välj den prenumeration som du vill registrera:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Registrera den här prenumerationen:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```
### <a name="cli"></a>CLI
Kör följande kommandon från ett gränssnitt:
1.  Logga in på ditt Azure-konto:

    ```
    az login
    ```

2.  Välj den prenumeration som du vill registrera:

    ```
    az account set --subscription "Subscription Name"
    ```

3.  Registrera den här prenumerationen:

    ```
    az feature register --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>Verifiera att uppgraderingen är klar
### <a name="powershell"></a>PowerShell
Kör följande cmdlet från en upphöjd PowerShell-terminalen:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" -ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>CLI
Från ashell, kör du följande kommando:

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

Om det står ”Registered” har din prenumeration uppgraderats till säkerhetskopieringsstack V2.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Följande frågor och svar har samlats in från forum och kundfrågor.

### <a name="will-upgrading-to-v2-impact-current-backups"></a>Påverkar aktuella när du uppgraderar till V2?
Om du uppgraderar till V2, finns det ingen inverkan på dina aktuella säkerhetskopior och du behöver inte konfigurera om din miljö. Uppgradering och miljön backup fortsätter att fungera som den har.

### <a name="what-does-it-cost-to-upgrade-to-azure-vm-backup-stack-v2"></a>Vad kostar det om du vill uppgradera till säkerhetskopiering för Azure stack v2?
Det kostar ingenting att uppgradera stacken till v2. Ögonblicksbilder lagras lokalt för att påskynda skapa en återställningspunkt och återställning. Därför visas lagringskostnader för ögonblicksbilder som tas under sju dagar.

### <a name="does-upgrading-to-stack-v2-increase-the-premium-storage-account-snapshot-limit-by-10-tb"></a>Kan du öka premium storage-konto gränsen för ögonblicksbilder av 10 TB genom att uppgradera stack v2?
Nej, gränsen för totalt antal ögonblicksbilder per storage-konto det finns fortfarande på 10TB.

### <a name="in-premium-storage-accounts-do-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>I Premium Storage-konton ögonblicksbilder som tas för omedelbar återställningspunkt uppta 10 TB-gränsen för ögonblicksbilder?
Ja, för premium storage-konton, ögonblicksbilder som tas för omedelbar återställningspunkt tar upp de allokerade 10 TB utrymme.

### <a name="how-does-the-snapshot-work-during-the-seven-day-period"></a>Hur fungerar ögonblicksbilden under sju dagar?
Varje dag som en ny ögonblicksbild tas. Det finns sju enskilda ögonblicksbilder. Tjänsten **inte** ta en kopia på den första dagen och lägga till ändringarna under nästa sex dagar.

### <a name="is-a-v2-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Är en ögonblicksbild av v2 en inkrementell ögonblicksbild eller en fullständig ögonblicksbild?
Inkrementella ögonblicksbilder används för ohanterade diskar. För hanterade diskar återställningspunkt samling som skapats av Azure Backup använder blob-ögonblicksbilder och därför är inkrementell.

### <a name="how-to-get-standard-ssd-managed-disk-support-for-a-virtual-machine"></a>Så här hämtar du standard SSD hanterade diskar som stöd för en virtuell dator?
Uppgradera till säkerhetskopiering för Azure stack V2 kan få support för Azure Backup för den [Standard SSD Managed Disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).
