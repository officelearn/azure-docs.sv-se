---
title: Anslut Azure Active Directory data till Azure Sentinel | Microsoft Docs
description: Lär dig att samla in data från Azure Active Directory och strömma inloggnings loggar för Azure AD och gransknings loggar i Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.author: yelevin
ms.openlocfilehash: a19ced1cb9496bddd1f9aa7ea9e3eb58ba7eea3d
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185697"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Anslut data från Azure Active Directory (Azure AD)



Med Azure Sentinel kan du samla in data från [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) och strömma dem till Azure Sentinel. Du kan välja att strömma [inloggnings loggar](../active-directory/reports-monitoring/concept-sign-ins.md) och [gransknings loggar](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Krav

- Alla Azure AD-licenser (ledig/O365/P1/P2) kan integreras med Azure Monitor och mata in inloggnings loggar i Azure Sentinel. Ytterligare per GB-kostnader kommer att gälla för Azure Monitor eller Azure Sentinel.

- Användare med behörighet som global administratör eller säkerhets administratör på den klient som du vill strömma loggarna från.

- Du måste ha behörighet att komma åt Azure AD-diagnostikloggar för att kunna se anslutnings status. 


## <a name="connect-to-azure-active-directory"></a>Anslut till Azure Active Directory

1. I Azure Sentinel väljer du **data kopplingar** på navigerings menyn.

1. I data kopplings galleriet väljer du **Azure Active Directory** och klickar sedan på knappen **Öppna kopplings sida** .

1. Markera kryss rutorna bredvid de loggar som du vill strömma till Azure Sentinel och klicka på **Anslut**.

1. Du kan välja om du vill att aviseringarna från Azure AD automatiskt ska generera incidenter i Azure Sentinel. Under **skapa incidenter** väljer du **Aktivera** för att aktivera den standard analys regel som skapar incidenter automatiskt från aviseringar som genereras i den anslutna säkerhets tjänsten. Du kan sedan redigera den här regeln under **analys** och sedan **aktiva regler**.

1. Om du vill använda det relevanta schemat i Log Analytics för att skicka frågor till Azure AD-aviseringar skriver du `SigninLogs` eller `AuditLogs` i frågefönstret.




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure Active Directory till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
