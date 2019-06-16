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
ms.openlocfilehash: 050d3314345e64e3d69a07367a0e9acc318fa106
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66271562"
---
| Resource | Standardgräns | Övre gräns |
| --- | --- | --- |
| Inställningarna för automatisk skalning |100 per region per prenumeration. | Samma som standard. |
| Aviseringar för mått (klassisk) |100 aktiva Varningsregler per prenumeration. | Kontakta supporten. |
| Måttaviseringar |1000 active Varningsregler per prenumeration (i offentliga moln) och 100 aktiva Varningsregler per prenumeration i Azure Kina och Azure Government. | Kontakta supporten. |
| Aktivitetsloggaviseringar | 100 aktiva Varningsregler per prenumeration. | Samma som standard. |
| Loggaviseringar | 512 | Kontakta supporten. |
| Åtgärdsgrupper |2 000 åtgärdsgrupper per prenumeration. | Kontakta supporten. |

**Åtgärden gruppspecifika gränser**

| Resource | Standardgräns | Övre gräns |
| --- | --- | --- |
| Azure-app-push | 10 azure åtgärder per åtgärdsgruppen. | Kontakta supporten. |
| E-post | 1 000 e poståtgärder i en åtgärdsgrupp. Se även de [begränsar information frekvensbegränsningen](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Kontakta supporten. |
| ITSM | 10 ITSM-åtgärder i en åtgärdsgrupp. | Kontakta supporten. | 
| Logikapp | 10 åtgärder för logikappar i en åtgärdsgrupp. | Kontakta supporten. |
| Runbook | 10 runbook-åtgärder i en åtgärdsgrupp. | Kontakta supporten. |
| SMS | 10 SMS-åtgärder i en åtgärdsgrupp. Se även de [begränsar information frekvensbegränsningen](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Kontakta supporten. |
| Röst | 10 voice åtgärder i en åtgärdsgrupp. Se även de [begränsar information frekvensbegränsningen](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Kontakta supporten. |
| Webhook | 10 webhook-åtgärder i en åtgärdsgrupp.  Maximala antalet webhook-anrop är 1500 per minut per prenumeration. Det finns andra begränsningar på [åtgärd-specifik information](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Kontakta supporten. |
