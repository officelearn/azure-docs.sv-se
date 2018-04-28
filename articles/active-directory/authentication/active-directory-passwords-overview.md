---
title: Översikt för återställning av lösenord för självbetjäning av Azure AD | Microsoft Docs
description: Vad kan Azure AD självbetjäning lösenord lösenordsåterställning gör för din organisation?
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 7d1103e0ab21d2eac336a0e460156d47012b2a86
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
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

Om du är klar kan du kan komma igång med Azure AD SSPR med hjälp av vår [Snabbstartsguide vägledning](quickstart-sspr.md). Du kan snabbt ge användarna möjlighet att återställa sina lösenord.

## <a name="azure-ad-sspr-availability"></a>Azure AD SSPR-tillgänglighet

Azure AD SSPR finns i tre nivåer beroende på din prenumeration:

* **Azure AD-lediga**: endast molnbaserad administratörer kan återställa sina lösenord.
* **Azure AD Basic** eller **betald prenumeration på Office 365**: endast molnbaserad användare kan återställa sina lösenord.
* **Azure AD Premium**: alla användare eller administratör, inklusive endast molnbaserad federerade, genomströmning autentisering eller lösenord hash-synkroniserade användare kan återställa sina lösenord. Lokala lösenord kräver tillbakaskrivning av lösenord som ska aktiveras.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD prissättning, SLA, uppdateringar och -översikt

Mer information om licensiering priser och framtida planer finns på följande webbplatser:

* [Azure AD prisinformation](https://azure.microsoft.com/pricing/details/active-directory/)
* [Priser för Office 365](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Azure servicenivåavtal](https://azure.microsoft.com/support/legal/sla/)
* [Serviceavtal för Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Azure-uppdateringar](https://azure.microsoft.com/updates/)
* [Azure-översikt](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Nästa steg

* Är du redo att sätta igång med SSPR? [Konfigurera Azure AD-lösenordet för självbetjäning återställa](quickstart-sspr.md).
* Planera en lyckad SSPR-distribution till dina användare med hjälp av de riktlinjer som finns i vår [distributionen guiden](howto-sspr-deployment.md).
* [Återställ eller ändra ditt lösenord](../active-directory-passwords-update-your-own-password.md).
* [Registrera för återställning av lösenord för självbetjäning](../active-directory-passwords-reset-register.md).
