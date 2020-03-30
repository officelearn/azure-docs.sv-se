---
title: Lägga till branding på organisationens inloggningssida - Azure AD
description: Instruktioner om hur du lägger till organisationens varumärke på inloggningssidan för Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441fdd14cc2c734b6ce532f3ad1d30663b2f56c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049794"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Lägga till branding på organisationens inloggningssida för Azure Active Directory
Använd organisationens logotyp och anpassade färgscheman för att ge en konsekvent look-and-feel på dina Inloggningssidor för Azure Active Directory (Azure AD). Dina inloggningssidor visas när användare loggar in på organisationens webbaserade appar, till exempel Office 365, som använder Azure AD som identitetsleverantör.

>[!Note]
>Om du lägger till anpassade varumärken måste du använda Azure Active Directory Premium 1,Premium 2 eller Basic-utgåvor, eller för att ha en Office 365-licens. Mer information om licensiering och utgåvor finns i [Registrera dig för Azure AD Premium](active-directory-get-started-premium.md).<br><br>Azure AD Premium och Basic är tillgängliga för kunder i Kina genom den globala instansen av Azure Active Directory. Azure AD Premium och Basic stöds inte för närvarande i den Azure-tjänst som drivs av 21Vianet i Kina. Om du vill ha mer information kontaktar du oss via [Azure Active Directory-forumet](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Anpassa din Azure AD-inloggningssida
Du kan anpassa dina Azure AD-inloggningssidor, som visas när användare loggar in `https://outlook.com/contoso.com`på organisationens klientspecifika appar, `https://passwordreset.microsoftonline.com/?whr=contoso.com`till exempel , eller när de passerar en domänvariabel, till exempel .

Din anpassade varumärke visas inte omedelbart när användarna går till\.webbplatser som www office.com. I stället måste användaren logga in innan det anpassade varumärket visas. När användaren har loggat in kan det ta 15 minuter eller längre att visas. 

> [!NOTE]
> Alla varumärkeselement är valfria. Om du till exempel anger en bannerlogotyp utan bakgrundsbild visas logotypen med en standardbakgrundsbild från målplatsen (till exempel Office 365).<br><br>Dessutom överstÃ:er inte inskildring av sidprofiler till personliga Microsoft-konton. Om användarna eller företagsgästerna loggar in med ett personligt Microsoft-konto återspeglar inloggningssidan inte organisationens varumärke.

### <a name="to-customize-your-branding"></a>Så här anpassar du ditt varumärke
1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**och välj sedan **Företagsprofilering**och välj sedan **Konfigurera**.

    ![Contoso - Företagets varumärkessida, Konfigurera alternativet markerat](media/customize-branding/company-branding-configure-button.png)

3. Ange någon eller all följande information på sidan **Konfigurera företagsprofilering.**

    >[!Important]
    >Alla anpassade bilder som du lägger till på den här sidan har bildstorlek (pixlar) och potentiellt filstorlek (KB), begränsningar. På grund av dessa begränsningar måste du med största sannolikhet använda en fotoredigerare för att skapa bilder i rätt storlek.

    - **Allmänna inställningar**

        ![Konfigurera företagets varumärkessida med allmänna inställningar slutförda](media/customize-branding/configure-company-branding-general-settings.png)

        - **Språk.** Språket ställs automatiskt in som standard och kan inte ändras.
        
        - **Bakgrundsbild för inloggningssidan.** Välj en PNG- eller .jpg-bildfil som ska visas som bakgrund för dina inloggningssidor. 
        
            Bilden kan inte vara större än 1920x1080 pixlar i storlek och måste ha en filstorlek på mindre än 300 KB.

        - **Banner logotyp.** Välj en PNG- eller .jpg-version av logotypen som ska visas på inloggningssidan när användaren har angett ett användarnamn och på portalsidan **För Mina appar.**
            
            Bilden får inte vara högre än 60 pixlar eller bredare än 280 pixlar. Vi rekommenderar att du använder en genomskinlig bild eftersom bakgrunden kanske inte matchar din logotypbakgrund. Vi rekommenderar också att du inte lägger till utfyllnad runt bilden eller så kan det göra att logotypen ser liten ut.

        - **Tips för användarnamn.** Skriv tipstexten som visas för användarna om de glömmer sitt användarnamn. Den här texten måste vara Unicode, utan länkar eller kod, och får inte överstiga 64 tecken. Om gäster loggar in i din app föreslår vi att du inte lägger till den här ledtråden.

        - **In-text för inloggningssidan.** Skriv texten som visas längst ned på inloggningssidan. Du kan använda den här texten för att kommunicera ytterligare information, till exempel telefonnumret till din helpdesk eller ett juridiskt uttalande. Den här texten måste vara Unicode och inte överstiga 256 tecken. Vi föreslår också att du inte inkluderar länkar eller HTML-taggar.

    - **Avancerade inställningar**
            
        ![Konfigurera företagets varumärkessida, med avancerade inställningar slutförda](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Bakgrundsfärg för inloggningssidan.** Ange den hexadecimala färgen (till exempel vit #FFFFFF) som ska visas i stället för bakgrundsbilden i anslutningssituationer med låg bandbredd. Vi rekommenderar att du använder den primära färgen på din bannerlogotyp eller din organisationsfärg.

        - **Bild av square-logotypen.** Välj en PNG-bild (föredras) eller .jpg-avbildning av organisationens logotyp för att visas för användarna under installationsprocessen för nya Windows 10 Enterprise-enheter. Den här avbildningen används endast för Windows-autentisering och visas endast på klienter som använder [Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) för distribution eller för lösenordsinmatningssidor i andra Windows 10-upplevelser. I vissa fall kan det också visas i dialogrutan samtycke.
        
            Bilden får inte vara större än 240 x 240 pixlar och måste ha en filstorlek på mindre än 10 kB. Vi rekommenderar att du använder en genomskinlig bild eftersom bakgrunden kanske inte matchar din logotypbakgrund. Vi rekommenderar också att du inte lägger till utfyllnad runt bilden eller så kan det göra att logotypen ser liten ut.
    
        - **Square logo bild, mörkt tema.** Samma som den fyrkantiga logotypen bilden ovan. Den här logotypbilden placerar den fyrkantiga logotypbilden när den används med en mörk bakgrund, till exempel med Windows 10 Azure AD-anslutna skärmar under oobe-upplevelsen (out-of-box".This logo image takes the place of the square logo image when used with a dark background, such as with Windows 10 Azure AD joined screens during the out-of-box experience (OOBE).  Om logotypen ser bra ut på vita, mörkblå och svarta bakgrunder behöver du inte lägga till den här bilden. 
        
        - **Visa alternativet för att förbli inloggad.** Du kan välja att låta användarna förbli inloggade på Azure AD tills du uttryckligen loggar ut. Om du väljer **Nej**döljs det här alternativet och användarna måste logga in varje gång webbläsaren stängs och öppnas igen.
        
            >[!Note]
            >För vissa funktioner i SharePoint Online och Office 2010 måste användarna kunna välja att fortsätta vara inloggade. Om du ställer in den här inställningen till **No** (Nej) kan eventuellt ytterligare och oväntade uppmaningar att logga in visas för dina användare.
   

3. När du har lagt till ditt varumärke väljer du **Spara**.

    Om den här processen skapar din första anpassade varumärkeskonfiguration blir den standard för din klientorganisation. Om du har ytterligare konfigurationer kan du välja standardkonfiguration.
    
    >[!Important]
    >Om du vill lägga till fler företagsprofilkonfigurationer till din klientorganisation måste du välja **Nytt språk** på sidan **Contoso - Företagets varumärke.** Då öppnas sidan **Konfigurera företagsprofilering,** där du kan följa samma steg som ovan.

## <a name="update-your-custom-branding"></a>Uppdatera ditt anpassade varumärke
När du har skapat ditt anpassade varumärke kan du gå tillbaka och ändra vad du vill.

### <a name="to-edit-your-custom-branding"></a>Så här redigerar du ditt anpassade varumärke
1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**och välj sedan **Företagsprofilering**och välj sedan **Konfigurera**.

    ![Contoso - Företagets varumärkessida, med standardkonfigurationen visad](media/customize-branding/company-branding-default-config.png)

3. På sidan **Konfigurera företagsprofilering** lägger du till, tar bort eller ändrar någon av informationen, baserat på beskrivningarna i avsnittet [Anpassa din Azure AD-inloggningssida i](#customize-your-azure-ad-sign-in-page) den här artikeln.

4. Välj **Spara**.

   Det kan ta upp till en timme innan ändringar som du har gjort på inloggningssidan visas.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Lägga till språkspecifik varumärkesanpassning till din katalog
Du kan inte ändra den ursprungliga konfigurationens språk från standardspråket. Om du behöver en konfiguration på ett annat språk kan du skapa en ny konfiguration.

### <a name="to-add-a-language-specific-branding-configuration"></a>Så här lägger du till en språkspecifik varumärkeskonfiguration

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**och välj sedan **Företagsprofilering**och välj sedan **Nytt språk**.

    ![Contoso - Företagets varumärkessida, med nytt språkalternativ markerat](media/customize-branding/company-branding-new-language.png)

3. På sidan **Konfigurera företagsprofiler väljer** du ditt språk (till exempel franska) och lägger sedan till den översatta informationen, baserat på beskrivningarna i avsnittet [Anpassa din Azure AD-inloggningssida i](#customize-your-azure-ad-sign-in-page) den här artikeln.

4. Välj **Spara**.

    **Den Contoso - Företaget branding** sida uppdateringar för att visa din nya franska konfiguration.

    ![Contoso - Företagets varumärkessida, med standardkonfigurationen visad](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Lägga till din anpassade varumärke på sidor
Lägg till din anpassade branding på sidor genom att `?whr=yourdomainname`ändra slutet av webbadressen med texten. Den här ändringen fungerar på flera sidor, inklusive installationssidan för MFA -konfiguration (Multi-Factor Authentication), inställningssidan för självbetjäningslösenordsåterställning (SSPR) och inloggningssidan.

**Exempel:**

**Ursprunglig WEBBADRESS:**https://aka.ms/MFASetup<br>
**Anpassad URL:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**Ursprunglig WEBBADRESS:**https://aka.ms/SSPR<br>
**Anpassad URL:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`

 
