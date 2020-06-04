---
title: Hanterings lösning i Azure Best Practices
description: I den här artikeln lär du dig hur du skapar en hanterings lösnings fil. Se hur du arbetar med data källor, Runbooks, vyer och aviseringar.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 7cb300297336edcce4294b800520ad570b12bcde
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84320936"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Metod tips för att skapa hanterings lösningar i Azure (för hands version)
> [!NOTE]
> Det här är en preliminär dokumentation för att skapa hanterings lösningar i Azure som för närvarande är en för hands version. Alla scheman som beskrivs nedan kan komma att ändras.  

Den här artikeln innehåller metod tips för att [skapa en hanterings lösnings fil](solutions-solution-file.md) i Azure.  Den här informationen kommer att uppdateras eftersom ytterligare metod tips identifieras.

## <a name="data-sources"></a>Datakällor
- Data källor kan [konfigureras med en Resource Manager-mall](../../azure-monitor/platform/template-workspace-configuration.md), men de bör inte tas med i en lösnings fil.  Orsaken är att konfiguration av data källor för närvarande inte idempotenta innebär att din lösning kan skriva över den befintliga konfigurationen i användarens arbets yta.<br><br>Din lösning kan till exempel kräva varnings-och fel händelser från program händelse loggen.  Om du anger det här som en data källa i din lösning riskerar du att ta bort informations händelser om användaren hade konfigurerat den i arbets ytan.  Om du inkluderar alla händelser kan du samla in alltför stora informations händelser på användarens arbets yta.

- Om din lösning kräver data från en av standard data källorna bör du definiera den som en förutsättning.  Tillstånd i dokumentation som kunden måste konfigurera data källan på egen hand.  
- Lägg till ett [data flödes verifierings](../../azure-monitor/platform/view-designer-tiles.md) meddelande i alla vyer i din lösning för att instruera användaren om data källor som måste konfigureras för att nödvändiga data ska samlas in.  Det här meddelandet visas på panelen i vyn när det inte går att hitta nödvändiga data.


## <a name="runbooks"></a>Runbooks
- Lägg till ett [Automation-schema](../../automation/automation-schedules.md) för varje Runbook i din lösning som måste köras enligt ett schema.
- Inkludera [modulen IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) i din lösning som ska användas av Runbooks som skriver data till Log Analytics-lagringsplatsen.  Konfigurera lösningen så att den [refererar](solutions-solution-file.md#solution-resource) till den här resursen så att den förblir om lösningen tas bort.  På så sätt kan flera lösningar dela modulen.
- Använd [Automation-variabler](../../automation/automation-schedules.md) för att ange värden för den lösning som användarna kan vilja ändra senare.  Även om lösningen har kon figurer ATS för att innehålla variabeln, kan dess värde fortfarande ändras.

## <a name="views"></a>Vyer
- Alla lösningar bör innehålla en enda vy som visas i användarens Portal.  Vyn kan innehålla flera [visualiserings delar](../../azure-monitor/platform/view-designer-parts.md) för att illustrera olika uppsättningar med data.
- Lägg till ett [data flödes verifierings](../../azure-monitor/platform/view-designer-tiles.md) meddelande i alla vyer i din lösning för att instruera användaren om data källor som måste konfigureras för att nödvändiga data ska samlas in.
- Konfigurera lösningen så att den [innehåller](solutions-solution-file.md#solution-resource) vyn så att den tas bort om lösningen tas bort.

## <a name="alerts"></a>Aviseringar
- Definiera listan mottagare som en parameter i lösnings filen så att användaren kan definiera dem när de installerar lösningen.
- Konfigurera lösningen så att den [refererar](solutions-solution-file.md#solution-resource) till varnings regler så att användarens konfiguration kan ändras.  De kan vilja göra ändringar som att ändra mottagar listan, ändra tröskelvärdet för aviseringen eller inaktivera varnings regeln. 


## <a name="next-steps"></a>Nästa steg
* Gå igenom den grundläggande processen för att [utforma och skapa en hanterings lösning](solutions-creating.md).
* Lär dig hur du [skapar en lösnings fil](solutions-solution-file.md).
* [Lägg till sparade sökningar och aviseringar](solutions-resources-searches-alerts.md) i hanterings lösningen.
* [Lägg till vyer](solutions-resources-views.md) i hanterings lösningen.
* [Lägg till Automation-runbooks och andra resurser](solutions-resources-automation.md) i hanterings lösningen.

