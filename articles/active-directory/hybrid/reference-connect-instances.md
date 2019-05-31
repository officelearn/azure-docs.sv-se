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
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c342eac5460d8d52422b0497b1283f367660eb3c
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298822"
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

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
Den [Microsoft Azure Government-molnet](https://azure.microsoft.com/features/gov/) är ett moln för amerikanska myndigheter.

Det här molnet har stöd av tidigare versioner av DirSync. Nästa generation av molnet stöds från build 1.1.180 av Azure AD Connect. Den här generation använder endast USA-baserade slutpunkter och har en annan lista över URL: er för att öppna i proxyservern.

| URL: er för att öppna i proxyservern |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (krävs för automatisk identifiering av Azure Government-klient) |
| \*.gov.us.microsoftonline.com |
| + Listor över återkallade certifikat |

> [!NOTE]
> Från och med Azure AD Connect version 1.1.647.0, inställningsvärde AzureInstance i registret behövs inte längre förutsatt att *. windows.net är öppen på proxyservrar. För kunder som inte tillåter Internet-anslutning från sina Azure AD Connect-servrar, men användas följande manuell konfiguration.

### <a name="manual-configuration"></a>Manuell konfiguration

Följande manuella konfigurationssteg för att säkerställa använder Azure Government synkronisering slutpunkter för Azure AD Connect.

1. Starta Azure AD Connect-installationen.
2. När du ser den första sidan där du ska godkänna SLUTANVÄNDARAVTALET, Fortsätt inte men lämna installationsguiden körs.
3. Starta regedit och ändra registernyckeln `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` till värdet `4`.
4. Gå tillbaka till installationsguiden för Azure AD Connect acceptera LICENSAVTALET och fortsätta. Under installationen, se till att använda den **anpassad konfiguration** installation sökväg (och inte snabbinstallationen), sedan fortsätta med installationen som vanligt.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
