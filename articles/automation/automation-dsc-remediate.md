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
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68614503"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Åtgärda icke-kompatibla DSC-servrar

När servrar registreras med Azure Automation State Configuration är konfigurationsläget inställt på ApplyOnly, ApplyandMonitor eller ApplyAndAutoCorrect.
Om läget inte är inställt på Autokorrigering förblir servrar som av någon anledning driver från ett kompatibelt tillstånd inte tills de korrigeras manuellt.

Azure compute erbjuder en funktion med namnet Run Command som gör att kunder kan köra skript inuti virtuella datorer.
Det här dokumentet innehåller exempelskript för den här funktionen när konfigurationsdrift ska korrigeras manuellt.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Korrekt drift av Windows virtuella datorer med PowerShell

Stegvisa instruktioner med funktionen Kör kommando på virtuella Windows-datorer finns [på dokumentationssidan Kör PowerShell-skript i windows VM med kommandot Kör](/azure/virtual-machines/windows/run-command).

Om du vill tvinga en Azure Automation State Configuration-nod `Update-DscConfiguration` att hämta den senaste konfigurationen och tillämpa den använder du cmdleten.
Mer information finns i cmdlet-dokumentationen [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Korrekt drift av Linux virtuella maskiner

Liknande funktioner är för närvarande inte tillgängliga för Linux-servrar.
Det enda alternativet är att upprepa registreringsprocessen.
För Azure-noder kan driftkorrigering göras från portalen eller använda Az Automation-cmdlets.
Information om den här processen dokumenteras på sidan [Onboarding-datorer för hantering av Azure Automation State Configuration](/azure/automation/automation-dsc-onboarding#azure-portal).
För hybridnoder kan driftkorrigering göras med de medföljande Python-skripten.
Se dokumentationen i [PowerShell DSC för Linux repo](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Nästa steg

- Cmdlet-referens för PowerShell finns i [cmdlets för Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation)
- Information om hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Kontinuerlig distribution med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md)
