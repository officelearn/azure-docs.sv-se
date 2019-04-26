---
title: Aktivera tillbakaskrivning av lösenord för Azure AD
description: I den här självstudien aktiverar du tillbakaskrivning av lösenord för att få molninitierade lösenordsändringar tillbaka till en lokal AD som en del av Azure AD Connect.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: dabe0ad1a556ee43f3e6cae0e1cd421db5cde0fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60413975"
---
# <a name="tutorial-enabling-password-writeback"></a>Självstudier: Aktivera tillbakaskrivning av lösenord

I den här självstudien aktiverar du tillbakaskrivning av lösenord för din hybridmiljö. Tillbakaskrivning av lösenord används för att synkronisera lösenordsändringar i Azure Active Directory (Azure AD) tillbaka till din lokala Active Directory Domain Services-miljö (AD DS). Tillbakaskrivning av lösenord är aktiverat som en del av Azure AD Connect för att tillhandahålla en säker metod för att skicka lösenordsändringar tillbaka till en befintlig lokal katalog från Azure AD. Du hittar mer information om mekanismerna bakom tillbakaskrivning av lösenord i artikeln [Vad är Tillbakaskrivning av lösenord?](concept-sspr-writeback.md).

> [!div class="checklist"]
> * Aktivera alternativet för tillbakaskrivning av lösenord i Azure AD Connect
> * Aktivera alternativet för tillbakaskrivning av lösenord i självåterställning av lösenord (SSPR)

## <a name="prerequisites"></a>Nödvändiga komponenter

* Åtkomst till en aktiv Azure AD-klientorganisation med minst en utvärderingslicens som tilldelats.
* Ett konto med behörigheter som global administratör i Azure AD-klientorganisationen.
* En befintlig server som konfigurerats för att köra en aktuell version av [Azure AD Connect](../hybrid/how-to-connect-install-express.md).
* Tidigare självstudier om självåterställning av lösenord (SSPR) har slutförts.

## <a name="enable-password-writeback-option-in-azure-ad-connect"></a>Aktivera alternativet för tillbakaskrivning av lösenord i Azure AD Connect

Om du vill aktivera tillbakaskrivning av lösenord måste vi först aktivera funktionen från den server som du har installerat Azure AD Connect på.

1. För att konfigurera och aktivera tillbakaskrivning av lösenord loggar du in på din Azure AD Connect-server och startar konfigurationsguiden för **Azure AD Connect**.
2. På sidan **Välkommen** klickar du på **Konfigurera**.
3. På sidan **Ytterligare uppgifter** väljer du **Anpassa synkroniseringsalternativ** och väljer sedan **Nästa**.
4. På sidan **Anslut till Azure AD** anger du autentiseringsuppgifter för global administratör och väljer sedan **Nästa**.
5. På filtreringssidorna **Anslut kataloger** och **Domän/OU** väljer du **Nästa**.
6. På sidan **Valfria funktioner** markerar du rutan bredvid **Tillbakaskrivning av lösenord** och väljer **Nästa**.
7. På sidan **Klart att konfigurera** väljer du **Konfigurera** och väntar tills processen slutförts.
8. När du ser att konfigurationen är klar väljer du **Avsluta**.

## <a name="enable-password-writeback-option-in-sspr"></a>Aktivera alternativet för tillbakaskrivning av lösenord i SSPR

Att aktivera funktionen för tillbakaskrivning av lösenord i Azure AD Connect är bara hälften av lösningen. Att tillåta SSPR att använda tillbakaskrivning av lösenord gör den komplett så att användare som ändrar eller återställer sina lösenord kan ha det lösenordet angett lokalt också.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som global administratör.
2. Bläddra till **Azure Active Directory** klicka på **Återställning av lösenord** och välj sedan **Lokal integration**.
3. Ställ in alternativet för **Skriv tillbaka lösenord till din lokala katalog** till **Ja**.
4. Ställ in alternativet för **Låt användare låsa upp konton utan att återställa sina lösenord** till **Ja**.
5. Klicka på **Spara**

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat tillbakaskrivning av lösenord för självåterställning av lösenord. Lämna fönstret för Azure-portalen öppet och fortsätt till nästa självstudie för att konfigurera ytterligare inställningar för självåterställning av lösenord innan du distribuerar lösningen i ett pilotprojekt.

> [!div class="nextstepaction"]
> [Aktivera SSPR på Windows-inloggningsskärmen](tutorial-sspr-windows.md)
