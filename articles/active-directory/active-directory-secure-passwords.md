---
title: Azure AD nivåer lösenordssäkerhet | Microsoft Docs
description: Förklarar hur Azure AD tillämpar starka lösenord och skyddar användarnas lösenord från cyber kriminella
services: active-directory
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barlan
ms.openlocfilehash: 04e437eb5a040f418df22e0857cb96d7ef99bbbb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>En metod med flera nivåer för säkerhet för Azure AD-lösenord

Den här artikeln beskrivs några metoder du kan följa som en användare eller administratör för att skydda din Azure Active Directory (AD Azure) eller Account.

 > [!NOTE]
 > **Är du här eftersom du har problem med att logga in?** I så fall är det [här som du ser hur du kan ändra och återställa ditt eget lösenord](active-directory-passwords-update-your-own-password.md).
 >
 > Azure AD-administratörer kan återställa användarlösenord med hjälp av anvisningarna i artikeln [återställa lösenordet för en användare i Azure Active Directory](active-directory-users-reset-password-azure-portal.md).
 >

## <a name="password-requirements"></a>Krav på lösenord

Azure AD använder följande vanliga metoder för att skydda lösenord:

* Krav på lösenordslängd
* Kraven på lösenordskomplexitet
* Regelbundet och periodiskt upphörande av lösenord

Information om lösenordsåterställning i Azure Active Directory finns i avsnittet [Azure AD lösenordsåterställning via Självbetjäning för IT-proffs](active-directory-passwords-update-your-own-password.md).

## <a name="azure-ad-password-protections"></a>Skydd med Azure AD-lösenord

Azure AD och Microsofts kontosystem använder branschen beprövade metoder för att säkerställa säker skydd av användar- och lösenord, inklusive:

* Lösenord som förbjuds dynamiskt
* Smart lösenordsutelåsning

Information om lösenordshantering baserat på aktuella research finns i vitboken [lösenord vägledning](https://aka.ms/passwordguidance).

### <a name="dynamically-banned-passwords"></a>Lösenord som förbjuds dynamiskt

Azure AD och Microsoft Accounts skydda lösenordsskydd genom dynamiskt förbjuda vanliga lösenord. Azure AD Identity Protection-teamet analyserar regelbundet förbjudna lösenordslistor, vilket förhindrar användare från att välja vanliga lösenord. Den här tjänsten är tillgänglig för Azure AD- och Microsoft Account Service-kunder.

När du skapar lösenord, är det viktigt för administratörer att uppmana användare att välja lösenord fraser som innehåller en unik kombination av bokstäver, siffror, tecknen eller orden. Den här metoden kan du göra användarlösenord nästan omöjligt att vara komprometterad men enklare för användare att komma ihåg.

#### <a name="password-breaches"></a>Överträdelser av lösenord

Microsoft arbetar alltid för att hålla ett steg i cyber kriminella.

Azure AD Identity Protection-teamet analyserar kontinuerligt lösenord som används ofta. Cyberbrottslingar använder också liknande strategier för sina attacker, t.ex. genom att skapa en [regnbågstabell](https://en.wikipedia.org/wiki/Rainbow_table) för att knäcka lösenordshashar.

Microsoft analyserar kontinuerligt [dataöverträdelser](https://www.privacyrights.org/data-breaches) och använder en dynamiskt uppdaterad lista med förbjudna lösenord, som gör att sårbara lösenord förbjuds innan de utgör ett verkligt hot för Azure AD-kunder. Mer information om vårt säkerhetsarbete finns i [Microsofts säkerhetsinformationsrapport](https://www.microsoft.com/security/sir/default.aspx).

### <a name="smart-password-lockout"></a>Smart lösenordsutelåsning

Om Azure AD upptäcker att en potentiell cyberbrottsling försöker hacka ett användarlösenord låser vi användarkontot med hjälp av smart lösenordsutelåsning. Azure AD har utformats för att bedöma riskerna med specifika inloggningssessioner. Sedan använder den senaste informationen om säkerhet, det använda semantiken för kontoutelåsning för att stoppa cyber hot.

Om en användare har låsts ute från Azure AD, liknar skärmen det som följer:

  ![Utelåst från Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)

För andra Microsoft-konton liknar skärmen det som följer:

  ![Utelåst från ett Microsoft-konto](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Information om lösenordsåterställning i Azure Active Directory finns i avsnittet [Azure AD lösenordsåterställning via Självbetjäning för IT-proffs](active-directory-passwords-update-your-own-password.md).

  >[!NOTE]
  >Om du är en Azure AD-administratör kan du använda [Windows Hello](https://www.microsoft.com/windows/windows-hello) för att helt och hållet förhindra att användare skapar traditionella lösenord.
  >

## <a name="next-steps"></a>Nästa steg

* [Uppdatera ditt eget lösenord](active-directory-passwords-update-your-own-password.md)
* [Grunderna i Azures identitetshantering](fundamentals-identity.md)
* [Återställningsaktivitet för rapporten på lösenord](authentication/howto-sspr-reporting.md)
