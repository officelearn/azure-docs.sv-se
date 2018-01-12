---
title: "Översikt för återställning av lösenord för självbetjäning av Azure AD | Microsoft Docs"
description: "Vad kan Azure AD självbetjäning lösenord lösenordsåterställning gör för din organisation?"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 074a22d0d3d6f4218be431409dcb0e516d226335
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Azure AD lösenordsåterställning via Självbetjäning för IT-proffs

Med Azure Active Directory (AD Azure) lösenordsåterställning via självbetjäning (SSPR), kan användare återställa sina lösenord på sina egna när och var de behöver. Administratörer kan styra hur en användare hämtar lösenordsåterställning för på samma gång. Användarna behöver inte längre att anropa en supportavdelningen att återställa sina lösenord. Azure AD SSPR innehåller:

* **Självbetjäning lösenordsändring**: användaren känner till lösenordet, men vill ändra det till något nytt.
* **Självbetjäning för lösenordsåterställning**: användaren kan inte logga in och vill återställa sina lösenord med hjälp av en eller flera av följande validerade autentiseringsmetoder:
   * Skicka ett SMS till mobiltelefon validerade.
   * Ringa ett telefonsamtal till en validerade mobile- eller telefon.
   * Skicka ett e-postmeddelande till ett godkänt sekundära e-postkonto.
   * Besvara sina säkerhetsfrågor.
* **Självbetjäning kontoupplåsning**: användaren kan inte logga in med sitt lösenord och är låst. Användaren vill låsa upp sitt konto utan inblandning av administratören med hjälp av sina autentiseringsmetoder.

## <a name="why-choose-azure-ad-sspr"></a>Varför ska jag välja Azure AD SSPR

Azure AD SSPR hjälper dig att:

* **Minska kostnaderna** som hjälp supportavdelningen assisterad lösenordsåterställning vanligtvis konto för 20% av en IT-organisation supportsamtal. 
* **Förbättra upplevelse för slutanvändare** och **minska help desk volym** genom att ge slutanvändare behörighet att lösa problem med sina egna lösenord. Det finns inget behov av att anropa en supportavdelningen eller öppna en supportbegäran.
* **Enheten mobility** som användare kan återställa sina lösenord från var de än är.
* **Behålla kontrollen** av säkerhetsprincipen. Administratörer kan fortsätta att tillämpa principer som de har idag.

Om du är klar kan du kan komma igång med Azure AD SSPR med hjälp av vår [Snabbstartsguide vägledning](active-directory-passwords-getting-started.md). Du kan snabbt ge användarna möjlighet att återställa sina lösenord.

## <a name="azure-ad-sspr-availability"></a>Azure AD SSPR-tillgänglighet

Azure AD SSPR finns i tre nivåer beroende på din prenumeration:

* **Azure AD-lediga**: endast molnbaserad administratörer kan återställa sina lösenord.
* **Azure AD Basic** eller **betald prenumeration på Office 365**: endast molnbaserad användare kan återställa sina lösenord.
* **Azure AD Premium**: alla användare eller administratör, inklusive endast molnbaserad, federerad eller lösenord synkroniseras användare, kan återställa sina lösenord. Lokala lösenord kräver tillbakaskrivning av lösenord som ska aktiveras.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD prissättning, SLA, uppdateringar och -översikt

Mer information om licensiering priser och framtida planer finns på följande webbplatser:

* [Azure AD prisinformation](https://azure.microsoft.com/pricing/details/active-directory/)
* [Priser för Office 365](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Azure servicenivåavtal](https://azure.microsoft.com/support/legal/sla/)
* [Serviceavtal för Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Azure-uppdateringar](https://azure.microsoft.com/updates/)
* [Azure-översikt](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Nästa steg

* Är du redo att sätta igång med SSPR? [Konfigurera Azure AD-lösenordet för självbetjäning återställa](active-directory-passwords-getting-started.md).
* Planera en lyckad SSPR-distribution till dina användare med hjälp av de riktlinjer som finns i vår [distributionen guiden](active-directory-passwords-best-practices.md).
* [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md).
* [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md).
