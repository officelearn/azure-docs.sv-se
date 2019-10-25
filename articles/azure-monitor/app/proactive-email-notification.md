---
title: Azure Application Smart identifiering av insikter – kommande ändring av standard mottagarna av meddelanden | Microsoft Docs
description: Övervaka program spår med Azure Application insikter om ovanliga mönster i trace telemetri.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: fa1f98b5a9ee592a4c702e87e365eff7941194d4
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820621"
---
# <a name="smart-detection-e-mail-notification-change"></a>Ändra e-postavisering om Smart identifiering

Baserat på kundfeedback, den 1 april 2019, ändrar vi standard rollerna som tar emot e-postaviseringar från Smart identifiering.

## <a name="what-is-changing"></a>Vad ändras?

För närvarande skickas e-postaviseringar för smart identifiering som standard till rollerna _prenumerations ägare_, _prenumerations deltagare_och _prenumerations läsare_ . De här rollerna omfattar dock ofta användare som inte är aktivt involverade i övervakningen och de har därför inget intresse av dessa meddelanden. För att förbättra den här upplevelsen gör vi en ändring så att e-postaviseringar endast går till rollerna [övervaknings läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) och [övervakning deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) som standard.

## <a name="scope-of-this-change"></a>Omfånget för den här ändringen

Detta påverkar alla regler för Smart identifiering utom följande:

* Regler för Smart identifiering som är under förhandsgranskning. Dessa regler för smart identifiering stöder inte e-postaviseringar idag.

* Regeln om felavvikelser. Den här regeln börjar riktas mot de nya standard rollerna när den har migrerats från en klassisk avisering till den enhetliga aviserings plattformen (mer information finns [här](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).)

## <a name="how-to-prepare-for-this-change"></a>Hur förbereder du för den här ändringen?

För att säkerställa att e-postmeddelanden från Smart identifiering skickas till relevanta användare måste användarna tilldelas [övervaknings läsaren](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) eller [övervaknings deltagar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) rollerna för prenumerationen.

Om du vill tilldela användare till övervaknings läsaren eller övervaknings deltagar roller via Azure Portal följer du stegen som beskrivs i artikeln [Lägg till en roll tilldelning](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) . Se till att välja _övervaknings läsare_ eller _övervaknings deltagare_ som den roll som användare tilldelas till.

> [!NOTE]
> Vissa mottagare av meddelanden om Smart identifiering, som kon figurer ATS med alternativet _ytterligare e-postmottagare_ i regel inställningarna, påverkas inte av den här ändringen. Dessa mottagare fortsätter att ta emot e-postaviseringar.

Om du har några frågor eller problem med den här ändringen kan du inte ovilliga [kontakta oss](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Nästa steg

Läs mer om Smart identifiering:

- [Felavvikelser](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Minnes läckor](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Prestandaavvikelser](../../azure-monitor/app/proactive-performance-diagnostics.md)