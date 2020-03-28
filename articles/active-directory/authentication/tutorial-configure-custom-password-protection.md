---
title: Konfigurera anpassade azure Active Directory-lösenordsskyddslistor
description: I den här självstudien får du lära dig hur du konfigurerar anpassade listor för skydd av förbjudna lösenord för Azure Active Directory för att begränsa vanliga ord i din miljö.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4abb15462689470c87e9cf5ba8d5be8af2e45bfd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78252847"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Självstudiekurs: Konfigurera anpassade förbjudna lösenord för lösenordsskydd i Azure Active Directory

Användare skapar ofta lösenord som använder vanliga lokala ord som en skola, ett idrottslag eller en känd person. Dessa lösenord är lätta att gissa, och svaga mot ordlistebaserade attacker. För att framtvinga starka lösenord i din organisation kan du lägga till specifika strängar för att utvärdera och blockera den anpassade listan med anpassade förbjudna lösenord i Azure Active Directory (Azure AD). En begäran om lösenordsändring misslyckas om det finns en matchning i den anpassade listan över förbjudna lösenord.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Aktivera anpassade förbjudna lösenord
> * Lägga till poster i listan med anpassade förbjudna lösenord
> * Testa lösenordsändringar med ett förbjudet lösenord

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och privilegier:

* En aktiv Azure AD-klientorganisation med minst en aktiverad utvärderingslicens.
    * Om det behövs, [skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett konto med globala administratörsbehörighet. *global administrator*
* En användare som inte är administratör med ett lösenord som du *känner till,* till exempel testanvändare . Du testar en händelse för lösenordsändring med det här kontot i den här självstudien.
    * Om du behöver skapa en användare läser du [Snabbstart: Lägg till nya användare i Azure Active Directory](../add-users-azure-active-directory.md).
    * Om du vill testa åtgärden för lösenordsändring med ett förbjudet lösenord måste Azure [AD-klienten konfigureras för återställning av lösenord med självbetjäning](tutorial-enable-sspr.md).

## <a name="what-are-banned-password-lists"></a>Vad är förbjudna lösenordslistor?

Azure AD innehåller en global lista över förbjudna lösenord. Innehållet i den globala listan över förbjudna lösenord baseras inte på någon extern datakälla. I stället baseras den globala listan över förbjudna lösenord på de pågående resultaten av Azure AD-säkerhetstelemetri och analys. När en användare eller administratör försöker ändra eller återställa sina autentiseringsuppgifter kontrolleras det önskade lösenordet mot listan över förbjudna lösenord. Begäran om lösenordsändring misslyckas om det finns en matchning i den globala listan över förbjudna lösenord.

För att ge dig flexibilitet i vilka lösenord som är tillåtna kan du också definiera en anpassad lista över förbjudna lösenord. Den anpassade listan över förbjudna lösenord fungerar tillsammans med den globala listan över förbjudna lösenord för att framtvinga starka lösenord i din organisation. Organisationsspecifika termer kan läggas till i listan med anpassade förbjudna lösenord, till exempel följande exempel:

* Varumärken
* Produktnamn
* Platser, till exempel företagets huvudkontor
* Företagsspecifika interna termer
* Förkortningar som har specifik företagsmening

När en användare försöker återställa ett lösenord till något som finns med i den globala eller anpassade listan över förbjudna lösenord visas något av följande felmeddelanden:

* *Tyvärr innehåller ditt lösenord ett ord, en fras eller ett mönster som gör att ditt lösenord lätt kan gissas. Försök igen med ett annat lösenord.*
* *Tyvärr kan du inte använda lösenordet eftersom det innehåller ord eller tecken som har blockerats av administratören. Försök igen med ett annat lösenord.*

Den anpassade listan över förbjudna lösenord är begränsad till högst 1000 termer. Den är inte avsedd för att blockera stora listor med lösenord. För att maximera fördelarna med den anpassade listan över förbjudna lösenord, granska de [anpassade förbjudna lösenordslistebegreppen](concept-password-ban-bad.md#custom-banned-password-list) och [översikten över lösenordsutvärderingsalgoritmen](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="configure-custom-banned-passwords"></a>Konfigurera anpassade förbjudna lösenord

Låt oss aktivera den anpassade listan över förbjudna lösenord och lägga till några poster. Du kan lägga till ytterligare poster i den anpassade listan över förbjudna lösenord när som helst.

Så här aktiverar du listan med anpassade förbjudna lösenord och lägger till poster i den:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto med globala administratörsbehörigheter. *global administrator*
1. Sök efter och välj **Azure Active Directory**och välj sedan **Säkerhet** på menyn till vänster.
1. Under menyn **Hantera** väljer du **Autentiseringsmetoder**och sedan **Lösenordsskydd**.
1. Ange alternativet för **Framtvinga anpassad lista** till *Ja*.
1. Lägg till strängar i **listan Anpassade förbjudna lösenord**, en sträng per rad. Följande överväganden och begränsningar gäller för den anpassade listan över förbjudna lösenord:

    * Den anpassade listan över förbjudna lösenord kan innehålla upp till 1 000 termer.
    * Den anpassade förbjudna lösenordslistan är skiftlägesokänslig.
    * Den anpassade listan över förbjudna lösenord tar hänsyn till vanliga teckenersättning, till exempel "o" och "0", eller "a" och "@".
    * Den minsta stränglängden är fyra tecken och den maximala är 16 tecken.

    Ange dina egna anpassade lösenord som ska förbjudas, vilket visas i följande exempel

    [![](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png "Modify the custom banned password list under Authentication Methods in the Azure portal")](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Lämna alternativet för **Aktivera lösenordsskydd i Active Directory** i Windows Server till *Nej*.
1. Om du vill aktivera anpassade förbjudna lösenord och dina poster väljer du **Spara**.

Det kan ta flera timmar innan uppdateringar av listan med anpassade förbjudna lösenord tillämpas.

För en hybridmiljö kan du också [distribuera Azure AD-lösenordsskydd till en lokal miljö](howto-password-ban-bad-on-premises-deploy.md). Samma globala och anpassade förbjudna lösenordslistor används för både begäranden om lösenordsändring i molnet och på prem.

## <a name="test-custom-banned-password-list"></a>Testa anpassad lista över förbjudna lösenord

Om du vill se den anpassade listan över förbjudna lösenord i praktiken kan du försöka ändra lösenordet till en variant av en som du lade till i föregående avsnitt. När Azure AD försöker bearbeta lösenordsändringen matchas lösenordet mot en post i listan med anpassade förbjudna lösenord. Ett fel visas sedan för användaren.

> [!NOTE]
> Innan en användare kan återställa sitt lösenord i den webbaserade portalen måste Azure [AD-klienten konfigureras för återställning av lösenord med självbetjäning](tutorial-enable-sspr.md).

1. Gå till sidan Mina [https://myapps.microsoft.com](https://myapps.microsoft.com) **appar** på .
1. I det övre högra hörnet väljer du ditt namn och väljer sedan **Profil** på rullgardinsmenyn.

    ![Välj profil](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. Välj **Ändra lösenord**på sidan **Profil** .
1. På sidan **Ändra lösenord** anger du det befintliga (gamla) lösenordet. Ange och bekräfta ett nytt lösenord som finns i listan med anpassade förbjudna lösenord som du definierade i föregående avsnitt och välj sedan **Skicka**.
1. Ett felmeddelande returneras som anger att lösenordet har blockerats av administratören, vilket visas i följande exempel:

    ![Felmeddelande som visas när du försöker använda ett lösenord som ingår i den anpassade listan över förbjudna lösenord](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre vill använda den anpassade listan över förbjudna lösenord som du har konfigurerat som en del av den här självstudien gör du följande:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj **Azure Active Directory**och välj sedan **Säkerhet** på menyn till vänster.
1. Under menyn **Hantera** väljer du **Autentiseringsmetoder**och sedan **Lösenordsskydd**.
1. Ange alternativet för **Framtvinga anpassad lista** till *Nej*.
1. Om du vill uppdatera den anpassade förbjudna lösenordskonfigurationen väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat och konfigurerat anpassade lösenordsskyddslistor för Azure AD. Du har lärt dig att:

> [!div class="checklist"]
> * Aktivera anpassade förbjudna lösenord
> * Lägga till poster i listan med anpassade förbjudna lösenord
> * Testa lösenordsändringar med ett förbjudet lösenord

> [!div class="nextstepaction"]
> [Aktivera riskbaserad Azure Multi-Factor Authentication](tutorial-mfa-applications.md)
