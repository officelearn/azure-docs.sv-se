---
title: Anslut Azure ATP-data till Azure Sentinel-Preview | Microsoft Docs
description: Lär dig hur du ansluter Azure ATP-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: f0d86a62c59df5bebd34137d0903fcaa7014573d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204264"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Anslut data från Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Du kan strömma loggar från [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) i Azure Sentinel-med ett enda klick.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Användare med global administratör eller administratörsbehörighet för säkerhet
- Du måste vara en privat förhandsversionskund av Azure ATP

## <a name="connect-to-azure-atp"></a>Ansluta till Azure ATP

Kontrollera att den privata förhandsversionen Azure ATP är [aktiverat i nätverket](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Om Azure ATP distribueras och mata in dina data, misstänkt aviseringarna kan enkelt strömmas till Sentinel-Azure. Det kan ta upp till 24 timmar för vilka aviseringar som ska starta direktuppspelning i Azure Sentinel.



1. I Azure Sentinel väljer **datakopplingar** och klicka sedan på den **Azure ATP** panelen.

2. Klicka på **Anslut**.

6. Om du vill använda relevanta schemat i Log Analytics för Azure ATP-aviseringar, Sök efter **SecurityAlert**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet lärde du dig att ansluta Azure Advanced Threat Protection till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

