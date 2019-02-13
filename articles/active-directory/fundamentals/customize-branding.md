---
title: Lägg till företagsanpassning till din organisations inloggningssida – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du lägger till din organisations anpassning till inloggningssidan för Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: lizross
ms.reviewer: kexia
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 807643146c4f2821cecf60499ea1985f56e9d251
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168603"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Lägg till företagsanpassning till din organisations Azure Active Directory-inloggningssida
Använda organisationens logotyp och egna färgscheman för att tillhandahålla ett konsekvent utseende och känsla på webbsidorna för Azure Active Directory (AD Azure). Logga in sidorna visas när användare loggar in på din organisations webbaserade appar, till exempel Office 365, som använder Azure AD som identitetsprovider.

>[!Note]
>Lägga till anpassade anpassning kräver att du använder Azure Active Directory Premium 1, 2 för Premium eller Basic-utgåvor och ha en licens för Office 365. Läs mer om licensiering och utgåvor [registrera dig för Azure AD Premium](active-directory-get-started-premium.md).<br><br>Azure AD Premium och Basic är tillgängliga för kunder i Kina genom den globala instansen av Azure Active Directory. Azure AD Premium och Basic stöds inte för närvarande i den Azure-tjänst som drivs av 21Vianet i Kina. Om du vill ha mer information kontaktar du oss via [Azure Active Directory-forumet](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Anpassa din Azure AD-inloggningssida
Du kan anpassa dina Azure AD-inloggningssidor, som visas när användare loggar in till din organisations klientspecifik appar, till exempel [ *https://outlook.com/contoso.com* ](https://outlook.com/contoso.com), eller vid sändning av en domänvariabel i, till exempel [ *https://passwordreset.microsoftonline.com/?whr=contoso.com*](https://passwordreset.microsoftonline.com/?whr=contoso.com).

Ditt varumärke visas inte direkt när dina användare gå till platser, till exempel www.office.com. Användaren måste i stället logga in innan din anpassningsfunktionerna visas.

> [!NOTE]
> Alla företagsanpassning element är valfria. Exempel: Om du anger en banderollslogotyp med ingen bakgrundsbild visas på inloggningssidan din logotyp med en standard bakgrundsbild från målplatsen (till exempel Office 365).<br><br>Dessutom sprids inloggningssidan anpassning inte till personliga Microsoft-konton. Om dina användare eller företagsgäster loggar du in med ett personligt microsoftkonto, visas inte på inloggningssidan anpassning av din organisation.

### <a name="to-customize-your-branding"></a>Anpassa din företagsanpassning
1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**, och välj sedan **Varumärkesexponering**, och välj sedan **konfigurera**.

    ![Contoso - företagets företagsanpassning sida, konfigurera alternativet markerat](media/customize-branding/company-branding-configure-button.png)

3. På den **konfigurera företagsanpassning** anger allt eller något av följande information.

    >[!Important]
    >Alla anpassade avbildningar som du lägger till på den här sidan har bildstorlek (bildpunkter) och potentiellt filstorlek (KB), begränsningar. På grund av dessa begränsningar måste du mest sannolika du använder en fotoredigerare för att skapa avbildningar rätt storlek.

    - **Allmänna inställningar**

        ![Konfigurera företagsanpassning sidan med allmänna inställningar som har slutförts](media/customize-branding/configure-company-branding-general-settings.png)

        - **Språk.** Språket är konfigureras automatiskt som standard och kan inte ändras.
        
        - **Bakgrundsbild för inloggningssidan.** Välj en PNG- eller JPG-bildfil ska visas som bakgrund för din inloggningssidorna. 
        
            Bilden får inte vara större än 1 920 x 1 080 bildpunkter i storlek och måste ha en storlek på mindre än 300 KB.

        - **Banderoll logotyp.** Välj en .png eller .jpg version av din logotyp som ska visas på sidan logga in när användaren anger ett användarnamn och på den **Mina appar** portalsidan.
            
            Bilden får inte vara högre än 36 bildpunkter eller större än 245 bildpunkter. Vi rekommenderar att du använder en transparent bild eftersom bakgrunden inte kanske matchar din logotyp bakgrund. Vi rekommenderar att det inte att lägga till utfyllnad runt bilden eller det kan vara din logotyp ser liten.

        - **Användarnamn-tipset.** Ange tips-text som visas för användarna om de glömmer sitt lösenord. Den här texten måste vara Unicode, utan länkar eller kod och får inte överstiga 64 tecken. Om gäster loggar in på din app, föreslår vi att du inte lägger till den här tipset.

        - **Text på inloggningssidan.** Ange den text som visas längst ned på sidan logga in. Du kan använda den här texten för att ge ytterligare information, till exempel telefonnumret till supportavdelningen eller ett juridiskt meddelande. Den här texten måste vara Unicode och högst 256 tecken. Vi rekommenderar också att inte, inklusive länkar eller HTML-taggar.

    - **Avancerade inställningar**
            
        ![Konfigurera företagsanpassning sidan med avancerade inställningar har slutförts](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Bakgrundsfärg på inloggningssidan.** Ange hexadecimal färgen (till exempel vit är #FFFFFF) som ska visas i stället din bakgrund i långsam anslutning situationer. Vi rekommenderar att du använder den primära färgen i din banderollslogotyp eller i din organisation färg.

        - **Fyrkantig logotyp.** Välj en PNG (föredragen) eller .jpg-bild av organisationens logotyp som ska visas för användarna under konfigurationsprocessen för nya Windows 10 Enterprise-enheter. Den här avbildningen används endast för Windows-autentisering och visas endast på klienter som använder [Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) för distribution eller för lösenordsinmatning sidor i andra Windows-10 inträffar.
        
            Bilden får inte vara större än 240 x 240 bildpunkter i storlek och måste ha en storlek på mindre än 10 KB. Vi rekommenderar att du använder en transparent bild eftersom bakgrunden inte kanske matchar din logotyp bakgrund. Vi rekommenderar att det inte att lägga till utfyllnad runt bilden eller det kan vara din logotyp ser liten.
    
        - **Fyrkantig logotyp, mörkt tema.** Samma som fyrkantiga logotypen bilden ovan. Den här logotypbild sker fyrkantig logotyp när det används med en mörk bakgrund, till exempel med Windows 10 Azure AD ansluten skärmar under out-of-box experience (OOBE).  Om din logotyp ser bra ut på vit, mörkt blå och svarta bakgrunder, behöver du inte lägga till den här avbildningen. 
        
        - **Visa alternativet för att förbli inloggad.** Du kan välja att låta användarna förbli inloggad till Azure AD tills du uttryckligen utloggningen. Om du väljer **nr**, det här alternativet är dolt och användarna måste logga in varje gång som webbläsaren är stängd och igen.
        
            >[!Note]
            >För vissa funktioner i SharePoint Online och Office 2010 måste användarna kunna välja att fortsätta vara inloggade. Om du ställer in den här inställningen till **No** (Nej) kan eventuellt ytterligare och oväntade uppmaningar att logga in visas för dina användare.
   

3. När du har lagt till din företagsanpassning, väljer **spara**.

    Om den här processen skapar din första anpassade företagsanpassning konfiguration, blir standard för din klient. Om du har ytterligare konfigurationer, kommer du att kunna välja standardkonfigurationen.
    
    >[!Important]
    >Att lägga till mer företagets anpassning konfigurationer till din klient, måste du välja **nytt språk** på den **Contoso - företagsanpassning** sidan. Då öppnas det **konfigurera företagsanpassning** sidan där du kan följa samma steg som ovan.

## <a name="update-your-custom-branding"></a>Uppdatera ditt varumärke
När du har skapat ditt varumärke, kan du gå tillbaka och ändra vad du vill.

### <a name="to-edit-your-custom-branding"></a>Så här redigerar du ditt varumärke
1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**, och välj sedan **Varumärkesexponering**, och välj sedan **konfigurera**.

    ![Contoso - företagets anpassning sidan med standardkonfiguration som visas](media/customize-branding/company-branding-default-config.png)

3. På den **konfigurera företagsanpassning** sidan, lägga till, ta bort eller ändra några av information utifrån beskrivningarna i den [anpassa din Azure AD-inloggningssida](#customize-your-azure-ad-sign-in-page) i den här artikeln.

4. Välj **Spara**.

  Det kan ta upp till en timme innan ändringar som du har gjort på inloggningssidan visas.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Lägga till språkspecifik varumärkesanpassning till din katalog
Du kan inte ändra den ursprungliga konfigurationen språk från din standardspråk. Om du behöver en konfiguration på ett annat språk kan du skapa en ny konfiguration.

### <a name="to-add-a-language-specific-branding-configuration"></a>Att lägga till en språkspecifik företagsanpassning konfiguration

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**, och välj sedan **Varumärkesexponering**, och välj sedan **nytt språk**.

    ![Contoso - företagets företagsanpassning sida med den nya språkalternativet markerat](media/customize-branding/company-branding-new-language.png)

3. På den **konfigurera företagsanpassning** väljer du språk (till exempel franska) och Lägg sedan till din översatta information utifrån beskrivningarna i den [anpassa din Azure AD-inloggningssida](#customize-your-azure-ad-sign-in-page) i den här artikeln.

4. Välj **Spara**.

    Den **Contoso – företagsanpassning** sidan uppdateringar att visa den nya franska konfigurationen.

    ![Contoso - företagets anpassning sidan med standardkonfiguration som visas](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Lägg till ditt varumärke till sidor
Lägg till ditt varumärke till sidor genom att ändra i slutet av URL med texten, `?whr=yourdomainname`. Den här ändringen fungerar på flera sidor, inklusive konfigurationssidan Multi-Factor Authentication (MFA), konfigurationssidan Self-service lösenord återställer (SSPR) och tecknet på sidan.

**Exempel:**

**Ursprungliga URL: en:** https://aka.ms/MFASetup<br>
**Anpassad URL:** https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com

**Ursprungliga URL: en:** https://aka.ms/SSPR<br>
**Anpassad URL:** https://passwordreset.microsoftonline.com/?whr=contoso.com

 
