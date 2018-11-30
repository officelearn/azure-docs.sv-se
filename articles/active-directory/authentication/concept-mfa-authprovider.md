---
title: När och hur du använder en Azure Multi-Factor Authentication-Provider?
description: När ska du använda en Autentiseringsleverantör med Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 41edbc621baede92c548f43ad2730b73f59756c0
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496107"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>När du ska använda en Azure Multi-Factor Authentication-Provider

Tvåstegsverifiering är tillgängligt som standard för globala administratörer med Azure Active Directory och Office 365-användare. Men om du vill dra nytta av [avancerade funktioner](howto-mfa-mfasettings.md) ska du köpa den fullständiga versionen av Azure Multi-Factor Authentication (MFA).

Azure Multi-Factor Authentication-Provider används för att dra nytta av funktionerna som tillhandahålls av Azure Multi-Factor Authentication för användare som **inte har licenser**.

Om du har licenser som omfattar alla användare i din organisation kan behöver du inte en Azure Multi-Factor Authentication-Provider. Skapa en Azure Multi-Factor Authentication-Provider endast om du måste också ange tvåstegsverifiering för vissa användare som inte har licenser.

> [!NOTE]
> Effektiva den 1 September 2018 nya auth-providers kan inte längre skapas. Befintliga autentiseringsleverantörer kan fortsätta att användas och uppdateras. Multifaktorautentisering fortsätter att vara tillgängliga som en funktion i Azure AD Premium-licenser.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Varningar relaterade till Azure MFA SDK

Observera SDK är inaktuell och kommer bara att fortsätta att fungera fram till den 14 November 2018. Efter det misslyckas anrop till SDK.

## <a name="what-is-an-mfa-provider"></a>Vad är en MFA-provider?

Det finns två typer av Authentication-providers och skillnaden gäller hur din Azure-prenumeration debiteras. Med alternativet per autentisering räknas antalet autentiseringar som utförs mot din klientorganisation under en månad. Det här alternativet är bäst om du har ett antal användare som bara autentiserar sig ibland. Med alternativet per användare räknas hur många användare i din klientorganisation som utför en tvåstegsverifiering under en månad. Det här alternativet är bäst om du har några användare med licenser men behöver utöka MFA till fler användare utanför licensgränsen.

## <a name="manage-your-mfa-provider"></a>Hantera din MFA-provider

Du kan inte ändra användningsmodellen (per aktiverad användare eller per autentisering) efter att en MFA-provider har skapats.

Om du har köpt tillräckligt med licenser för att täcka alla användare som är aktiverade för MFA, kan du ta bort MFA-providern helt och hållet.

Om MFA-providern inte är kopplad till en Azure AD-klientorganisation, eller om du kopplar den nya MFA-providern till en annan Azure AD-klientorganisation, överförs inte användarinställningar eller konfigurationsalternativ. Befintliga Azure MFA-servrar måste också aktiveras med hjälp av autentiseringsuppgifter för aktivering genereras genom MFA-providern. Återaktivera MFA-servrar för att koppla dem till MFA-providern påverkar inte telefonsamtal och SMS-autentisering, men aviseringar i mobilappen slutar att fungera för alla användare tills de återaktiverar mobilappen.

## <a name="next-steps"></a>Nästa steg

[Konfigurera inställningar för Multi-Factor Authentication](howto-mfa-mfasettings.md)
