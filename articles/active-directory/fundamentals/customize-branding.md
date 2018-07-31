---
title: Anpassa inloggningssidan för Azure AD-klientorganisationen | Microsoft Docs
description: Lär dig hur du varumärkesanpassar inloggningssidan i Azure till ditt företag
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 07/20/2018
ms.author: lizross
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 7804d6b0d4a100997fb545e678458424dac6ceed
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227288"
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Snabbstart: Varumärkesanpassa inloggningssidan i Azure AD till ditt företag
För att undvika förvirring vill många företag använda ett enhetligt utseende på alla webbplatser och tjänster som de hanterar. Azure Active Directory (Azure AD) erbjuder den här möjligheten genom att låta dig anpassa utseendet på inloggningssidan med företagets logotyp och egna färgscheman. Inloggningssidan visas när du loggar in på webbaserade program, till exempel Office 365, som använder Azure AD som identitetsprovider. Du kan interagera med den här sidan för att ange dina autentiseringsuppgifter.

> [!NOTE]
> * Funktionen för varumärkesanpassning är bara tillgänglig om du har köpt Premium- eller Basic-licensen för Azure Active Directory, eller om du har en Office 365-licens. Om du vill veta om en viss funktion stöds av din licenstyp kan du titta på sidan med [prisinformation för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Azure AD Premium och Basic är tillgängliga för kunder i Kina genom den globala instansen av Azure Active Directory. Azure AD Premium och Basic stöds inte för närvarande i den Azure-tjänst som drivs av 21Vianet i Kina. Om du vill ha mer information kontaktar du oss via [Azure Active Directory-forumet](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Anpassa inloggningssidan

<!--You can customize the following elements on the sign-in page: <attach image>-->

Varumärkesanpassningar visas på inloggningssidan för Azure AD när användare kommer åt en klientorganisationsspecifik URL som [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com) eller skickar domänvariabeln i URL:en som [*https://passwordreset.microsoftonline.com/?whr=contoso.com*](https://passwordreset.microsoftonline.com/?whr=contoso.com)

När användare till exempel besöker www.office.com visar inte inloggningssidan några varumärkesanpassningar eftersom användaren ännu inte har angett några autentiseringsuppgifter. När en användaren har angett sitt användar-ID eller väljer en användarpanel visas varumärkesanpassningen.

> [!NOTE]
> * Domännamnet måste visas som ”Active” (Aktivt) i **Domains**-delen (Domäner) av den Azure-portal där du har konfigurerat varumärkesanpassningen. Mer information finns i [Lägg till ett anpassat domännamn](add-custom-domain.md).
> * Varumärkesanpassningen på inloggningssidan förs inte över till inloggningssidan för personliga Microsoft-konton. Om dina anställda eller företagsgäster loggar in med ett personligt Microsoft-konto återges inte organisationens varumärkesanpassning på deras inloggningssida.


### <a name="banner-logo"></a>Banderollslogotyp 

Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Banderollslogotypen visas på sidorna för inloggning och åtkomstpanel.<br>På inloggningssidan visas logotypen när användarnamnet har angetts. | Transparent JPG eller PNG<br>Maximal höjd: 36 pixlar<br>Maximal bredd: 245 pixlar | Använd organisationens logotyp här.<br>Använd en transparent bild. Förutsätt inte att bakgrunden är vit.<br>Lägg inte till utfyllnad kring logotypen i bilden; annars ser logotypen oproportionerligt liten ut.

### <a name="username-hint"></a>Ledtråd för användarnamn   
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Det här alternativet anpassar tipstexten i fältet för användarnamn. | Unicode-text, upp till 64 tecken<br>Endast oformaterad text | Om du förväntar dig att gästanvändare utanför organisationen loggar in på din app rekommenderar vi att du inte konfigurerar det här alternativet.
            
### <a name="sign-in-page-text"></a>Text på inloggningssidan   
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Det här alternativet visas längst ned i inloggningsformuläret och kan användas för att ge ytterligare information, till exempel telefonnumret till supportavdelningen eller ett juridiskt meddelande. | Unicode-text, upp till 256 tecken<br>Endast oformaterad text (inga länkar eller HTML-taggar)    

### <a name="sign-in-page-image"></a>Bild på inloggningssidan  
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Det här alternativet visas i bakgrunden på inloggningssidan, är fäst på mitten av det visningsbara området och skalar och beskärs för att fylla webbläsarfönstret.    <br>På smala skärmar såsom mobiltelefoner visas inte den här bilden.<br>En svart mask med 0,55 opacitet tillämpas över den här bilden när sidan läses in. | JPG eller PNG<br>Bilddimensioner: 1920 x 1080 pixlar<br>Filstorlek: &lt; 300 KB | <br>Använd bilder i de fall då det inte finns något starkt fokus på ett ämne. Det täckande inloggningsformuläret visas över mitten av den här bilden och kan täcka vilken del av bilden som helst, beroende på storleken på webbläsarfönstret.<br>Håll filstorleken liten för att ge snabba inläsningstider. 

### <a name="sign-in-page-background-color"></a>Bakgrundsfärg på inloggningssidan
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Den här färgen används i stället för bakgrundsbilden för anslutningar med låg bandbredd. | RGB-färg i hexadecimal (exempel: #FFFFFF | Vi föreslår att du använder den primära färgen från banderollslogotypen eller organisationens färg.

### <a name="square-logo-image"></a>Fyrkantig logotypbild
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Den här bilden visas under installationen för nya Enterprise Windows 10-datorer. Den ger kontext till de anställda när de konfigurerar sina nya arbetsdatorer. Bilden visas för klientorganisationer som använder [Windows AutoPilot](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97) för att distribuera sina arbetsenheter samt på lösenordsinmatningssidor i andra Windows 10-miljöer. | Transparent PNG (föredras) eller JPG<br>Bilddimensioner: 240 x 240 pixlar<br>Filstorlek: &lt; 10 KB | Använd organisationens logotyp här.<br> Använd en transparent bild.<br>Förutsätt inte att bakgrunden är vit.<br>Lägg inte till utfyllnad till logotypen i bilden; annars ser logotypen oproportionerligt liten ut.

### <a name="show-option-to-remain-signed-in"></a>Visa alternativet för att förbli inloggad
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Azure AD-inloggningen ger användaren alternativet att förbli inloggad när webbläsaren stängs och öppnas igen. Den här inställningen döljer det alternativet.<br>Ställ in på **No** (Nej) för att dölja det här alternativet från användarna. | &nbsp; | Om du döljer det här alternativet påverkas inte sessionens varaktighet.<br>För vissa funktioner i SharePoint Online och Office 2010 måste användarna kunna välja att fortsätta vara inloggade. Om du ställer in den här inställningen till **No** (Nej) kan eventuellt ytterligare och oväntade uppmaningar att logga in visas för dina användare.

> [!NOTE]
> Alla element är valfria. Om du till exempel anger en banderollslogotyp utan bakgrundsbild visar inloggningssidan din logotyp och bakgrundsbilden för målplatsen (till exempel Office 365).

## <a name="add-company-branding-to-your-directory"></a>Lägga till varumärkesanpassning till din katalog

1. Logga in på [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för klientorganisationen.
2. Välj **Azure Active Directory** > **Company branding (Varumärkesanpassning)** > **Redigera**.
  
  ![Öppna varumärkesanpassning](./media/customize-branding/navigation-to-branding.png)
3. Ändra de element som du vill anpassa. Alla element är valfria.
  
  ![Redigera varumärkesanpassning](./media/customize-branding/edit-branding.png)
4. När du är klar väljer du **Spara**.

Det kan ta upp till en timme innan ändringar som du har gjort på inloggningssidan visas.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Lägga till språkspecifik varumärkesanpassning till din katalog

1. Logga in på [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **Azure Active Directory** > **Company branding (Varumärkesanpassning)** > **Nytt språk**.
  
  ![Lägga till språkspecifika varumärkesanpassningselement](./media/customize-branding/add-language.png)
3. Ändra de element som du vill anpassa. Alla element är valfria.
4. När du är klar väljer du **Spara**.

Det kan ta upp till en timme innan ändringar som du har gjort på inloggningssidan visas.

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du lärt dig hur du lägger till varumärkesanpassning för din Azure AD-katalog. 

Du kan använda följande länk för att konfigurera varumärkesanpassningen i Azure AD från Azure-portalen.

> [!div class="nextstepaction"]
> [Konfigurera varumärkesexponering](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 