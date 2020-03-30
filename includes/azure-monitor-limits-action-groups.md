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
ms.openlocfilehash: 2922a1fb92f3df07429d3463b8bf639f336776af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67305337"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Push för Azure-appar | 10 Azure-appåtgärder per åtgärdsgrupp. | Ring support. |
| E-post | 1 000 e-poståtgärder i en åtgärdsgrupp.<br>Inte mer än 100 e-postmeddelanden på en timme.<br>Se även [den hastighetsbegränsande informationen](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Ring support. |
| ITSM | 10 ITSM-åtgärder i en aktionsgrupp. | Ring support. | 
| Logikapp | 10 logikappåtgärder i en åtgärdsgrupp. | Ring support. |
| Runbook | 10 runbook-åtgärder i en åtgärdsgrupp. | Ring support. |
| SMS | 10 SMS-åtgärder i en aktionsgrupp.<br>Högst 1 SMS-meddelande var 5:e minut.<br>Se även [den hastighetsbegränsande informationen](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Ring support. |
| Röst | 10 röståtgärder i en aktionsgrupp.<br>Högst 1 röstsamtal var femte minut.<br>Se även [den hastighetsbegränsande informationen](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Ring support. |
| Webhook | 10 webhook åtgärder i en åtgärdsgrupp.  Maximalt antal webhook-samtal är 1500 per minut och prenumeration. Andra gränser finns tillgängliga på [åtgärdsspecifik information](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Ring support. |
