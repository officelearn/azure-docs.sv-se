---
title: Hanteringslösning i Metodtips för Azure | Microsoft Docs
description: ''
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: d6d2414935bb5d1f095ad2b200acafa97b3b9b32
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53192710"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Metodtips för att skapa lösningar för hantering i Azure (förhandsversion)
> [!NOTE]
> Det här är preliminära dokumentationen för att skapa lösningar för hantering i Azure som för närvarande i förhandsversion. Ett schema som beskrivs nedan kan komma att ändras.  

Den här artikeln innehåller metodtips för [skapar en fil för lösningen](solutions-solution-file.md) i Azure.  Den här informationen kommer att uppdateras när ytterligare metoder identifieras.

## <a name="data-sources"></a>Datakällor
- Datakällor kan vara [konfigurerats med en Resource Manager-mall](../../azure-monitor/platform/template-workspace-configuration.md), men de bör inte ingå i en lösningsfil.  Orsaken är att konfigurera datakällor inte för närvarande är idempotenta, vilket innebär att din lösning kan du skriva över befintliga konfiguration i användarens arbetsyta.<br><br>Din lösning kan exempelvis kräva varnings- och händelser från programmets händelselogg.  Om du anger detta som en datakälla i din lösning, riskerar du tar bort händelser för Information om användaren hade detta konfigurerat på sin arbetsyta.  Om du ingår alla händelser, det kan du samla överdriven informationshändelser i användarens arbetsyta.

- Om din lösning kräver data från en av datakällorna som standard, bör du definiera detta som ett krav.  Tillstånd att kunden måste konfigurera datakällan på egen hand i dokumentationen.  
- Lägg till en [Flow dataverifieringen](../../azure-monitor/platform/view-designer-tiles.md) meddelandet till alla vyer i din lösning för att instruera användaren på datakällor som måste konfigureras för nödvändiga data samlas in.  Det här meddelandet visas på panelen i vyn när det går inte att hitta nödvändiga data.


## <a name="runbooks"></a>Runbooks
- Lägg till en [Automation schema](../../automation/automation-schedules.md) för varje runbook i din lösning som ska köras enligt ett schema.
- Inkludera den [IngestionAPI modulen](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) i din lösning som ska användas av runbooks som skriver data till Log Analytics-databasen.  Konfigurera lösningen till [referens](solutions-solution-file.md#solution-resource) den här resursen så att den finns kvar om lösningen har tagits bort.  På så sätt kan flera metoder för att modulen.
- Använd [automationsvariabler](../../automation/automation-schedules.md) ange värden i lösningen som användare kan ändra senare.  Även om lösningen har konfigurerats så att den innehåller variabeln, kan så är dess värde fortfarande ändras.

## <a name="views"></a>Vyer
- Alla lösningar bör innehålla en enda vy som visas i användarens-portalen.  Vyn kan innehålla flera [visualisering delar](../../azure-monitor/platform/view-designer-parts.md) att illustrera olika uppsättningar av data.
- Lägg till en [Flow dataverifieringen](../../azure-monitor/platform/view-designer-tiles.md) meddelandet till alla vyer i din lösning för att instruera användaren på datakällor som måste konfigureras för nödvändiga data samlas in.
- Konfigurera lösningen till [innehålla](solutions-solution-file.md#solution-resource) vyn så att den är tas bort om lösningen har tagits bort.

## <a name="alerts"></a>Aviseringar
- Definiera mottagarlistan som en parameter i lösningsfilen så att du kan definiera dem när de installerar lösningen.
- Konfigurera lösningen till [referens](solutions-solution-file.md#solution-resource) aviseringsregler så att användaren kan ändra sin konfiguration.  De kanske vill göra ändringar, till exempel ändra mottagarlistan, ändra tröskelvärdet för aviseringen eller inaktivera varningsregeln. 


## <a name="next-steps"></a>Nästa steg
* Gå igenom den grundläggande processen för [utforma och skapa en lösning för](solutions-creating.md).
* Lär dig hur du [skapa en lösningsfil](solutions-solution-file.md).
* [Lägg till sparade sökningar och aviseringar](solutions-resources-searches-alerts.md) till din lösning.
* [Lägga till vyer](solutions-resources-views.md) till din lösning.
* [Lägg till Automation-runbooks och andra resurser](solutions-resources-automation.md) till din lösning.

