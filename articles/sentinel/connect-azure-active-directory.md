---
title: Samla in Azure AD-data i Azure-Sentinel-förhandsversionen | Microsoft Docs
description: Lär dig mer om att samla in Azure Active Directory-data i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2019
ms.author: rkarlin
ms.openlocfilehash: e145807b3170b7b822eabba09ce2e97da5467761
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993260"
---
# <a name="collect-data-from-azure-active-directory"></a>Samla in data från Azure Active Directory

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel kan du samla in data från [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) och strömma det till Azure Sentinel. Du kan välja att strömma [inloggning loggar](../active-directory/reports-monitoring/concept-sign-ins.md) och [granskningsloggar](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Förutsättningar

- Om du vill exportera inloggningsdata från Active Directory, måste du ha en Azure AD P1 eller P2-licens.

- Användare med globala administratören eller säkerhetsadministratören administratörsbehörighet på den klient som du vill strömma loggar från.


## <a name="connect-to-azure-ad"></a>Anslut till Azure AD

1. I Azure Sentinel väljer **datainsamling** och klicka sedan på den **Azure Active Directory** panelen.

2. Bredvid loggarna som du vill spela in Azure Sentinel, klickar du på **Connect**.






## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure AD till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).
