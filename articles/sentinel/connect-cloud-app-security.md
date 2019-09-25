---
title: Anslut Cloud App Security data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Cloud App Security data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6e71028855344d083dedf3493682e1e27685de48
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240111"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Anslut data från Microsoft Cloud App Security 



Du kan strömma loggar från [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) till Azure Sentinel med ett enda klick. Med den här anslutningen kan du strömma aviseringarna från Cloud App Security till Azure Sentinel. 

## <a name="prerequisites"></a>Förutsättningar

- Användare med behörighet som global administratör eller säkerhets administratör

## <a name="connect-to-cloud-app-security"></a>Anslut till Cloud App Security

Om du redan har Cloud App Security kontrollerar du att den är [aktive rad i nätverket](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Om Cloud App Security distribueras och matas in med dina data kan aviserings informationen enkelt strömmas i Azure Sentinel.


1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Cloud App Security** .

1. Välj vilka loggar du vill strömma till i Azure Sentinel. du kan välja **aviseringar**. 

1. Du kan välja om du vill att aviseringarna från Microsoft Cloud App Security automatiskt ska generera incidenter i Azure Sentinel automatiskt. Under **skapa incidenter** väljer du **Aktivera** för att aktivera den standard analys regel som skapar incidenter automatiskt från aviseringar som genereras i den anslutna säkerhets tjänsten. Du kan sedan redigera den här regeln under **analys** och sedan **aktiva regler**.

1. Klicka på **Anslut**.

1. Om du vill använda det relevanta schemat i Log Analytics för Cloud App Security aviseringar söker du efter **SecurityAlert**.




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Microsoft Cloud App Security till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
