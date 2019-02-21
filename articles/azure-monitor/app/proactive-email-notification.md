---
title: Azure Application Insights Smart identifiering – kommande ändringen meddelandemottagare standard | Microsoft Docs
description: Övervaka programspårningar med Azure Application Insights för onormala mönster i spårningstelemetri.
services: application-insights
author: harelbr
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 02/12/2019
ms.author: harelbr
ms.openlocfilehash: 4bcbed82a78caff62a9459ecb44c6513f367f6b7
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56457995"
---
# <a name="smart-detection-e-mail-notification-change"></a>Ändra smart identifiering av e-postavisering

Baserat på feedback från kunder den 1 April 2019 ändrar vi standardroller som tar emot e-postmeddelanden från Smart identifiering.

## <a name="what-is-changing"></a>Vad ändras?

För närvarande Smart identifiering e-postmeddelanden skickas som standard till den _Prenumerationsägaren_, _Prenumerationsdeltagare_, och _läsare i prenumeration för_ roller. Dessa roller innehåller ofta användare som inte är aktivt inblandad i övervakning, vilket gör att många av dessa användare att ta emot meddelanden onödan. För att förbättra den här ska vi gör en ändring så att e-postaviseringar bara gå till den [övervakning läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) och [övervakning deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) roller som standard.

## <a name="scope-of-this-change"></a>Omfånget för den här ändringen

Den här ändringen påverkar alla regler för Smart identifiering, förutom följande:

* Regler för smart identifiering som markerats som förhandsversion. Dessa regler för Smart identifiering stöder inte e-postmeddelanden i dag.

* Fel vid avvikelser regel. Den här regeln startar riktar in sig på de nya standardrollerna när den har migrerats från en avisering i klassiska till den enhetliga varningsplattformen (Mer information finns [här](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).)

## <a name="how-to-prepare-for-this-change"></a>Så här förbereder du för den här ändringen?

För att säkerställa att e-postmeddelanden från Smart identifiering skickas till de relevanta användarna, dessa användare måste tilldelas den [övervakning läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) och [övervakning deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) roller. Tilldelningen ska göras på antingen prenumerationsnivå (påverkar alla Application Insights-resurser i prenumerationen) eller på Application Insights-resurs (påverkar endast den specifika resursen).

Om du vill tilldela användare till databasrollerna övervakning läsaren eller deltagare för övervakning via Azure portal följer du stegen som beskrivs i den [Lägg till en rolltilldelning](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) artikeln. Se till att välja den _övervakning läsare_ eller _övervakning deltagare_ som rollen som användare har tilldelats.

> [!NOTE]
> Specifika mottagare för Smart identifiering-meddelanden som har konfigurerats med hjälp av den _ytterligare e-postmottagare_ i Regelinställningarna för, påverkas inte av den här ändringen. Dessa mottagare kommer att fortsätta e-postmeddelanden.

Om du har frågor om den här ändringen kan du gärna [Kontakta oss](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Nästa steg

Läs mer om Smart identifiering:

- [Felavvikelser](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Minnesläckor](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Prestandaavvikelser](../../azure-monitor/app/proactive-performance-diagnostics.md)