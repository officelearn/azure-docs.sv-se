---
title: "Metodtips för OMSManagement lösning | Microsoft Docs"
description: 
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: b3d07ad3164609a5628c0d9805de55a32870ab94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-creating-management-solutions-in-operations-management-suite-oms-preview"></a>Metodtips för att skapa lösningar för hantering i Operations Management Suite (OMS) (förhandsgranskning)
> [!NOTE]
> Den här är dokumentationen preliminär för att skapa lösningar för hantering i OMS som för närvarande finns i förhandsgranskningen. Ett schema som beskrivs nedan kan ändras.  

Den här artikeln innehåller metodtips för [att skapa en management lösningsfil](operations-management-suite-solutions-solution-file.md) i Operations Management Suite (OMS).  Den här informationen kommer att uppdateras när ytterligare metodtips identifieras.

## <a name="data-sources"></a>Datakällor
- Datakällor kan vara [konfigurerats med en Resource Manager-mall](../log-analytics/log-analytics-template-workspace-configuration.md), men de bör inte ingå i en lösningsfil.  Det beror på att konfigurera datakällor inte är för närvarande idempotent vilket innebär att din lösning kan du skriva över befintliga konfigurationen i användarens arbetsyta.<br><br>Lösningen kan exempelvis kräva varnings- och händelser från i programmets händelselogg.  Om du anger detta som en datakälla i din lösning, riskerar du att ta bort händelser med Information om användaren har konfigurerat i arbetsytan.  Om du ingår alla händelser, det kan du samla mycket informationshändelser i användarens arbetsyta.

- Om din lösning kräver att data från en av datakällorna som standard, bör du definiera detta som ett krav.  Tillstånd att kunden måste konfigurera datakällan på egen hand i dokumentationen.  
- Lägg till en [flöda dataverifieringen](../log-analytics/log-analytics-view-designer-tiles.md) meddelandet till alla vyer i din lösning för att instruera användaren på datakällor som måste konfigureras för nödvändiga data samlas in.  Det här meddelandet visas på ikonen i vyn när data som krävs saknas.


## <a name="runbooks"></a>Runbooks
- Lägg till en [Automation schema](../automation/automation-schedules.md) för varje runbook i din lösning som ska köras enligt ett schema.
- Inkludera den [IngestionAPI modulen](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) i din lösning som ska användas av runbooks data skrivs till Log Analytics-databasen.  Konfigurera lösningen till [referens](operations-management-suite-solutions-solution-file.md#solution-resource) resursen så att den förblir om lösningen har tagits bort.  Detta gör att flera metoder för att modulen.
- Använd [automationsvariabler](../automation/automation-schedules.md) att ange värden i lösningen som användare kan ändra senare.  Även om lösningen har konfigurerats så att den innehåller variabeln kan fortfarande så är dess värde ändras.

## <a name="views"></a>Vyer
- Alla lösningar ska innehålla en enda vy som visas i användarens portal.  Vyn kan innehålla flera [visualiseringen delar](../log-analytics/log-analytics-view-designer-parts.md) att illustrera olika datauppsättningar.
- Lägg till en [flöda dataverifieringen](../log-analytics/log-analytics-view-designer-tiles.md) meddelandet till alla vyer i din lösning för att instruera användaren på datakällor som måste konfigureras för nödvändiga data samlas in.
- Konfigurera lösningen till [innehåller](operations-management-suite-solutions-solution-file.md#solution-resource) vyn så att den har tagits bort om lösningen har tagits bort.

## <a name="alerts"></a>Aviseringar
- Definiera mottagarlistan som en parameter i lösningsfilen så att användaren kan ange dem när de installerar lösningen.
- Konfigurera lösningen till [referens](operations-management-suite-solutions-solution-file.md#solution-resource) avisering regler så att användaren kan ändra sin konfiguration.  De kanske vill göra ändringar, till exempel ändra listan över mottagare, ändra tröskelvärdet för aviseringen eller inaktivera regeln. 


## <a name="next-steps"></a>Nästa steg
* Gå igenom den grundläggande processen för [designa och skapa en lösning för](operations-management-suite-solutions-creating.md).
* Lär dig hur du [skapa en lösningsfil](operations-management-suite-solutions-solution-file.md).
* [Lägg till sparade sökningar och aviseringar](operations-management-suite-solutions-resources-searches-alerts.md) att din lösning för hantering.
* [Lägga till vyer](operations-management-suite-solutions-resources-views.md) att din lösning för hantering.
* [Lägg till Automation-runbooks och andra resurser](operations-management-suite-solutions-resources-automation.md) att din lösning för hantering.

