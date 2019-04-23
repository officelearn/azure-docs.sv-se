---
title: Ansluta Azure Security Center-data till Azure Sentinel-Preview | Microsoft Docs
description: Lär dig hur du ansluter Azure Security Center-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: bd6fe027da8c16157c7fce6c348e49c0a6b73ea3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791180"
---
# <a name="connect-data-from-azure-security-center"></a>Anslut data från Azure Security Center

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Azure Sentinel kan du ansluta aviseringar från [Azure Security Center](../security-center/security-center-intro.md) och strömma dem till Azure Sentinel. 

## <a name="prerequisites"></a>Nödvändiga komponenter

- Om du vill exportera aviseringar från Azure Security Center måste du vara deltagare för prenumerationen vars loggar strömmas.

- Du måste ha den [Azure Security Center Standard-nivån](../security-center/security-center-pricing.md) som körs på prenumerationen. Om inte, [uppgradera din prenumeration till standard](https://azure.microsoft.com/pricing/details/security-center/).

- Du måste logga in med en användare som har global administratör eller säkerhet administratörsbehörighet på varje prenumeration som du vill ansluta till.


## <a name="connect-to-azure-security-center"></a>Ansluta till Azure Security Center

1. I Azure Sentinel väljer **datakopplingar** och klicka sedan på den **Azure Security Center** panelen.
1. I högra hörnet, klickar du på **Connect** bredvid varje vars aviseringar du vill strömma till Sentinel-Azure-prenumeration. Se till att uppgradera varje prenumeration till Azure Security Center Standard-nivån till stream-aviseringar till Azure Sentinel.

3. Klicka på **Anslut**.

4. Om du vill använda relevanta schemat i Log Analytics för Azure Security Center-aviseringar, Sök efter **SecurityEvent**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure Security Center till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).
