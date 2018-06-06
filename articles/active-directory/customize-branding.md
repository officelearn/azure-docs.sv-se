---
title: Anpassa sidan logga in för din Azure Active Directory-klient | Microsoft Docs
description: Lär dig hur du lägger till en företagsanpassning till Azure inloggningssidan
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 05/23/2018
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 5573399b33da64df8b9bbf78a9583572cf148d7b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713855"
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Snabbstart: Lägga till företagsanpassning till din inloggningssidan i Azure AD
För att undvika förvirring vill många företag använda ett enhetligt utseende på alla webbplatser och tjänster som de hanterar. Azure Active Directory (AD Azure) tillhandahåller den här funktionen genom att låta dig anpassa utseendet på inloggningssidan med företagets logotyp och egna färgscheman. På inloggningssidan visas när du loggar in till webbaserade program, till exempel Office 365 som använder Azure AD som identitetsprovider. Du kan interagera med den här sidan om du vill ange dina autentiseringsuppgifter.

> [!NOTE]
> * Företagsanpassning är bara tillgängligt om du har köpt Premium eller Basic licens för Azure AD eller ha en licens för Office 365. Om du vill veta om en funktion som stöds av din licenstypen, kontrollera den [Azure Active Directory information prissättningssidan](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Azure AD Premium och Basic är tillgängliga för kunder i Kina genom den globala instansen av Azure Active Directory. Azure AD Premium och Basic stöds inte för närvarande i Azure tjänsten som drivs av 21Vianet i Kina. Mer information, kontakta oss på den [Azure Active Directory-forumet](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Anpassa inloggningssidan

<!--You can customize the following elements on the sign-in page: <attach image>-->

Företagets företagsanpassning anpassningar som visas på sidan för Azure AD när användare har åtkomst till en klient-specifika URL som [ *https://outlook.com/contoso.com* ](https://outlook.com/contoso.com).

När användare besöker www.office.com, visar inte på inloggningssidan alla företagsanpassning anpassningar eftersom användaren inte har ännu trätt autentiseringsuppgifter. När en användaren anger sitt användar-ID eller väljer en Användarikon, visar företagsinformation.

> [!NOTE]
> * Domännamnet måste visas som ”aktiv” i den **domäner** del av Azure-portalen där du har konfigurerat anpassningen. Mer information finns i [lägga till ett anpassat domännamn](add-custom-domain.md).
> * Varumärkesanpassningen på inloggningssidan förs inte över till inloggningssidan för personliga Microsoft-konton. Om dina anställda eller business gäster loggar du in med ett personligt microsoftkonto, avspeglar inte deras inloggningssidan anpassning av din organisation.


### <a name="banner-logo"></a>Banderollslogotyp 

Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Banderollslogotypen visas på inloggningssidan och åtkomst panelen sidor.<br>På sidan logga in visas logotypen efter användarnamnet har angetts. | Transparent JPG eller PNG<br>Maximal höjd: 36 px<br>Maximal bredd: 245 px | Använda organisationens logotyp här.<br>Använda en transparent bild. Inte förutsätt att bakgrunden vit.<br>Lägg inte till utfyllnad kring din logotyp i bilden eller din logotyp ser oproportionerligt små.

### <a name="username-hint"></a>Ledtråd för användarnamn   
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Det här alternativet anpassar instruktionstexten i fältet för användarnamn. | Unicode-text, upp till 64 tecken<br>Endast oformaterad text | Vi rekommenderar att du inte ställer in det här alternativet om du räknar gästanvändare utanför din organisation att logga in på din app.
            
### <a name="sign-in-page-text"></a>Text på inloggningssidan   
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Det här alternativet kan användas för att skicka ytterligare information, till exempel telefonnumret till supportavdelningen eller ett juridiskt meddelande visas längst ned i formuläret för inloggning. | Unicode-text, upp till 256 tecken<br>Endast oformaterad text (inga länkar eller HTML-taggar)    

### <a name="sign-in-page-image"></a>Bild av inloggningssidan  
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Det här alternativet visas i bakgrunden på inloggningssidan, är förankrad vid center visas utrymme och skalar och Beskär till fyller webbläsarfönstret.    <br>Den här bilden visas inte på smala skärmar, till exempel mobiltelefoner.<br>En svart mask med 0,55 opacitet tillämpas via den här avbildningen när sidan har lästs in. | JPG eller PNG<br>Bild dimensioner: 1 920 x 1 080 bildpunkter<br>Filstorlek: &lt; 300 KB | <br>Använda avbildningar där det inte finns ett starkt ämne fokus. Täckande inloggning formuläret visas mitt över den här avbildningen och kan omfatta någon del av avbildningen, beroende på storleken på webbläsarfönstret.<br>Behåll filstorlek liten för att säkerställa snabb inläsningstiden. 

### <a name="sign-in-page-background-color"></a>Bakgrundsfärg på inloggningssidan
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Den här färgen används i stället för bakgrundsbilden på anslutningar med låg bandbredd. | RGB-färg i hexadecimalt (exempel: #FFFFFF | Vi rekommenderar att du använder den primära färgen i banderollogotypen eller din organisation färg.

### <a name="square-logo-image"></a>Kvadratisk logotyp
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Den här bilden visas under installationen för nya Enterprise Windows 10-datorer. Det ger kontext till anställda när de registrerar sina nya arbetsdatorer. Bilden visas för klienter som använder [Windows AutoPilot](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97) att distribuera sina arbets-enheter och på lösenordsinmatning upplever sidor i andra Windows 10. | Transparent PNG (rekommenderas) eller JPG<br>Bild dimensioner: 240 x 240 px<br>Filstorlek: &lt; 10 KB | Använda organisationens logotyp här.<br> Använda en transparent bild.<br>Inte förutsätt att bakgrunden vit.<br>Lägg inte till utfyllnad till din logotyp i bilden eller din logotyp ser oproportionerligt små.

### <a name="show-option-to-remain-signed-in"></a>Visa alternativet för att förbli inloggad
Beskrivning | Villkor | Rekommendationer
------- | ------- | ----------
Azure AD-inloggning ger användaren kan förbli inloggad när de Stäng och öppna webbläsaren. Den här inställningen döljer det alternativet.<br>Ange till **nr** att dölja det här alternativet från användarna. | &nbsp; | Dölja alternativet påverkar inte sessioners livstid.<br>Vissa funktioner i SharePoint Online och Office 2010 är beroende av användare kan välja att förbli inloggad i. Om du väljer det här alternativet **nr**, dina användare kan se ytterligare och oväntat anvisningarna för att logga in.

> [!NOTE]
> Alla element är valfria. Till exempel om du anger en banderollslogotyp med ingen bakgrundsbild visas på inloggningssidan din logotyp och bakgrundsbild för målplatsen (till exempel Office 365).

## <a name="add-company-branding-to-your-directory"></a>Lägga till företagsanpassning till din katalog

1. Logga in på [Administrationscenter Azure AD](https://aad.portal.azure.com) med ett konto som är en global administratör för klienten.
2. Välj **Azure Active Directory** > **företagets anpassning** > **redigera**.
  
  ![Öppna anpassning](./media/customize-branding/navigation-to-branding.png)
3. Ändra de element som du vill anpassa. Alla element är valfria.
  
  ![Redigera anpassning](./media/customize-branding/edit-branding.png)
4. När du är klar väljer du **spara**.

Det kan ta upp till en timme innan ändringarna inloggningssidan företagsanpassning ska visas.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Lägga till språkspecifik företagsanpassning för din katalog

1. Logga in på [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **Azure Active Directory** > **företagets anpassning** > **nytt språk**.
  
  ![Lägga till språkspecifik företagsanpassning element](./media/customize-branding/add-language.png)
3. Ändra de element som du vill anpassa. Alla element är valfria.
4. När du är klar väljer du **spara**.

Det kan ta upp till en timme innan ändringarna inloggningssidan företagsanpassning ska visas.

## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du lägger till företagsanpassning till Azure AD-katalogen i den här snabbstarten. 

Du kan använda följande länk för att konfigurera din företagsanpassning i Azure AD från Azure-portalen.

> [!div class="nextstepaction"]
> [Konfigurera varumärkesexponering](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 