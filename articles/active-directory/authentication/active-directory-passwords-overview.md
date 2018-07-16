---
title: Översikt över återställning av lösenord för självbetjäning av Azure AD | Microsoft Docs
description: Vilka kan Azure AD lösenord för självbetjäning återställning göra för din organisation?
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e28918fe9e26221738fe234ad41923c58a6ac260
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049029"
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Azure AD lösenordsåterställning via Självbetjäning för IT-proffs

Med Azure Active Directory (Azure AD) lösenordsåterställning via självbetjäning (SSPR), kan användare återställa sina lösenord på sina egna när och där det behövs. Administratörer kan styra hur en användare hämtar för lösenordsåterställning för på samma gång. Användarna behöver inte längre att anropa en supportavdelning om du bara för att återställa sina lösenord. Azure AD SSPR innehåller:

* **Ändring av lösenord via självbetjäning**: användaren känner till lösenordet, men vill ändra det till något nytt.
* **Lösenordsåterställning via självbetjäning**: användaren kan inte logga in och vill återställa sina lösenord med hjälp av en eller flera av följande verifierade autentiseringsmetoder:
   * Skicka ett textmeddelande till en verifierade mobiltelefon.
   * Ringa ett telefonsamtal till en verifierade mobil- eller office-telefon.
   * Skicka ett e-postmeddelande till ett verifierade sekundära e-postkonto.
   * Besvara sina säkerhetsfrågor.
* **Självbetjäning kontoupplåsning**: användaren kan inte logga in med sitt lösenord och är låst. Användaren vill låsa upp sitt konto utan inblandning av administratören med hjälp av sina autentiseringsmetoder.

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

## <a name="why-choose-azure-ad-sspr"></a>Varför ska jag välja Azure AD SSPR

Azure AD SSPR hjälper dig att:

* **Minska kostnaderna** som hjälp skrivbord assisterad lösenordsåterställning normalt konto för 20% av en IT-organisation supportsamtal. 
* **Förbättra användarupplevelser** och **minska help desk volym** genom att ge användarna kraften att lösa problem med sina egna lösenord. Det finns inget behov att anropa en supportavdelning eller öppna en supportbegäran.
* **Driv mobility** som användare kan återställa sina lösenord från oavsett var de finns.
* **Behålla kontrollen** av säkerhetsprincipen. Administratörer kan fortsätta att tillämpa principer som de har idag.

Om du är klar kan du kan komma igång med Azure AD SSPR med hjälp av vår [Snabbstartsguide vägledning](quickstart-sspr.md). Du kan snabbt ge användarna möjlighet att kunna återställa sina egna lösenord.

## <a name="azure-ad-sspr-availability"></a>Azure AD SSPR-tillgänglighet

Azure AD SSPR finns tre nivåer beroende på din prenumeration:

* **Azure AD kostnadsfri**: endast molnbaserad administratörer kan återställa sina egna lösenord.
* **Azure AD Basic** eller andra **betald prenumeration på Office 365**: endast molnbaserade användare kan återställa sina egna lösenord.
* **Azure AD Premium**: alla användare och administratörer, inklusive autentisering för enbart i molnet, federerade, direkt eller lösenordshash synkroniserade användare kan återställa sina egna lösenord. Lokala lösenord kräver tillbakaskrivning av lösenord ska aktiveras.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD-priser, serviceavtal, uppdateringar och -översikt

Mer information om licensiering, prissättning och framtida planer finns på följande webbplatser:

* [Priser för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Priser för Office 365](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Servicenivåavtal för Azure](https://azure.microsoft.com/support/legal/sla/)
* [Serviceavtal för Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Azure-uppdateringar](https://azure.microsoft.com/updates/)
* [Azure-översikt](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Nästa steg

* Är du redo att komma igång med SSPR? [Konfigurera Azure AD-självbetjäning lösenord återställa](quickstart-sspr.md).
* Planera distributionen SSPR till dina användare med hjälp av informationen som finns i vår [distributionen guiden](howto-sspr-deployment.md).
* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registrera för återställning av lösenord för självbetjäning](../user-help/active-directory-passwords-reset-register.md).
