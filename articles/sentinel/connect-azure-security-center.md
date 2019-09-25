---
title: Ansluta Azure Security Center data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Azure Security Center data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: a9c210531f2c4cab1c3c023eab795023c3ad9f0c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240226"
---
# <a name="connect-data-from-azure-security-center"></a>Anslut data från Azure Security Center





Med Azure Sentinel kan du ansluta aviseringar från [Azure Security Center](../security-center/security-center-intro.md) och strömma dem till Azure Sentinel. 

## <a name="prerequisites"></a>Förutsättningar

- Om du vill exportera aviseringar från Azure Security Center måste du vara deltagare i prenumerationen vars loggar du strömmar.

- Du måste ha [Azure Security Center standard nivå](../security-center/security-center-pricing.md) som körs på prenumerationen. Annars uppgraderar du [din prenumeration till standard](https://azure.microsoft.com/pricing/details/security-center/).

- Du måste logga in med en användare som har behörighet som global administratör eller säkerhets administratör för varje prenumeration som du vill ansluta.


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
