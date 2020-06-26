---
title: Anslut Azure Security Center data till Azure Sentinel
description: Lär dig hur du ansluter Azure Security Center data till Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 3d4b22196598d971c49f0364038b74ba81c73805
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374805"
---
# <a name="connect-data-from-azure-security-center-asc"></a>Anslut data från Azure Security Center (ASC)

Med Azure Sentinel kan du ansluta aviseringar från [Azure Security Center](../security-center/security-center-intro.md) och strömma dem till Azure Sentinel. 

## <a name="prerequisites"></a>Krav

- Om du vill exportera aviseringar från Azure Security Center måste du ha rollen som säkerhets läsare i prenumerationen på de loggar du strömmar.

- Du måste ha [Azure Security Center standard nivå](../security-center/security-center-pricing.md) som körs på prenumerationen. Annars uppgraderar du [din prenumeration till standard](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="connect-to-azure-security-center"></a>Anslut till Azure Security Center

1. I Azure Sentinel väljer du **data kopplingar** på navigerings menyn.

1. I data kopplings galleriet väljer du **Azure Security Center**och klickar på knappen **Öppna kopplings sida** .

1. Under **konfiguration**klickar du på **Anslut** bredvid varje prenumeration vars aviseringar du vill strömma till Azure Sentinel. Knappen Anslut är bara tillgänglig om du har de behörigheter som krävs och ASC-prenumerationen på standard nivå.

1. Du kan välja om du vill att aviseringarna från Azure Security Center automatiskt ska generera incidenter i Azure Sentinel. Under **skapa incidenter**väljer du **aktive rad** för att aktivera standard analys regeln som automatiskt skapar incidenter från aviseringar. Du kan sedan redigera regeln under **analys**på fliken **aktiva regler** .

1. Om du vill använda det relevanta schemat i Log Analytics för Azure Security Center aviseringar söker du efter **SecurityAlert**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure Security Center till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
