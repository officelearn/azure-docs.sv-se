---
title: Hanteringslösning i Azures metodtips
description: I den här artikeln kan du läsa tips om hur du skapar en hanteringslösningsfil. Se hur du arbetar med datakällor, runbooks, vyer och aviseringar.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 7cb300297336edcce4294b800520ad570b12bcde
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548167"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Metodtips för att skapa hanteringslösningar i Azure (förhandsversion)
> [!NOTE]
> Detta är preliminär dokumentation för att skapa hanteringslösningar i Azure som för närvarande är i förhandsversion. Alla scheman som beskrivs nedan kan komma att ändras.  

Den här artikeln innehåller metodtips för [att skapa en hanteringslösningsfil](solutions-solution-file.md) i Azure.  Denna information kommer att uppdateras när ytterligare bästa praxis identifieras.

## <a name="data-sources"></a>Datakällor
- Datakällor kan [konfigureras med en Resource Manager-mall](../../azure-monitor/platform/template-workspace-configuration.md), men de bör inte inkluderas i en lösningsfil.  Anledningen är att konfigurera datakällor för närvarande inte är idempotent vilket innebär att din lösning kan skriva över befintlig konfiguration i användarens arbetsyta.<br><br>Lösningen kan till exempel kräva varnings- och felhändelser från programhändelseloggen.  Om du anger detta som en datakälla i din lösning riskerar du att ta bort informationshändelser om användaren har konfigurerat detta på sin arbetsyta.  Om du har inkluderat alla händelser kan du samla in överdrivna informationshändelser på användarens arbetsyta.

- Om din lösning kräver data från en av standarddatakällorna bör du definiera detta som en förutsättning.  Tillstånd i dokumentation som kunden måste konfigurera datakällan på egen hand.  
- Lägg till ett [dataflödesverifieringsmeddelande](../../azure-monitor/platform/view-designer-tiles.md) i alla vyer i din lösning för att instruera användaren om datakällor som måste konfigureras för att nödvändiga data ska samlas in.  Det här meddelandet visas på panelen i vyn när nödvändiga data inte hittas.


## <a name="runbooks"></a>Runbooks
- Lägg till ett [automatiseringsschema](../../automation/automation-schedules.md) för varje runbook i din lösning som måste köras enligt ett schema.
- Inkludera [IngestionAPI-modulen](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) i lösningen som ska användas av runbooks som skriver data till Log Analytics-databasen.  Konfigurera lösningen för att [referera](solutions-solution-file.md#solution-resource) till den här resursen så att den finns kvar om lösningen tas bort.  Detta gör det möjligt för flera lösningar att dela modulen.
- Använd [Automatiseringsvariabler](../../automation/automation-schedules.md) för att ange värden till den lösning som användarna kanske vill ändra senare.  Även om lösningen är konfigurerad för att innehålla variabeln kan dess värde fortfarande ändras.

## <a name="views"></a>Vyer
- Alla lösningar bör innehålla en enda vy som visas i användarens portal.  Vyn kan innehålla flera [visualiseringsdelar](../../azure-monitor/platform/view-designer-parts.md) för att illustrera olika datauppsättningar.
- Lägg till ett [dataflödesverifieringsmeddelande](../../azure-monitor/platform/view-designer-tiles.md) i alla vyer i din lösning för att instruera användaren om datakällor som måste konfigureras för att nödvändiga data ska samlas in.
- Konfigurera lösningen så att den [innehåller](solutions-solution-file.md#solution-resource) vyn så att den tas bort om lösningen tas bort.

## <a name="alerts"></a>Aviseringar
- Definiera mottagarlistan som en parameter i lösningsfilen så att användaren kan definiera dem när de installerar lösningen.
- Konfigurera lösningen för att [referera](solutions-solution-file.md#solution-resource) till varningsregler så att användarens kan ändra sin konfiguration.  De kanske vill göra ändringar som att ändra mottagarlistan, ändra tröskelvärdet för aviseringen eller inaktivera varningsregeln. 


## <a name="next-steps"></a>Nästa steg
* Gå igenom den grundläggande processen [att utforma och bygga en förvaltningslösning.](solutions-creating.md)
* Lär dig hur du [skapar en lösningsfil](solutions-solution-file.md).
* [Lägg till sparade sökningar och aviseringar](solutions-resources-searches-alerts.md) i hanteringslösningen.
* [Lägg till vyer](solutions-resources-views.md) i hanteringslösningen.
* [Lägg till Automation-runbooks och andra resurser](solutions-resources-automation.md) i hanteringslösningen.

