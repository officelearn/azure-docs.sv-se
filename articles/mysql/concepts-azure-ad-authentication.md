---
title: Active Directory-autentisering – Azure Database för MySQL
description: Lär dig mer om begreppen Azure Active Directory för autentisering med Azure Database för MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 960536c3f80aa7870d6f2056d8e95cd1a4338dfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299030"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Använda Azure Active Directory för autentisering med MySQL

Microsoft Azure Active Directory (Azure AD) autentisering är en mekanism för att ansluta till Azure Database för MySQL med hjälp av identiteter som definierats i Azure AD.
Med Azure AD-autentisering kan du hantera databasanvändaridentiteter och andra Microsoft-tjänster på en central plats, vilket förenklar behörighetshanteringen.

> [!IMPORTANT]
> Azure AD-autentisering för Azure Database för MySQL är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Fördelarna med att använda Azure AD är:

- Autentisering av användare i Azure Services på ett enhetligt sätt
- Hantering av lösenordsprinciper och lösenordsrotation på en enda plats
- Flera former av autentisering som stöds av Azure Active Directory, vilket kan eliminera behovet av att lagra lösenord
- Kunder kan hantera databasbehörigheter med hjälp av externa (Azure AD) grupper.
- Azure AD-autentisering använder MySQL-databasanvändare för att autentisera identiteter på databasnivå
- Stöd för tokenbaserad autentisering för program som ansluter till Azure Database for MySQL

Om du vill konfigurera och använda Azure Active Directory-autentisering använder du följande process:

1. Skapa och fyll i Azure Active Directory med användaridentiteter efter behov.
2. Associera eller ändra Active Directory som för närvarande är associerat med din Azure-prenumeration.
3. Skapa en Azure AD-administratör för Azure-databasen för MySQL-servern.
4. Skapa databasanvändare i databasen mappade till Azure AD-identiteter.
5. Anslut till databasen genom att hämta en token för en Azure AD-identitet och logga in.

> [!NOTE]
> Mer information om hur du skapar och fyller i Azure AD och sedan konfigurerar Azure AD med Azure Database för MySQL finns [i Konfigurera och logga in med Azure AD för Azure Database för MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="architecture"></a>Arkitektur

Följande diagram på hög nivå sammanfattar hur autentisering fungerar med Azure AD-autentisering med Azure Database for MySQL. Pilarna visar kommunikationsvägar.

![autentiseringsflöde][1]

## <a name="administrator-structure"></a>Administratörsstruktur

När du använder Azure AD-autentisering finns det två administratörskonton för MySQL-servern. den ursprungliga MySQL-administratören och Azure AD-administratören. Endast administratören baserat på ett Azure AD-konto kan skapa den första Azure AD-innehållna databasanvändaren i en användardatabas. Azure AD-administratörsinloggningen kan vara en Azure AD-användare eller en Azure AD-grupp. När administratören är ett gruppkonto kan det användas av valfri gruppmedlem, vilket aktiverar flera Azure AD-administratörer för MySQL-servern. Om du använder ett gruppkonto som administratör kan du förbättra hanterbarheten genom att du kan lägga till och ta bort gruppmedlemmar centralt i Azure AD utan att ändra användare eller behörigheter på MySQL-servern. Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras när som helst.

![administratörsstruktur][2]

## <a name="permissions"></a>Behörigheter

Om du vill skapa nya användare som kan autentisera med Azure AD måste du vara den utformade Azure AD-administratören. Den här användaren tilldelas genom att konfigurera Azure AD-administratörskontot för en specifik Azure-databas för MySQL-server.

Om du vill skapa en ny Azure AD-databasanvändare måste du ansluta som Azure AD-administratör. Detta visas i [Konfigurera och logga in med Azure AD för Azure Database för MySQL](howto-configure-sign-in-azure-ad-authentication.md).

All Azure AD-autentisering är endast möjlig om Azure AD-administratören skapades för Azure Database for MySQL. Om Azure Active Directory-administratören har tagits bort från servern kan befintliga Azure Active Directory-användare som skapats tidigare inte längre ansluta till databasen med sina Azure Active Directory-autentiseringsuppgifter.

## <a name="connecting-using-azure-ad-identities"></a>Ansluta med Azure AD-identiteter

Azure Active Directory-autentisering stöder följande metoder för att ansluta till en databas med Hjälp av Azure AD-identiteter:

- Azure Active Directory-lösenord
- Integrerad i Azure Active Directory
- Azure Active Directory Universal med MFA
- Använda Active Directory-programcertifikat eller klienthemligheter

När du har autentiserats mot Active Directory hämtar du sedan en token. Den här token är ditt lösenord för inloggning.

> [!NOTE]
> Mer information om hur du ansluter till en Active Directory-token finns [i Konfigurera och logga in med Azure AD för Azure Database för MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="additional-considerations"></a>Annat som är bra att tänka på

- Endast en Azure AD-administratör kan konfigureras för en Azure-databas för MySQL-server när som helst.
- Endast en Azure AD-administratör för MySQL kan inledningsvis ansluta till Azure Database for MySQL med ett Azure Active Directory-konto. Active Directory-administratören kan konfigurera efterföljande Azure AD-databasanvändare.
- Om en användare tas bort från Azure AD kan användaren inte längre autentisera med Azure AD, och därför går det inte längre att hämta en åtkomsttoken för den användaren. I det här fallet, även om den matchande användaren fortfarande kommer att vara i databasen, kommer det inte att vara möjligt att ansluta till servern med den användaren.
> [!NOTE]
> Inloggning med den borttagna Azure AD-användaren kan fortfarande göras tills token upphör att gälla (upp till 60 minuter från tokenutgivning).  Om du också tar bort användaren från Azure Database för MySQL återkallas den här åtkomsten omedelbart.
- Om Azure AD-administratören tas bort från servern associeras inte längre servern med en Azure AD-klientorganisation och därför inaktiveras alla Azure AD-inloggningar för servern. Om du lägger till en ny Azure AD-administratör från samma klient återaktiverar Azure AD-inloggningar.
- Azure Database for MySQL matchar åtkomsttoken till Azure-databasen för MySQL-användare med användarens unika Azure AD-användar-ID, i motsats till att använda användarnamnet. Det innebär att om en Azure AD-användare tas bort i Azure AD och en ny användare som skapats med samma namn, anser Azure Database for MySQL att en annan användare. Om en användare tas bort från Azure AD och sedan en ny användare med samma namn läggs till, kommer den nya användaren inte att kunna ansluta till den befintliga användaren.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar och fyller i Azure AD och sedan konfigurerar Azure AD med Azure Database för MySQL finns [i Konfigurera och logga in med Azure AD för Azure Database för MySQL](howto-configure-sign-in-azure-ad-authentication.md).
- En översikt över inloggningar och databasanvändare för Azure Database för MySQL finns [i Skapa användare i Azure Database for MySQL](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
