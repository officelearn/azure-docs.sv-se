---
title: Användningsvillkor - Azure Active Directory | Microsoft-dokument
description: Kom igång med Azure Active Directory-användningsvillkor för att presentera information för anställda eller gäster innan du får åtkomst.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2f06a7c88a7c17f5f93201192664c2d4a97564e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480971"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory användningsvillkor

Azure AD användarvillkor ger en enkel metod som organisationer kan använda för att presentera information för slutanvändare. Den här presentationen gör att användare kan se relevanta ansvarsfriskrivningar för juridiska krav eller efterlevnadskrav. I den här artikeln beskrivs hur du kommer igång med användningsvillkor.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Översikt videor

Följande video ger en snabb översikt över användningsvillkoren.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Fler videor finns i:
- [Så här distribuerar du användningsvillkor i Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Så här distribuerar du användningsvillkor i Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Vad kan jag göra med användarvillkoren?

Azure AD-användningsvillkor har följande funktioner:

- Kräv att anställda eller gäster accepterar dina användarvillkor innan du får tillgång.
- Kräv att anställda eller gäster accepterar dina användarvillkor på alla enheter innan du får tillgång.
- Kräv att anställda eller gäster accepterar dina användarvillkor enligt ett återkommande schema.
- Kräv att anställda eller gäster accepterar dina användarvillkor innan du registrerar säkerhetsinformation i Azure Multi-Factor Authentication (MFA).
- Kräv att anställda accepterar dina användarvillkor innan du registrerar säkerhetsinformation i Azure AD-självbetjäningslösenordsåterställning (SSPR).
- Presentera allmänna användningsvillkor för alla användare i organisationen.
- Presentera specifika användningsvillkor baserat på ett användarattribut (t.ex. läkare jämfört med sjuksköterskor eller inhemska jämfört med internationella medarbetare, via [dynamiska grupper](../users-groups-roles/groups-dynamic-membership.md)).
- Presentera specifika användningsvillkor när du använder program med hög påverkan, till exempel Salesforce.
- Nuvarande användningsvillkor på olika språk.
- Lista vem som har eller inte har accepterat dina användarvillkor.
- Hjälp till att uppfylla sekretessregler.
- Visa en logg över användningsvillkor för efterlevnad och granskning.
- Skapa och hantera användningsvillkor med hjälp av [Microsoft Graph API:er](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (för närvarande i förhandsversion).

## <a name="prerequisites"></a>Krav

Om du vill använda och konfigurera Azure AD-användningsvillkor måste du ha:

- Azure AD Premium P1-, P2-, EMS E3- eller EMS E5-prenumeration.
   - Om du inte har en av de här prenumerationerna, kan du [hämta Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) eller [aktivera Azure AD Premium-utvärderingsversionen](https://azure.microsoft.com/trial/get-started-active-directory/).
- Ett av följande administratörskonton för den katalog som du vill konfigurera:
   - Global administratör
   - Säkerhetsadministratör
   - Administratör för villkorsstyrd åtkomst

## <a name="terms-of-use-document"></a>Dokument med användningsvillkor

Azure AD-användningsvillkor använder PDF-formatet för att presentera innehåll. PDF-filen kan innehålla valfritt innehåll, som befintliga kontraktdokument, så att du kan samla in slutanvändaravtal under inloggningen. För att stödja användare på mobila enheter är den rekommenderade teckenstorleken i PDF-filen 24 punkter.

## <a name="add-terms-of-use"></a>Lägg till användningsvillkor

När du har slutfört användningsdokumentet använder du följande procedur för att lägga till det.

1. Logga in på Azure som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Gå till **användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

   ![Villkorlig åtkomst - Användningsvillkor blad](./media/terms-of-use/tou-blade.png)

1. Klicka på **Nya villkor**.

   ![Nytt användningsfönster för att ange användningsvillkor](./media/terms-of-use/new-tou.png)

1. I rutan **Namn** anger du ett namn för de användningsvillkor som ska användas i Azure-portalen.
1. Ange en rubrik som användarna ser när de loggar in i rutan **Visningsnamn.**
1. För **användningsvillkor dokument,** bläddra till dina slutliga användarvillkor PDF och välj den.
1. Välj språk för ditt användningsdokument. Via språkalternativet kan du ladda upp flera användningsvillkor, vart och ett med olika språk. Versionen av användningsvillkoren som en användare ser baseras på deras inställningar för webbläsaren.
1. Om du vill att slutanvändarna ska visa användningsvillkoren innan de accepteras anger du **Kräv att användarna utökar användningsvillkoren** till **På**.
1. Om du vill att slutanvändarna ska acceptera dina användarvillkor på alla enheter som de använder från ställer du **in Kräv att användare samtycker på varje enhet** till **På**. Användare kan behöva installera ytterligare program om det här alternativet är aktiverat. Mer information finns i [Användarvillkoren per enhet](#per-device-terms-of-use).
1. Om du vill upphöra att gälla villkor för användning samtycker på ett schema, ange **Expire samtycker** till **På**. När den är inställd på På visas ytterligare två schemainställningar.

   ![Förfalla inställningar för upphör att gälla för att ange startdatum, frekvens och varaktighet](./media/terms-of-use/expire-consents.png)

1. Använd **inställningarna För upphör att gälla och** **Frekvens** för att ange schemat för giltighetstid för användningsvillkor. I följande tabell visas resultatet för ett par exempelinställningar:

   | Upphör att gälla från och med | Frequency | Resultat |
   | --- | --- | --- |
   | Dagens datum  | Månadsvis | Från och med idag måste användarna acceptera användarvillkoren och sedan acceptera varje månad. |
   | Datum i framtiden  | Månadsvis | Från och med idag måste användarna acceptera användarvillkoren. När det framtida datumet inträffar upphör medgivanden och sedan måste användarna accepteras igen varje månad.  |

   Om du till exempel anger att förfalla från och med den **1 januari** och frekvensen till **Månadsvis,** är det så här förfallodatum kan inträffa för två användare:

   | Användare | Första acceptera datum | Första förfallodatum | Andra utgångsdatum | Tredje utgångsdatum |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 1 februari | 1 mars | Den 1 april |
   | Bob | 15 jan | 1 februari | 1 mars | Den 1 april |

1. Använd inställningen **Varaktighet innan omacceptans kräver (dagar)** för att ange antalet dagar innan användaren måste acceptera användarvillkoren igen. Detta gör det möjligt för användare att följa sitt eget schema. Om du till exempel anger varaktigheten till **30** dagar kan du använda förfallodatum för två användare:

   | Användare | Första acceptera datum | Första förfallodatum | Andra utgångsdatum | Tredje utgångsdatum |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | Den 31 januari | 2 mars | Den 1 april |
   | Bob | 15 jan | Den 14 februari | Den 16 mars | Den 15 april |

   Det är möjligt att använda **godkännerna upphör att gälla** och Varaktighet **innan inställningarna för återacceptans kräver (dagar)** tillsammans, men vanligtvis använder du det ena eller det andra.

1. Under **Villkorlig åtkomst**använder du listan **Framtvinga med principmall** för villkorlig åtkomst för att välja mallen för att framtvinga användningsvillkoren.

   ![Listruta för villkorlig åtkomst för att välja en principmall](./media/terms-of-use/conditional-access-templates.png)

   | Mall | Beskrivning |
   | --- | --- |
   | **Tillgång till molnappar för alla gäster** | En policy för villkorlig åtkomst skapas för alla gäster och alla molnappar. Den här principen påverkar Azure-portalen. När detta har skapats kan du behöva logga ut och logga in. |
   | **Åtkomst till molnappar för alla användare** | En princip för villkorlig åtkomst skapas för alla användare och alla molnappar. Den här principen påverkar Azure-portalen. När detta har skapats måste du logga ut och logga in. |
   | **Anpassad princip** | Välj de användare, grupper och appar som de här användningsvillkoren ska tillämpas på. |
   | **Skapa princip för villkorlig åtkomst senare** | De här användningsvillkoren visas i listan över bidragskontroll när du skapar en princip för villkorlig åtkomst. |

   >[!IMPORTANT]
   >Policykontroller för villkorlig åtkomst (inklusive användningsvillkor) stöder inte tvingande på tjänstkonton. Vi rekommenderar att du utesluter alla tjänstkonton från principen villkorlig åtkomst.

    Principer för anpassad villkorlig åtkomst möjliggör detaljerade användningsvillkor, ned till ett visst molnprogram eller en viss grupp av användare. Mer information finns i [Snabbstart: Kräv att användningsvillkor accepteras innan du öppnar molnappar](require-tou.md).

1. Klicka på **Skapa**.

   Om du har valt en anpassad villkorsstyrd åtkomstmall visas en ny skärm som gör att du kan skapa den anpassade principen för villkorlig åtkomst.

   ![Nytt villkorsanpassningsfönster om du väljer principmallen för anpassad princip för villkorlig åtkomst](./media/terms-of-use/custom-policy.png)

   Du bör nu se dina nya användarvillkor.

   ![Nya användarvillkor som anges i användningsbladet](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Visa rapport över vem som har accepterat och avböjt

Bladet för användningsvillkor visar antalet användare som har godkänt och avböjt. Dessa räknas och som accepterade / avböjt lagras under hela användarvillkoren.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

   ![Användarvillkor blad lista antalet användare visar har accepterat och minskat](./media/terms-of-use/view-tou.png)

1. Om du vill använda användarvillkor klickar du på siffrorna under **Accepterad** eller **Avböjd** om du vill visa det aktuella tillståndet för användarna.

   ![Villkor för användning samtycker rutan med en lista över de användare som har accepterat](./media/terms-of-use/accepted-tou.png)

1. Om du vill visa historiken för en enskild användare klickar du på ellipsen (**...**) och sedan **på Visa historik**.

   ![Snabbmeny för visa historik för en användare](./media/terms-of-use/view-history-menu.png)

   I fönstret Vyhistorik visas en historik över alla accepterar, avböjningar och förfallodatum.

   ![I fönstret Visa historik visas historiken accepterar, avböjer och upphör att gälla för en användare](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Visa Granskningsloggar för Azure AD

Om du vill visa ytterligare aktivitet innehåller Azure AD-användningsvillkor granskningsloggar. Varje användarmedgivande utlöser en händelse i granskningsloggarna som lagras i **30 dagar**. Du kan visa dessa loggar i portalen eller hämta dem som en CSV-fil.

Använd följande procedur för att komma igång med Azure AD-granskningsloggar:

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).
1. Välj ett användningsvillkor.
1. Klicka på **Visa granskningsloggar**.

   ![Villkor för användning blad med alternativet Visa granskningsloggar markerat](./media/terms-of-use/audit-tou.png)

1. På skärmen Azure AD-granskningsloggar kan du filtrera informationen med hjälp av de angivna listorna för att rikta in dig på specifik granskningslogginformation.

   Du kan också klicka på **Hämta** för att ladda ned informationen i en CSV-fil och använda den lokalt.

   ![Skärmlista för Azure AD-granskning loggar, målprincip, initierad av och aktivitet](./media/terms-of-use/audit-logs-tou.png)

   Om du klickar på en logg visas en ruta med ytterligare aktivitetsinformation.

   ![Aktivitetsinformation för en logg som visar aktivitet, aktivitetsstatus, initierad av, målprincip](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Hur användningsvillkoren ser ut för användarna

När ett användningsvillkor har skapats och verkställts ser användare, som är i omfånget, följande skärm under inloggningen.

![Exempel på användningsvillkor som visas när en användare loggar in](./media/terms-of-use/user-tou.png)

Användare kan visa användningsvillkoren och vid behov använda knappar för att zooma in och ut.

![Visa användningsvillkor med zoomknappar](./media/terms-of-use/zoom-buttons.png)

Följande skärm visar hur användningsvillkoren ser ut på mobila enheter.

![Exempel på användningsvillkor som visas när en användare loggar in på en mobil enhet](./media/terms-of-use/mobile-tou.png)

Användare behöver bara acceptera användarvillkoren en gång och de kommer inte att se användarvillkoren igen vid efterföljande inloggningar.

### <a name="how-users-can-review-their-terms-of-use"></a>Hur användare kan se över sina användarvillkor

Användare kan granska och se de användarvillkor som de har accepterat med hjälp av följande procedur.

1. Logga in [https://myapps.microsoft.com](https://myapps.microsoft.com)på .
1. Klicka på ditt namn i det övre högra hörnet och välj **Profil**.

   ![MyApps-webbplatsen med användarens fönsterruta öppen](./media/terms-of-use/tou14.png)

1. Klicka på **Granska användningsvillkoren** på din profilsida.

   ![Profilsida för en användare som visar länken Granska användningsvillkor](./media/terms-of-use/tou13a.png)

1. Därifrån kan du granska användningsvillkoren som du har accepterat.

## <a name="edit-terms-of-use-details"></a>Redigera användningsvillkor

Du kan redigera vissa information om användningsvillkoren, men du kan inte ändra ett befintligt dokument. I följande procedur beskrivs hur du redigerar informationen.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).
1. Välj de användningsvillkor som du vill redigera.
1. Klicka på **Redigera termer**.
1. Ändra namn, visningsnamn eller kräva att användarna expanderar värden i fönstret Redigera användningsvillkor.

   Om det finns andra inställningar som du vill ändra, till exempel PDF-dokument, kräver att användare samtycker på varje enhet, upphör att gälla, varaktighet före återacceptans eller policy för villkorlig åtkomst, måste du skapa ett nytt användningsvillkor.

   ![Redigera användningstermer med namn- och expanderingsalternativ](./media/terms-of-use/edit-tou.png)

1. Klicka på **Spara** om du vill spara ändringarna.

   När du har sparat ändringarna behöver användarna inte acceptera ändringarna igen.

## <a name="add-a-terms-of-use-language"></a>Lägga till ett villkor för användningsspråk

I följande procedur beskrivs hur du lägger till ett användningsspråk.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).
1. Välj de användningsvillkor som du vill redigera.
1. Klicka på fliken **Språk** i informationsfönstret.

   ![Valda användningsvillkor och fliken Språk i informationsfönstret](./media/terms-of-use/languages-tou.png)

1. Klicka på **Lägg till språk**.
1. Ladda upp den lokaliserade PDF-filen och välj språk i fönstret Lägg till användningsvillkor.

   ![Lägg till användningstermer med alternativ för att ladda upp lokaliserade PDF-filer](./media/terms-of-use/language-add-tou.png)

1. Klicka på **Lägg till** om du vill lägga till språket.

## <a name="per-device-terms-of-use"></a>Användningsvillkor per enhet

**Med Kräv användarna att godkänna varje enhetsinställning** kan du kräva att slutanvändare accepterar dina användarvillkor på alla enheter de kommer åt från. Slutanvändaren måste registrera sin enhet i Azure AD. När enheten registreras används enhets-ID:t för att tillämpa användningsvillkoren på varje enhet.

Här är en lista över de plattformar och program som stöds.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Annat |
> | --- | --- | --- | --- | --- |
> | **Inbyggd app** | Ja | Ja | Ja |  |
> | **Microsoft Edge** | Ja | Ja | Ja |  |
> | **Internet Explorer** | Ja | Ja | Ja |  |
> | **Chrome (med tillägg)** | Ja | Ja | Ja |  |

Användningsvillkoren per enhet har följande begränsningar:

- En enhet kan bara anslutas till en klient.
- En användare måste ha behörighet att ansluta till sin enhet.
- Appen Intune-registrering stöds inte.
- Azure AD B2B-användare stöds inte.

Om användarens enhet inte är ansluten får de ett meddelande om att de måste ansluta till sin enhet. Deras erfarenhet kommer att vara beroende av plattformen och programvaran.

### <a name="join-a-windows-10-device"></a>Anslut en Windows 10-enhet

Om en användare använder Windows 10 och Microsoft Edge får de ett meddelande som liknar följande för att [gå med i sin enhet](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 och Microsoft Edge - Meddelande om att enheten måste vara registrerad](./media/terms-of-use/per-device-win10-edge.png)

Om de använder Chrome uppmanas de att installera [tillägget Windows 10-konton](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="register-an-ios-device"></a>Registrera en iOS-enhet

Om en användare använder en iOS-enhet uppmanas de att installera [Microsoft Authenticator-appen](https://apps.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="register-an-android-device"></a>Registrera en Android-enhet

Om en användare använder en Android-enhet uppmanas de att installera [Microsoft Authenticator-appen](https://play.google.com/store/apps/details?id=com.azure.authenticator).

### <a name="browsers"></a>Webbläsare

Om en användare använder webbläsare som inte stöds uppmanas de att använda en annan webbläsare.

![Meddelande om att enheten måste vara registrerad, men webbläsaren stöds inte](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Ta bort användningsvillkor

Du kan ta bort gamla användningsvillkor med hjälp av följande procedur.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).
1. Välj de användarvillkor du vill ta bort.
1. Klicka på **Ta bort villkor**.
1. På meddelandet som visas som frågar om du vill fortsätta klickar du **Ja**.

   ![Meddelande som ber om bekräftelse för att ta bort användningsvillkor](./media/terms-of-use/delete-tou.png)

   Du bör inte längre se dina användarvillkor.

## <a name="deleted-users-and-active-terms-of-use"></a>Borttagna användare och aktiva användningsvillkor

Som standard är en borttagen användare i borttagen status i Azure AD i 30 dagar. Under den här perioden kan användaren återställas av en administratör om det behövs. Efter 30 dagar tas användaren bort permanent. Dessutom kan en global administratör, med hjälp av Azure Active Directory-portalen, uttryckligen [permanent ta bort en nyligen borttagen användare](../fundamentals/active-directory-users-restore.md) innan den tidsperioden har uppnåtts. En som en användare har tagits bort permanent, efterföljande data om den användaren tas bort från de aktiva användarvillkoren. Granskningsinformation om borttagna användare finns kvar i spårningsloggen.

## <a name="policy-changes"></a>Principändringar

Principer för villkorlig åtkomst träder i kraft omedelbart. När detta inträffar börjar administratören se "sorgliga moln" eller "Azure AD-tokenproblem". Administratören måste logga ut och logga in igen för att uppfylla den nya principen.

> [!IMPORTANT]
> Användare inom området behöver logga ut och logga in för att uppfylla en ny princip om:
>
> - en policy för villkorlig åtkomst är aktiverad på ett användningsvillkor
> - eller andra användningsvillkor skapas

## <a name="b2b-guests-preview"></a>B2B-gäster (Preview)

De flesta organisationer har en process på plats för sina anställda att samtycka till organisationens användarvillkor och sekretesspolicyer. Men hur kan du tillämpa samma medgivanden för Azure AD business-to-business (B2B) gäster när de läggs till via SharePoint eller Teams? Med villkorlig åtkomst och användarvillkor kan du tillämpa en policy direkt mot B2B-gästanvändare. Under inbjudningsflödet presenteras användaren med användningsvillkoren. Det här stödet är för närvarande i förhandsversion.

Användningsvillkor visas endast när användaren har ett gästkonto i Azure AD. SharePoint Online har för närvarande en [ad hoc extern delningsmottagare för](/sharepoint/what-s-new-in-sharing-in-targeted-release) att dela ett dokument eller en mapp som inte kräver att användaren har ett gästkonto. I det här fallet visas inte användningsvillkor.

![Fönstret Användare och grupper – fliken Inkludera med alternativet Alla gästanvändare markerat](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Stöd för molnappar (förhandsversion)

Användningsvillkor kan användas för olika molnappar, till exempel Azure Information Protection och Microsoft Intune. Det här stödet är för närvarande i förhandsversion.

### <a name="azure-information-protection"></a>Azure Information Protection

Du kan konfigurera en princip för villkorlig åtkomst för Azure Information Protection-appen och kräva ett användningsvillkor när en användare kommer åt ett skyddat dokument. Detta utlöser ett användningsvillkor innan en användare öppnar ett skyddat dokument för första gången.

![Fönstret Molnappar med Microsoft Azure Information Protection-appen markerad](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune-registrering

Du kan konfigurera en princip för villkorlig åtkomst för Microsoft Intune-registreringsappen och kräva ett användningsvillkor innan en enhet i Intune registreringsvillkor. Mer information finns i Läs [välja rätt villkor lösning för din organisation blogginlägg](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Fönstret Molnappar med Microsoft Intune-appen markerad](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Appen Intune-registrering stöds inte för [användningsvillkor per enhet](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Hur ser jag om/när en användare har godkänt användningsvillkoren?**<br />
S: Klicka på numret under **Accepterad**på användningsbladet. Du kan också visa eller söka i acceptera-aktiviteten i Azure AD-granskningsloggarna. Mer information finns i Visa rapport över vem som har accepterat och avböjt och [Visa Granskningsloggar](#view-azure-ad-audit-logs)för Azure AD .

**F: Hur länge lagras informationen?**<br />
S: Användaren räknas i användarvillkoren rapport och som accepterade / avböjt lagras under hela användarvillkoren. Azure AD-granskningsloggarna lagras i 30 dagar.

**F: Varför ser jag ett annat antal medgivanden i användningsrapporten jämfört med Azure AD-granskningsloggarna?**<br />
S: Användningsanspråksrapporten lagras under den livstid som används, medan Azure AD-granskningsloggarna lagras i 30 dagar. I användningsbeskrivningsrapporten visas också bara användarnas aktuella samtyckestillstånd. Om en användare till exempel avböjer och sedan accepterar, visar användarvillkoren endast att användaren accepterar. Om du behöver se historiken kan du använda Azure AD-granskningsloggarna.

**F: Om jag redigerar informationen för ett användningsvillkor, kräver det att användarna accepterar igen?**<br />
S: Nej, om en administratör redigerar informationen för ett användningsvillkor (namn, visningsnamn, kräver att användarna expanderar eller lägger till ett språk) kräver den inte att användarna ska acceptera de nya termerna igen.

**F: Kan jag uppdatera ett befintligt användningsdokument?**<br />
S: För närvarande kan du inte uppdatera ett befintligt användningsdokument. Om du vill ändra ett användningsvillkor måste du skapa en ny användningstermer.

**F: Om hyperlänkar är i termer av användning PDF-dokument, kommer slutanvändarna att kunna klicka på dem?**<br />
S: Ja, slutanvändare kan välja hyperlänkar till ytterligare sidor, men länkar till avsnitt i dokumentet stöds inte.

**F: Kan användningsvillkor ha stöd för flera språk?**<br />
S: Ja. För närvarande finns det 108 olika språk som en administratör kan konfigurera för en enda användningsvillkor. En administratör kan ladda upp flera PDF-dokument och tagga dessa dokument med ett motsvarande språk (upp till 108). När slutanvändare loggar in tittar vi på deras språkinställningar för webbläsaren och visar det matchande dokumentet. Om det inte finns någon matchning visar vi standarddokumentet, som är det första dokumentet som överförs.

**F: När tillämpas användningsvillkoren?**<br />
S: Användningsvillkoren tillämpas under inloggningen.

**F: Vilka program kan jag rikta användningsvillkor mot?**<br />
S: Du kan skapa en princip för villkorlig åtkomst för företagsprogram med hjälp av modern autentisering. Mer information finns i [enterprise applications](./../manage-apps/view-applications-portal.md) (företagsprogram).

**F: Kan jag lägga till flera användningsvillkor till en viss användare eller app?**<br />
S: Ja, genom att skapa flera principer för villkorlig åtkomst som riktar sig till dessa grupper eller program. Om en användare omfattas av flera användningsvillkor accepterar de ett användningsvillkor åt gången.

**F: Vad händer om en användare nekar användningsvillkoren?**<br />
S: Användare blockeras från åtkomst till programmet. Användaren måste logga in igen och acceptera villkoren för att få åtkomst.

**F: Är det möjligt att acceptera ett användningsvillkor som tidigare accepterats?**<br />
S: Du kan [granska tidigare accepterade användarvillkor](#how-users-can-review-their-terms-of-use), men för närvarande finns det inte ett sätt att unaccept.

**F: Vad händer om jag också använder Intunes villkor?**<br />
S: Om du har konfigurerat både Azure AD-användarvillkor och [Intune-villkor](/intune/terms-and-conditions-create)måste användaren acceptera båda. Mer information finns i lösningen [Välja rätt villkor för ditt organisations blogginlägg](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**F: Vilka slutpunkter används användarvillkoren för autentisering?**<br />
S: Användningsvillkoren använder följande slutpunkter för https://tokenprovider.termsofuse.identitygovernance.azure.com https://account.activedirectory.windowsazure.comautentisering: och . Om din organisation har en tillåt lista över webbadresser för registrering måste du lägga till dessa slutpunkter i listan över tillåtna enheter, tillsammans med Azure AD-slutpunkterna för inloggning.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Kräv att användningsvillkor accepteras innan du öppnar molnappar](require-tou.md)
- [Metodtips för villkorlig åtkomst i Azure Active Directory](best-practices.md)
