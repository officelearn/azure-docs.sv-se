---
title: LinkedIn-datadelning och -samtycke - Azure Active Directory | Microsoft-dokument
description: Förklarar hur LinkedIn-integrering delar data via Microsoft-appar i Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a21c986ccfe96bae5d341e0ba2ee6622d998d076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025396"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>LinkedIn-kontoanslutningar datadelning och samtycke

Du kan göra det möjligt för användare i din Active Directory-organisation (Azure AD) att samtycka till att ansluta sitt Microsoft-arbets- eller skolkonto till sitt LinkedIn-konto. När en användare har anslutit sina konton finns information och höjdpunkter från LinkedIn tillgängliga i vissa Appar och tjänster från Microsoft. Användare kan också förvänta sig att deras nätverksupplevelse på LinkedIn förbättras och berikas med information från Microsoft.

Om du vill visa LinkedIn-information i Microsofts appar och tjänster måste användarna samtycka till att ansluta sina egna Microsoft- och LinkedIn-konton. Användarna uppmanas att ansluta sina konton första gången de klickar för att se någons LinkedIn-information på ett profilkort i Outlook, OneDrive eller SharePoint Online. LinkedIn-kontoanslutningar är inte helt aktiverade för dina användare förrän de samtycker till upplevelsen och kopplar sina konton.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Fördelar med att dela LinkedIn-information

Åtkomst till LinkedIn-information i Microsofts appar och tjänster gör det enklare för användarna att ansluta, engagera och skapa professionella relationer med kollegor, kunder och partner inom och utanför organisationen. Nya användare kan komma igång snabbare genom att ansluta till kollegor, lära sig mer om dem och enkelt komma åt mer information. Här är ett exempel på hur LinkedIn-information visas på profilkortet i Microsoft-appar:

![Aktivera LinkedIn-integrering i din organisation](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Aktivera och meddela LinkedIn-integrering

Du måste vara Azure Active Directory Admin för att hantera inställningen för din organisation. Du kan aktivera den för alla användare eller för en viss uppsättning användare.

1. Om du vill aktivera eller inaktivera integreringen följer du stegen i [Consent to LinkedIn-integrering för din Azure AD-organisation](linkedin-integration.md).
2. När du meddelar LinkedIn-integreringen i organisationen pekar du användarna på vanliga frågor och svar om [LinkedIn-information i Microsofts appar och tjänster](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). Artikeln innehåller information om var LinkedIn-information visas, hur du ansluter konton med mera.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Användarens medgivande för dataåtkomst i Microsoft och LinkedIn

Data som används från LinkedIn lagras inte permanent i Microsoft-tjänster. Data som används från Microsoft lagras inte permanent med LinkedIn, förutom Kontakter. Microsoft Kontakter lagras på LinkedIn tills användarna tar bort dem, enligt beskrivningen i [att ta bort importerade kontakter från LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

När användare ansluter sina konton finns information och insikter från LinkedIn i vissa Microsoft-appar, till exempel profilkortet. Användare kan också förvänta sig att deras nätverksupplevelse på LinkedIn förbättras och berikas med information från Microsoft.
När användare i organisationen ansluter sina LinkedIn- och Microsoft-arbets- eller skolkonton har de två alternativ:

* Ge behörighet för data som ska nås från båda kontona. Det innebär att de ger behörighet för sitt Microsoft- eller arbetskonto att komma åt data från sitt LinkedIn-konto och [att deras LinkedIn-konto får åtkomst till data från sitt Microsoft-arbets- eller skolkonto](https://www.linkedin.com/help/linkedin/answer/84077).
* Ge tillstånd för endast LinkedIn-data som ska nås av deras Microsoft-arbets- och skolkonto.

Användare kan koppla från konton och ta bort dataåtkomstbehörigheter när som helst, och [användarna kan styra hur deras egen LinkedIn-profil visas](https://www.linkedin.com/help/linkedin/answer/83), inklusive om deras profil kan visas i Microsoft-appar.

### <a name="linkedin-account-data"></a>LinkedIn-kontodata

När du ansluter dina Microsoft- och LinkedIn-konton tillåter du att LinkedIn tillhandahåller följande data till Microsoft:

* Profildata - inkluderar LinkedIn-identitet, kontaktinformation och den information du delar med andra i din [LinkedIn-profil](https://www.linkedin.com/help/linkedin/answer/15493).
* Intressendata - inkluderar intressen på LinkedIn, till exempel personer och ämnen du följer, kursgrupper och innehåll som du gillar och delar.
* Prenumerationsdata - inkluderar prenumerationer på LinkedIn-program och -tjänster tillsammans med tillhörande data. 
* Anslutningsdata - inkluderar ditt [LinkedIn-nätverk](https://www.linkedin.com/help/linkedin/answer/110) inklusive profiler och kontaktinformation för dina första gradens anslutningar.

Data som används från LinkedIn lagras inte permanent i Microsoft-tjänster. Mer information om Microsofts användning av personuppgifter finns i [Microsofts sekretesspolicy](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Microsofts jobb- eller skolkontodata

När du ansluter dina Microsoft- och LinkedIn-konton tillåter du att Microsoft tillhandahåller följande data till LinkedIn:

* Profildata – innehåller information som förnamn, efternamn, profilfoto, e-postadress, chef och personer som du hanterar.
* Kalenderdata – innehåller möten i kalendrar, tider, platser och deltagarnas kontaktinformation. Information om mötet, till exempel dagordning, innehåll eller mötesrubrik, ingår inte i kalenderdata.
* Intressen data - inkluderar de intressen som är associerade med ditt konto, baserat på din användning av Microsoft-tjänster, till exempel Cortana och Bing för företag.
* Prenumerationsdata – inkluderar prenumerationer som din organisation tillhandahåller Microsofts appar och tjänster, till exempel Office 365.
* Kontaktdata – innehåller kontaktlistor i Outlook, Skype och andra Microsoft-kontotjänster, inklusive kontaktinformation för personer som du ofta kommunicerar eller samarbetar med. Kontakter importeras, lagras och används regelbundet av LinkedIn, till exempel för att föreslå anslutningar, ordna kontakter och visa uppdateringar om kontakter.

Data som används från Microsoft lagras inte permanent med LinkedIn, förutom Kontakter. Microsoft Kontakter lagras på LinkedIn tills användarna tar bort dem. Läs mer om [hur du tar bort importerade kontakter från LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Mer information om LinkedIns användning av personuppgifter finns i [LinkedIns sekretesspolicy](https://www.linkedin.com/legal/privacy-policy). För LinkedIn-tjänster, dataöverföring och lagring kan data flöda från EU till USA och tillbaka, och din integritet skyddas enligt beskrivningen i [EU:s dataöverföringar](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>Nästa steg

* [LinkedIn i Microsoft-program med ditt arbets- eller skolkonto](https://www.linkedin.com/help/linkedin/answer/84077)
