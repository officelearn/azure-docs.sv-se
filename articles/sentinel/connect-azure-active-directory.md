---
title: Anslut Azure AD-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Azure Active Directory data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: f08cd731e40b204d042e5df418b03626b9082c3b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894663"
---
# <a name="connect-data-from-azure-active-directory"></a>Anslut data från Azure Active Directory



Med Azure Sentinel kan du samla in data från [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) och strömma dem till Azure Sentinel. Du kan välja att strömma [inloggnings loggar](../active-directory/reports-monitoring/concept-sign-ins.md) och [gransknings loggar](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Krav

- Om du vill exportera inloggnings data från Active Directory måste du ha en Azure AD P1-eller P2-licens.

- Användare med behörighet som global administratör eller säkerhets administratör på den klient som du vill strömma loggarna från.

- Du måste ha behörighet att komma åt Azure AD-diagnostikloggar för att kunna se anslutnings status. 


## <a name="connect-to-azure-ad"></a>Anslut till Azure AD

1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Azure Active Directory** .

1. Klicka på **Anslut**bredvid de loggar som du vill strömma till Azure Sentinel.

1. Du kan välja om du vill att aviseringarna från Azure AD automatiskt ska generera incidenter i Azure Sentinel. Under **skapa incidenter** väljer du **Aktivera** för att aktivera den standard analys regel som skapar incidenter automatiskt från aviseringar som genereras i den anslutna säkerhets tjänsten. Du kan sedan redigera den här regeln under **analys** och sedan **aktiva regler**.

1. Om du vill använda det relevanta schemat i Log Analytics för Azure AD-aviseringar söker du efter **SigninLogs** och **AuditLogs**.




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure AD till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
