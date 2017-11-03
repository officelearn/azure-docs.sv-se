---
title: "Översikt för återställning av lösenord för självbetjäning av Azure AD | Microsoft Docs"
description: "Vad kan Azure AD självbetjäning lösenord lösenordsåterställning gör för din organisation?"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: f577ee95c34f9bc3065c026bc943d30622b9d658
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Azure AD lösenordsåterställning via Självbetjäning för IT-proffs

Azure Active Directory (AD Azure) lösenordsåterställning via självbetjäning (SSPR) kan användare återställa sina lösenord på egen hand när och var de ska, samtidigt som administratörer kontroll över hur de hämtar för lösenordsåterställning. Användare behöver inte längre ringa supporten att återställa sina lösenord.

* **Självbetjäning lösenordsändring** – användaren känner till lösenordet, men vill ändra det till något nytt.
* **Självbetjäning för lösenordsåterställning** – användare kan inte logga in och vill återställa sina lösenord med hjälp av en eller flera av följande validerade autentiseringsmetoder.
   * SMS till mobiltelefon validerad
   * Telefonsamtal till en validerade mobile- eller office-telefon
   * E-post till en validerade sekundära e-postkonto
   * Svar på sina säkerhetsfrågor
* **Självbetjäning kontoupplåsning** – användaren kan inte logga in med sitt lösenord har låsts ute och vill låsa upp sitt konto utan inblandning av administratören använder sina autentiseringsmetoder.

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Varför ska jag välja Azure AD Självbetjäning för lösenordsåterställning

* **Minska kostnaderna** som supportavdelningen assisterad lösenordsåterställning vanligtvis konto för 20% av en IT-organisation supportsamtal. 
* **Förbättra upplevelse för slutanvändare** och **minska supportavdelningen volym** genom att ge slutanvändare behörighet att lösa lösenordsproblem med sina egna på en gång utan anropar en supportavdelning eller öppna en supportbegäran.
* **Enheten mobility** som användare kan återställa sina lösenord från var de än är.
* **Behålla kontrollen** av säkerhetsprincip. Administratörer kan fortsätta att tillämpa principer som de har idag.

Om du är klar kan du komma igång med Azure AD SSPR med hjälp av vår [Snabbstartsguide vägledning](active-directory-passwords-getting-started.md) och snabbt har användarna återställa sina lösenord.

## <a name="azure-ad-self-service-password-reset-availability"></a>Tillgänglighet för återställning av lösenord för självbetjäning av Azure AD

Azure AD självbetjäning lösenordet för återställning finns i tre nivåer beroende på din prenumeration.

* **Azure AD-lediga** – endast molnbaserad administratörer kan återställa sina lösenord.
* **Azure AD Basic** eller **betald prenumeration på Office 365** – endast molnbaserad användare kan återställa sina lösenord.
* **Azure AD Premium** – alla användare eller administratör, inklusive endast molnbaserad, federerad eller lösenord synkroniseras användare, kan återställa sina lösenord. Lokala lösenord kräver tillbakaskrivning av lösenord som ska aktiveras.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD prissättning, SLA, uppdateringar och -översikt

Mer information om licensiering, priser och framtida planer finns på platser som följer.

* [**Prisinformation för Azure AD**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Priser för Office 365**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Azure servicenivåavtal**](https://azure.microsoft.com/support/legal/sla/)
* [**Serviceavtal för Microsoft Online Services**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Azure-uppdateringar**](https://azure.microsoft.com/updates/)
* [**Azure-översikt**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Nästa steg

* Är du redo att sätta igång med SSPR? [Konfigurera Azure AD self service lösenordsåterställning](active-directory-passwords-getting-started.md).
* Planera en lyckad SSPR-distribution till dina användare med hjälp av vägledningen finns i vår [ **distributionen guiden**](active-directory-passwords-best-practices.md).
* [Återställa eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md).
* [Registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md).