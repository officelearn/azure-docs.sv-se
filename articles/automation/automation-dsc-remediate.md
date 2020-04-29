---
title: Reparera inkompatibla Azure Automation tillstånds konfigurations servrar
description: Så här tillämpar du konfigurationer på begäran på servrar där konfigurations statusen har använts
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: dfe62c54bfb10d70f1dbf19daec90eec68e66431
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406074"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Reparera inkompatibla DSC-servrar

När servrar registreras med Azure Automation tillstånds konfiguration är konfigurations läget inställt `ApplyOnly`på `ApplyandMonitor`, eller `ApplyAndAutoCorrect`. Om läget inte är inställt på `ApplyAndAutoCorrect`är servrar som reser från ett kompatibelt tillstånd av någon anledning fortfarande inkompatibla tills de har korrigerats manuellt.

Azure Compute erbjuder en funktion med namnet kör kommando som gör att kunder kan köra skript på virtuella datorer.
Det här dokumentet innehåller exempel skript för den här funktionen när du korrigerar konfigurations avvikelser manuellt.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Korrigera virtuella Windows-datorer med PowerShell

Stegvisa instruktioner med hjälp av kommando funktionen kör på virtuella Windows-datorer finns i dokumentations sidan [kör PowerShell-skript i din virtuella Windows-dator med kommandot kör](/azure/virtual-machines/windows/run-command).

Använd cmdleten [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) om du vill tvinga fram en nod för konfiguration av Azure Automation tillstånd för att ladda ned den senaste konfigurationen och tillämpa den.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Korrekt drift av virtuella Linux-datorer

Liknande funktioner är för närvarande inte tillgängliga för Linux-servrar.
Det enda alternativet är att upprepa registrerings processen.
För Azure-noder kan du korrigera avvikelser från Azure Portal eller genom att använda cmdlets för AZ-moduler. Information om den här processen dokumenteras i [onboarding Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal).
För Hybrid-noder kan du korrigera driften med hjälp av de Python-skript som ingår.
Se [POWERSHELL DSC för Linux lagrings platsen](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Nästa steg

- En PowerShell-cmdlet-referens finns i [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Om du vill se ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med Azure Automation tillstånds konfiguration och choklad](automation-dsc-cd-chocolatey.md).