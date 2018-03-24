---
title: Uppgradera till Virtuella Azure-säkerhetskopiering stacken V2 | Microsoft Docs
description: Uppgradera processen och vanliga frågor och svar för VM-säkerhetskopiering stack V2
services: backup, virtual-machines
documentationcenter: ''
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk, sogup
ms.openlocfilehash: 6d214072bccb8b2b42828ee003dcf349985b4f43
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="upgrade-to-vm-backup-stack-v2"></a>Uppgradera till VM-säkerhetskopieringsstack V2
Virtuell dator (VM) säkerhetskopiering stack V2 uppgraderingen ger följande funktionsförbättringar:
* Möjlighet att se ögonblicksbild vidtas som en del av jobbet för att vara tillgängliga för återställning utan att vänta på dataöverföring att slutföra.
Det minskar vänta på ögonblicksbild som ska kopieras för valvet innan återställning. Dessutom eliminerar detta kravet ytterligare lagringsutrymme för att säkerhetskopiera premium virtuella datorer utom första säkerhetskopieringen.  

* En reducering i säkerhetskopiering och återställning gånger behålla ögonblicksbilder lokalt för 7 dagar. 

* Stöd för disk storlek upp till 4 TB.  

* Möjligheten att använda ursprungliga storage-konton (även om VM har diskar som är fördelade på storage-konton) när du gör en återställning av en ohanterad dator. Detta gör snabbare återställningar för en mängd olika konfigurationer för virtuell dator. 
    > [!NOTE] 
    > Detta är inte samma som åsidosätter den ursprungliga virtuella datorn. 
    > 
    >

## <a name="what-is-changing-in-the-new-stack"></a>Vad ändras i den nya stacken?
Idag består säkerhetskopieringsjobbet av två faser:
1.  Ta ögonblicksbilder på VM. 
2.  Överföra VM-ögonblicksbild till Azure Backup-valvet. 

En återställningspunkt anses skapas bara när du är klar med steg 1 och 2. Som en del av den nya stacken skapa en återställningspunkt när ögonblicksbilden har slutförts. Du kan även återställa från denna återställningspunkt med hjälp av samma flöde för återställning. Du kan identifiera den här återställningspunkten i Azure-portalen med ”ögonblicksbild” som typ av återställning punkt. När ögonblicksbilden överförs för valvet ändras recovery punkttyp till ”ögonblicksbild och valvet”. 

![Säkerhetskopieringsjobbet i VM säkerhetskopiering stack V2](./media/backup-azure-vms/instant-rp-flow.jpg) 

Ögonblicksbilder kommer att sparas i sju dagar som standard. Detta gör att återställningen ska slutföras snabbare från dessa ögonblicksbilder genom att minska den tid som krävs för att kopiera data från valvet till kunden storage-konto. 

## <a name="considerations-before-upgrade"></a>Överväganden inför uppgradering
* Det här är en enkelriktad uppgradering av VM säkerhetskopiering stacken. Alla framtida säkerhetskopieringar försätts därför i detta flöde. Eftersom **aktiveras på en prenumerationsnivå, alla virtuella datorer kommer att gå till det här flödet**. Alla nya funktionen tillägg kommer att baseras på samma stacken. Möjlighet att kontrollera detta på principnivå kommer i framtida versioner. 
* Kontrollera att lagringsutrymmet motsvarar storleken på den virtuella datorn finns i lagringskontot tills första säkerhetskopieringen har slutförts för virtuella datorer med premiumdiskar under den första säkerhetskopieringen. 
* Eftersom ögonblicksbilder lagras lokalt att höja återställningspunkt och snabbare återställning visas lagringskostnader motsvarar ögonblicksbilder under sju dagar.
* Om du gör en återställning från ögonblicksbild återställningspunkt för en Premium virtuell visas en temporär lagringsplats som används när den virtuella datorn skapas som en del av återställningen. 

## <a name="how-to-upgrade"></a>Så här uppgraderar du?
### <a name="the-azure-portal"></a>Azure-portalen
Om du använder med Azure-portalen, ska du se ett meddelande på valvet instrumentpanelen relaterade till stöd för stora diskar och säkerhetskopierar och återställer går snabbare.

![Säkerhetskopieringsjobbet i VM säkerhetskopiering stack V2](./media/backup-azure-vms/instant-rp-banner.png) 

Välj popup-meddelandet om du vill öppna en skärm för att uppgradera till den nya stacken. 

![Säkerhetskopieringsjobbet i VM säkerhetskopiering stack V2](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Kör följande cmdlets från en upphöjd PowerShell terminal:
1.  Logga in på ditt Azure-konto. 

```
PS C:> Login-AzureRmAccount
```

2.  Välj den prenumeration som du vill registrera dig för förhandsgranskning:

```
PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
```

3.  Registrera den här prenumerationen för privat förhandsversion:

```
PS C:>  Register-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

## <a name="verify-whether-the-upgrade-is-complete"></a>Kontrollera om uppgraderingen är slutförd
Kör följande cmdlet från en upphöjd PowerShell terminal:

```
Get-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

Om det står registrerade har din prenumeration uppgraderats till VM säkerhetskopiering stack V2. 



