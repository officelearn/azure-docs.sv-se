---
title: Ansluta Azure AD Identity Protection-data till Azure Sentinel-Preview | Microsoft Docs
description: Lär dig hur du ansluter Azure AD Identity Protection data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d8306056655809c69af2ed39b5bbf8efebe05d3f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60445476"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Anslut data från Azure AD Identity Protection

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma loggar från [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) i Azure Sentinel-stream-aviseringar i Azure Sentinel att visa instrumentpaneler, skapa anpassade varningar och förbättra undersökningen. Azure Active Directory Identity Protection ger en samlad vy användare i farozonen, riskhändelser och sårbarheter, med möjlighet att åtgärda risker omedelbart och ange principer för att automatiskt åtgärda framtida händelser. Tjänsten bygger på Microsofts erfarenhet av att skydda konsumentidentiteter och får enorm Precision från signalen via 13 miljarder log-moduler per dag. 


## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha en [Azure Active Directory Premium P1 eller P2-licens](https://azure.microsoft.com/pricing/details/active-directory/)
- Användare med global administratör eller administratörsbehörighet för säkerhet


## <a name="connect-to-azure-ad-identity-protection"></a>Ansluta till Azure AD Identity Protection

Om du redan har Azure AD Identity Protection garanterar att de är [aktiverat i nätverket](../active-directory/identity-protection/enable.md).
Om Azure AD Identity Protection har distribuerats och hämtar data aviseringsdata kan enkelt strömmas till Sentinel-Azure.


1. I Azure Sentinel väljer **datakopplingar** och klicka sedan på den **Azure AD Identity Protection** panelen.

2. Klicka på **Connect** att starta direktuppspelning av Azure AD Identity Protection-händelser till Azure Sentinel.


6. Om du vill använda relevanta schemat i Log Analytics för Azure AD Identity Protection-aviseringar, Sök efter **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure AD Identity Protection till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).
