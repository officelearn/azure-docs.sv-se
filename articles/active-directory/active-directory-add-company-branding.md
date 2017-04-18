---
title: "Lägga till företagsinformation till inloggnings- och åtkomstpanelsidor i Azure Active Directory"
description: "Lär dig hur du anpassar inloggningssidan och åtkomstpanelsidan i Azure till ditt företag"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f74621b4-4ef0-4899-8c0e-0c20347a8c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/07/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 6d4fbfe97288fcb76628b45649b8b678152198a9
ms.lasthandoff: 04/07/2017


---
# <a name="add-company-branding-to-sign-in-and-access-panel-pages"></a>Anpassa inloggnings- och åtkomstpanelsidorna till ditt företag
Många organisationer vill ha ett konsekvent utseende och en enhetlig stil på de webbplatser och tjänster som de hanterar. I Azure Active Directory kan IT-experter anpassa utseendet på följande webbsidor med företagslogotyper och företagsbilder:

* **Inloggningssidan** – Det här är den sida som visas när anställda och företagsgäster loggar in i Office 365 eller andra program som använder Azure AD.
* **Åtkomstpanelsidan** – Åtkomstpanelen är en webbaserad portal där du kan visa och starta de molnbaserade program som din Azure AD-administratör har gett dig åtkomst till. Åtkomstpanelen finns på: [https://myapps.microsoft.com](https://myapps.microsoft.com).

I det här avsnittet lär du dig hur du kan anpassa inloggningssidan och åtkomstpanelsidan.

> [!NOTE]
> * Funktionen för varumärkesanpassning är bara tillgänglig om du har uppgraderat till Premium- eller Basic-versionen av Azure Active Directory, eller om du har en Office 365-licens. Mer information finns i avsnittet om Azure Active Directory-versioner.
> 
> * Azure Active Directory Premium och Basic är tillgängliga för kunder i Kina genom den globala instansen av Azure Active Directory. Azure Active Directory Premium och Basic stöds inte för närvarande i Microsoft Azure-tjänsten som drivs av 21Vianet i Kina. Om du vill ha mer information kontaktar du oss via Azure Active Directory-forumet.


## <a name="customizing-the-sign-in-page"></a>Anpassa inloggningssidan
Användarna interagerar vanligtvis med Azure AD-inloggningssidan när de försöker komma åt molnprogram och molntjänster som din organisation prenumererar på.

Om du har använt varumärkesanpassningar på inloggningssidan kan det ta upp till en timme innan ändringarna visas för slutanvändarna.

Varumärkesanpassningen visas på inloggningssidan för Azure AD när användarna ansluter till en klientspecifik URL, till exempel https://outlook.com/contoso.com.

Om användarna besöker en tjänst på en allmän URL, till exempel www.office.com, visas ingen varumärkningsanpassning på inloggningssidan eftersom systemet inte veta vem användaren är. Varumärkesanpassningen visas dock när användarna har angett sitt användar-ID eller valt en användarpanel.

> [!NOTE]
> * Domännamnet måste visas som ”Aktiv” i avsnittet **Active Directory** > **Katalog** > **Domäner** på den klassiska Azure-portalen där du har konfigurerat anpassningen.
> * Varumärkesanpassningen på inloggningssidan förs inte över till inloggningssidan för personliga Microsoft-konton. Om dina anställda eller företagsgäster loggar in med ett personligt Microsoft-konto återges inte organisationens varumärkesanpassning på deras inloggningssida.
>

Följande skärmbilder förklarar hur inloggningssidor anpassas.

### <a name="scenario-1-contoso-employee-goes-to-a-generic-app-url-for-example-wwwofficecom"></a>Scenario 1: Contosos anställda går till en allmän URL för en app (till exempel www.office.com)

I det här exemplet loggar en Contoso-användare in i ett mobilt program, eller i ett webbprogram med hjälp av en allmän URL. Bilden till vänster representerar alltid appen och interaktionsrutan till höger uppdateras för att visa Contoso-varumärkeselement när det är lämpligt.

![Office 365-inloggningssidan före och efter anpassning][1]

### <a name="scenario-2-contoso-employee-goes-to-contoso-app-thats-restricted-to-internal-users"></a>Scenario 2: Contosos anställda går till en Contoso-app som är begränsad till interna användare

I det här exemplet loggar en Contoso-användare in i ett internt program med en företagsspecifik URL. Bilden till vänster representerar företagets varumärke (Contoso). Interaktionsrutan till höger är låst till Contoso och hjälper anställda genom inloggningen.

![inloggningssida för begränsad app][2]

### <a name="scenario-3-contoso-employee-goes-to-a-contoso-app-thats-open-to-external-users"></a>Scenario 3: Contosos anställda går till en Contoso-app som är öppen för externa användare

I det här exemplet loggar användarna in i ett verksamhetsspecifikt program från Contoso. Användarna kan men måste inte vara Contoso-anställda. Bilden till vänster representerar resursägaren (Contoso), precis som i fall \#2 ovan. Men den här gången är interaktionsrutan till höger inte låst till Contoso, och externa användare är välkomna att logga in.

![inloggning med öppen åtkomst][3]

### <a name="scenario-4-fabrikam-business-guest-goes-to-contoso-app-thats-open-to-external-users"></a>Scenario 4: Fabrikam-företagsgäster går till en Contoso-app som är öppen för externa användare

I det här exemplet loggar en Contoso-användare in i ett internt program med en företagsspecifik URL. Bilden till vänster representerar företagets varumärke (Contoso). Interaktionsrutan till höger är låst till Contoso och hjälper anställda genom inloggningen.

![inloggning som en extern användare][4]


## <a name="what-elements-on-the-page-can-i-customize"></a>Vilka element på sidan kan jag anpassa?

Du kan anpassa följande element på inloggningssidan:

![][5]

| Sidelement | Plats på sidan |
|:--- | --- |
| Banderollslogotyp | Visas längst upp till höger på sidan. Ersätter applogotypen när användarens organisation har fastställts (vanligtvis när användarnamnet har angetts). |
| Bakgrundsbild | Visas som en helt utfallande bild till vänster på inloggningssidan. Ersätter appens bild i inloggningsscenarier med klientorganisationer (när användarna kommer åt ett program som publicerats av deras egen organisation eller en organisation där de är företagsgäster).<br>I anslutningar med låg bandbredd ersätts bakgrundsbilden med en bakgrundsfärg. Bilden visas inte på smala skärmar som till exempel telefoner.<br>Bakgrundsbilden beskärs när användare ändrar storlek på webbläsaren. När du utformar din bild bör du placera viktiga visuella element i det övre högra hörnet så att de inte beskärs. | 
| Kryssrutan ”Håll mig inloggad” | Visas under rutan **Lösenord**. |
| Text på inloggningssidan | Formaterad exempeltext som ska visas ovanför sidfoten. Den kan användas för att förmedla användbar information till användare, till exempel telefonnumret till supporten eller ett juridiskt meddelande. |

> [!NOTE]
> Alla element är valfria. Om du till exempel lägger till en banderollslogotyp men ingen bakgrundsbild så visas din logotyp och målwebbplatsens bild (dvs. bilden på en motorväg i Kalifornien för Office 365) på inloggningssidan.
>

På inloggningssidan kan användare markera kryssrutan **Håll mig inloggad** för att förbli inloggade när de stänger och sedan öppnar webbläsaren igen, och sessionens livslängd påverkas inte.

Inställningen för **Dölj KMSI** avgör om kryssrutan visas.

![Inställningen Dölj KMSI][6]

Om du vill dölja den här kryssrutan konfigurerar du den här inställningen till **Dold**.

> [!NOTE]
> För vissa funktioner i SharePoint Online och Office 2010 måste användarna kunna markera den här kryssrutan. Om du konfigurerar den här inställningen som dold kan eventuellt ytterligare och oväntade uppmaningar om att logga in visas för dina användare.
>
>

Du kan också lokalisera alla element på den här sidan. När du har konfigurerat en standarduppsättning med anpassningselement kan du konfigurera fler versioner för olika språk. Du kan också blanda och matcha olika element. Du kan till exempel:

* Skapa en förvald bild som passar alla kulturer och sedan skapa specifika versioner för engelska och franska. När något av dessa språk anges i webbläsaren visas den specifika bilden, medan standardbilden visas för andra språk.
* Konfigurera olika logotyper för din organisation (till exempel japanska och hebreiska versioner).

## <a name="access-panel-page-customization"></a>Anpassning av åtkomstpanelsidan
Åtkomstpanelsidan är i grunden en portalsida för snabb åtkomst till molnappar som du har beviljats åtkomst till av administratören. På den här sidan visas dina appar som klickbara programpaneler.

Följande skärmbild visar ett exempel på en åtkomstpanelsida efter anpassningen.

![][8]

## <a name="configure-your-directory-with-company-branding"></a>Konfigurera katalogen med företagsinformation
Du kan konfigurera en standarduppsättning med anpassningsbara element för varje katalog på den klassiska Azure-portalen. När standardinställningarna har sparats kan en administratör lägga till lokaliserade versioner av varje element för olika språk. Alla anpassningsbara element är valfria.

Om du till exempel konfigurerar en standardbanderollslogotyp men ingen stor bild så visas din logotyp längst upp till höger på inloggningssidan. Dock visas webbplatsens standardbild.

Föreställ dig följande konfiguration:

* En standardbanderollslogotyp och text på engelska på en inloggningssida
* Text på tyska på en språkspecifik inloggningssida

Om din språkinställning är tyska visas standardbanderollslogotypen men med den tyska texten.

Tekniskt sett kan du konfigurera olika uppsättningar för varje språk som stöds av Azure AD, men av prestanda- och underhållsskäl rekommenderar vi att du nöjer dig med några få varianter.
 
**Utför följande steg om du vill lägga till företagsprofilering för din katalog:**

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som administratör för den katalog som du vill anpassa.
2. Välj den katalog som du vill anpassa.
3. Klicka på **Konfigurera** i verktygsfältet högst upp.
4. Klicka på **Anpassa profilering**.
5. Ändra de element som du vill anpassa. Alla fält är valfria.
6. Klicka på **Spara**.

Det kan ta upp till en timme innan nya ändringar som du har gjort på inloggningssidan visas.

**Utför följande steg om du vill lägga till språkspecifik företagsanpassning:**

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som administratör för den katalog som du vill anpassa.
2. Välj den katalog som du vill anpassa.
fs3. Klicka på **Konfigurera** i verktygsfältet högst upp.
4. Klicka på **Anpassa profilering**.
5. Klicka på **Lägg till företagsanpassning för ett specifikt språk**.
6. Välj det språk som du vill anpassa logotypen för och klicka sedan på **Nästa**.
7. Redigera endast element som du vill konfigurera språkspecifika åsidosättningar för. Alla fält är valfria. Om ett fält lämnas tomt visas det anpassade standardvärdet i stället (eller Microsofts standardvärde om inget anpassat värde har konfigurerats).
8. Klicka på **Spara**.

**Utför följande steg om du vill ta bort en företagsanpassning från katalogen:**

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som administratör för den katalog som du vill anpassa.
2. Välj den katalog som du vill anpassa.
3. Klicka på **Konfigurera** i verktygsfältet högst upp.
4. Klicka på **Anpassa profilering**.
5. Välj **Redigera befintliga anpassningsinställningar** på sidan för att anpassa profilering och gå sedan till nästa sida.
6. Beroende på vilka element du vill ta bort gör du något av följande:

    a. Under **Banderollslogotyp** väljer du **Ta bort den uppladdade logotypen**.

    b. Under **Ikonlogotyp** väljer du **Ta bort den uppladdade logotypen**.

    c. Ta bort texten från alla textrutor.

    d. Klicka på **Next**.

    e. Ta bort texten från alla textrutor.
7. Klicka på **Spara** för att ta bort elementen.
8. Om det behövs klickar du på alternativet för att **anpassa profilering** igen och upprepar de här stegen för all språkspecifik anpassning som ska tas bort.
    Alla inställningar för företagsanpassning har tagits bort när du klickar på alternativet för att **anpassa profilering** och ser formuläret **Anpassa standardanpassning** utan några konfigurerade inställningar.


## <a name="customizable-elements"></a>Anpassningsbara element
Företagslogotyper används för inloggnings- och åtkomstpanelsidor, medan andra element endast används på inloggningssidan. Följande tabell innehåller information om de olika anpassningsbara elementen.

| Namn | Beskrivning | Villkor | Rekommendationer |
| --- | --- | --- | --- |
| Banderollslogotyp |Banderollslogotypen visas på inloggningssidan och på åtkomstpanelen. |<p>JPG eller PNG</p><p>60 × 280 bildpunkter</p><p>10 kB</p> |<p>Använd organisationens hela logotyp (inklusive grafik och text)</p><p>Se till att den inte är högre än 30 bildpunkter för att undvika att rullningslistor visas på mobila enheter</p><p>Se till att den inte är större än 4 kB</p><p>Använd en transparent PNG (förutsätt inte att inloggningssidan alltid har en vit bakgrund)</p> |
| Panellogotyp | Används inte för tillfället |<p>JPG eller PNG</p><p>120 × 120 bildpunkter</p><p>10 kB</p> |<p>Håll det enkelt (ingen liten text) eftersom bildens storlek kan minska till 50 % |
| </p> | | | |
| Användarnamnsetikett för inloggning | Används inte för tillfället |<p>Unicode-text, upp till 50 tecken</p><p>Endast oformaterad text (inga länkar eller HTML-taggar)</p> |<p>Håll det kort och enkelt</p><p>Fråga användarna hur de vanligtvis refererar till arbets- eller skolkontot som du ger dem.</p> |
| Formaterad exempeltext för inloggningssidan |Den här exempeltexten visas under formuläret på inloggningssidan och kan användas för att ge ytterligare instruktioner eller information om var användaren kan få hjälp och support. |<p>Unicode-text, upp till 256 tecken</p><p>Endast oformaterad text (inga länkar eller HTML-taggar)</p> |Se till att den inte är längre än 250 tecken (cirka 3 rader med text) |
| Bakgrundsbild för inloggningssidan | Stor bild som visas till vänster på inloggningssidan (till höger för höger-till-vänster-språk) när användare kommer åt klientspecifika URL:er. |<p>JPG eller PNG</p><p>1420 × 1200</p><p>500 kB</p> |<p>1420 × 1200 bildpunkter</p><p>Viktigt! Håll den så liten som möjligt, helst under 200 kB. Om den här bilden är för stor påverkas inloggningssidans prestanda om bilden inte cachelagras</p><p>Den här bilden beskärs nästan alltid för att passa olika skärmproportioner. Behåll de primära visuella elementen i det övre vänstra hörnet.</p> |
| Bakgrundsfärg på inloggningssidan | Den här färgen används i stället för bakgrundsbilden vid anslutningar med låg bandbredd. | Måste vara en RGB-färg i hexadecimalt format (exempel: \#FFFFFF) | Vi rekommenderar att du väljer den primära färgen i banderollslogotypen. |

## <a name="next-steps"></a>Nästa steg
* [Komma igång med Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Visa åtkomst- och användningsrapporterna](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/signin-page_before-customization.png
[2]: ./media/active-directory-add-company-branding/signin-page-restricted-app.png
[3]: ./media/active-directory-add-company-branding/signin-page-open-access.png
[4]: ./media/active-directory-add-company-branding/signin-page-external-guest.png
[5]: ./media/active-directory-add-company-branding/which-elements-can-i-customize.png
[6]: ./media/active-directory-add-company-branding/hide-kmsi.png
[8]: ./media/active-directory-add-company-branding/APBranding.png
[9]: ./media/active-directory-add-company-branding/hidekmsi.png

