---
title: Åtgärda icke-kompatibla konfigurationsservrar för Azure Automation-tillstånd
description: Så här återanvänder du konfigurationer på begäran på servrar där konfigurationstillståndet har drivit
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: cc5884e1f70bdccee4e7a113e6e3ee2d6604b50a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406074"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Åtgärda icke-kompatibla DSC-servrar

När servrar registreras med Azure Automation State Configuration `ApplyOnly`är `ApplyandMonitor`konfigurationsläget inställt på , eller `ApplyAndAutoCorrect`. Om läget inte är `ApplyAndAutoCorrect`inställt på förblir servrar som av någon anledning driver från ett kompatibelt tillstånd inte kompatibla tills de korrigeras manuellt.

Azure compute erbjuder en funktion med namnet Run Command som gör att kunder kan köra skript inuti virtuella datorer.
Det här dokumentet innehåller exempelskript för den här funktionen när konfigurationsdrift ska korrigeras manuellt.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Korrekt drift av Windows virtuella datorer med PowerShell

Stegvisa instruktioner med funktionen Kör kommando på virtuella Windows-datorer finns [på dokumentationssidan Kör PowerShell-skript i windows VM med kommandot Kör](/azure/virtual-machines/windows/run-command).

Om du vill tvinga en Azure Automation State Configuration-nod att hämta den senaste konfigurationen och tillämpa den använder du cmdlet för [uppdatering DscConfiguration.](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Korrekt drift av Linux virtuella maskiner

Liknande funktioner är för närvarande inte tillgängliga för Linux-servrar.
Det enda alternativet är att upprepa registreringsprocessen.

För Azure-noder kan du korrigera drift från Azure-portalen eller använda Az-modul cmdlets. Information om den här processen dokumenteras i [Onboarding-datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md#onboard-vms-by-using-the-azure-portal).
För hybridnoder kan du korrigera drift med de medföljande Python-skripten.
Se [PowerShell DSC för Linux-repo](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Nästa steg

- En PowerShell-cmdlet-referens finns i [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Information om hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Kontinuerlig distribution med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md).