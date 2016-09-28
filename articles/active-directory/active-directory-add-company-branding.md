<properties
    pageTitle="Anpassa inloggnings- och åtkomstpanelsidorna till ditt företag"
    description="Lär dig hur du anpassar inloggningssidan och åtkomstpanelsidan i Azure till ditt företag"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/13/2016"
    ms.author="MarkVi"/>


# Anpassa inloggnings- och åtkomstpanelsidorna till ditt företag


För att undvika förvirring vill många företag använda ett enhetligt utseende på alla webbplatser och tjänster som de hanterar. Azure Active Directory erbjuder den här möjligheten genom att låta dig anpassa utseendet på följande webbsidor med företagets logotyp och egna färgscheman:

- **Inloggningssidan** – Det här är den sida som visas när du loggar in i Office 365 eller andra webbaserade program som använder Azure AD som identitetsprovider. Du kan interagera med den här sidan antingen under identifieringen av startsfären eller för att ange dina autentiseringsuppgifter. Identifieringen av startsfären gör att systemet kan omdirigera externa användare till deras lokala STS (till exempel AD FS).

- **Åtkomstpanelsidan** – Åtkomstpanelen är en webbaserad portal där du kan visa och starta de molnbaserade program som din Azure AD-administratör har gett dig åtkomst till. Använd följande URL för att komma åt åtkomstpanelen: [https://myapps.microsoft.com](https://myapps.microsoft.com).

I det här avsnittet lär du dig hur du kan anpassa inloggningssidan och åtkomstpanelsidan.

> [AZURE.NOTE]
>
- Funktionen för företagsanpassning är en funktion som bara är tillgänglig om du har uppgraderat till Premium- eller Basic-versionen av Azure Active Directory. Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md).
- Azure Active Directory Premium och Basic är tillgängliga för kunder i Kina genom den globala instansen av Azure Active Directory. Azure Active Directory Premium och Basic stöds inte för närvarande i Microsoft Azure-tjänsten som drivs av 21Vianet i Kina. Om du vill ha mer information kontaktar du oss via [Azure Active Directory-forumet](https://feedback.azure.com/forums/169401-azure-active-directory/).



## Anpassa inloggningssidan

Du använder vanligtvis inloggningssidan om du behöver webbläsarbaserad åtkomst till molnappar och tjänster som din organisation prenumererar på.

Om du har gjort ändringar på inloggningssidan kan det ta upp till en timme innan ändringarna visas.

En företagsanpassad inloggningssida visas bara när du besöker en tjänst med en URL som är specifik för en klientorganisation, till exempel https://outlook.com/**contoso**.com eller https://mail.**contoso**.com.

När du besöker en tjänst med en URL som inte är specifik för en klientorganisation (t.ex. https://mail.office365.com) visas en icke företagsanpassad inloggningssida. I så fall visas din anpassning först när du har angett ditt användar-ID eller då du har valt en användarikon.

> [AZURE.NOTE]
>
- Domännamnet måste visas som ”Aktiv” i avsnittet **Active Directory** > **Katalog** > **Domäner** på den klassiska Azure-portalen där du har konfigurerat anpassningen.
- Anpassningen av inloggningssidan överförs inte till Microsofts konsumentinloggningssida. Om du loggar in med ett personligt Microsoft-konto kan du se en företagsanpassad lista över användarikoner som återges av Azure AD, men organisationens företagsanpassning används inte på inloggningssidan för Microsoft-konton.


Om du vill att ditt företags varumärke, färger och andra anpassningsbara element ska återges på den här sidan tittar du på följande bilder som visar skillnaden mellan de båda upplevelserna.

Följande skärmbild visar ett exempel på Office 365-inloggningssidan på en stationär dator **före** en anpassning:

![Office 365-inloggningssida före anpassning][1]

Följande skärmbild visar ett exempel på Office 365-inloggningssidan på en stationär dator **efter** en anpassning:

![Office 365-inloggningssida efter anpassning][2]

Följande skärmbild visar ett exempel på Office 365-inloggningssidan på en mobil enhet **före** en anpassning:

![Office 365-inloggningssida före anpassning][3]


Följande skärmbild visar ett exempel på Office 365-inloggningssidan på en mobil enhet **efter** en anpassning:

![Office 365-inloggningssida efter anpassning][4]


När du ändrar storlek på ett webbläsarfönster beskärs ofta den stora bilden, som den du såg tidigare, för att passa olika skärmproportioner. Med detta i åtanke bör du försöka behålla viktiga visuella element i bilden så att de alltid visas i det övre vänstra hörnet (längst upp till höger för höger-till-vänster-språk). Detta är viktigt eftersom storleksändringar vanligtvis sker från det nedre högra hörnet upp mot det vänstra hörnet eller nedifrån och uppåt.

Följande bild visar hur bilden beskärs när webbläsaren storleksändras åt vänster:

![][6]

Så här ser det ut när webbläsaren har storleksändrats mot överkanten:

![][7]

## Vilka element på sidan kan jag anpassa?

Du kan anpassa följande element på inloggningssidan:

![][5]

 Sidelement  | Plats på sidan
    ------------- | -------------
Banderollslogotyp | Visas längst upp till höger på sidan. Ersätter logotypen som visas på målwebbplatsen som du loggar in på (till exempel Office 365 eller Azure).
Stor bild/bakgrundsfärg | Visas till vänster på sidan. Ersätter bilden som visas på målwebbplatsen som du loggar in på. Bakgrundsfärgen kanske visas i stället för den stora bilden vid anslutningar med låg bandbredd eller på smala skärmar.
Text på inloggningssidan | Visas ovanför sidfoten och kan förmedla praktisk information till användare innan de loggar in med ett arbets- eller skolkonto. Du kanske vill lägga till telefonnumret till supportavdelningen eller ett juridiskt meddelande.

> [AZURE.NOTE]
Alla element är valfria. Om du till exempel lägger till en banderollslogotyp men ingen stor bild så visas din logotyp och målwebbplatsens bild (dvs. bilden på en motorväg i Kalifornien för Office 365).

Du kan också lokalisera alla element på den här sidan. När du har konfigurerat en standarduppsättning med anpassningselement kan du konfigurera fler versioner för olika språk. Du kan också blanda och matcha olika element. Du kan till exempel:

- Skapa en förvald stor bild som passar alla kulturer och sedan skapa specifika versioner för engelska och franska. När något av dessa språk anges i webbläsaren visas den specifika bilden, medan standardbilden visas för andra språk.

- Konfigurera olika logotyper för din organisation (t.ex. japanska och hebreiska versioner).



## Anpassning av åtkomstpanelsidan

Åtkomstpanelsidan är i grunden en portalsida för snabb åtkomst till molnappar som du har beviljats åtkomst till av administratören. På den här sidan visas dina appar som klickbara programpaneler.


Följande skärmbild visar ett exempel på en åtkomstpanelsida efter anpassningen.

![][8]

## Konfigurera katalogen med företagsinformation

Du kan konfigurera en standarduppsättning med anpassningsbara element för varje katalog på den klassiska Azure-portalen. När standardinställningarna har sparats kan en administratör lägga till lokaliserade versioner av varje element för olika språk. Alla anpassningsbara element är valfria.

Om du till exempel konfigurerar en standardbanderollslogotyp men ingen stor bild så visas din logotyp längst upp till höger på inloggningssidan. Dock visas webbplatsens standardbild.

Föreställ dig följande konfiguration:

- En standardbanderollslogotyp och text på engelska på en inloggningssida
- Text på tyska på en språkspecifik inloggningssida

Om din språkinställning är tyska visas standardbanderollslogotypen men med den tyska texten.

Tekniskt sett kan du konfigurera olika uppsättningar för varje språk som stöds av Azure AD, men av prestanda- och underhållsskäl rekommenderar vi att du nöjer dig med några få varianter.

**Utför följande steg om du vill lägga till företagsprofilering för din katalog:**

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som administratör för den katalog som du vill anpassa.
2. Välj den katalog som du vill anpassa.
3. Klicka på **Konfigurera** i verktygsfältet högst upp.
4. Klicka på **Anpassa profilering**.
4. Ändra de element som du vill anpassa. Alla fält är valfria.
5. Klicka på **Spara**.

Det kan ta upp till en timme innan nya ändringar som du har gjort på inloggningssidan visas.

**Utför följande steg om du vill lägga till språkspecifik företagsanpassning:**

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som administratör för den katalog som du vill anpassa.
2. Välj den katalog som du vill anpassa.
3. Klicka på **Konfigurera** i verktygsfältet högst upp.
4. Klicka på **Anpassa profilering**.
2. Klicka på **Lägg till företagsanpassning för ett specifikt språk**.
3. Välj det språk som du vill anpassa logotypen för och klicka sedan på **Nästa**.
3. Redigera endast element som du vill konfigurera språkspecifika åsidosättningar för. Alla fält är valfria. Om ett fält lämnas tomt visas det anpassade standardvärdet i stället (eller Microsofts standardvärde om inget anpassat värde har konfigurerats).
4. Klicka på **Spara**.

**Utför följande steg om du vill ta bort en företagsanpassning från katalogen:**

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som administratör för den katalog som du vill anpassa.
2. Välj den katalog som du vill anpassa.
3. Klicka på **Konfigurera** i verktygsfältet högst upp.
4. Klicka på **Anpassa profilering**.
5. Välj **Redigera befintliga anpassningsinställningar** på sidan för att anpassa profilering och gå sedan till nästa sida.
3. Beroende på vilka element du vill ta bort gör du något av följande:

    a. Under **Banderollslogotyp** väljer du **Ta bort den uppladdade logotypen**.

    b. Under **Ikonlogotyp** väljer du **Ta bort den uppladdade logotypen**.

    c. Ta bort texten från alla textrutor.

    d. Klicka på **Next**.

    e. Ta bort texten från alla textrutor.

4. Klicka på **Spara** för att ta bort elementen.
5. Om det behövs klickar du på alternativet för att **anpassa profilering** igen och upprepar de här stegen för all språkspecifik anpassning som ska tas bort.
    Alla inställningar för företagsanpassning har tagits bort när du klickar på alternativet för att **anpassa profilering** och ser formuläret **Anpassa standardanpassning** utan några konfigurerade inställningar.

## Testning och exempel

Vi rekommenderar att du experimenterar med en testklient innan du gör ändringar i produktionsmiljön.

**Så här kontrollerar du om din företagsanpassning har tillämpats:**

1. Öppna en InPrivate- eller Incognito-webbläsarsession.
2. Besök https://outlook.com/contoso.com och ersätt contoso.com med den domän som du har anpassat.

Detta fungerar även med domäner som ser ut som contoso.onmicrosoft.com.

För att hjälpa dig att skapa effektiva anpassningar har vi anpassat följande två fiktiva inloggningssidor:

- [http://aka.ms/aaddemo001](http://aka.ms/aaddemo001)
- [http://aka.ms/aaddemo002](http://aka.ms/aaddemo002)

För att testa de språkspecifika inställningarna måste du ändra standardspråkinställningarna i webbläsaren till ett språk som du har angett i anpassningen. I Internet Explorer konfigurerar du detta på **Internetalternativ**-menyn.

## Anpassningsbara element

Vissa anpassningsbara element i Azure AD har flera användningsscenarier. Du kan konfigurera en företagslogotyp en gång för varje katalog så används den både på inloggningssidan och åtkomstpanelsidan. Vissa anpassningsbara element är specifika för inloggningssidan. Följande tabell innehåller information om de olika anpassningsbara elementen.

Namn | Beskrivning | Villkor | Rekommendationer
    ------------- | ------------- | ------------- | -------------
Banderollslogotyp | Banderollslogotypen visas på inloggningssidan och på åtkomstpanelen. | <p>JPG eller PNG</p><p>60 × 280 bildpunkter</p><p>10 kB</p> | <p>Använd organisationens hela logotyp (inklusive grafik och text)</p><p>Se till att den inte är högre än 30 bildpunkter för att undvika att rullningslistor visas på mobila enheter</p><p>Se till att den inte är större än 4 kB</p><p>Använd en transparent PNG (förutsätt inte att inloggningssidan alltid har en vit bakgrund)</p>
Ikonlogotyp | (används inte på inloggningssidan för närvarande) I framtiden kan den här texten användas för att ersätta det generella piktogrammet för arbets- och skolkonton på olika platser i miljön. | <p>JPG eller PNG</p><p>120 × 120 bildpunkter</p><p>10 kB</p> | <p>Håll det enkelt (ingen liten text) eftersom bildens storlek kan minska till 50 %
</p> |
Etikett för användarnamn på inloggningssidan | (används inte på inloggningssidan för närvarande) I framtiden kan den här texten användas för att ersätta den generella strängen för arbets- och skolkonton på olika ställen i miljön. Du kan välja något som ”Contoso-konto” eller ”Contoso-ID” | <p>Unicode-text, upp till 50 tecken</p><p>Endast oformaterad text (inga länkar eller HTML-taggar)</p> | <p>Håll det kort och enkelt</p><p>Fråga användarna hur de vanligtvis refererar till arbets- eller skolkontot som du ger dem.</p>
Text på inloggningssidan | Den här exempeltexten visas under formuläret på inloggningssidan och kan användas för att ge ytterligare instruktioner eller information om var användaren kan få hjälp och support. | <p>Unicode-text, upp till 256 tecken</p><p>Endast oformaterad text (inga länkar eller HTML-taggar)</p> | Se till att den inte är längre än 250 tecken (cirka 3 rader med text)
Bild av inloggningssidan | Bilden är en stor bild som visas på inloggningssidan till vänster om inloggningssidans formulär. | <p>JPG eller PNG</p><p>1420 × 1200</p><p>500 kB</p> | <p>1420 × 1200 bildpunkter</p><p>Viktigt! Håll den så liten som möjligt, helst under 200 kB. Om den här bilden är för stor påverkas inloggningssidans prestanda om bilden inte cachelagras</p><p>Den här bilden beskärs ofta för att passa olika skärmproportioner. Se till att viktiga visuella element är placerade i det övre vänstra hörnet (övre högra hörnet för höger-till-vänster-språk) eftersom storleksändringen görs från det nedre högra hörnet och upp mot det övre vänstra hörnet när webbläsarfönstret krymper.</p>
Bakgrundsfärg på inloggningssidan | Bakgrundsfärgen på inloggningssidan används i området till vänster om inloggningssidans formulär. | Måste vara en RGB-färg i hexadecimalt format (exempel: #FFFFFF) | <p>Bakgrundsfärgen kan visas i stället för den stora bilden vid anslutningar med låg bandbredd</p><p>Vi rekommenderar att du väljer den primära färgen i banderollogotypen</p>


## Nästa steg

- [Komma igång med Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Visa åtkomst- och användningsrapporterna](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/SignInPage_beforecustomization.png
[2]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization.png
[3]: ./media/active-directory-add-company-branding/SignInPage_mobile_beforecustomization.png
[4]: ./media/active-directory-add-company-branding/SignInPage_mobile_aftercustomization.png
[5]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_elements.png
[6]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedleft.png
[7]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedtop.png
[8]: ./media/active-directory-add-company-branding/APBranding.png



<!--HONumber=Sep16_HO3-->


