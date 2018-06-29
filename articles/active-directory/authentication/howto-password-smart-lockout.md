---
title: Förhindra brute force-attacker med hjälp av Azure AD smart låsning
description: Azure Active Directory smart kontoutelåsning skyddar din organisation från brute force-attacker försöker gissa lösenord
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: d5beb5ce6e167cd100bec2ed54dc6ea0e78ba37b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036258"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory smart kontoutelåsning

Smart kontoutelåsning använder molnet intelligence för att låsa ute obehöriga som försöker att gissa användarnas lösenord eller använda brute force-metoder för att hämta i. Det intelligence kan känna igen inloggningar som kommer från giltiga användare och behandlas annorlunda än de som angripare och andra okända källor. Smart kontoutelåsning kan låsa ut angripare medan användarna fortfarande komma åt sina konton och vara produktiva.

Smart kontoutelåsning är alltid aktiverad för alla Azure AD-kunder med standardinställningar som erbjuder den rätta blandningen av säkerhet och användbarhet. Anpassning av inställningarna smart kontoutelåsning med värden som är specifik för din organisation kräver Azure AD Basic eller högre licenser för dina användare.

Smart kontoutelåsning kan integreras med hybriddistributioner med lösenordshashsynkronisering eller direktautentisering för att skydda lokala Active Directory-konton från angripare som låst. Genom att ange principer för kontoutelåsning smart i Azure AD korrekt filtreras attacker innan de når lokala Active Directory.

När du använder [direktautentisering](../connect/active-directory-aadconnect-pass-through-authentication.md), måste du se till att:

   * Azure AD-utelåsningströskeln är **mindre** än tröskelvärde för kontoutelåsning Active Directory. Ange värden så att Active Directory tröskelvärde för kontoutelåsning är minst två eller tre gånger längre än utelåsningströskeln Azure AD. 
   * Azure AD-utelåsningstiden **i sekunder** är **längre** än Active Directory Återställ räknaren för kontoutelåsning efter längd **minuter**.

> [!IMPORTANT]
> En administratör kan inte låser upp användarens molnet konton om de har låsts ute av Smart kontoutelåsning kapaciteten. Administratören måste vänta tills utelåsningstiden ska upphöra att gälla.

## <a name="verify-on-premises-account-lockout-policy"></a>Kontrollera lokalt kontoutelåsning

Använd följande instruktioner för att verifiera din lokala Active Directory-kontoutelåsning:

1. Öppna verktyget hantering av Grupprincip.
2. Redigera grupprincipen som innehåller din organisations kontoutelåsning, till exempel den **Standarddomänprincip**.
3. Bläddra till **Datorkonfiguration** > **principer** > **Windowsinställningar** > **säkerhetsinställningar**   >  **Konto principer** > **kontoprincip kontoutelåsning**.
4. Kontrollera din **tröskelvärde för kontoutelåsning** och **Återställ räknaren för kontoutelåsning efter** värden.

![Ändra den lokala Active Directory kontoutelåsning med hjälp av ett grupprincipobjekt](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Hantera Azure AD smart kontoutelåsning värden

Baserat på din organisations krav, kan smart kontoutelåsning värden behöva anpassas. Anpassning av inställningarna smart kontoutelåsning med värden som är specifik för din organisation kräver Azure AD Basic eller högre licenser för dina användare.

Om du vill kontrollera eller ändra smart kontoutelåsning värden för din organisation använder du följande steg:

1. Logga in på den [Azure-portalen](https://portal.azure.com), och klicka på **Azure Active Directory**, sedan **autentiseringsmetoder**.
1. Ange den **tröskelvärde för kontoutelåsning**, baserat på hur många misslyckade inloggningar tillåts för ett konto innan dess första utelåsning. Standardvärdet är 10.
1. Ange den **utelåsningstiden i sekunder**, varje kontoutelåsning längd i sekunder.

> [!NOTE]
> Om den första inloggningen när en utelåsning också misslyckas, kontot låser ut igen. Om ett konto har låsts upprepade gånger kan ökar utelåsningstiden.

![Anpassa Azure AD-princip för smart kontoutelåsning i Azure-portalen](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)
## <a name="next-steps"></a>Nästa steg

[Lär dig hur du förbjuda felaktiga lösenord i din organisation använder Azure AD.](howto-password-ban-bad.md)

[Konfigurera Självbetjäning för återställning av lösenord för att tillåta användare att låsa upp sina egna konton.](quickstart-sspr.md)