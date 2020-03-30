---
title: Ansluta Azure ATP-data till Azure Sentinel| Microsoft-dokument
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588594"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Ansluta data från Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Azure Advanced Threat Protection-dataanslutningen i Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma loggar från [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) till Azure Sentinel med ett enda klick.

## <a name="prerequisites"></a>Krav

- Användare med global administratörs- eller säkerhetsadministratörsbehörighet
- Du måste vara en förhandsversion av Azure ATP och aktivera integration mellan Azure ATP och Microsoft Cloud App Security. Mer information finns i [Azure Advanced Protection Integration](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-azure-atp"></a>Ansluta till Azure ATP

Kontrollera att förhandsversionen av Azure ATP är [aktiverad i nätverket](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Om Azure ATP distribueras och intag av dina data kan de misstänkta aviseringarna enkelt strömmas till Azure Sentinel. Det kan ta upp till 24 timmar för aviseringarna att börja strömma till Azure Sentinel.


1. Om du vill ansluta Azure ATP till Azure Sentinel måste du först aktivera integrering mellan Azure ATP och Microsoft Cloud App Security. Information om hur du gör detta finns i [Azure Advanced Threat Protection integration](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. I Azure Sentinel väljer du **Data-kopplingar** och klickar sedan på panelen **Azure Advanced Threat Protection (Preview).**

1. Du kan välja om du vill att aviseringarna från Azure ATP automatiskt ska generera incidenter i Azure Sentinel automatiskt. Under **Skapa incidenter** väljer du **Aktivera** för att aktivera standardanalytiska regeln som skapar incidenter automatiskt från aviseringar som genereras i den anslutna säkerhetstjänsten. Du kan sedan redigera den här regeln under **Analytics** och sedan **aktiva regler**.

1. Klicka på **Anslut**.

1. Om du vill använda det relevanta schemat i Log Analytics för Azure ATP-aviseringar söker du efter **SecurityAlert**.

> [!NOTE]
> Om aviseringarna är större än 30 kB slutar Azure Sentinel att visa fältet Entiteter i aviseringarna.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure Advanced Threat Protection till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

