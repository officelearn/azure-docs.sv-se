---
title: Åtgärda konfigurations servrar som inte är kompatibla Azure Automation tillstånd
description: Så här tillämpar du konfigurationer på begäran på servrar där konfigurations statusen har använts
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68614503"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Åtgärda icke-kompatibla DSC-servrar

När servrar registreras med Azure Automation tillstånds konfiguration anges konfigurations läget till ApplyOnly, ApplyandMonitor eller ApplyAndAutoCorrect.
Om läget inte är inställt på Autokorrigering, förblir servrar som reser från ett kompatibelt tillstånd av någon anledning inte kompatibla förrän de har korrigerats manuellt.

Azure Compute erbjuder en funktion med namnet kör kommando som gör att kunder kan köra skript på virtuella datorer.
Det här dokumentet innehåller exempel skript för den här funktionen när du korrigerar konfigurations avvikelser manuellt.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Korrigera virtuella Windows-datorer med PowerShell

Stegvisa instruktioner med hjälp av kommando funktionen kör på virtuella Windows-datorer finns i dokumentations sidan [kör PowerShell-skript i din virtuella Windows-dator med kommandot kör](/azure/virtual-machines/windows/run-command).

Använd `Update-DscConfiguration` cmdleten om du vill tvinga fram en nod för Azure Automation tillstånds konfiguration för att ladda ned den senaste konfigurationen och tillämpa den.
Mer information finns i cmdlet-dokumentationen [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Korrekt drift av virtuella Linux-datorer

Liknande funktioner är för närvarande inte tillgängliga för Linux-servrar.
Det enda alternativet är att upprepa registrerings processen.
För Azure-noder kan du göra avvikelser från portalen eller använda AZ Automation-cmdletar.
Information om den här processen finns dokumenterad på sidan [onboarding Machines for Management genom att Azure Automation tillstånds konfiguration](/azure/automation/automation-dsc-onboarding#azure-portal).
För Hybrid-noder kan du göra avvikelser genom att använda de Python-skript som ingår.
Se dokumentationen i [POWERSHELL DSC för Linux lagrings platsen](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Nästa steg

- Referens för PowerShell-cmdlet finns i [Azure Automation cmdlets för tillstånds konfiguration](/powershell/module/azurerm.automation/#automation)
- Om du vill se ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med Azure Automation tillstånds konfiguration och choklad](automation-dsc-cd-chocolatey.md)
