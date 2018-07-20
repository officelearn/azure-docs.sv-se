---
title: 'Anvisningar: Konfigurera tillbakaskrivning av lösenord för Azure AD SSPR'
description: Använd Azure AD och Azure AD Connect för tillbakaskrivning av lösenord till en lokal katalog
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e613ff742096077fe1765d4b855b6c7d409cc228
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158955"
---
# <a name="how-to-configure-password-writeback"></a>Anvisningar: Konfigurera tillbakaskrivning av lösenord

Vi rekommenderar att du använder funktionen för automatisk uppdatering av [Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md) när du använder tillbakaskrivning av lösenord.

Följande steg förutsätter att du redan har konfigurerat Azure AD Connect i din miljö med hjälp av den [Express](./../connect/active-directory-aadconnect-get-started-express.md) eller [anpassade](./../connect/active-directory-aadconnect-get-started-custom.md) inställningar.

1. För att konfigurera och aktivera tillbakaskrivning av lösenord, logga in på din Azure AD Connect-servern och starta den **Azure AD Connect** konfigurationsguiden.
2. På den **Välkommen** väljer **konfigurera**.
3. På den **ytterligare uppgifter** väljer **anpassa synkroniseringsalternativ**, och välj sedan **nästa**.
4. På den **Anslut till Azure AD** , ange autentiseringsuppgift för global administratör och välj sedan **nästa**.
5. På den **Anslut kataloger** och **domän-/ OU** filtrering sidor, Välj **nästa**.
6. På den **valfria funktioner** markerar du rutan bredvid **tillbakaskrivning av lösenord** och välj **nästa**.
   ![Aktivera tillbakaskrivning av lösenord i Azure AD Connect][Writeback]
7. På den **redo att konfigurera** väljer **konfigurera** och vänta tills processen slutförts.
8. När du ser konfigurationen är klar väljer du **avsluta**.

Vanliga uppgifter för felsökning för tillbakaskrivning av lösenord finns i avsnittet [felsöka tillbakaskrivning av lösenord](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) i vår felsökningsartikel.

## <a name="active-directory-permissions"></a>Active Directory-behörigheter

Konton som angetts i Azure AD Connect-verktyget måste ha följande ange om du vill ska vara i omfånget för SSPR:

* **Återställ lösenord** 
* **Ändra lösenord** 
* **Skrivbehörighet** på `lockoutTime`
* **Skrivbehörighet** på `pwdLastSet`
* **Utökade rättigheter för** på antingen:
   * Rotobjektet av *varje domän* i den skogen
   * Användaren organisationsenheter (OU) som du vill ska vara i omfånget för SSPR

Om du inte vet vilket konto beskrivs kontot refererar öppnar Azure Active Directory Connect Konfigurationsgränssnittet och väljer den **visa aktuell konfiguration** alternativet. Det konto som du behöver lägga till behörigheter till anges under **synkroniserade kataloger**.

Om du anger dessa behörigheter kan MA-tjänstkontot för varje skog hantera lösenord för användarkonton i skogen. 

> [!IMPORTANT]
> Om du inte tilldelar dessa behörigheter, sedan, visas även om tillbakaskrivning av verkar vara korrekt konfigurerade, användare felmeddelanden när de försöker hantera sina lokala lösenord från molnet.
>

> [!NOTE]
> Det kan ta upp till ungefär en timme för behörigheterna att replikeras till alla objekt i din katalog.
>

Om du vill konfigurera lämpliga behörigheter för tillbakaskrivning av lösenord ska ske, gör du följande:

1. Öppna Active Directory-användare och datorer med ett konto som har administratörsbehörighet för lämplig domän.
2. Från den **visa** menyn, se till att **avancerade funktioner** är påslagen.
3. I den vänstra rutan högerklickar du på det objekt som representerar roten för domänen och välj **egenskaper** > **Security** > **Avancerat**.
4. Från den **behörigheter** fliken **Lägg till**.
5. Välj det konto som behörigheter som används för (från Azure AD Connect-konfiguration).
6. I den **gäller** listrutan, väljer **underordnade** objekt.
7. Under **behörigheter**, markerar kryssrutorna för följande:
    * **Återställ lösenord**
    * **Ändra lösenord**
    * **Skriva lockoutTime**
    * **Skriva pwdLastSet**
8. Välj **Använd/OK** att tillämpa ändringarna och stänga alla öppna dialogrutor.

## <a name="next-steps"></a>Nästa steg

[Vad är tillbakaskrivning av lösenord?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Aktivera tillbakaskrivning av lösenord i Azure AD Connect"
