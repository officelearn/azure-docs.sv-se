---
title: Samla in Cloud App Security-data i Azure-Sentinel-förhandsversionen | Microsoft Docs
description: Lär dig mer om att samla in Cloud App Security-data i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b0033f5f8636053f88825541b8b2cfcbf2fc9f8b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245496"
---
# <a name="collect-data-from-microsoft-cloud-app-security"></a>Samla in data från Microsoft Cloud App Security 

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma loggar från [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) i Azure Sentinel-med ett enda klick. Den här anslutningen kan du strömma aviseringar från Cloud App Security till Sentinel-Azure. 

## <a name="prerequisites"></a>Förutsättningar

- Användare med global administratör eller administratörsbehörighet för säkerhet

## <a name="connect-to-cloud-app-security"></a>Ansluta till Cloud App Security

Om du redan har Cloud App Security kan du se till att det är [aktiverat i nätverket](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Om Cloud App Security distribueras och mata in dina data, aviseringsdata kan enkelt strömmas till Sentinel-Azure.


1. I Azure Sentinel väljer **datainsamling** och klicka sedan på den **Cloud App Security** panelen.

2. Klicka på **Anslut**.

3. Om du vill använda relevanta schemat i Log Analytics för Cloud App Security-aviseringar, Sök efter **SecurityAlert**.


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Microsoft Cloud App Security till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).
