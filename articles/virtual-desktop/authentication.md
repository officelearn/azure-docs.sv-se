---
title: Windows-autentisering för virtuella skriv bord – Azure
description: Autentiseringsmetoder för virtuella Windows-datorer.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5681228e5da2708912d69f16a4b09a4a93d8bb04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89500308"
---
# <a name="supported-authentication-methods"></a>Autentiseringsmetoder som stöds

I den här artikeln får du en kort översikt över vilka typer av autentisering som du kan använda i det virtuella Windows-skrivbordet.

## <a name="session-host-authentication"></a>Autentisering av sessionsbiljett

Windows Virtual Desktop stöder både NT LAN Manager (NTLM) och Kerberos för autentisering av session-värden. Om du däremot vill använda Kerberos måste klienten Hämta Kerberos-säkerhetsbiljetter från en Key Distribution Center-tjänst (KDC) som körs på en domänkontrollant. För att få biljetter måste klienten ha en direkt insikt för domänkontrollanten. Du kan få en direkt rad med detaljerad information genom att använda företags nätverket. Du kan också använda en VPN-anslutning till företagets nätverk.

Det här är de inloggnings metoder som stöds för närvarande:

- Klient för Windows-skrivbordet
    - Användarnamn och lösenord
    - Smartkortsinloggning
    - Windows Hello
- Windows Store-klient
    - Användarnamn och lösenord
- Webbklient
    - Användarnamn och lösenord
- Android
    - Användarnamn och lösenord
- iOS
    - Användarnamn och lösenord
- macOS
    - Användarnamn och lösenord

>[!NOTE]
>Smartkort och Windows Hello kan bara använda Kerberos för att logga in. Att logga in med Kerberos kräver att en detaljerad information anges för domänkontrollanten.

## <a name="hybrid-identity"></a>Hybrididentitet

Windows Virtual Desktop stöder [Hybrid identiteter](../active-directory/hybrid/whatis-hybrid-identity.md) via Azure Active Directory (AD), inklusive de federerade med Active Directory Federation Services (AD FS) (ADFS). Eftersom användarna måste kunna identifieras via Azure AD stöder inte Windows Virtual Desktop fristående Active Directory distributioner med ADFS.

## <a name="single-sign-on-sso"></a>Enkel inloggning (SSO)

Det virtuella Windows-skrivbordet stöder för närvarande inte Active Directory Federation Services (AD FS) (ADFS) för SSO.

Det enda sättet att undvika att uppmanas att ange dina autentiseringsuppgifter för sessionens värd är att spara dem i klienten. Vi rekommenderar att du bara gör detta med säkra enheter för att hindra andra användare från att komma åt dina resurser.

## <a name="next-steps"></a>Nästa steg

Är du nyfiken på andra sätt att skydda din distribution? Se [metod tips för säkerhet](security-guide.md).
