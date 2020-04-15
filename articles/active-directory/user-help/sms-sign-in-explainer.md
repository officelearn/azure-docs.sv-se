---
title: Användarupplevelse för SMS-inloggning för telefonnummer (förhandsversion) – Azure AD
description: Läs mer om sms-inloggningsanvändarupplevelse för nya eller befintliga telefonnummer
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e5369247fc1e31bafb158e83a54bf8badf721d0d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378839"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Använda ditt telefonnummer som användarnamn (förhandsgranskning)

Om du registrerar en enhet får telefonen åtkomst till organisationens tjänster och organisationen får inte åtkomst till telefonen. Om du är administratör kan du hitta mer information i [Konfigurera och aktivera användare för SMS-baserad autentisering](../authentication/howto-authentication-sms-signin.md).

Om din organisation inte har gjort SMS-inloggning tillgänglig visas inget alternativ för den när du registrerar en telefon med ditt konto.  

## <a name="when-you-have-a-new-phone-number"></a>När du har ett nytt telefonnummer

Om du får en ny telefon eller ett nytt nummer och registrerar den hos en organisation för vilken SMS-inloggning är tillgänglig, upplever du den normala telefonregistreringsprocessen:

1. Välj **Lägg till metod**.
1. Välj **Telefon**.
1. Ange telefonnummer och välj **Text mig en kod**.
1. När du har angett koden väljer du **Nästa**.
1. Du kommer att se en uppmaning som säger "SMS verifierad. Telefonen har registrerats."

> [!Important]
> På grund av ett känt problem i förhandsgranskningen registrerar inte numret för SMS-inloggning under en kort tid. Du måste logga in med det tillagda numret och sedan följa anvisningarna för att registrera numret för SMS-inloggning.

### <a name="when-the-phone-number-is-in-use"></a>När telefonnumret används

Om du försöker använda ett telefonnummer som någon annan i organisationen använder visas följande meddelande:

![Felmeddelande när ditt telefonnummer redan används](media/sms-sign-in-explainer/sms-sign-in-error.png)

Kontakta administratören för att åtgärda problemet.

## <a name="when-you-have-an-existing-number"></a>När du har ett befintligt nummer

Om du redan använder ett telefonnummer med en organisation och använder ditt telefonnummer som användarnamn blir tillgängligt kan följande steg hjälpa dig att logga in.

1. När SMS-inloggning är tillgänglig visas en banderoll med en fråga om du vill aktivera telefonnumret för SMS-inloggning:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Knappen **Aktivera** visas också om du väljer smekt på telefonmetodpanelen:

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Om du vill aktivera metoden väljer du **Aktivera**. Du uppmanas att bekräfta åtgärden:

    ![Dialogrutan Bekräftelse för att aktivera SMS-inloggning för ett telefonnummer](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Välj **Aktivera**.

## <a name="when-you-remove-your-phone-number"></a>När du tar bort ditt telefonnummer

1. Om du vill ta bort telefonnumret markerar du knappen ta bort på panelen sms-inloggningstelefonmetod.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. När du uppmanas att bekräfta åtgärden väljer du **OK**.

Du kan inte ta bort ett telefonnummer som används som standardinställningsmetod. Om du vill ta bort numret måste du ändra standard inloggningsmetoden och sedan ta bort telefonnumret igen.
