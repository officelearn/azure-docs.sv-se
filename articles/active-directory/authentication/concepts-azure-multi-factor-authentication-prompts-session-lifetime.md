---
title: Azure Multi-Factor Authentication-prompter och sessionens livs längd
description: Läs om den rekommenderade konfigurationen för omautentiserings-prompter med Azure Multi-Factor Authentication och hur sessionens livs längd används.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4834cccff11a70249140f49b498b8f7891787c72
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86169348"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-multi-factor-authentication"></a>Optimera omautentiserings-prompter och förstå sessionens livs längd för Azure Multi-Factor Authentication

Azure Active Directory (Azure AD) har flera inställningar som avgör hur ofta användare behöver autentiseras igen. Denna omautentisering kan vara en första faktor, till exempel lösen ord, FIDO eller lösen ords lös Microsoft Authenticator, eller för att utföra Multi-Factor Authentication (MFA). Du kan konfigurera de här inställningarna för autentisering efter behov för din egen miljö och den användar upplevelse som du vill använda.

Den här artikeln beskriver rekommenderade konfigurationer och hur olika inställningar fungerar och interagerar med varandra.

## <a name="recommended-settings"></a>Rekommenderade inställningar

För att ge användarna rätt balans mellan säkerhet och enkel användning genom att be dem att logga in med rätt frekvens rekommenderar vi följande konfigurationer:

* Om du har Azure AD Premium:
    * Aktivera enkel inloggning (SSO) i alla program med [hanterade enheter](../devices/overview.md) eller [sömlös SSO](../hybrid/how-to-connect-sso.md).
    * Om omautentisering krävs använder du en [princip för inloggnings frekvens för](../conditional-access/howto-conditional-access-session-lifetime.md)villkorlig åtkomst.
    * För användare som loggar in från icke-hanterade enheter eller scenarier med mobila enheter använder du villkorlig åtkomst för att aktivera beständiga webbläsar-och inloggnings frekvens principer.
* Om du har licenser för Office 365-appar eller den kostnads fria Azure AD-nivån:
    * Aktivera enkel inloggning (SSO) i alla program med [hanterade enheter](../devices/overview.md) eller [sömlös SSO](../hybrid/how-to-connect-sso.md).
    * Se till att alternativet *förbli inloggat* är aktiverat och hjälper användarna att godkänna det.

Vår forskning visar att de här inställningarna är rätt för de flesta klienter. Vissa kombinationer av dessa inställningar, t. ex. att *komma ihåg MFA* och *förbli*inbyggda, kan leda till att användarna autentiseras för ofta. Vanliga omautentiserings-prompter är felaktiga för användar produktivitet och kan göra dem mer sårbara för attacker.

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Konfigurations inställningar för Azure AD-sessionens livs längd

Om du vill optimera frekvensen för autentiserings-prompter för dina användare kan du konfigurera livs längd alternativen för Azure AD-sessionen. Förstå behoven hos ditt företag och dina användare och konfigurera inställningar som ger bästa möjliga balans för din miljö.

### <a name="evaluate-session-lifetime-policies"></a>Utvärdera livs längds principer för sessioner

Utan några livstids inställningar för sessioner finns det inga permanenta cookies i webbläsarsessionen. Varje gång en användare stänger och öppnar webbläsaren, får de en uppvarning om omautentisering. I Office-klienter är standard tids perioden ett rullande fönster på 90 dagar. Med den här standard-Office-konfigurationen, om användaren har återställt sitt lösen ord eller om det har varit inaktivt över 90 dagar, måste användaren autentiseras igen med alla nödvändiga faktorer (första och andra faktorn).

I Azure AD avgör den mest restriktiva principen för sessionens livs längd när användaren behöver autentiseras igen. Föreställ dig följande scenario:

* Du aktiverar *förblir inloggad*, som använder en beständig webb läsar-cookie och
* Du aktiverar även *kom ihåg MFA i 14 dagar*

I det här exempel scenariot måste användaren autentisera var 14: e dag. Den här funktionen följer den mest restriktiva principen, även om du inte vill att användaren ska *göra* en omautentisering i webbläsaren.

### <a name="managed-devices"></a>Hanterade enheter

Enheter som är anslutna till Azure AD med hjälp av Azure AD Join eller hybrid Azure AD Join tar emot ett [primärt uppdateringstoken (PRT)](../devices/concept-primary-refresh-token.md) för att använda enkel inloggning (SSO) i alla program. Med den här PRT kan en användare logga in en gång på enheten och göra det möjligt för IT-personalen att se till att standarder för säkerhet och efterlevnad är uppfyllda. Om en användare behöver uppmanas att logga in oftare på en ansluten enhet för vissa appar eller scenarier kan detta uppnås med [inloggnings frekvensen för villkorlig åtkomst](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="show-option-to-remain-signed-in"></a>Visa alternativet för att förbli inloggad

När en användare väljer **Ja** i alternativet är *inloggad?* under inloggningen anges en beständig cookie i webbläsaren. Den här permanenta cookien kommer att bli medlem i både den första och andra faktorn och gäller endast för autentiseringsbegäranden i webbläsaren.

![Skärm bild av exempel frågan om att vara inloggad](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

Om du har en licens för Azure AD Premium 1 rekommenderar vi att du använder en princip för villkorlig åtkomst för en *beständig webbläsarsession*. Den här principen skriver över *förblir inloggad?* inställningen och ger en bättre användar upplevelse. Om du inte har en licens för Azure AD Premium 1 rekommenderar vi att du aktiverar inställningen förbli inloggad för dina användare.

Mer information om hur du konfigurerar alternativet att låta användarna förbli inloggade finns i [Anpassa inloggnings sidan för Azure AD](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page).

### <a name="remember-multi-factor-authentication"></a>Kom ihåg Multi-Factor Authentication  

Med den här inställningen kan du konfigurera värden mellan 1-60 dagar och ange en beständig cookie i webbläsaren när en användare väljer alternativet **fråga inte igen om X dagar** vid inloggning.

![Skärm bild av exempel fråga om att godkänna en inloggningsbegäran](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

Medan den här inställningen minskar antalet autentiseringar i webbappar, ökar antalet autentiseringar för moderna autentiserings klienter, till exempel Office-klienter. De här klienterna visas normalt bara efter lösen ords återställning eller inaktivitet på 90 dagar. Det högsta värdet för att *komma ihåg MFA* är dock 60 dagar. När det används tillsammans med **fortfarande inloggade** eller villkorliga åtkomst principer kan det öka antalet autentiseringsbegäranden.

Om du använder *kom ihåg MFA* och har Azure AD Premium 1-licenser bör du överväga att migrera de här inställningarna till inloggnings frekvensen för villkorlig åtkomst. Annars kan du överväga att använda *Håll mig inloggad?* i stället.

Mer information finns i [kom ihåg Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

### <a name="authentication-session-management-with-conditional-access"></a>Hantering av autentisering med villkorlig åtkomst

Med **inloggnings frekvens** kan administratören välja inloggnings frekvens som gäller för både den första och andra faktorn i både klienten och webbläsaren. Vi rekommenderar att du använder de här inställningarna tillsammans med att använda hanterade enheter i scenarier när du behöver begränsa autentiseringsprocessen, till exempel för kritiska företags program.

Med **beständig** webbläsarsession kan användarna vara inloggade när de har stängt och öppnat sina webbläsarfönster igen. Precis som inställningen *förbli inloggad* anger den en beständig cookie i webbläsaren. Men eftersom den har kon figurer ATS av administratören kräver den inte att användaren väljer **Ja** i den *förbli inloggad?* alternativet ger en bättre användar upplevelse. Om du använder alternativet *förbli inloggad?* rekommenderar vi att du aktiverar den **permanenta webbläsarsessionen** i stället.

Mer information. Se [Konfigurera hantering av autentisering med villkorlig åtkomst](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="configurable-token-lifetimes"></a>Konfigurerbara livstider för token

Med den här inställningen kan du konfigurera livs längd för token som utfärdas av Azure Active Directory. Den här principen ersätts av *hantering av autentisering med villkorlig åtkomst*. Om du använder *konfigurerbara livs längder för token* i dag rekommenderar vi att du startar migreringen till principerna för villkorlig åtkomst.

## <a name="review-your-tenant-configuration"></a>Granska klient konfigurationen  

Nu när du förstår hur olika inställningar fungerar och den rekommenderade konfigurationen är det dags att kontrol lera konfigurationen för klient organisationen och göra ändringar i enlighet med detta:

Utför följande steg för att konfigurera eller granska alternativet för *fortsatt inloggad* :

1. I Azure AD-portalen söker du efter och väljer *Azure Active Directory*.
1. Välj **företags anpassning**, välj sedan Visa alternativ för varje språk version **som är inloggad**.
1. Välj *Ja*och välj sedan **Spara**.

Utför följande steg för att komma ihåg inställningarna för Multi-Factor Authentication:

1. I Azure AD-portalen söker du efter och väljer *Azure Active Directory*.
1. Välj **säkerhet**och sedan **MFA**.
1. Under **Konfigurera**väljer du **ytterligare Cloud-baserade MFA-inställningar**.
1. På sidan *Inställningar för Multi-Factor Authentication Service* bläddrar du för att **komma ihåg inställningarna för Multi-Factor Authentication**. Inaktivera inställningen genom att avmarkera kryss rutan.

Utför följande steg för att konfigurera principer för villkorlig åtkomst för inloggnings frekvens och beständig webbläsarsession:

1. I Azure AD-portalen söker du efter och väljer *Azure Active Directory*.
1. Välj **säkerhet**och sedan **villkorlig åtkomst**.
1. Konfigurera en princip med hjälp av rekommenderade alternativ för hantering av sessioner som beskrivs i den här artikeln.

Om du vill granska livs längd för token [använder du Azure AD PowerShell för att skicka frågor till alla Azure AD-principer](../develop/active-directory-configurable-token-lifetimes.md#prerequisites). Inaktivera eventuella principer som du har på plats.

Om mer än en inställning har Aktiver ATS i din klient organisation rekommenderar vi att du uppdaterar dina inställningar baserat på vilken licensiering som är tillgänglig för dig. Om du till exempel har Azure AD Premium-licenser bör du bara använda principen för villkorlig åtkomst för *inloggnings frekvens* och *beständig webbläsarsession*. Om du har Office 365-appar eller Azure AD-kostnads fria licenser bör du använda konfigurationen *är fortfarande inloggad?*

Om du har aktiverat konfigurerbara livs längder för token kommer den här funktionen att tas bort snart. Planera en migrering till en princip för villkorlig åtkomst.

I följande tabell sammanfattas rekommendationerna baserat på licenser:

|              | Azure AD Free-och Office 365-appar | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **Enkel inloggning**                      | [Azure AD Join](../devices/concept-azure-ad-join.md) eller [hybrid Azure AD Join](../devices/concept-azure-ad-join-hybrid.md)eller [sömlös enkel inloggning](../hybrid/how-to-connect-sso.md) för ohanterade enheter. | Azure Active Directory-anslutning<br />Hybrid Azure Active Directory-anslutning |
| **Inställningar för omautentisering** | Förbli inloggad                  | Använd principer för villkorlig åtkomst för inloggnings frekvens och beständig webbläsarsession |

## <a name="next-steps"></a>Nästa steg

Kom igång genom att slutföra självstudien för att [skydda användar inloggnings händelser med Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md) eller [Använd risk identifieringar för användar inloggningar för att utlösa Azure Multi-Factor Authentication](tutorial-risk-based-sspr-mfa.md).
