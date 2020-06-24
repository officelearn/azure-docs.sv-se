---
title: Konfigurera "hållet inloggat?" begär Azure Active Directory konton
description: Lär dig mer om att hålla mig inloggad (KMSI avgör), vilket visar att förbli inloggad? Du kan konfigurera den i Azure Active Directory Portal och felsöka inloggnings problem.
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c4b253a3017f1a836ac989bb897065a6a7d8337
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84739954"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>Konfigurera "hållet inloggat?" prompt för Azure AD-konton

Vill du hålla mig inloggad (KMSI avgör) visar en **förbli inloggad?** fråga när en användare har loggat in. Om en användare svarar **Ja** på den här varningen, ger tjänsten Behåll mig inloggad en beständig [uppdateringstoken](../develop/developer-glossary.md#refresh-token). För federerade klienter visas meddelandet när användaren har autentiserats med den federerade identitets tjänsten.

Följande diagram visar användar inloggnings flödet för en hanterad klient organisation och en federerad klient och den nya uppmana mig att förbli inloggad-prompt. Det här flödet innehåller smart logik så att den **förblir inloggad?** alternativet visas inte om Machine Learning-systemet identifierar en högrisk inloggning eller inloggning från en delad enhet.

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="Diagram som visar användar inloggnings flödet för en hanterad eller federerad klient":::

> [!NOTE]
> Om du konfigurerar alternativet för att förbli inloggad måste du använda Azure Active Directory (Azure AD) Premium 1, Premium 2 eller Basic-versioner, eller ha en Office 365-licens. Mer information om licenser och versioner finns i [Registrera dig för Azure AD Premium](active-directory-get-started-premium.md).<br><br>Azure AD Premium-och Basic-versioner är tillgängliga för kunder i Kina med den globala instansen av Azure AD. Azure AD Premium och Basic stöds inte för närvarande i den Azure-tjänst som drivs av 21Vianet i Kina. Om du vill ha mer information kan du kontakta oss med [Azure AD-forumet](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="configure-kmsi"></a>Konfigurera KMSI avgör

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.
1. Välj **Azure Active Directory**, Välj **företags anpassning**och välj sedan **Konfigurera**.
1. I avsnittet **Avancerade inställningar** letar du reda på **alternativet för att förbli inloggad** .

   Med den här inställningen kan du välja om användarna ska förbli inloggade i Azure AD tills de uttryckligen loggar ut.
   * Om du väljer **Nej**är alternativet **förbli inloggad?** dolt när användaren har loggat in och användaren måste logga in varje gång webbläsaren stängs och öppnas igen.
   * Om du väljer **Ja**visas alternativet **förbli inloggad?** alternativet visas för användaren.

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="Skärm bild som visar alternativet Visa är inloggat":::

## <a name="troubleshoot-sign-in-issues"></a>Felsöka inloggnings problem

Om en användare inte agerar på den person som är **inloggad?** uppmanas du, som du ser i följande diagram, men avbryter inloggnings försöket, så visas en inloggnings logg post som anger avbrottet.

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="Visar du förbli inloggad? visas":::

Information om inloggnings felet är följande och markerat i exemplet.

* **Inloggnings fel kod**: 50140
* **Fel orsak**: det här felet inträffade på grund av "Håll mig inloggad" Avbryt när användaren loggade in.

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="Exempel på inloggnings loggen med alternativet Håll mig inloggad i avbrott":::

Du kan hindra användare från att se avbrottet genom att ställa in **alternativet för att förbli inloggade** på **Nej** i inställningarna för avancerad anpassning.

## <a name="next-steps"></a>Nästa steg

Läs om andra inställningar som påverkar timeout för inloggnings sessioner:

* Office 365 – [tids gräns för inaktiv session](https://docs.microsoft.com/sharepoint/sign-out-inactive-users)
* Villkorlig åtkomst för Azure AD – [användar inloggnings frekvens](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)
* Azure Portal – [tids gräns för inaktivitet på katalog nivå](https://docs.microsoft.com/azure/azure-portal/admin-timeout)
