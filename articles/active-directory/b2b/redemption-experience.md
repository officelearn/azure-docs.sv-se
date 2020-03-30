---
title: Inlösen av inbjudan i B2B-samarbete - Azure AD
description: Beskriver Azure AD B2B-samarbetsinbjudan inlösen erfarenhet för slutanvändare, inklusive avtalet till sekretessvillkor.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 043e0f3a0ff2c1c642c63a387c571b575f77cf7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050841"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Inlösen av Azure Active Directory B2B-samarbetsinbjudan

I den här artikeln beskrivs hur gästanvändare kan komma åt dina resurser och den medgivandeprocess de stöter på. Om du skickar ett e-postmeddelande till gästen innehåller inbjudan en länk som gästen kan lösa in för att få åtkomst till din app eller portal. E-post inbjudan är bara ett av de sätt som gästerna kan få tillgång till dina resurser. Som ett alternativ kan du lägga till gäster i din katalog och ge dem en direkt länk till portalen eller appen som du vill dela. Oavsett vilken metod de använder guidas gästerna genom en förstagångssamtyckeprocess. Den här processen säkerställer att dina gäster godkänner sekretessvillkor och accepterar alla [användarvillkor](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) som du har ställt in.

När du lägger till en gästanvändare i katalogen har gästanvändarkontot en samtyckesstatus (kan visas i PowerShell) som ursprungligen är inställd **på PendingAcceptans**. Den här inställningen kvarstår tills gästen accepterar din inbjudan och godkänner din sekretesspolicy och användarvillkor. Därefter **ändras samtyckesstatusen**till Accepterad och samtyckessidorna visas inte längre för gästen.

   > [!IMPORTANT]
   > **Från och med den 31 mars 2021**stöder Microsoft inte längre inlösen av inbjudningar genom att skapa ohanterade Azure AD-konton och klienter för B2B-samarbetsscenarier. Som förberedelse uppmuntrar vi kunder att välja [e-post engångskodautentisering.](one-time-passcode.md) Vi välkomnar din feedback på den här offentliga förhandsversionen och är glada över att skapa ännu fler sätt att samarbeta.

## <a name="redemption-through-the-invitation-email"></a>Inlösen via e-postinbjudan

När du lägger till en gästanvändare i din katalog med hjälp av [Azure-portalen](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)skickas ett e-postmeddelande för inbjudan till gästen i processen. Du kan också välja att skicka e-postmeddelanden med inbjudningar när du [använder PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) för att lägga till gästanvändare i katalogen. Här är en beskrivning av gästens upplevelse när de löser in länken i e-postmeddelandet.

1. Gästen får ett [e-postmeddelande](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) med en inbjudan som skickas från **Microsoft-inbjudningar**.
2. Gästen väljer **Kom igång** i e-postmeddelandet.
3. Om gästen inte har ett Azure AD-konto, ett Microsoft-konto (MSA) eller ett e-postkonto i en federerad organisation uppmanas de att skapa ett MSA (såvida inte [engångslösenordsfunktionen](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) är aktiverad, vilket inte kräver en MSA).
4. Gästen guidas genom [samtyckesupplevelsen](#consent-experience-for-the-guest) som beskrivs nedan.

## <a name="redemption-through-a-direct-link"></a>Inlösen via en direktlänk

Som ett alternativ till e-postmeddelandet med inbjudan kan du ge en gäst en direktlänk till din app eller portal. Du måste först lägga till gästanvändaren i din katalog via [Azure-portalen](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) eller [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Sedan kan du använda något av de [anpassningsbara sätten att distribuera program till användare,](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)inklusive direkta inloggningslänkar. När en gäst använder en direktlänk i stället för e-postmeddelandet för inbjudan guidas de fortfarande genom den första samtyckesupplevelsen.

> [!IMPORTANT]
> Den direkta länken måste vara klientspecifik. Med andra ord måste den innehålla ett klient-ID eller verifierad domän så att gästen kan autentiseras i din klient, där den delade appen finns. En vanlig https://myapps.microsoft.com URL som fungerar inte för en gäst eftersom den omdirigeras till deras hemklient för autentisering. Här är några exempel på direkta kopplingar till klientkontext:
 > - Åtkomstpanelen https://myapps.microsoft.com/?tenantid=&ltför appar: ;klient-ID&gt; 
 > - Åtkomstpanelen för appar https://myapps.microsoft.com/&ltför en verifierad domän: ;verifierad domän&gt;
 > - Azure-portal: https://portal.azure.com/&lt;klient-ID&gt;
 > - Individuell app: se hur du använder en [direkt inloggningslänk](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Det finns vissa fall där inbjudan e-post rekommenderas via en direkt länk. Om dessa specialfall är viktiga för din organisation rekommenderar vi att du bjuder in användare med metoder som fortfarande skickar e-postmeddelandet med inbjudan:
 - Användaren har inget Azure AD-konto, ett MSA- eller ett e-postkonto i en federerad organisation. Om du inte använder engångskodfunktionen måste gästen lösa in e-postmeddelandet för inbjudan för att kunna styras genom stegen för att skapa en MSA.
 - Ibland kanske det inbjudna användarobjektet inte har någon e-postadress på grund av en konflikt med ett kontaktobjekt (till exempel ett Outlook-kontaktobjekt). I det här fallet måste användaren klicka på inlösen-URL:en i e-postmeddelandet för inbjudan.
 - Användaren kan logga in med ett alias för den e-postadress som har bjudits in. (Ett alias är ytterligare en e-postadress som är kopplad till ett e-postkonto.) I det här fallet måste användaren klicka på inlösen-URL:en i e-postmeddelandet för inbjudan.

## <a name="consent-experience-for-the-guest"></a>Samtyckesupplevelse för gästen

När en gäst loggar in för att komma åt resurser i en partnerorganisation för första gången guidas de via följande sidor. 

1. Gästen granskar sidan **Granska behörigheter** som beskriver den inbjudande organisationens sekretesspolicy. En användare måste **acceptera** användningen av sin information i enlighet med den inbjudande organisationens sekretesspolicy för att fortsätta.

   ![Skärmbild som visar sidan Granskningsbehörigheter](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Information om hur du som klientadministratör kan länka till organisationens sekretesspolicy finns [i How-to: Lägg till organisationens sekretessinformation i Azure Active Directory](https://aka.ms/adprivacystatement).

2. Om användningsvillkoren är konfigurerade öppnar och granskar du användarvillkoren och väljer sedan **Acceptera**. 

   ![Skärmbild som visar nya användningsvillkor](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Du kan konfigurera se [användningsvillkor](../governance/active-directory-tou.md) i **Hantera** > **organisationsrelationer** > **Användarvillkor**.

3. Om inget annat anges omdirigeras gästen till åtkomstpanelen Appar, som listar de program som gästen kan komma åt.

   ![Skärmbild som visar åtkomstpanelen Appar](media/redemption-experience/myapps.png) 

I katalogen ändras gästens **inbjudan som accepteras** värde till **Ja**. Om ett MSA har skapats visar gästens **källa** **Microsoft-konto**. Mer information om egenskaper för gästanvändarkonto finns i [Egenskaper för en Azure AD B2B-samarbetsanvändare](user-properties.md). 

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Lägga till Azure Active Directory B2B-samarbetsanvändare i Azure-portalen](add-users-administrator.md)
- [Hur lägger informationsarbetare till B2B-samarbetsanvändare i Azure Active Directory?](add-users-information-worker.md)
- [Lägga till Azure Active Directory B2B-samarbetsanvändare med hjälp av PowerShell](customize-invitation-api.md#powershell)
- [Lämna en organisation som gästanvändare](leave-the-organization.md)
