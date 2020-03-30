---
title: Ändring av meddelande om smart identifiering – Azure Application Insights
description: Ändra till standardmeddelandemottagarna från Smart Identifiering. Med Smart Identifiering kan du övervaka programspårningar med Azure Application Insights för ovanliga mönster i spårningstelemetri.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f73e5bbdd8585b3367e529a8fa00630042e56cac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671791"
---
# <a name="smart-detection-e-mail-notification-change"></a>Ändring av e-postmeddelande för smart identifiering

Baserat på feedback från kunder ändrar vi standardrollerna som får e-postmeddelanden från Smart Detection den 1 april 2019.

## <a name="what-is-changing"></a>Vad förändras?

För närvarande skickas e-postmeddelanden om smart identifiering som standard till rollerna _Prenumerationsägare,_ _Prenumerationsdeltagare_och _Prenumerationsläsare._ Dessa roller omfattar ofta användare som inte är aktivt involverade i övervakningen, vilket gör att många av dessa användare får meddelanden i onödan. För att förbättra den här upplevelsen gör vi en ändring så att e-postmeddelanden endast går till [rollerna Övervakningsläsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) och [Övervakningsbidragsgivare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) som standard.

## <a name="scope-of-this-change"></a>Omfattningen av denna ändring

Den här ändringen påverkar alla regler för smart identifiering, med undantag för följande:

* Smarta identifieringsregler markerade som förhandsgranskning. Dessa regler för smart identifiering stöder inte e-postmeddelanden idag.

* Regel för avvikelser vid fel. Den här regeln börjar rikta in sig på de nya standardrollerna när den har migrerats från en klassisk avisering till plattformen för enhetliga aviseringar (mer information finns [här](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).)

## <a name="how-to-prepare-for-this-change"></a>Hur förbereder man sig för denna förändring?

För att säkerställa att e-postmeddelanden från Smart Identifiering skickas till relevanta användare måste dessa användare tilldelas [rollerna Övervakningsläsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) eller [Övervakningsdeltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) för prenumerationen.

Om du vill tilldela användare till roller för övervakningsläsare eller övervakningsdeltagare via Azure-portalen följer du stegen som beskrivs i artikeln [Lägg till en rolltilldelning.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) Se till att välja _övervakningsläsaren_ eller _övervakningsdeltagaren_ som den roll som användarna tilldelas.

> [!NOTE]
> Specifika mottagare av meddelanden om smart identifiering, konfigurerade med alternativet _Ytterligare e-postmottagare_ i regelinställningarna, påverkas inte av den här ändringen. Dessa mottagare kommer att fortsätta att ta emot e-postmeddelanden.

Om du har några frågor eller funderingar om denna förändring, tveka inte att [kontakta oss.](mailto:smart-alert-feedback@microsoft.com)

## <a name="next-steps"></a>Nästa steg

Läs mer om Smart Identifiering:

- [Felavvikelser](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Minnesläckor](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Prestandaavvikelser](../../azure-monitor/app/proactive-performance-diagnostics.md)
