---
title: Uppgradera till Azure Resource Manager-distributionsmodellen för Virtuella Azure-säkerhetskopiering stack
description: Uppgradera processen och vanliga frågor och svar för VM säkerhetskopiering stacken, Resource Manager-distributionsmodellen
services: backup, virtual-machines
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: trinadhk
ms.openlocfilehash: e822e0c354fd671ee2802506e0e268d4078b395e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606910"
---
# <a name="upgrade-to-the-azure-resource-manager-deployment-model-for-azure-vm-backup-stack"></a>Uppgradera till Azure Resource Manager-distributionsmodellen för Virtuella Azure-säkerhetskopiering stack
Resource Manager distributionsmodell för uppgradering till virtuell dator (VM) säkerhetskopiering stack ger följande funktionsförbättringar:
* Möjlighet att se ögonblicksbilder som en del av ett säkerhetskopieringsjobb som är tillgängliga för återställning utan att vänta på dataöverföring ska slutföras. Det minskar väntetiden för ögonblicksbilder att kopiera till valvet innan återställning. Dessutom eliminerar denna funktion kravet ytterligare lagringsutrymme för att säkerhetskopiera virtuella datorer premium, utom den första säkerhetskopieringen.  

* En reducering i säkerhetskopiering och återställning gånger behålla ögonblicksbilder lokalt i sju dagar.

* Stöd för disk storlek upp till 4 TB.

* Möjlighet att använda en ohanterad VM ursprungliga storage-konton när du återställer. Denna möjlighet finns även när VM har diskar som är fördelade på storage-konton. Den gör snabbare återställningar för en mängd olika konfigurationer för virtuell dator.
    > [!NOTE] 
    > Den här möjligheten är inte samma som åsidosätter den ursprungliga virtuella datorn. 
    >

## <a name="whats-changing-in-the-new-stack"></a>Vad ändras i den nya stacken?
För närvarande består säkerhetskopieringsjobbet av två faser:
1.  Ta en ögonblicksbild av VM. 
2.  Överföra en VM-ögonblicksbild till Azure Backup-valvet. 

En återställningspunkt anses skapas bara när du är klar med steg 1 och 2. Som en del av den nya stacken skapa en återställningspunkt när ögonblicksbilden har slutförts. Du kan även återställa från denna återställningspunkt med hjälp av samma flöde för återställning. Du kan identifiera den här återställningspunkten i Azure portal genom att använda ”ögonblicksbild” som typ av återställning. När ögonblicksbilden har överförts till valvet, ändras typ av återställning till ”ögonblicksbild och valvet”. 

![Säkerhetskopieringsjobbet i VM säkerhetskopiering stack Resource Manager-distributionsmodellen--lagring och valvet](./media/backup-azure-vms/instant-rp-flow.jpg) 

Som standard behålls ögonblicksbilder i sju dagar. Den här funktionen gör återställningen till slut snabbare från dessa ögonblicksbilder. Det minskar den tid som krävs för att kopiera data från valvet tillbaka till kundens lagringskonto. 

## <a name="considerations-before-upgrade"></a>Överväganden inför uppgradering
* Uppgraderingen av säkerhetskopiering stacken VM är en riktad. Det innebär att alla säkerhetskopieringar försättas i detta flöde. Eftersom den är aktiverad på prenumerationsnivån går alla virtuella datorer i det här flödet. Alla nya funktionen tillägg är baserade på samma stacken. Möjlighet att kontrollera detta på principnivå kommer i framtida versioner.

* Ögonblicksbilder lagras lokalt att höja återställningspunkt och snabbare återställning. Därför kan se du lagringskostnader som motsvarar ögonblicksbilder under sju dagar.

* Inkrementell ögonblicksbilder lagras som sidblobar. Alla kunder som använder ohanterade diskar debiteras för de sju dagarna ögonblicksbilderna lagras i kundens lokala storage-konto. Enligt prismodellen kostar det inget för kunder på hanterade diskar.

* Om du gör en återställning från en ögonblicksbild återställningspunkt för en premium virtuell dator kan du se en temporär lagringsplats som används när den virtuella datorn skapas som en del av återställningen.

* Ögonblicksbilder som vidtas för omedelbar återställning upptar 10 TB allokerat utrymme för premium-lagringskonton.

## <a name="upgrade"></a>Uppgradera
### <a name="the-azure-portal"></a>Azure-portalen
Om du använder Azure-portalen kan se du ett meddelande på instrumentpanelen för valvet. Det här meddelandet relaterar till stöd för stora diskar och förbättringar för säkerhetskopiering och återställning hastighet.

![Säkerhetskopieringsjobbet i VM säkerhetskopiering stack Resource Manager-distributionsmodellen--stöd meddelande](./media/backup-azure-vms/instant-rp-banner.png) 

Välj popup-meddelandet om du vill öppna en skärm för att uppgradera till den nya stacken. 

![Uppgradera säkerhetskopieringsjobbet i VM säkerhetskopiering stack Resource Manager-distributionsmodellen--](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Kör följande cmdlets från en upphöjd PowerShell terminal:
1.  Logga in på ditt Azure-konto: 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Välj den prenumeration som du vill registrera dig för förhandsgranskning:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Registrera den här prenumerationen för privat förhandsversion:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>Kontrollera att uppgraderingen är klar
Kör följande cmdlet från en upphöjd PowerShell terminal:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

Om det står ”registrerad” har din prenumeration uppgraderats till VM säkerhetskopiering stack Resource Manager-modellen.
