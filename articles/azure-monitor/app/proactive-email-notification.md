---
title: Azure Application Insights Smart identifiering – kommande ändringen meddelandemottagare standard | Microsoft Docs
description: Övervaka programspårningar med Azure Application Insights för onormala mönster i spårningstelemetri.
services: application-insights
author: harelbr
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/13/2019
ms.author: harelbr
ms.openlocfilehash: f984a34be1c5d5fdd18a00812107318df8f5d9bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61299009"
---
# <a name="smart-detection-e-mail-notification-change"></a>Ändra smart identifiering av e-postavisering

Baserat på feedback från kunder den 1 April 2019 ändrar vi standardroller som tar emot e-postmeddelanden från Smart identifiering.

## <a name="what-is-changing"></a>Vad ändras?

För närvarande Smart identifiering e-postmeddelanden skickas som standard till den _Prenumerationsägaren_, _Prenumerationsdeltagare_, och _läsare i prenumeration för_ roller. De här rollerna omfattar dock ofta användare som inte är aktivt involverade i övervakningen och de har därför inget intresse av dessa meddelanden. För att förbättra den här ska vi gör en ändring så att e-postaviseringar bara gå till den [övervakning läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) och [övervakning deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) roller som standard.

## <a name="scope-of-this-change"></a>Omfånget för den här ändringen

Detta påverkar alla regler för Smart identifiering utom följande:

* Regler för Smart identifiering som är under förhandsgranskning. Dessa regler för Smart identifiering stöder inte e-postmeddelanden i dag.

* Regeln om felavvikelser. Den här regeln startar riktar in sig på de nya standardrollerna när den har migrerats från en avisering i klassiska till den enhetliga varningsplattformen (Mer information finns [här](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).)

## <a name="how-to-prepare-for-this-change"></a>Så här förbereder du för den här ändringen?

För att säkerställa att e-postmeddelanden från Smart identifiering skickas till relevanta användare, dessa användare måste tilldelas den [övervakning läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) eller [övervakning deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) roller för prenumerationen.

Om du vill tilldela användare till databasrollerna övervakning läsaren eller deltagare för övervakning via Azure portal följer du stegen som beskrivs i den [Lägg till en rolltilldelning](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) artikeln. Se till att välja den _övervakning läsare_ eller _övervakning deltagare_ som rollen som användare har tilldelats.

> [!NOTE]
> Specifika mottagare för Smart identifiering-meddelanden som har konfigurerats med hjälp av den _ytterligare e-postmottagare_ i Regelinställningarna för, påverkas inte av den här ändringen. Dessa mottagare kommer att fortsätta e-postmeddelanden.

Om du har frågor om den här ändringen kan du gärna [Kontakta oss](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Nästa steg

Läs mer om Smart identifiering:

- [Felavvikelser](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Minnesläckor](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Prestandaavvikelser](../../azure-monitor/app/proactive-performance-diagnostics.md)