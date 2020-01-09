---
title: Anslut Azure AD Identity Protection data till Azure Sentinel
description: Lär dig hur du ansluter Azure AD Identity Protection data till Azure Sentinel.
author: rkarlin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: rkarlin
ms.openlocfilehash: 8d916606913b19a9eb15cb280defbeb0440565e4
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563725"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Anslut data från Azure AD Identity Protection



Du kan strömma loggar från [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) till Azure Sentinel för att strömma aviseringar till Azure Sentinel för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Azure Active Directory Identity Protection ger en samlad vy över risk användare, risk identifiering och sårbarheter, med möjlighet att åtgärda risker direkt och ange principer för att automatiskt reparera framtida händelser. Tjänsten bygger på Microsofts erfarenhet av att skydda konsument identiteter och får en fantastisk precision från signalen från över 13 000 000 000 inloggningar per dag. 


## <a name="prerequisites"></a>Krav

- Du måste ha en [Azure Active Directory Premium P1-eller P2-licens](https://azure.microsoft.com/pricing/details/active-directory/)
- Användare med behörighet som global administratör eller säkerhets administratör


## <a name="connect-to-azure-ad-identity-protection"></a>Anslut till Azure AD Identity Protection

Om du redan har Azure AD Identity Protection kontrollerar du att den är [aktive rad i nätverket](../active-directory/identity-protection/overview-identity-protection.md).
Om Azure AD Identity Protection distribueras och hämtar data kan aviserings data enkelt strömmas till Azure Sentinel.


1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Azure AD Identity Protection** .

2. Klicka på **Anslut** för att börja strömma Azure AD Identity Protection händelser till Azure Sentinel.


6. Om du vill använda det relevanta schemat i Log Analytics för Azure AD Identity Protection aviseringar söker du efter **SecurityAlert**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure AD Identity Protection till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
