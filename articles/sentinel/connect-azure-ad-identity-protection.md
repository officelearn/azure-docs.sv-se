---
title: Ansluta Azure AD Identity Protection data till Azure Sentinel Preview | Microsoft Docs
description: Lär dig hur du ansluter Azure AD Identity Protection data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 7478e5a5ec2260760bb6ddb1a90a66e3acdf2201
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129262"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Anslut data från Azure AD Identity Protection

> [!IMPORTANT]
> Azure Sentinel är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma loggar från [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) till Azure Sentinel för att strömma aviseringar till Azure Sentinel för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Azure Active Directory Identity Protection ger en samlad vy över risk användare, risk identifiering och sårbarheter, med möjlighet att åtgärda risker direkt och ange principer för att automatiskt reparera framtida händelser. Tjänsten bygger på Microsofts erfarenhet av att skydda konsument identiteter och får en fantastisk precision från signalen från över 13 000 000 000 inloggningar per dag. 


## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en [Azure Active Directory Premium P1-eller P2-licens](https://azure.microsoft.com/pricing/details/active-directory/)
- Användare med behörighet som global administratör eller säkerhets administratör


## <a name="connect-to-azure-ad-identity-protection"></a>Anslut till Azure AD Identity Protection

Om du redan har Azure AD Identity Protection kontrollerar du att den är [aktive rad i nätverket](../active-directory/identity-protection/enable.md).
Om Azure AD Identity Protection distribueras och hämtar data kan aviserings data enkelt strömmas till Azure Sentinel.


1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Azure AD Identity Protection** .

2. Klicka på **Anslut** för att börja strömma Azure AD Identity Protection händelser till Azure Sentinel.


6. Om du vill använda det relevanta schemat i Log Analytics för Azure AD Identity Protection aviseringar söker du efter **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure AD Identity Protection till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
