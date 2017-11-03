---
title: Anpassa inloggningssidan i Azure Active Directory | Microsoft Docs
description: "Lär dig hur du lägger till en företagsanpassning till Azure inloggningssidan"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 41f4ceea4842303760c5b156e90bd9e0746a7825
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2017
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Snabbstart: Lägga till företagsanpassning till din inloggningssidan i Azure AD
För att undvika förvirring vill många företag använda ett enhetligt utseende på alla webbplatser och tjänster som de hanterar. Azure Active Directory (AD Azure) tillhandahåller den här funktionen genom att låta dig anpassa utseendet på inloggningssidan med företagets logotyp och egna färgscheman. På inloggningssidan är den sida som visas när du loggar in på Office 365 eller andra webbaserade program som använder Azure AD som identitetsprovider. Du kan interagera med den här sidan om du vill ange dina autentiseringsuppgifter.

> [!NOTE]
> * Företagsanpassning är bara tillgängligt om du har uppgraderat till Premium eller Basic-versionen av Azure AD eller ha en licens för Office 365. Om du vill veta om en funktion som stöds av din licenstypen, kontrollera den [Azure Active Directory information prissättningssidan](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Azure Active Directory Premium och Basic är tillgängliga för kunder i Kina genom den globala instansen av Azure Active Directory. Azure Active Directory Premium och Basic stöds inte för närvarande i Microsoft Azure-tjänsten som drivs av 21Vianet i Kina. Om du vill ha mer information kontaktar du oss via [Azure Active Directory-forumet](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Anpassa inloggningssidan

<!--You can customize the following elements on the sign-in page: <attach image>-->

Företagets företagsanpassning anpassningar som visas på sidan för Azure AD när användare har åtkomst till en klient-specifika URL som [ *https://outlook.com/contoso.com*](https://outlook.com/contoso.com).

När användare besöker en allmän URL, till exempel www.office.com, innehåller inte inloggningssidan företagsanpassning anpassningar eftersom systemet inte vet vem användaren är. Företagsanpassning visas när användare ange sitt användar-ID eller välj en Användarikon.

> [!NOTE]
> * Domännamnet måste visas som ”aktiv” i den **domäner** del av Azure-portalen där du har konfigurerat anpassningen. Mer information finns i [lägga till ett anpassat domännamn](add-custom-domain.md).
> * Varumärkesanpassningen på inloggningssidan förs inte över till inloggningssidan för personliga Microsoft-konton. Om dina anställda eller business gäster loggar du in med ett personligt microsoftkonto, avspeglar inte deras inloggningssidan anpassning av din organisation.


### <a name="banner-logo"></a>Banderollslogotyp 

Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Banderollslogotypen visas på inloggningssidan och åtkomst panelen sidor.<br>På sidan logga in visas här när användarens organisation bestäms, vanligtvis efter användarnamnet har angetts.  | Transparent JPG eller PNG<br>Maximal höjd: 36 px<br>Maximal bredd: 245 px | Använda organisationens logotyp här.<br>Använda en transparent bild. Inte förutsätt att bakgrunden vit.<br>Lägg inte till utfyllnad kring din logotyp i bilden eller din logotyp ser oproportionerligt små.

### <a name="username-hint"></a>Ledtråd för användarnamn   
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Detta anpassar instruktionstexten i fältet för användarnamn. | Unicode-text, upp till 64 tecken<br>Endast oformaterad text | Vi rekommenderar att du inte anger detta om du räknar gästanvändare utanför din organisation att logga in på din app.
            
### <a name="sign-in-page-text"></a>Text på inloggningssidan   
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Det visas längst ned i formuläret för inloggning och kan användas för att skicka ytterligare information, till exempel telefonnumret till supportavdelningen eller ett juridiskt meddelande. | Unicode-text, upp till 256 tecken<br>Endast oformaterad text (inga länkar eller HTML-taggar)   

### <a name="sign-in-page-image"></a>Bild av inloggningssidan  
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Detta kommer visas i bakgrunden på sidan logga in, är förankrad vid center visas utrymme och skala och Beskär så att den fyller webbläsarfönstret.    <br>Den här bilden visas inte på smala skärmar, till exempel mobiltelefoner.<br>En svart mask med 0,55 opacitet tillämpas via den här avbildningen av vår kod när sidan har lästs in. | JPG eller PNG<br>Bild dimensioner: 1 920 x 1 080 bildpunkter<br>Filstorlek: &lt; 300 KB | <br>Använda avbildningar där det inte finns ett starkt ämne fokus. Täckande inloggning formuläret visas mitt över den här avbildningen och kan omfatta någon del av avbildningen beroende på storleken på webbläsarfönstret.<br>Behåll filstorleken så mycket som möjligt så snabbt inläsningstiden. 

### <a name="background-color"></a>Bakgrundsfärg
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Den här färgen används i stället för bakgrundsbilden på anslutningar med låg bandbredd. | RGB-färg i hexadecimalt (exempel: #FFFFFF | Vi rekommenderar att du använder den primära färgen i banderollogotypen eller din organisation färg.

### <a name="show-option-to-remain-signed-in"></a>Visa alternativet förbli inloggad
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Azure AD inloggning kan användaren förblir inloggad när de stänga och öppna webbläsaren igen. Används för att dölja det alternativet.<br>Ange detta till ”Nej” om du vill dölja det här alternativet från användarna. | &nbsp; | Detta påverkar inte sessioners livstid.<br>Vissa funktioner i SharePoint Online och Office 2010 är beroende av användare kan välja att förbli inloggad i. Om du ställer in det dolda kan användarna se ytterligare och oväntat anvisningarna för att logga in.

> [!NOTE]
> Alla element är valfria. Till exempel om du anger en banderollslogotyp med ingen bakgrundsbild visas på inloggningssidan din logotyp och bakgrundsbild för målplatsen (till exempel Office 365).

## <a name="add-company-branding-to-your-directory"></a>Lägga till företagsanpassning till din katalog

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **fler tjänster**, ange **användare och grupper** i textrutan och välj sedan **RETUR**.

   ![Öppna användarhantering](./media/customize-branding/user-management.png)
3. På den **användare och grupper** bladet väljer **företagets företagsanpassning**.
4. På den **användare och grupper – företagets anpassning** bladet väljer den **redigera** kommando.

    ![Redigera anpassning](./media/customize-branding/edit-branding.png)
5. Ändra de element som du vill anpassa. Alla element är valfria.
6. Klicka på **Spara**.

Det kan ta upp till en timme innan ändringarna inloggningssidan företagsanpassning ska visas.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Lägga till språkspecifik företagsanpassning för din katalog

1. Logga in på [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **användare och grupper** i textrutan och välj sedan **RETUR**.

   ![Öppna användarhantering](./media/customize-branding/user-management.png)
3. På den **användare och grupper** bladet väljer **företagets företagsanpassning**.
4. På den **användare och grupper – företagets anpassning** bladet väljer den **lägga till språk** kommando.

    ![Lägga till språkspecifik företagsanpassning element](./media/customize-branding/add-language.png)
5. Ändra de element som du vill anpassa. Alla element är valfria.
6. Klicka på **Spara**.

Det kan ta upp till en timme innan ändringarna inloggningssidan företagsanpassning ska visas.

## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du lägger till företagsanpassning till Azure AD-katalogen i den här snabbstarten. 

Du kan använda följande länk för att konfigurera din företagsanpassning i Azure AD från Azure-portalen.

> [!div class="nextstepaction"]
> [Konfigurera varumärkesexponering](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 