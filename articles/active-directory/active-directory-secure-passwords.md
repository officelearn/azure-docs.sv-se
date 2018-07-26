---
title: Nivåindelad lösenordssäkerhet i Azure AD | Microsoft Docs
description: Förklarar hur Azure AD kräver starka lösenord och skyddar användarnas lösenord från cyberhot,
services: active-directory
documentationcenter: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/28/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 277e66e954d0fdff026d473ba3f6ad07a8b87da7
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173417"
---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>En metod med flera nivåer för Azure AD-lösenordssäkerhet

Den här artikeln beskriver några regelverk som användare eller administratörer kan följa för att skydda Azure Active Directory- (Azure AD) eller Microsoft Account-konton.

 > [!NOTE]
 > **Är du här eftersom du har problem med att logga in?** I så fall är det [här som du ser hur du kan ändra och återställa ditt eget lösenord](user-help/active-directory-passwords-update-your-own-password.md).
 >
 > Azure AD-administratörer kan återställa användarlösenord genom att följa vägledningen i artikeln om att [återställa lösenordet för en användare i Azure Active Directory](fundamentals/active-directory-users-reset-password-azure-portal.md).
 >

## <a name="password-requirements"></a>Lösenordskrav

Azure AD använder följande vanliga metoder för att skydda lösenord:

* Krav på lösenordslängd
* Krav på lösenordskomplexitet
* Regelbundet och periodiskt upphörande av lösenord

Information om lösenordsåterställning i Azure Active Directory finns i avsnittet om [Azure AD-lösenordsåterställning via självbetjäning för IT-proffs](user-help/active-directory-passwords-update-your-own-password.md).

## <a name="azure-ad-password-protections"></a>Azure AD-lösenordsskydd

Azure AD och Microsoft Account System använder följande beprövade metoder för att säkerställa att lösenord för användare och administratörer skyddas på ett säkert sätt:

* Lösenord som förbjuds dynamiskt
* Smart lösenordsutelåsning

Information om lösenordshantering baserat på den senaste forskningen finns i faktabladet [Password Guidance](https://aka.ms/passwordguidance) (Riktlinjer för lösenord).

### <a name="dynamically-banned-passwords"></a>Lösenord som förbjuds dynamiskt

Azure AD- och Microsoft-konton upprätthåller lösenordsskyddet genom att dynamiskt förbjuda vanliga lösenord. Azure AD Identity Protection-teamet analyserar rutinmässigt listor med förbjudna lösenord, och hindrar användare från att välja vanliga lösenord. Den här tjänsten är tillgänglig för Azure AD- och Microsoft Account Service-kunder.

När lösenord skapas är det viktigt att administratören uppmanar användarna att välja lösenord som innehåller en unik kombination av bokstäver, siffror, tecken och ord. Den här metoden hjälper till att göra användarlösenord nästan omöjliga att kompromettera men enklare för användarna att komma ihåg.

#### <a name="password-breaches"></a>Lösenordsöverträdelser

Microsoft försöker alltid ligga steget före cyberbrottslingarna.

Azure AD Identity Protection-teamet analyserar kontinuerligt lösenord som används ofta. Cyberbrottslingar använder också liknande strategier för sina attacker, t.ex. genom att skapa en [regnbågstabell](https://en.wikipedia.org/wiki/Rainbow_table) för att knäcka lösenordshashar.

Microsoft analyserar kontinuerligt [dataöverträdelser](https://www.privacyrights.org/data-breaches) och använder en dynamiskt uppdaterad lista med förbjudna lösenord, som gör att sårbara lösenord förbjuds innan de utgör ett verkligt hot för Azure AD-kunder. Mer information om vårt säkerhetsarbete finns i [Microsofts säkerhetsinformationsrapport](https://www.microsoft.com/security/sir/default.aspx).

### <a name="smart-password-lockout"></a>Smart lösenordsutelåsning

Om Azure AD upptäcker att en potentiell cyberbrottsling försöker hacka ett användarlösenord låser vi användarkontot med hjälp av smart lösenordsutelåsning. Azure AD har utformats för att bedöma riskerna med specifika inloggningssessioner. Sedan används uppdaterade säkerhetsdata och utelåsningssemantik för att stoppa cyberhot.

Om Azure AD låser ut en användare visas en skärm liknande den följande:

  ![Utelåst från Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)

För andra Microsoft-konton ser skärmen ut som den följande:

  ![Utelåst från ett Microsoft-konto](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Information om lösenordsåterställning i Azure Active Directory finns i avsnittet om [Azure AD-lösenordsåterställning via självbetjäning för IT-proffs](user-help/active-directory-passwords-update-your-own-password.md).

  >[!NOTE]
  >Om du är en Azure AD-administratör kan du använda [Windows Hello](https://www.microsoft.com/windows/windows-hello) för att helt och hållet förhindra att användare skapar traditionella lösenord.
  >

## <a name="next-steps"></a>Nästa steg

* [Uppdatera ditt eget lösenord](user-help/active-directory-passwords-update-your-own-password.md)
* [Grunderna i Azures identitetshantering](fundamentals-identity.md)
* [Rapportera om lösenordsåterställningsaktivitet](authentication/howto-sspr-reporting.md)
