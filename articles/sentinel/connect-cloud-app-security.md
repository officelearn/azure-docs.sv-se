---
title: Anslut Cloud App Security data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Cloud App Security data till Azure Sentinel.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588373"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Anslut data från Microsoft Cloud App Security 



Du kan strömma loggar från [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) till Azure Sentinel med ett enda klick. Med den här anslutningen kan du strömma aviseringarna från Cloud App Security till Azure Sentinel. 

## <a name="prerequisites"></a>Förutsättningar

- Användare med behörighet som global administratör eller säkerhets administratör
- Om du vill strömma Cloud Discovery loggar till Azure Sentinel [aktiverar du Azure Sentinel som Siem i Microsoft Cloud App Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Inmatning av Cloud Discovery loggar är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Anslut till Cloud App Security

Om du redan har Cloud App Security kontrollerar du att den är [aktive rad i nätverket](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Om Cloud App Security distribueras och matas in med dina data kan aviserings informationen enkelt strömmas i Azure Sentinel.


1. I Azure Sentinel väljer du **data kopplingar**, klickar på panelen **Cloud App Security** och väljer **Öppna kopplings sida**.

1. Välj vilka loggar du vill strömma till i Azure Sentinel, du kan välja **aviseringar** och **Cloud Discovery loggar** (för hands version). 

1. Klicka på **Anslut**.

1. Om du vill använda det relevanta schemat i Log Analytics för Cloud App Security aviseringar söker du efter **SecurityAlert**.




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Microsoft Cloud App Security till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
