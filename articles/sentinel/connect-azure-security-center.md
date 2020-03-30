---
title: Ansluta Azure Security Center-data till Azure Sentinel
description: Lär dig hur du ansluter Azure Security Center-data till Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588543"
---
# <a name="connect-data-from-azure-security-center"></a>Ansluta data från Azure Security Center





Med Azure Sentinel kan du ansluta aviseringar från [Azure Security Center](../security-center/security-center-intro.md) och strömma dem till Azure Sentinel. 

## <a name="prerequisites"></a>Krav

- Om du vill exportera aviseringar från Azure Security Center måste du ha rollen Säkerhetsläsare i prenumerationen på loggarna som du strömmar.

- Du måste ha [Azure Security Center Standard-nivån](../security-center/security-center-pricing.md) som körs på prenumerationen. Om inte, [uppgradera din prenumeration till standard](https://azure.microsoft.com/pricing/details/security-center/).



## <a name="connect-to-azure-security-center"></a>Ansluta till Azure Security Center

1. I Azure Sentinel väljer du **Data-kopplingar** och klickar sedan på panelen **Azure Security Center.**

1. Till höger klickar du på **Anslut** bredvid varje prenumeration vars aviseringar du vill strömma till Azure Sentinel. Se till att uppgradera varje prenumeration till Azure Security Center Standard-nivå för att strömma aviseringar till Azure Sentinel.

1. Du kan välja om du vill att aviseringarna från Azure Security Center automatiskt ska generera incidenter i Azure Sentinel automatiskt. Under **Skapa incidenter** väljer du **Aktivera** för att aktivera standardanalytiska regeln som skapar incidenter automatiskt från aviseringar som genereras i den anslutna säkerhetstjänsten. Du kan sedan redigera den här regeln under **Analytics** och sedan **aktiva regler**.

3. Klicka på **Anslut**.

4. Om du vill använda det relevanta schemat i Logganalys för Azure Security Center-aviseringar söker du efter **SecurityAlert**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure Security Center till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
