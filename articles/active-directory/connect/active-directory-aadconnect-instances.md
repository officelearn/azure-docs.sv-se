---
title: "Azure AD Connect: Synkronisera tjänstinstanser | Microsoft Docs"
description: "Den här sidan dokument att tänka på för Azure AD-instanser."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: e8321c3d16253226a5931cacbce6fa5d50b697bd
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Speciella överväganden vid instanser
Azure AD Connect används oftast med globalt instans av Azure AD och Office 365. Men det finns också andra instanser och de har olika krav för URL: er och andra saker.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
Den [Microsoft Cloud Tyskland](http://www.microsoft.de/cloud-deutschland) är ett suveräna moln som drivs av en förvaltare tyska data.

| URL: er för att öppna i proxyserver |
| --- |
| \*. microsoftonline.de |
| \*.windows.net |
| + Listor över återkallade certifikat |

När du loggar in på Azure AD-klient måste du använda ett konto i domänen onmicrosoft.de.

Funktioner som för närvarande inte finns i Microsoft Cloud-Tyskland:

* **Azure AD Connect Health** är inte tillgänglig.
* **Automatiska uppdateringar** är inte tillgänglig.
* **Tillbakaskrivning av lösenord** är tillgängliga för förhandsgranskning med Azure AD Connect-version 1.1.570.0 och efter.
* Andra Azure AD Premium-tjänster är inte tillgängliga.

## <a name="microsoft-azure-government-cloud"></a>Microsoft Azure Government-moln
Den [Microsoft Azure Government molnet](https://azure.microsoft.com/features/gov/) är en molntjänst för USA: S regering.

Detta moln har stöd av tidigare versioner av DirSync. Nästa generation av molnet stöds från build 1.1.180 av Azure AD Connect. Den här generationen använder endast USA-baserade slutpunkter och har en annan lista över URL: er för att öppna i din proxyserver.

| URL: er för att öppna i proxyserver |
| --- |
| \*.microsoftonline.com |
| \*. microsoftonline.us |
| \*. gov.us.microsoftonline.com |
| + Listor över återkallade certifikat |

Azure AD Connect kan inte identifieras automatiskt att Azure AD-klienten finns i det offentliga molnet. I stället måste du vidta följande åtgärder när du installerar Azure AD Connect.

1. Starta installationen av Azure AD Connect.
2. När du ser den första sidan där du ska godkänna LICENSAVTALET, behöver du inte fortsätta men lämna installationsguiden körs.
3. Starta regedit och ändra registernyckeln `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` till värdet `2`.
4. Gå tillbaka till installationsguiden för Azure AD Connect acceptera LICENSAVTALET och fortsätta. Under installationen, se till att använda den **anpassad konfiguration** installation sökväg (och inte snabbinstallationen). Fortsätt sedan installationen som vanligt.

Funktioner som för närvarande inte finns i Microsoft Azure Government-molnet:

* **Azure AD Connect Health** är inte tillgänglig.
* **Automatiska uppdateringar** är inte tillgänglig.
* **Tillbakaskrivning av lösenord** är tillgängliga för förhandsgranskning med Azure AD Connect-version 1.1.570.0 och efter.
* Andra Azure AD Premium-tjänster är inte tillgängliga.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
