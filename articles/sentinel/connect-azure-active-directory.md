---
title: Ansluta Azure AD-data till Azure Sentinel-förhandsgranskning | Microsoft Docs
description: Lär dig hur du ansluter Azure Active Directory-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: f29ae9a8fcaecfc345efae02084f31d133e67b5d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494892"
---
# <a name="connect-data-from-azure-active-directory"></a>Anslut data från Azure Active Directory

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel kan du samla in data från [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) och strömma det till Azure Sentinel. Du kan välja att strömma [inloggning loggar](../active-directory/reports-monitoring/concept-sign-ins.md) och [granskningsloggar](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Nödvändiga komponenter

- Om du vill exportera inloggningsdata från Active Directory, måste du ha en Azure AD P1 eller P2-licens.

- Användare med globala administratören eller säkerhetsadministratören administratörsbehörighet på den klient som du vill strömma loggar från.


## <a name="connect-to-azure-ad"></a>Anslut till Azure AD

1. I Azure Sentinel väljer **datakopplingar** och klicka sedan på den **Azure Active Directory** panelen.

2. Bredvid loggarna som du vill spela in Azure Sentinel, klickar du på **Connect**.

6. Om du vill använda relevanta schemat i Log Analytics för Azure AD-aviseringar, Sök efter **SigninLogs** och **AuditLogs**.




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure AD till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).
