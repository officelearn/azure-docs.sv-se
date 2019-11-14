---
title: Anvisningar för att konfigurera tillbakaskrivning av lösen ord för Azure AD SSPR-Azure Active Directory
description: Använd Azure AD och Azure AD Connect för att ångra lösen ord till en lokal katalog
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e12fe38ba69f6ac8f27130e01baff0c358aa409
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021761"
---
# <a name="how-to-configure-password-writeback"></a>Anvisningar: Konfigurera tillbakaskrivning av lösen ord

Följande steg förutsätter att du redan har konfigurerat Azure AD Connect i din miljö med hjälp av inställningarna för [Express](../hybrid/how-to-connect-install-express.md) eller [anpassad](../hybrid/how-to-connect-install-custom.md) .

1. För att konfigurera och aktivera tillbakaskrivning av lösenord loggar du in på din Azure AD Connect-server och startar konfigurationsguiden för **Azure AD Connect**.
2. På sidan **Välkommen** klickar du på **Konfigurera**.
3. På sidan **Ytterligare uppgifter** väljer du **Anpassa synkroniseringsalternativ** och väljer sedan **Nästa**.
4. På sidan **Anslut till Azure AD** anger du autentiseringsuppgifter för global administratör och väljer sedan **Nästa**.
5. På filtreringssidorna **Anslut kataloger** och **Domän/OU** väljer du **Nästa**.
6. På sidan **Valfria funktioner** markerar du rutan bredvid **Tillbakaskrivning av lösenord** och väljer **Nästa**.
   ![Aktivera tillbakaskrivning av lösen ord i Azure AD Connect][Writeback]
7. På sidan **Klart att konfigurera** väljer du **Konfigurera** och väntar tills processen slutförts.
8. När du ser att konfigurationen är klar väljer du **Avsluta**.

Vanliga fel söknings uppgifter om tillbakaskrivning av lösen ord finns i avsnittet [Felsöka tillbakaskrivning av lösen ord](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) i vår fel söknings artikel.

> [!WARNING]
> Tillbakaskrivning av lösen ord slutar att fungera för kunder som använder Azure AD Connect versioner 1.0.8641.0 och äldre när [Azure Access Control Service (ACS) tas ur bruk den 7 November 2018](../develop/active-directory-acs-migration.md). Azure AD Connect-versioner 1.0.8641.0 och äldre kommer inte längre att tillåta tillbakaskrivning av lösen ord vid den tidpunkten eftersom de är beroende av ACS för den funktionen.
>
> För att undvika avbrott i tjänsten, uppgradera från en tidigare version av Azure AD Connect till en nyare version, se artikeln [Azure AD Connect: uppgradera från en tidigare version till den senaste](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="licensing-requirements-for-password-writeback"></a>Licensierings krav för tillbakaskrivning av lösen ord

**Lösen ords återställning via självbetjäning/ändring/upplåsning med lokal tillbakaskrivning är en Premium funktion i Azure AD**. Mer information om licensiering finns på webbplatsen för [Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory/).

Om du vill använda tillbakaskrivning av lösen ord måste du ha en av följande licenser tilldelade till klienten:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 eller a3
* Enterprise Mobility + Security E5 eller A5
* Microsoft 365 E3 eller a3
* Microsoft 365 E5 eller A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Fristående Office 365-licens planer *stöder inte "självbetjäning för återställning av lösen ord/ändra/Lås upp med lokal tillbakaskrivning"* och kräver att du har en av de föregående planerna för att den här funktionen ska fungera.
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Active Directory behörigheter och komplexitets principer för lokala lösen ord 

Det konto som anges i Azure AD Connect-verktyget måste ha följande objekt angivna om du vill ha en omfattning för SSPR:

* **Återställ lösenord** 
* **Ändra lösenord** 
* **Skriv behörigheter** för `lockoutTime`
* **Skriv behörigheter** för `pwdLastSet`
* **Utökade rättigheter** på något av följande:
   * Rotobjektet för *varje domän* i skogen
   * De användar organisationsenheter (OU) som du vill ska ingå i omfånget för SSPR

Om du inte är säker på vilket konto som det beskrivna kontot refererar till, öppnar du konfigurations gränssnittet för Azure Active Directory Connect och väljer alternativet **Visa aktuell konfiguration** . Kontot som du behöver lägga till behörighet för visas under **synkroniserade kataloger**.

Om du ställer in dessa behörigheter kan kontot för MA-tjänsten för varje skog hantera lösen ord för användar kontots räkning i skogen. 

> [!IMPORTANT]
> Om du inte väljer att tilldela dessa behörigheter, kommer användarna att upptäcka fel när de försöker hantera lokala lösen ord från molnet även om tillbakaskrivning verkar vara korrekt konfigurerad.
>

> [!NOTE]
> Det kan ta upp till en timme eller mer för dessa behörigheter att replikera till alla objekt i din katalog.
>

Utför följande steg för att ställa in rätt behörigheter för tillbakaskrivning av lösen ord:

1. Öppna Active Directory användare och datorer med ett konto som har rätt behörighet för domän administration.
2. I menyn **Visa** ser du till att **avancerade funktioner** är aktiverade.
3. Högerklicka på det objekt som representerar roten för domänen i den vänstra panelen och välj **egenskaper** > **säkerhets** > **Avancerat**.
4. På fliken **behörigheter** väljer du **Lägg till**.
5. Välj det konto som behörigheter tillämpas på (från Azure AD Connect-installationen).
6. I list rutan **gäller väljer du** **underordnade användar objekt**.
7. Under **behörigheter**väljer du rutorna för följande alternativ:
    * **Ändra lösenord**
    * **Återställ lösenord**
8. Under **Egenskaper**väljer du rutorna för följande alternativ:
    * **Skriv lockoutTime**
    * **Skriv pwdLastSet**
9. Välj **tillämpa/OK** för att tillämpa ändringarna och avsluta alla öppna dialog rutor.

Eftersom auktoritets källan är lokalt gäller principerna för lösen ords komplexitet från samma anslutna data källa. Kontrol lera att du har ändrat de befintliga grup principerna för "lägsta ålder för lösen ord". Grup principen bör inte vara inställd på 1, vilket innebär att lösen ordet ska vara minst en dag gammal innan den kan uppdateras. Du måste kontrol lera att den är inställd på 0. Du hittar de här inställningarna i `gpmc.msc` under **dator konfiguration > principer > Windows-inställningar > säkerhets inställningar > konto principer**. Kör `gpupdate /force` för att säkerställa att ändringen börjar gälla. 

## <a name="next-steps"></a>Nästa steg

[Vad är tillbakaskrivning av lösen ord?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Aktivera tillbakaskrivning av lösen ord i Azure AD Connect"
