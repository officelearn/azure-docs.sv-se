---
title: ta med fil
description: ta med fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 29550581bbc395126d820d93b608e14ea318e268
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56653647"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Autoskalningsinställningar |100 per region per prenumeration | samma som standard |
| Aviseringar för mått (klassisk) |100 aktiva Varningsregler per prenumeration | Kontakta supporten |
| Måttaviseringar |100 aktiva Varningsregler per prenumeration | Kontakta supporten |
| Åtgärdsgrupper |2000 åtgärdsgrupper per prenumeration | Kontakta supporten |

**Åtgärdsgrupp specifika begränsningar**
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Azure-app-Push | 10 azure åtgärder per åtgärdsgrupp | Kontakta supporten |
| E-post | 1000 e-åtgärder i en åtgärdsgrupp. Se även de [begränsar information frekvensbegränsningen](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Kontakta supporten |
| ITSM | 10 ITSM-åtgärder i en åtgärdsgrupp | Kontakta supporten | 
| Logikapp | 10 logic App-åtgärder i en åtgärdsgrupp | Kontakta supporten |
| Runbook | 10 Runbook-åtgärder i en åtgärdsgrupp | Kontakta supporten |
| SMS | 10 SMS-åtgärder i en åtgärdsgrupp. Se även de [begränsar information frekvensbegränsningen](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Kontakta supporten |
| Röst | 10 voice-åtgärder i en åtgärdsgrupp. Se även de [begränsar information frekvensbegränsningen](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Kontakta supporten |
| Webhook | 10 Webhook-åtgärder i en åtgärdsgrupp. Andra begränsningar som är tillgängliga på [information om specifika](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Kontakta supporten |
