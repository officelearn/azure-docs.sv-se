---
title: Anslut säkerhetsdata för molnappar till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Cloud App Security-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588373"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Ansluta data från Microsoft Cloud App Security 



Du kan strömma loggar från [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) till Azure Sentinel med ett enda klick. Med den här anslutningen kan du strömma aviseringarna från Cloud App Security till Azure Sentinel. 

## <a name="prerequisites"></a>Krav

- Användare med global administratörs- eller säkerhetsadministratörsbehörighet
- Om du vill strömma Cloud Discovery-loggar till Azure Sentinel [aktiverar du Azure Sentinel som din SIEM i Microsoft Cloud App Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Inmatning av Cloud Discovery-loggar är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Ansluta till cloud app-säkerhet

Om du redan har Cloud App Security kontrollerar du att den är [aktiverad i nätverket](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Om Cloud App Security distribueras och intag av dina data kan varningsdata enkelt strömmas till Azure Sentinel.


1. Välj **Data-anslutningsappar**i Azure Sentinel, klicka på panelen **Cloud App Security** och välj Öppna **kopplingssida**.

1. Välj vilka loggar du vill strömma till Azure Sentinel, du kan välja **Aviseringar** och **Cloud Discovery loggar** (förhandsversion). 

1. Klicka på **Anslut**.

1. Om du vill använda det relevanta schemat i Log Analytics för Cloud App Security-aviseringar söker du efter **SecurityAlert**.




## <a name="next-steps"></a>Nästa steg
I det här dokumentet fick du lära dig hur du ansluter Microsoft Cloud App Security till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
