---
title: Anslut Azure ATP-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Azure ATP-data till Azure Sentinel.
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
ms.openlocfilehash: ebb727055296ba7886a9307ada113ab5a6e0c9e0
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240179"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp---preview"></a>Ansluta data från Azure Advanced Threat Protection (ATP) – för hands version




Du kan strömma loggar från [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) till Azure Sentinel med ett enda klick.

## <a name="prerequisites"></a>Förutsättningar

- Användare med behörighet som global administratör eller säkerhets administratör
- Du måste vara en förhands gransknings kund för Azure ATP

## <a name="connect-to-azure-atp"></a>Anslut till Azure ATP

Se till att Azure ATP Preview-versionen är [aktive rad i nätverket](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Om Azure ATP distribueras och data matas in kan de misstänkta aviseringarna enkelt strömmas i Azure Sentinel. Det kan ta upp till 24 timmar innan aviseringarna börjar strömma till Azure Sentinel.


1. För att ansluta Azure ATP till Azure Sentinel måste du först aktivera integrering mellan Azure ATP och Microsoft Cloud App Security. Information om hur du gör detta finns i [integrering med Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Azure ATP** .

1. Du kan välja om du vill att aviseringarna från Azure ATP automatiskt ska generera incidenter i Azure Sentinel automatiskt. Under **skapa incidenter** väljer du **Aktivera** för att aktivera den standard analys regel som skapar incidenter automatiskt från aviseringar som genereras i den anslutna säkerhets tjänsten. Du kan sedan redigera den här regeln under **analys** och sedan **aktiva regler**.

1. Klicka på **Anslut**.

1. Om du vill använda det relevanta schemat i Log Analytics för Azure ATP-aviseringar söker du efter **SecurityAlert**.

> [!NOTE]
> Om aviseringarna är större än 30 KB slutar Azure Sentinel att visa fältet entiteter i aviseringarna.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure Advanced Threat Protection till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

