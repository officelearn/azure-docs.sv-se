---
title: LinkedIn-konto anslutningar användargodkännande för Datadelning i Azure Active Directory | Microsoft Docs
description: Förklarar hur LinkedIn-kontoanslutningar dela data via Microsoft-appar i Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: b6b7f6f51e848223adcf618ebbc47df08e7ea7ef
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44392267"
---
# <a name="user-consent-and-linkedin-account-connections-data-sharing"></a>Användarens medgivande och delning av LinkedIn konto anslutningar data

Som administratör för Azure Active Directory (Azure AD) kan ge du användarna i din organisation samtycker till att ansluta sina Microsoft arbets- eller skolkonto med ett LinkedIn-konto. När användarna ansluter sina konton, information och höjdpunkter från LinkedIn finns i vissa Microsoft-appar och tjänster. Användare kan också förväntar sig sin nätverksupplevelse på LinkedIn att förbättras och berikats med information från Microsoft.

Om du vill se LinkedIn-information i Microsoft-appar och tjänster, måste användarna cconsent för att ansluta sina egna Microsoft och LinkedIn-konton. Användare uppmanas att ansluta sina konton första gången de klickar på om du vill se en persons LinkedIn-information på ett Profilkort i Outlook, OneDrive eller SharePoint Online. LinkedIn-kontoanslutningar är inte helt aktiverade för dina användare tills de godkänna att upplevelsen och för att ansluta sina konton.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Fördelar med att dela LinkedIn-information

Åtkomst till LinkedIn-information i Microsoft-appar och tjänster gör det enklare för användarna att ansluta, engagera och skapa professionella relationer med kollegor, kunder och partner i och utanför organisationen. Nya användare kan få igång snabbare genom att ansluta med kollegor, mer information om dem och enkel åtkomst till mer information. Här är ett exempel på hur LinkedIn-information visas på kortet profil i Microsoft-appar:

![Aktivera LinkedIn-kontoanslutningar](./media/linkedin-user-consent/display-example-02.png)

## <a name="enable-and-announce-linkedin-account-connections"></a>Aktivera och meddela LinkedIn-kontoanslutningar

Du måste vara en Azure Active Directory-administratör att hantera inställningen för din organisation. Du kan aktivera det för alla användare eller för en specifik uppsättning användare.

1. Om du vill aktivera eller inaktivera konto anslutningar integration, följer du stegen i [LinkedIn-kontoanslutningar](linkedin-integration.md).
2. När du meddela LinkedIn-integrering i din organisation, peka användarna på vanliga frågor om [LinkedIn-information i Microsoft-appar och tjänster](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). Artikeln innehåller information om var LinkedIn-information visas hur du ansluter konton och mycket mer.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Användargodkännande för dataåtkomst i Microsoft och LinkedIn

Data som öppnas från LinkedIn lagras inte permanent i Microsoft-tjänster. Data som kan nås från Microsoft lagras inte permanent med LinkedIn, förutom kontakter. Microsoft Contacts lagras på LinkedIn tills användare tar bort dem, enligt beskrivningen i [tar bort importerade kontakter från LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

När användarna ansluter sina konton, finns information och insikter från LinkedIn i vissa Microsoft-appar, t.ex-profil. Användare kan också förväntar sig sin nätverksupplevelse på LinkedIn att förbättras och berikats med information från Microsoft.
När användare i din organisation ansluta sitt arbete med LinkedIn- och Microsoft- eller skolkonton, har de två alternativ:

* Ge behörighet för de data som nås från båda kontona. Detta innebär att de ge behörighet för Microsoft- eller arbetskonto att komma åt data från sina LinkedIn-konto och för [sitt LinkedIn-konto för att komma åt data från sina Microsoft arbets- eller skolkonto](https://www.linkedin.com/help/linkedin/answer/84077).
* Ge behörighet LinkedIn data som kan nås av sina Microsoft fungerar och skolkonto.

Användare kan koppla från konton och ta bort åtkomstbehörighet för data när som helst och [användare kan styra hur deras egen LinkedIn-profil visas](https://www.linkedin.com/help/linkedin/answer/83), inklusive om deras profil visas i Microsoft-appar.

### <a name="linkedin-account-data"></a>LinkedIn-kontodata

När du ansluter dina Microsoft och LinkedIn-konton, tillåter du att LinkedIn tillhandahåller följande data till Microsoft:

* Profildata – omfattar LinkedIn-identitet, kontaktuppgifter och den information som du delar med andra på din [LinkedIn-profil](https://www.linkedin.com/help/linkedin/answer/15493).
* Intresserar data – omfattar intressen på LinkedIn, som personer och ämnen som du följer, kurser grupper och innehåll du vill och dela.
* Prenumerationer data – omfattar prenumerationer till LinkedIn-program och tjänster tillsammans med associerade data. 
* Anslutningsdata – omfattar din [LinkedIn-nätverk](https://www.linkedin.com/help/linkedin/answer/110) inklusive profiler och ytterligare kontaktinformation över 1-graders-anslutningar.

Data som öppnas från LinkedIn lagras inte permanent i Microsoft-tjänster. Mer information om Microsofts användning av personliga data finns i den [Microsoft Privacy Statement](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Arbets- eller skoldata konto för Microsoft

När du ansluter dina Microsoft och LinkedIn-konton kan tillåta du Microsoft att förse LinkedIn följande data:

* Profildata – innehåller information som ditt förnamn, senaste namn, profilfoto, e-postadress, manager och personer som du hanterar.
* Kalender data – omfattar möten i dina kalendrar, deras tider, platser och deltagarnas kontaktinformation. Information om mötet som agenda, innehåll eller uppfyller rubrik ingår inte i kalenderdata.
* Intresserar data – omfattar intressen som är associerade med ditt konto, baserat på din användning av Microsoft-tjänster som Cortana och Bing för företag.
* Prenumerationer data – omfattar prenumerationer som tillhandahålls av din organisation till Microsoft-appar och tjänster, till exempel Office 365.
* Kontaktar data – omfattar kontaktlistor i Outlook, Skype och andra Microsoft-Kontotjänster, inklusive kontaktuppgifter för personer som du ofta kommunicera eller samarbeta med. Kontakter kommer importeras med jämna mellanrum, lagras, och används av LinkedIn, till exempel att föreslå anslutningar att ordna kontakter och visa uppdateringar om kontakter.

Data som kan nås från Microsoft lagras inte permanent med LinkedIn, förutom kontakter. Microsoft Contacts lagras på LinkedIn tills användare tar bort dem. Läs mer om [tar bort importerade kontakter från LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Läs mer på Linkedins användning av personliga data, den [LinkedIn-sekretesspolicy](https://www.linkedin.com/legal/privacy-policy). För LinkedIn-tjänster, dataöverföring och lagring, data kan flöda från den Europeiska unionen till USA och tillbaka och din integritet skyddas som beskrivs i [Europeiska unionen dataöverföringar](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>Nästa steg

* [LinkedIn i Microsoft-program med ditt arbets- eller skolkonto konto](https://www.linkedin.com/help/linkedin/answer/84077)
