---
title: 'Azure AD Connect: Synkronisera tjänstinstanser | Microsoft Docs'
description: Den här sidan dokument tänka på för Azure AD-instanser.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca481d50efb99d6e36c66388192e9f27cd66bf45
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217198"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Att tänka på för instanser
Azure AD Connect används oftast med världsomfattande instans av Azure AD och Office 365. Men det finns även andra instanser och de har olika krav för URL: er och andra saker.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
Den [Microsoft Cloud Tyskland](https://www.microsoft.de/cloud-deutschland) är ett nationellt moln som drivs av en tysk dataförvaltning.

| URL: er för att öppna i proxyservern |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + Listor över återkallade certifikat |

När du loggar in på Azure AD-klienten måste du använda ett konto i domänen onmicrosoft.de.

Funktioner som finns för närvarande inte i Microsoft Cloud Tyskland:

* **Tillbakaskrivning av lösenord** är tillgängligt som förhandsversion med Azure AD Connect version 1.1.570.0 och efter.
* Andra Azure AD Premium-tjänster är inte tillgängliga.

## <a name="microsoft-azure-government-cloud"></a>Microsoft Azure Government-molnet
Den [Microsoft Azure Government-molnet](https://azure.microsoft.com/features/gov/) är ett moln för amerikanska myndigheter.

Det här molnet har stöd av tidigare versioner av DirSync. Nästa generation av molnet stöds från build 1.1.180 av Azure AD Connect. Den här generation använder endast USA-baserade slutpunkter och har en annan lista över URL: er för att öppna i proxyservern.

| URL: er för att öppna i proxyservern |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (krävs för automatisk identifiering av Azure AD government klient) |
| \*.gov.us.microsoftonline.com |
| + Listor över återkallade certifikat |

> [!NOTE]
> Från och med AAD Connect version 1.1.647.0, inställningsvärde AzureInstance i registret behövs inte längre förutsatt att *. windows.net är öppen på proxyservrar.

Funktioner som finns för närvarande inte i Microsoft Azure Government-molnet:

* **Tillbakaskrivning av lösenord** är tillgängligt som förhandsversion med Azure AD Connect version 1.1.570.0 och efter.
* Andra Azure AD Premium-tjänster är inte tillgängliga.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
