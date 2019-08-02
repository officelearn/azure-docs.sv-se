---
title: Anslut Azure ATP-data till Azure Sentinel Preview | Microsoft Docs
description: Lär dig hur du ansluter Azure ATP-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6f41a5704c783ba8aeab7bc2e82ef731c6a257ac
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599150"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Anslut data från Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Azure Sentinel är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Du kan strömma loggar från [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) till Azure Sentinel med ett enda klick.

## <a name="prerequisites"></a>Förutsättningar

- Användare med behörighet som global administratör eller säkerhets administratör
- Du måste vara en förhands gransknings kund för Azure ATP

## <a name="connect-to-azure-atp"></a>Anslut till Azure ATP

Se till att Azure ATP Preview-versionen är [aktive rad i nätverket](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Om Azure ATP distribueras och data matas in kan de misstänkta aviseringarna enkelt strömmas i Azure Sentinel. Det kan ta upp till 24 timmar innan aviseringarna börjar strömma till Azure Sentinel.


1. För att ansluta Azure ATP till Azure Sentinel måste du först aktivera integrering mellan Azure ATP och Microsoft Cloud App Security. Information om hur du gör detta finns i [integrering med Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Azure ATP** .

2. Klicka på **Anslut**.

6. Om du vill använda det relevanta schemat i Log Analytics för Azure ATP-aviseringar söker du efter **SecurityAlert**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure Advanced Threat Protection till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).

