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
ms.openlocfilehash: c608389136bca2c6add7edfb1ae4eaca218f2b99
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553848"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Inställningarna för automatisk skalning |100 per region per prenumeration. | Samma som standard. |
| Aviseringar för mått (klassisk) |100 aktiva Varningsregler per prenumeration. | Kontakta supporten. |
| Måttaviseringar |100 aktiva Varningsregler per prenumeration. | Kontakta supporten. |
| Åtgärdsgrupper |2 000 åtgärdsgrupper per prenumeration. | Kontakta supporten. |

**Åtgärden gruppspecifika gränser**
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Push för Azure-app | 10 azure åtgärder per åtgärdsgruppen. | Kontakta supporten. |
| E-post | 1 000 e poståtgärder i en åtgärdsgrupp. Se även de [begränsar information frekvensbegränsningen](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Kontakta supporten. |
| ITSM | 10 ITSM-åtgärder i en åtgärdsgrupp. | Kontakta supporten. | 
| Logikapp | 10 åtgärder för logikappar i en åtgärdsgrupp. | Kontakta supporten. |
| Runbook | 10 runbook-åtgärder i en åtgärdsgrupp. | Kontakta supporten. |
| SMS | 10 SMS-åtgärder i en åtgärdsgrupp. Se även de [begränsar information frekvensbegränsningen](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Kontakta supporten. |
| Röst | 10 voice åtgärder i en åtgärdsgrupp. Se även de [begränsar information frekvensbegränsningen](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Kontakta supporten. |
| Webhook | 10 webhook-åtgärder i en åtgärdsgrupp. Det finns andra begränsningar på [åtgärd-specifik information](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Kontakta supporten. |
