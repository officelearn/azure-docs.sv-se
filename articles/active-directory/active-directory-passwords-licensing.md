---
title: 'Licensiering: Azure AD SSPR | Microsoft Docs'
description: "Azure AD Självbetjäning för lösenordsåterställning licenskrav"
services: active-directory
keywords: 
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
ms.openlocfilehash: db849cd1e9da634064f79fbc041098542580ad02
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Återställ Licensieringskrav för självbetjäning Azure AD-lösenord

För återställning av Azure AD-lösenord ska fungera, du **måste ha minst en licens för i din organisation**. Vi behöver inte använda per användare-licensiering om återställning av lösenord. Du måste tilldela licenser till användare som använder premiumfunktioner för att bibehålla kompatibilitet med ditt Microsoft-licensavtalet.

* **Endast molnbaserad användare** -Office 365 (O365) någon betald SKU eller Azure AD Basic
* **Molnet** och/eller **lokala användare** -Azure AD Premium P1 eller P2, Enterprise Mobility + Security (EMS) eller säker produktiva Enterprise (a)

## <a name="licenses-required-for-password-writeback"></a>Licenser som krävs för tillbakaskrivning av lösenord

Om du vill använda tillbakaskrivning av lösenord, måste du ha en av de följande licenser som tilldelats i din klient.

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5

> [!WARNING]
> Fristående Office 365-licensiering planer **har inte stöd för tillbakaskrivning av lösenord** och kräver en av de föregående planerna för den här funktionen ska fungera.

Ytterligare licenser uppgifter om kostnaderna kan hittas på följande sidor

* [Platsen för Azure Active Directory-priser](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory-funktioner och funktioner](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Aktivera grupp eller användarbaserade licensiering

Azure AD nu stöder gruppbaserade licensiering vilket gör att administratörer för att tilldela licenser gruppvis till en grupp användare, snarare än tilldela dem en i taget. [Tilldela kontrollerar och lösa problem med licenser](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en användare kan tilldelas en licens, måste administratören ange egenskapen ”användningsplats” för användaren. Tilldelning av licenser kan göras under användare > Profil > Inställningar i Azure-portalen. **När du använder gruppen licenstilldelning ärver alla användare utan en användningsplats anges platsen för katalogen.**

## <a name="next-steps"></a>Nästa steg

* [Hur jag för att slutföra en lyckad distribution av SSPR?](active-directory-passwords-best-practices.md)
* [Återställa eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md).
* [Registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md).
* [Vilka data används av SSPR och vilka data bör du fylla för dina användare?](active-directory-passwords-data.md)
* [Vilka autentiseringsmetoder som är tillgängliga för användarna?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Vad är policyalternativen med SSPR?](active-directory-passwords-policy.md)
* [Vad är tillbakaskrivning av lösenord och varför jag är intresserad av den?](active-directory-passwords-writeback.md)
* [Hur rapporterar på aktivitet i SSPR?](active-directory-passwords-reporting.md)
* [Vad är alla alternativ i SSPR och vad de betyder?](active-directory-passwords-how-it-works.md)
* [Jag tror att något har brutits. Hur felsöker SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte var motsvarar någon annan](active-directory-passwords-faq.md)
