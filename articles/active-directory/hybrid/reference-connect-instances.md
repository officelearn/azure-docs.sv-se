---
title: 'Azure AD Connect: Synkroniseringstjänstinstanser | Microsoft-dokument'
description: Den här sidan dokumenterar särskilda överväganden för Azure AD-instanser.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66298822"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Särskilda överväganden för instanser
Azure AD Connect används oftast med den globala instansen av Azure AD och Office 365. Men det finns också andra instanser och dessa har olika krav på webbadresser och andra särskilda överväganden.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Tyskland
[Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) är ett suveränt moln som drivs av en tysk dataförvaltare.

| Url:er som ska öppnas i proxyservern |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +Listor över återkallade certifikat |

När du loggar in på din Azure AD-klient måste du använda ett konto i domänen onmicrosoft.de.

Funktioner som för närvarande inte finns i Microsoft Cloud Tyskland:

* **Återställning av lösenord** är tillgänglig för förhandsgranskning med Azure AD Connect version 1.1.570.0 och efter.
* Andra Azure AD Premium-tjänster är inte tillgängliga.

## <a name="microsoft-azure-government"></a>Microsoft Azure-regering
[Microsoft Azure Government-molnet](https://azure.microsoft.com/features/gov/) är ett moln för amerikanska myndigheter.

Det här molnet har stötts av tidigare versioner av DirSync. Från build 1.1.180 av Azure AD Connect stöds nästa generation av molnet. Den här generationen använder usa-baserade slutpunkter och har en annan lista med webbadresser att öppna i proxyservern.

| Url:er som ska öppnas i proxyservern |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net (Krävs för automatisk identifiering av Azure Government-klientorganisation) |
| \*.gov.us.microsoftonline.com |
| +Listor över återkallade certifikat |

> [!NOTE]
> Från och med Azure AD Connect version 1.1.647.0 krävs inte längre inställningen av AzureInstance-värdet i registret, förutsatt att *.windows.net är öppet på proxyservrarna. För kunder som inte tillåter Internet-anslutning från sina Azure AD Connect-servrar kan följande manuella konfiguration användas.

### <a name="manual-configuration"></a>Manuell konfiguration

Följande manuella konfigurationssteg används för att säkerställa att Azure AD Connect använder slutpunkter för Synkronisering av Azure Government.

1. Starta Azure AD Connect-installationen.
2. När du ser den första sidan där du ska acceptera licensavtalet ska du inte fortsätta utan låta installationsguiden vara igång.
3. Starta regedit och ändra `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` registernyckeln `4`till värdet .
4. Gå tillbaka till installationsguiden för Azure AD Connect, acceptera licensavtalet och fortsätt. Under installationen, se till att använda den **anpassade konfigurationsinstallationssökvägen** (och inte Express-installationen) och fortsätt sedan installationen som vanligt.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
