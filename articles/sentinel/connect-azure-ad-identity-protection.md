---
title: Ansluta Azure AD Identity Protection-data till Azure Sentinel
description: Lär dig hur du ansluter Azure AD Identity Protection-data till Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: 7d42ff28ddd2d883feb25139096d781efe64d50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588577"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Ansluta data från Azure AD Identity Protection



Du kan strömma loggar från [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) till Azure Sentinel för att strömma aviseringar till Azure Sentinel för att visa instrumentpaneler, skapa anpassade aviseringar och förbättra undersökningen. Azure Active Directory Identity Protection ger en konsoliderad vy med riskanvändare, riskidentifieringar och säkerhetsproblem, med möjlighet att åtgärda risker omedelbart och ange principer för att automatiskt åtgärda framtida händelser. Tjänsten bygger på Microsofts erfarenhet av att skydda konsumentidentiteter och får enorm noggrannhet från signalen från över 13 miljarder inloggningar per dag. 


## <a name="prerequisites"></a>Krav

- Du måste ha en [Azure Active Directory Premium P1- eller P2-licens](https://azure.microsoft.com/pricing/details/active-directory/)
- Användare med global administratörs- eller säkerhetsadministratörsbehörighet


## <a name="connect-to-azure-ad-identity-protection"></a>Ansluta till Azure AD-identitetsskydd

Om du redan har Azure AD Identity Protection kontrollerar du att det är [aktiverat i nätverket](../active-directory/identity-protection/overview-identity-protection.md).
Om Azure AD Identity Protection distribueras och hämtar data kan varningsdata enkelt strömmas till Azure Sentinel.


1. I Azure Sentinel väljer du **Data-kopplingar** och klickar sedan på panelen **Azure AD Identity Protection.**

2. Klicka på **Anslut** om du vill börja strömma Azure AD Identity Protection-händelser till Azure Sentinel.


6. Om du vill använda det relevanta schemat i Logganalys för Azure AD Identity Protection-aviseringar söker du efter **SecurityAlert**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure AD Identity Protection till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
