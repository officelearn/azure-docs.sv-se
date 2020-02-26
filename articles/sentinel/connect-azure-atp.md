---
title: Anslut Azure ATP-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Azure ATP-data till Azure Sentinel.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588594"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Anslut data från Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Azure Advanced Threat Protection data Connector i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma loggar från [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) till Azure Sentinel med ett enda klick.

## <a name="prerequisites"></a>Förutsättningar

- Användare med behörighet som global administratör eller säkerhets administratör
- Du måste vara en förhands gransknings kund för Azure ATP och aktivera integrering mellan Azure ATP och Microsoft Cloud App Security. Mer information finns i [Azure Advanced Protection integration](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-azure-atp"></a>Anslut till Azure ATP

Se till att Azure ATP Preview-versionen är [aktive rad i nätverket](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Om Azure ATP distribueras och data matas in kan de misstänkta aviseringarna enkelt strömmas i Azure Sentinel. Det kan ta upp till 24 timmar innan aviseringarna börjar strömma till Azure Sentinel.


1. För att ansluta Azure ATP till Azure Sentinel måste du först aktivera integrering mellan Azure ATP och Microsoft Cloud App Security. Information om hur du gör detta finns i [integrering med Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Azure Advanced Threat Protection (för hands version)** .

1. Du kan välja om du vill att aviseringarna från Azure ATP automatiskt ska generera incidenter i Azure Sentinel automatiskt. Under **skapa incidenter** väljer du **Aktivera** för att aktivera den standard analys regel som skapar incidenter automatiskt från aviseringar som genereras i den anslutna säkerhets tjänsten. Du kan sedan redigera den här regeln under **analys** och sedan **aktiva regler**.

1. Klicka på **Anslut**.

1. Om du vill använda det relevanta schemat i Log Analytics för Azure ATP-aviseringar söker du efter **SecurityAlert**.

> [!NOTE]
> Om aviseringarna är större än 30 KB slutar Azure Sentinel att visa fältet entiteter i aviseringarna.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure Advanced Threat Protection till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

