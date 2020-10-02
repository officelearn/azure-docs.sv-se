---
title: Fråga efter krav
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: 518865f78c170f1fbe4e65b96dc149c1b449a88b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631397"
---
I frågan använder du `@StartTime` parametern för att hämta mått data för en enskild tidsstämpel. Metrics Advisor ersätter parametern med en `yyyy-MM-ddTHH:mm:ss` format sträng när frågan körs.

> [!IMPORTANT]
> Frågan ska returnera högst en post för varje dimensions kombination vid varje tidsstämpel. Och alla poster som returneras av frågan måste ha samma tidsstämplar. Metrics Advisor kör den här frågan för varje tidsstämpel för att mata in dina data. Mer information och exempel finns i [avsnittet Vanliga frågor och svar om frågor](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) . 