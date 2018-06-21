---
title: 'Azure AD Connect: Synkronisera tjänstinstanser | Microsoft Docs'
description: Den här sidan dokument att tänka på för Azure AD-instanser.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 351f3e34f5050d922655a633ec89f51d191b946e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295619"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Speciella överväganden vid instanser
Azure AD Connect används oftast med globalt instans av Azure AD och Office 365. Men det finns också andra instanser och de har olika krav för URL: er och andra saker.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
Den [Microsoft Cloud Tyskland](http://www.microsoft.de/cloud-deutschland) är ett suveräna moln som drivs av en förvaltare tyska data.

| URL: er för att öppna i proxyserver |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + Listor över återkallade certifikat |

När du loggar in på Azure AD-klient måste du använda ett konto i domänen onmicrosoft.de.

Funktioner som för närvarande inte finns i Microsoft Cloud-Tyskland:

* **Tillbakaskrivning av lösenord** är tillgängliga för förhandsgranskning med Azure AD Connect-version 1.1.570.0 och efter.
* Andra Azure AD Premium-tjänster är inte tillgängliga.

## <a name="microsoft-azure-government-cloud"></a>Microsoft Azure Government-moln
Den [Microsoft Azure Government molnet](https://azure.microsoft.com/features/gov/) är en molntjänst för USA: S regering.

Detta moln har stöd av tidigare versioner av DirSync. Nästa generation av molnet stöds från build 1.1.180 av Azure AD Connect. Den här generationen använder endast USA-baserade slutpunkter och har en annan lista över URL: er för att öppna i din proxyserver.

| URL: er för att öppna i proxyserver |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (krävs för automatisk identifiering av Azure AD government klient) |
| \*.gov.us.microsoftonline.com |
| + Listor över återkallade certifikat |

> [!NOTE]
> Från och med AAD Connect version 1.1.647.0, ange AzureInstance värde i registret inte längre behövs, under förutsättning att *. windows.net är öppen på proxyservrar.

Funktioner som för närvarande inte finns i Microsoft Azure Government-molnet:

* **Tillbakaskrivning av lösenord** är tillgängliga för förhandsgranskning med Azure AD Connect-version 1.1.570.0 och efter.
* Andra Azure AD Premium-tjänster är inte tillgängliga.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
