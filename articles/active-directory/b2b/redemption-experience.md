---
title: Inlösning av inbjudan i B2B - samarbete i Azure Active Directory | Microsoft Docs
description: Beskriver Azure AD B2B-samarbete inbjudan inlösen upplevelse för slutanvändare, inklusive avtalet till sekretesspolicyn.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: a80eaa134130195fce00ee6a4d68851e478c4532
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052515"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Inlösning av inbjudan Azure Active Directory B2B-samarbete

Den här artikeln beskriver hur gästanvändare kan komma åt dina resurser och medgivande processen de stöter på. Om du skickar ett e-postinbjudan till gästen inbjudan innehåller en länk som kan lösa in gästen kommer åt din app eller portalen. E-postinbjudan är bara en av de sätt som gäster kan få åtkomst till dina resurser. Alternativt kan du lägga till gäster i din katalog och ge dem en direktlänk till den portal eller en app som du vill dela. Oavsett vilken metod som de använder vägleds gäster genom en process för första gången medgivande. Den här processen säkerställer att din gäster samtycker till sekretesspolicyn och godkänna alla [användningsvillkoren](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) du har konfigurerat.

När du lägger till en gästanvändare till din katalog gästanvändarkontot har medgivande statusen (som kan visas i PowerShell) som är inledningsvis **PendingAcceptance**. Den här inställningen förblir tills gästen har accepterat din inbjudan och samtycker till att din sekretesspolicy och användningsvillkor. Därefter ändras statusen för medgivande till **godkända**, och sidorna för medgivande visas inte längre i gäst.

## <a name="redemption-through-the-invitation-email"></a>Inlösen via e-postinbjudan

När du lägger till en gästanvändare i katalogen genom [med Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), skickas ett e-postinbjudan till gäst i processen. Du kan också välja att skicka inbjudan e-postmeddelanden när du är [med hjälp av PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) att lägga till gästanvändare i katalogen. Här är en beskrivning av gästens upplevelse när de lösa in länken i e-postmeddelandet.

1. Gästen tar emot en [e-postinbjudan](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) som skickas från **Microsoft Invitations**.
2. Gästen väljer **börjar** i e-postmeddelandet.
3. Om gästoperativsystemet inte har en Azure AD-konto, en Microsoft-konto (MSA) eller ett e-postkonto i en federerad organisation, uppmanas de att skapa en MSA (såvida inte den [engångskod](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) funktionen är aktiverad, vilket kräver inte en MSA ).
4. Gästen leds genom den [godkänna upplevelse](#consent-experience-for-the-guest) som beskrivs nedan.

## <a name="redemption-through-a-direct-link"></a>Inlösen via en direktlänk

Som ett alternativ till e-postinbjudan kan du ge en gäst en direktlänk till din app eller portalen. Du måste först lägga till gästanvändaren i din katalog via den [Azure-portalen](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) eller [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Du kan använda någon av de [anpassningsbara sätt att distribuera program till användare](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), inklusive inloggning Direktlänkar. När en gäst använder en direktlänk i stället för e-postinbjudan, får de fortfarande guidas genom den första gång samtycke upplevelsen.

> [!IMPORTANT]
> En direktlänk måste vara klientspecifik. Med andra ord måste innehålla ett klient-ID eller verifierad domän så gästen kan autentiseras i din klient, där den delade appen finns. En gemensam URL som https://myapps.microsoft.com fungerar inte för gäst eftersom det omdirigerar till sina startklientorganisation för autentisering. Här följer några exempel på Direktlänkar med klient-kontext:
 > - Åtkomstpanel för appar: https://myapps.microsoft.com/?tenantid=&lt; klient-id&gt; 
 > - Appar åtkomstpanelen för en verifierad domän: https://myapps.microsoft.com/&lt; verifierad domän&gt;
 > - Azure-portalen: https://portal.azure.com/&lt; klient-id&gt;
 > - Enskild app: se hur du använder en [inloggning direktlänk](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Det finns tillfällen där e-postinbjudan rekommenderas framför en direktlänk. Om dessa särskilda fall är viktiga för din organisation, rekommenderar vi att du bjuder in användare med hjälp av metoder som fortfarande skickar e-postinbjudan:
 - Användaren har inte en Azure AD-konto, en MSA eller ett e-postkonto i en extern organisation. Om du inte använder funktionen för engångslösenord måste lösa in e-postinbjudan till guidas genom stegen för att skapa en MSA gästen.
 - Ibland kanske inbjudna användarobjektet inte har en e-postadress på grund av en konflikt med ett kontaktobjekt (till exempel ett Outlook kontakta objekt). I så fall måste användaren klicka på URL-Adressen för inlösen i e-postinbjudan.
 - Användaren kan logga in med ett alias för den e-postadressen har bjudit in. (Ett alias är en ytterligare e-postadress som är associerad med ett e-postkonto.) I så fall måste användaren klicka på URL-Adressen för inlösen i e-postinbjudan.

## <a name="consent-experience-for-the-guest"></a>Samtycke upplevelsen för gäst

När en gäst loggar in till resurser i en partnerorganisation för första gången, är de leds genom följande sidor. 

1. Gäst-granskningar den **granska behörigheter** sida som beskriver den organisationen som bjuder in sekretesspolicy. En användare måste **acceptera** användningen av sin information i enlighet med den organisationen som bjuder in sekretessprinciper för att fortsätta.

   ![Skärmbild som visar sidan Granska behörigheter](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Information om hur du som en Innehavaradministratör kan länka till din organisations sekretesspolicy finns [anvisningar: Lägg till din organisations sekretess information i Azure Active Directory](https://aka.ms/adprivacystatement).

2. Om villkoren har konfigurerats, gästen öppnas granskar användningsvillkoren och väljer sedan **acceptera**. 

   ![Skärmbild som visar nya användningsvillkor](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Du kan konfigurera finns i [användningsvillkoren](../governance/active-directory-tou.md) i **hantera** > **organisationens relationer** > **användningsvillkoren**.

3. Om inget annat anges, omdirigeras gästen till åtkomstpanelen appar, som visar de program som kan komma åt gästen.

   ![Skärmbild som visar åtkomstpanelen appar](media/redemption-experience/myapps.png) 

I din katalog, gästen **inbjudan accepterades** ändras till **Ja**. Om en MSA skapades, gästens **källa** visar **Account**. Läs mer om egenskaperna för användarkontot guest [egenskaperna för en användare för Azure AD B2B-samarbete](user-properties.md). 

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Lägg till användare i Azure Active Directory B2B-samarbetet i Azure portal](add-users-administrator.md)
- [Hur lägger informationsarbetare till användare i B2B-samarbetet i Azure Active Directory?](add-users-information-worker.md)
- [Lägg till användare i Azure Active Directory B2B-samarbetet med hjälp av PowerShell](customize-invitation-api.md#powershell)
- [Lämna en organisation som gästanvändare](leave-the-organization.md)
