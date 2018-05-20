---
title: Inbjudan inlösning i B2B - samarbete i Azure Active Directory | Microsoft Docs
description: Beskriver Azure AD B2B-samarbete inbjudan inlösning upplevelse för slutanvändare, inklusive avtalet till sekretessvillkor.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0a0c900bbfbb2778d8fabcbb71e339fd3dabcf47
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B-samarbete inbjudan inlösning

Du kan erbjuda gästanvändare att komma åt delade appar om du vill samarbeta med användare från organisationer via Azure Active Directory (AD Azure) B2B-samarbete. När en gästanvändare läggs till katalogen via användargränssnittet eller uppmanas användaren via PowerShell, gästanvändare måste gå via en process för första gången medgivande där de godkänner [sekretessvillkor](#privacy-policy-agreement). Den här processen sker i något av följande sätt:

- Gästen avsändaren av inbjudan skickar ut en direktlänk till en delad app. När en användare klickar på länken för att logga in, accepterar du villkoren för sekretess och har åtkomst till sömlöst den delade resursen. (Gästanvändaren fortfarande får en e-postinbjudan med en inlösning URL, men än vissa särskilda fall kan den inte längre krävs för att använda e-postinbjudan.)  
- Gästanvändaren får ett e-postinbjudan och klickar på URL: en åtgärd. Som en del av inloggning första gången uppmanas de att acceptera villkoren sekretess.

## <a name="redemption-through-a-direct-link"></a>Inlösning via en direktlänk

En gäst bjuder in bjuda in gästanvändare genom att skicka ut en direktlänk till en delad app. Åtgärd-upplevelse är lika enkelt som att logga in i appen som delades med dem för gästanvändare. De kan klicka på en länk till appen, granska och acceptera villkoren sekretess och sömlöst till appen. I de flesta fall behöver gästanvändare längre klickar du på en inlösning URL i en e-postinbjudan.

Om du uppmanas gästanvändare via användargränssnittet eller väljer att skicka e-postinbjudan som en del av PowerShell inbjudan får inbjudna användaren fortfarande ett e-postinbjudan. Den här e-post är användbara för följande särskilda fall:

- Användaren har inte ett Azure AD-konto eller ett Microsoft-konto (MSA). I det här fallet måste användaren skapa ett MSA innan de klickar på länken eller de kan använda URL: en åtgärd i e-postinbjudan. Processen inlösning uppmanar automatiskt användaren att skapa en MSA.
- Ibland kanske inbjudna användarobjektet inte har en e-postadress på grund av en konflikt med ett kontaktobjekt (till exempel ett Outlook kontakta objekt). I det här fallet måste användaren klicka på inlösning URL-Adressen i e-postinbjudan.
- Användaren kan logga in med ett alias för e-postadressen har bjudits in. (Ett alias är en extra e-postadress som är associerad med ett e-postkonto.) I det här fallet måste användaren klicka på inlösning URL-Adressen i e-postinbjudan.

Om dessa särskilda fall är viktiga för din organisation, rekommenderar vi att bjuda in användare med hjälp av metoder som fortfarande skickar e-postinbjudan. Även om en användare inte faller under någon av dessa specialfall, de kan fortfarande klicka på Webbadressen i ett e-postinbjudan för att få åtkomst.

## <a name="redemption-through-the-invitation-email"></a>Inlösning via e-postinbjudan

Om inbjuden genom en metod som skickar ett e-postinbjudan kan användare också lösa in en inbjudan via e-postinbjudan. En inbjudna användare kan klicka på inlösning Webbadressen i e-post, och sedan granska och Godkänn villkoren sekretess. Processen beskrivs i detalj här:

1.  När du har fått en inbjudan, när en användare tar emot en inbjudan via e-post som skickas från **Microsoft Invitations**.
2.  När en användare väljer **Kom igång** i e-postmeddelandet.
3.  Om en användare inte har en Azure AD-kontot eller en MSA, uppmanas de att skapa en MSA.
4.  När en användare dirigeras till den **granska behörigheter** skärmen, där de kan granska bjuda in organisationens sekretesspolicy och accepterar villkoren.

## <a name="privacy-policy-agreement"></a>Sekretess princip avtal

När alla gästanvändaren loggar in till resurser i en organisation för första gången, visas en **granska behörigheter** skärmen. Här kan granska de bjuda in företagets sekretesspolicy. En användare måste acceptera att sina uppgifter i enlighet med bjuda in organisationens sekretesspolicy för att fortsätta.

![Skärmbild som visar användarinställningar i åtkomstpanelen](media/redemption-experience/ConsentScreen.png) 

Information om hur du som en Innehavaradministratör kan länka din organisations sekretesspolicy finns [anvisningar: lägga till din organisations sekretess information i Azure Active Directory](https://aka.ms/adprivacystatement).

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Lägg till Azure Active Directory B2B-samarbete användare i Azure-portalen](add-users-administrator.md)
- [Hur lägger informationsarbetare till B2B-samarbete användare till Azure Active Directory?](add-users-information-worker.md)
- [Lägg till användare i Azure Active Directory B2B-samarbete med hjälp av PowerShell](customize-invitation-api.md#powershell)
- [Lämna en organisation som gästanvändare](leave-the-organization.md)