---
title: Anslut Azure Security Center data till Azure Sentinel
description: Lär dig hur du ansluter Azure Security Center data till Azure Sentinel.
author: rkarlin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 4c75d1870ca3b966acdd658c91c0af8fd7465bfb
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190884"
---
# <a name="connect-data-from-azure-security-center"></a>Anslut data från Azure Security Center





Med Azure Sentinel kan du ansluta aviseringar från [Azure Security Center](../security-center/security-center-intro.md) och strömma dem till Azure Sentinel. 

## <a name="prerequisites"></a>Förutsättningar

- Om du vill exportera aviseringar från Azure Security Center måste du ha rollen som säkerhets läsare i prenumerationen på de loggar du strömmar.

- Du måste ha [Azure Security Center standard nivå](../security-center/security-center-pricing.md) som körs på prenumerationen. Annars uppgraderar du [din prenumeration till standard](https://azure.microsoft.com/pricing/details/security-center/).



## <a name="connect-to-azure-security-center"></a>Anslut till Azure Security Center

1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Azure Security Center** .

1. Till höger klickar du på **Anslut** bredvid varje prenumeration vars aviseringar du vill strömma till Azure Sentinel. Se till att uppgradera varje prenumeration till Azure Security Center standard nivån för att strömma aviseringar till Azure Sentinel.

1. Du kan välja om du vill att aviseringarna från Azure Security Center automatiskt ska generera incidenter i Azure Sentinel automatiskt. Under **skapa incidenter** väljer du **Aktivera** för att aktivera den standard analys regel som skapar incidenter automatiskt från aviseringar som genereras i den anslutna säkerhets tjänsten. Du kan sedan redigera den här regeln under **analys** och sedan **aktiva regler**.

3. Klicka på **Anslut**.

4. Om du vill använda det relevanta schemat i Log Analytics för Azure Security Center aviseringar söker du efter **SecurityAlert**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure Security Center till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
