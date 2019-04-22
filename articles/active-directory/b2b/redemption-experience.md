---
title: Inlösning av inbjudan i B2B - samarbete i Azure Active Directory | Microsoft Docs
description: Beskriver Azure AD B2B-samarbete inbjudan inlösen upplevelse för slutanvändare, inklusive avtalet till sekretesspolicyn.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e5a8d46f67553cc10bd9cdb31cf64511858948f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046503"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Inlösning av inbjudan Azure Active Directory B2B-samarbete

Du kan bjuda in gästanvändare rätt att komma åt delade appar för att samarbeta med användare från partnerorganisationer via Azure Active Directory (Azure AD) B2B-samarbete. När en gästanvändare läggs till katalogen via användargränssnittet eller uppmanas användaren via PowerShell, gästanvändare måste gå via en process för första gången medgivande där de godkänner [sekretessvillkor](#privacy-policy-agreement). Den här processen sker i något av följande sätt:

- Gäst bjuder in skickar ut en direktlänk till en delad app. När en användare klickar på länken för att logga in, accepterar sekretesspolicyn och har åtkomst till smidigt den delade resursen. (Gästanvändaren fortfarande får ett e-postinbjudan med en URL för inlösen, men än vissa särskilda fall kan den inte längre krävs för att använda e-postinbjudan.)  
- Gästanvändaren får ett e-postinbjudan och klickar på URL: en för inlösen. Som en del av inloggning första gången uppmanas de att godkänna villkoren sekretess.

## <a name="redemption-through-a-direct-link"></a>Inlösen via en direktlänk

En gästinbjudare kan bjuda in gästanvändare genom att skicka en [direktlänk till en delad app](../manage-apps/end-user-experiences.md#direct-sign-on-links). Inlösen upplevelsen är lika enkelt som att logga in på den app som har delats med dem för gästanvändaren. De kan klicka på en länk till appen, granska och Godkänn sekretesspolicyn och sedan få smidig åtkomst till appen. I de flesta fall behöver gästanvändare längre klickar du på en inlösen URL i en e-postinbjudan.

Om du har bjudit in gästanvändare via användargränssnittet eller väljer att skicka e-postinbjudan som en del av PowerShell inbjudan upplevelse, får inbjudna användaren fortfarande ett e-postinbjudan. Det här meddelandet är användbart i följande specialfall:

- Användaren har inte en Azure AD-konto eller ett Microsoft-konto (MSA). I det här fallet måste användaren skapa ett MSA innan de klickar på länken eller de kan använda URL: en för inlösen i e-postinbjudan. Inlösen processen uppmanar automatiskt användaren att skapa en MSA.
- Ibland kanske inbjudna användarobjektet inte har en e-postadress på grund av en konflikt med ett kontaktobjekt (till exempel ett Outlook kontakta objekt). I så fall måste användaren klicka på URL-Adressen för inlösen i e-postinbjudan.
- Användaren kan logga in med ett alias för den e-postadressen har bjudit in. (Ett alias är en ytterligare e-postadress som är associerad med ett e-postkonto.) I så fall måste användaren klicka på URL-Adressen för inlösen i e-postinbjudan.

Om dessa särskilda fall är viktiga för din organisation, rekommenderar vi att du bjuder in användare med hjälp av metoder som fortfarande skickar e-postinbjudan. Om en användare inte omfattas av någon av dessa specialfall, kan de fortfarande klickar du på Webbadressen i ett e-postinbjudan för att få åtkomst.

## <a name="redemption-through-the-invitation-email"></a>Inlösen via e-postinbjudan

Om inbjuden via en metod som skickar ett e-postinbjudan kan användare också lösa in inbjudan via e-postinbjudan. En inbjuden användare kan klicka på Webbadressen för inlösen i e-post, och sedan granska och Godkänn sekretesspolicyn. Processen beskrivs i detalj här:

1.  När du har fått en inbjudan, när en användare får en inbjudan via e-post som skickas från **Microsoft Invitations**.
2.  När en användare väljer **börjar** i e-postmeddelandet.
3.  Om en användare inte har en Azure AD-konto eller en MSA, uppmanas de att skapa en MSA.
4.  När en användare dirigeras till den **granska behörigheter** skärmen, där de kan läsa i organisationen som bjuder in sekretesspolicy och accepterar villkoren.

## <a name="privacy-policy-agreement"></a>Sekretess princip avtal

När alla gästanvändare loggar in till resurser i en partnerorganisation för första gången, visas en **granska behörigheter** skärmen. Här, kan de läsa den organisationen som bjuder in sekretesspolicy. En användare måste godkänna användningen av sin information i enlighet med den organisationen som bjuder in sekretessprinciper för att fortsätta.

![Skärmbild som visar användarinställningar i åtkomstpanelen](media/redemption-experience/ConsentScreen.png) 

Information om hur du som en Innehavaradministratör kan länka till din organisations sekretesspolicy finns [anvisningar: Lägg till din organisations sekretess information i Azure Active Directory](https://aka.ms/adprivacystatement).

## <a name="terms-of-use"></a>Användningsvillkor

Du kan visa användningsvillkoren till gästanvändaren under inledande inlösen med hjälp av Azure AD-villkoren i Använd funktion. I Azure Active Directory, kan du komma åt den här funktionen under **hantera** > **organisationens relationer** > **användningsvillkoren** eller under **Security** > **villkorlig åtkomst** > **användningsvillkoren**. Mer information finns i [Azure AD användningsvillkor Använd funktion](../conditional-access/terms-of-use.md).

![Skärmbild som visar nya användningsvillkor](media/redemption-experience/organizational-relationships-terms-of-use.png) 

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Lägg till användare i Azure Active Directory B2B-samarbetet i Azure portal](add-users-administrator.md)
- [Hur lägger informationsarbetare till användare i B2B-samarbetet i Azure Active Directory?](add-users-information-worker.md)
- [Lägg till användare i Azure Active Directory B2B-samarbetet med hjälp av PowerShell](customize-invitation-api.md#powershell)
- [Lämna en organisation som gästanvändare](leave-the-organization.md)
