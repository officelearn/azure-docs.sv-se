---
title: Villkor för användning – Azure Active Directory | Microsoft Docs
description: Beskriver hur du kommer igång med Azure Active Directory användningsvillkor att presentera information för anställda eller gäster innan de får åtkomst.
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/03/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1f03cd518a15d08971968e04fa69954951c77e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60357126"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Användningsvillkorsfunktion för Azure Active Directory
Användningsvillkoren för Azure AD tillhandahåller en enkel metod som organisationer kan använda för att presentera information för slutanvändare. Den här presentationen gör att användare kan se relevanta ansvarsfriskrivningar för juridiska krav eller efterlevnadskrav. Den här artikeln beskriver hur du kommer igång med användningsvillkoren.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Översikt över videor

Följande videoklipp ger en snabb översikt över användningsvillkoren.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Fler videor finns här:
- [Så här distribuerar du användningsvillkoren i Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Hur du ska distribuera villkor för användning i Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Vad kan jag göra med användningsvillkoren?
Azure AD-användningsvillkor för har följande funktioner:
- Kräv medarbetare eller gäster att godkänna dina användningsvillkor innan de får åtkomst.
- Kräv medarbetare eller gäster att godkänna dina användningsvillkor på alla enheter innan de får åtkomst.
- Kräv medarbetare eller gäster att godkänna dina användningsvillkor enligt ett återkommande schema.
- Presentera allmänna användningsvillkor för alla användare i din organisation.
- Specifika användningsvillkor baserat på användarattribut (t.ex. läkare jämfört med sjuksköterskor eller inhemska jämfört med internationella medarbetare, via [dynamiska grupper](../users-groups-roles/groups-dynamic-membership.md)).
- Presentera specifika användningsvillkor vid åtkomst till affärskritiska program, t.ex. Salesforce.
- Presentera användningsvillkor på olika språk.
- Lista som har eller inte har accepterat dina användningsvillkor.
- Hjälpa uppfyller sekretessbestämmelser.
- Visa en logg över villkoren för användning aktivitet för efterlevnad och granskning.
- Skapa och hantera användning med hjälp av [Microsoft Graph API: er](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (för närvarande i förhandsversion).

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda och konfigurera Azure AD-villkor för användning, måste du ha:

- Azure AD Premium P1-, P2-, EMS E3- eller EMS E5-prenumeration.
    - Om du inte har en av de här prenumerationerna, kan du [hämta Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) eller [aktivera Azure AD Premium-utvärderingsversionen](https://azure.microsoft.com/trial/get-started-active-directory/).
- Ett av följande administratörskonton för den katalog som du vill konfigurera:
    - Global administratör
    - Säkerhetsadministratör
    - Administratör för villkorsstyrd åtkomst

## <a name="terms-of-use-document"></a>Dokument med användningsvillkor

Användningsvillkoren för Azure AD presenteras i PDF-format. PDF-filen kan innehålla valfritt innehåll, som befintliga kontraktdokument, så att du kan samla in slutanvändaravtal under inloggningen. För användare på mobila enheter måste är den rekommenderade teckenstorleken i PDF-filen 24.

## <a name="add-terms-of-use"></a>Lägg till användningsvillkor
När du har skapat ditt dokument för användningsvillkor använder du följande metod för att lägga till det.

1. Logga in på Azure som en Global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.

1. Gå till **användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

    ![Blad för användningsvillkor](./media/terms-of-use/tou-blade.png)

1. Klicka på **Nya villkor**.

    ![Lägga till användningsvillkor](./media/terms-of-use/new-tou.png)

1. I den **namn** anger du ett namn för användningsvillkoren som ska användas i Azure-portalen.

1. I den **visningsnamn** anger en rubrik som användarna ser när de loggar in.

1. För **dokument med användningsvillkor**bläddrar du till dina färdigställda användningsvillkor PDF och markera den.

1. Välj språk för dokument med dina användningsvillkor. Via språkalternativet kan du ladda upp flera användningsvillkor, vart och ett med olika språk. Versionen av användningsvillkoren som en användare ser baseras på deras inställningar för webbläsaren.

1. Om du vill kräva av slutanvändarna läsa villkoren innan de accepterar dem., ange **kräva att användarna expanderar användningsvillkoren** till **på**.

1. Om du vill kräva av slutanvändarna att godkänna dina användningsvillkor på varje enhet som de får från, ange **användare måste samtycka på alla enheter** till **på**. Mer information finns i [Per enhet användningsvillkor](#per-device-terms-of-use).

1. Om du vill ska upphöra användningsvillkor godkänner enligt ett schema genom att ange **upphör att gälla medgivanden** till **på**. När värdet på visas två ytterligare schemainställningar.

    ![Upphör medgivanden](./media/terms-of-use/expire-consents.png)

1. Använd den **upphör att gälla från och med** och **frekvens** inställningar för att ange schemat för användningsvillkor använder förfallodatum. I följande tabell visar resultatet för ett par exempel på inställningar:

    | Upphörandet startar den | Frekvens | Resultat |
    | --- | --- | --- |
    | Dagens datum  | Månadsvis | Startar idag, måste användarna godkänna användningsvillkoren och måste godkänna varje månad. |
    | Datum i framtiden  | Månadsvis | Med början i dag kan måste användarna acceptera användningsvillkoren. När det framtida datumet infaller medgivanden upphör att gälla och sedan måste användarna måste godkänna varje månad.  |

    Exempel: Om du ställer in den upphör att gälla från och med datum till **1 januari** och frekvens för att **månatliga**, här är hur kontolösenordet kan inträffa i två användare:

    | Användare | Först acceptera datum | Först går ut datum | Andra upphör att gälla datum | Tredje upphör att gälla datum |
    | --- | --- | --- | --- | --- |
    | Alice | 1 januari | Den 1 februari | 1 mars | Den 1 april |
    | Bob | 15 jan | Den 1 februari | 1 mars | Den 1 april |

1. Använd den **varaktighet innan re godkännande krävs (dagar)** inställningen för att ange antal dagar innan användaren måste godkänna användningsvillkoren. På så sätt kan användarna att följa sitt eget schema. Exempel: Om du vill ange hur lång tid **30** dagar, här är hur kontolösenordet kan inträffa i två användare:

    | Användare | Först acceptera datum | Först går ut datum | Andra upphör att gälla datum | Tredje upphör att gälla datum |
    | --- | --- | --- | --- | --- |
    | Alice | 1 januari | Och med 31 jan | Mar 2 | Den 1 april |
    | Bob | 15 jan | Den 14 februari | 16 mars | Den 15 april |

    Det är möjligt att använda den **upphör att gälla medgivanden** och **varaktighet innan re godkännande krävs (dagar)** inställningar tillsammans, men normalt använda ena eller den andra.

1. Under **villkorlig åtkomst**, använda den **tvinga med villkorlig åtkomst Principmall** och välj mallen du vill framtvinga användningsvillkoren.

    ![Mallar för villkorlig åtkomst](./media/terms-of-use/conditional-access-templates.png)

    | Mall | Beskrivning |
    | --- | --- |
    | **Åtkomst till molnappar för alla gäster** | Principer för villkorlig åtkomst kommer att skapas för alla gäster och alla molnappar. Den här principen påverkar Azure-portalen. När den har skapats kan du bli ombedd att logga ut och logga in. |
    | **Åtkomst till molnappar för alla användare** | Principer för villkorlig åtkomst kommer att skapas för alla användare och alla molnappar. Den här principen påverkar Azure-portalen. När den har skapats, kommer du att behöva logga ut och logga in. |
    | **Anpassad princip** | Välj de användare, grupper och appar som de här användningsvillkoren ska gälla för. |
    | **Skapa princip för villkorlig åtkomst senare** | Det här användningsvillkoret visas i kontrollistan för beviljande när du skapar en princip för villkorsstyrd åtkomst. |

    >[!IMPORTANT]
    >Kontrollerna för principer för villkorlig åtkomst (inklusive användningsvillkoren) stöder inte tvingande för tjänstkonton. Vi rekommenderar att du exkluderar alla tjänstkonton från principen för villkorlig åtkomst.

     Anpassade villkorliga åtkomstprinciper möjliggör detaljerade användningsvillkor för ett specifikt molnprogram eller grupp med användare. Mer information finns i [ Snabbstart: Kräv användningsvillkor godkännas före åtkomst till molnappar](require-tou.md).

1. Klicka på **Skapa**.

    Om du har valt en mall för villkorlig åtkomst har en ny skärm visas där du kan skapa princip för villkorlig åtkomst.

    ![Anpassad princip](./media/terms-of-use/custom-policy.png)

    Nu ska du inte längre se dina nya användningsvillkor.

    ![Lägga till användningsvillkor](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Visa rapport över vem som har godkänts och nekats
Bladet för användningsvillkor visar antalet användare som har godkänt och avböjt. Detta antal och vem som accepterat/avböjt lagras under användningsvillkorens livslängd.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

    ![Blad för användningsvillkor](./media/terms-of-use/view-tou.png)

1. För användningsvillkor, klickar du på numren under **godkända** eller **avböjt** att visa det aktuella tillståndet för användare.

    ![Medgivnaden av användningsvillkor](./media/terms-of-use/accepted-tou.png)

1. Du kan visa historiken för en enskild användare genom att klicka på ellipsen (**...** ) och sedan **Visa historik**.

    ![Visa-menyn](./media/terms-of-use/view-history-menu.png)

    I fönstret Visa historik du ser en historik över alla de accepterar avböjningar och förfallodatum.

    ![Visningsfönster för historik](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Öppna Azure AD-granskningsloggar
Om du vill visa ytterligare aktivitet innehåller Azure AD-användningsvillkor granskningsloggar. Varje användargodkännande utlöser en händelse i granskningsloggarna som har lagrats för **30 dagar**. Du kan visa dessa loggar i portalen eller hämta dem som en CSV-fil.

Du kommer igång med Azure AD granskningsloggar, Följ stegen nedan:

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

1. Välj ett villkor för användning.

1. Klicka på **Visa granskningsloggar**.

    ![Blad för användningsvillkor](./media/terms-of-use/audit-tou.png)

1. Granska skärmen på Azure AD kan du filtrera informationen med de tillhandahållna listorna som ska target specifik granskningslogginformation.

    Du kan också klicka på **Hämta** för att ladda ned informationen i en CSV-fil och använda den lokalt.

    ![Granskningsloggar](./media/terms-of-use/audit-logs-tou.png)

    Om du klickar på en logg visas ett fönster med information om ytterligare aktivitet.

    ![information om datoraktivitet](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Hur användningsvillkor ser ut för användare
När användningsvillkoren har skapats och tillämpats kommer användare som ingår i området se följande skärm under inloggningen.

![Web användarinloggning](./media/terms-of-use/user-tou.png)

Användare kan visa användningsvillkoren och om det behövs använder du knapparna för att zooma in och ut.

![Visa villkor för användning med zoomning knappar](./media/terms-of-use/zoom-buttons.png)

Följande skärmbild visar hur användningsvillkor ser ut på mobila enheter.

![Mobila användarinloggning](./media/terms-of-use/mobile-tou.png)

Användarna behöver bara godkänna villkoren en gång och de kommer inte se användningsvillkoren igen på efterföljande inloggningar.

### <a name="how-users-can-review-their-terms-of-use"></a>Hur användarna kan granska deras användningsvillkor
Användare kan granska och se användningsvillkoren som de har godkänt genom att använda följande procedur.

1. Logga in på [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. I det övre högra hörnet, klickar du på ditt namn och välj **profil**.

    ![Profil](./media/terms-of-use/tou14.png)

1. Klicka på **Granska användningsvillkoren** på din profilsida.

    ![Profilera – granska användningsvillkoren](./media/terms-of-use/tou13a.png)

1. Därifrån kan du granska användningsvillkoren som du har accepterat.

## <a name="edit-terms-of-use-details"></a>Redigera information om användningsvillkor
Du kan redigera vissa detaljer för användningsvillkor, men du kan inte ändra ett befintligt dokument. Följande procedur beskriver hur du redigerar detaljerna.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

1. Välj användningsvillkoren som du vill redigera.

1. Klicka på **redigera villkoren**.

1. I Använd fönstret Redigera villkoren, byta namn, visningsnamn eller kräva att användarna expanderar värden.

    Om det finns andra inställningar som du vill ändra, till exempel PDF-dokumentet kräver att användarna samtycka på alla enheter, upphör att gälla medgivanden, varaktighet innan reacceptance eller princip för villkorlig åtkomst, måste du skapa nya användningsvillkor.

    ![Redigera användningsvillkoren](./media/terms-of-use/edit-tou.png)

1. Klicka på **spara** att spara dina ändringar.

    När du har sparat ändringarna kan användare inte måste godkänna dessa ändringar.

## <a name="add-a-terms-of-use-language"></a>Lägga till användningsvillkor Använd språk
Följande procedur beskriver hur du lägger till användningsvillkor Använd språk.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

1. Välj användningsvillkoren som du vill redigera.

1. I informationsfönstret klickar du på den **språk** fliken.

    ![Lägga till användningsvillkor](./media/terms-of-use/languages-tou.png)

1. Klicka på **Lägg till språk**.

1. Ladda upp din lokaliserade PDF-fil i Använd språk fönstret Lägg till villkor och Välj språk för.

    ![Lägga till användningsvillkor](./media/terms-of-use/language-add-tou.png)

1. Klicka på **Lägg till** att lägga till språk.

## <a name="per-device-terms-of-use"></a>Per enhet användningsvillkor

Den **användare måste samtycka på alla enheter** inställningen gör det möjligt för dig att slutanvändaren måste godkänna dina användningsvillkor på varje enhet som de får från. Användaren kommer att behöva ansluta sina enheter i Azure AD. När enheten är ansluten, används enhets-ID för att framtvinga användningsvillkoren på varje enhet.

Här är en lista över plattformar som stöds och programvara.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Annat |
> | --- | --- | --- | --- | --- |
> | **Inbyggd app** | Ja | Ja | Ja |  |
> | **Microsoft Edge** | Ja | Ja | Ja |  |
> | **Internet Explorer** | Ja | Ja | Ja |  |
> | **Chrome (med filtillägget)** | Ja | Ja | Ja |  |

Per enhet användningsvillkoren har följande begränsningar:

- En enhet kan endast kopplas till en klient.
- En användare måste ha behörighet att ansluta till sin enhet.
- Appen Intune-registrering stöds inte.
- Azure AD B2B-användare stöds inte.

Om användarens enhet inte är ansluten, visas ett meddelande om att de behöver för att ansluta sina enheter. Deras upplevelse blir beroende på plattform och programvara.

### <a name="join-a-windows-10-device"></a>Anslut en Windows 10-enhet

Om en användare använder Windows 10 och Microsoft Edge, får de ett meddelande som liknar följande för att [ansluta sina enheter](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 och Microsoft Edge - anslutning till enheten en dialogruta](./media/terms-of-use/per-device-win10-edge.png)

Om de använder Chrome, kommer de att uppmanas att installera den [Windows 10-konton tillägget](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Webbläsare

Om en användare använder webbläsaren som inte stöds, uppmanas de att använda en annan webbläsare.

![Webbläsaren stöds inte](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Ta bort användningsvillkor
Du kan ta bort gamla användningsvillkor på följande sätt.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

1. Välj de användningsvillkor du vill ta bort.

1. Klicka på **Ta bort villkor**.

1. På meddelandet som visas som frågar om du vill fortsätta klickar du **Ja**.

    ![Ta bort användningsvillkor](./media/terms-of-use/delete-tou.png)

    Nu bör du inte längre se dina användningsvillkor.

## <a name="deleted-users-and-active-terms-of-use"></a>Borttagna användare och aktiva användningsvillkor
Som standard är en borttagen användare i borttagen status i Azure AD i 30 dagar. Under den här perioden kan användaren återställas av en administratör om det behövs. Efter 30 dagar tas användaren bort permanent. En global administratör kan också använda Azure Active Directory-portalen för att uttryckligen och [permanent ta bort en användare som nyligen lagts till](../fundamentals/active-directory-users-restore.md) innan perioden är slut. När en användare har tagits bort permanent tas efterföljande data om den användaren bort från de aktiva användningsvillkoren. Granskningsinformation om borttagna användare finns kvar i spårningsloggen.

## <a name="policy-changes"></a>Principändringar
Principer för villkorlig åtkomst börjar gälla omedelbart. När detta händer börjar administratören Se ”sorgsna moln” eller ”Azure AD-tokenärenden”. Administratören måste logga ut och logga in igen för att uppfylla den nya principen.

>[!IMPORTANT]
> Användare inom området behöver logga ut och logga in för att uppfylla en ny princip om:
> - en villkorlig åtkomstprincip är aktiverad för ett användningsvillkor
> - eller andra användningsvillkor skapas

## <a name="b2b-guests-preview"></a>B2B-gäster (förhandsversion)

De flesta organisationer har en process för sina anställda kan samtycka till sin organisations användningsvillkor och sekretesspolicyn. Men hur kan du ange samma medgivanden för Azure AD business-to-business (B2B) gästerna när de läggs via SharePoint eller team? Du kan använda villkorlig åtkomst och användningsvillkor, för att tillämpa en princip direkt mot B2B-gästanvändare. Under inbjudan inlösen flödet, kommer användaren att visas med användningsvillkoren. Det här stödet förhandsvisas just nu.

Användningsvillkoren visas bara när användaren har ett gästkonto i Azure AD. SharePoint Online för närvarande har en [ad hoc-mottagare upplevelse med extern delning](/sharepoint/what-s-new-in-sharing-in-targeted-release) att dela ett dokument eller en mapp som inte kräver att användaren har ett gästkonto. I det här fallet visas inte användningsvillkor.

![Alla gästanvändare](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Stöd för appar i molnet (förhandsversion)

Villkor för användning kan användas för olika molnappar, t.ex Azure Information Protection och Microsoft Intune. Det här stödet förhandsvisas just nu.

### <a name="azure-information-protection"></a>Azure Information Protection

Du kan konfigurera en princip för villkorlig åtkomst för Azure Information Protection-appen och behöver användningsvillkor när en användare ansluter till ett skyddat dokument. Detta utlöser en villkoren innan en användare med åtkomst till ett skyddat dokument för första gången.

![Azure Information Protection-molnapp](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune-registrering

Du kan konfigurera en princip för villkorlig åtkomst för Microsoft Intune-registrering appen och behöver en användningsvillkoren innan du registrerar en enhet i Intune. Mer information finns i [välja rätt villkor lösning för din organisation blogginlägget](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Microsoft Intune molnapp](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Appen Intune-registrering stöds inte för [Per enhet användningsvillkor](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Hur ser jag om / när en användare har godkänt användningsvillkor?**<br />
S: Om villkoren för användning bladet, klickar du på numret under **godkända**. Du kan också visa eller söka efter accept-aktiviteten i Azure AD granskningsloggar. Mer information finns i Visa rapport över vem som har godkänts och nekats och [öppna Azure AD-granskningsloggar](#view-azure-ad-audit-logs).

**F: Hur länge är information som lagras?**<br />
S: Användaren räknar av villkoren i rapporten för användning och som accepterat/avvisade lagras för resten av användningsvillkoren. Azure AD-granskningen loggar lagras i 30 dagar.

**F: Varför ser jag ett annat antal medgivanden i villkoren för användning rapporten jämfört med Azure AD som granskningsloggar?**<br />
S: Använd rapporten villkoren lagras under livslängden för de användningsvillkoren när Azure AD-granskningen loggar lagras i 30 dagar. Dessutom visar villkoren i Använd rapporten endast användare aktuella medgivande tillstånd. Till exempel om en användare nekar och sedan accepterar villkoren i Använd rapporten visas bara den användaren Godkänn. Om du vill se historiken kan du använda Azure AD granskningsloggar.

**F: Om jag redigera information för användningsvillkor behöver användarna godkänna igen?**<br />
S: Nej, om en administratör redigerar detaljerna för användningsvillkor (namn, visningsnamn, kräva att användarna expanderar eller Lägg till ett språk), inte kräver att användarna måste godkänna de nya villkoren.

**F: Kan jag uppdatera ett befintligt dokument med användningsvillkor?**<br />
S: För närvarande kan uppdatera du inte ett befintligt dokument med användningsvillkor. För att ändra dokument med ett användningsvillkor, behöver du skapar en ny för användning instans.

**F: Om hyperlänkar finns i PDF-dokument med användningsvillkor, kommer användare kunna klickar du på dem?**<br />
S: PDF-filen renderas som standard som JPEG, så att inte klickbara hyperlänkar. Användare har möjlighet att välja **har du problem med att visa? Klicka här**, vilket visas med PDF-filen internt där hyperlänkar stöds.

**F: Kan användningsvillkor ha stöd för flera språk?**<br />
S: Ja. Det finns för närvarande 108 olika språk som en administratör kan konfigurera för ett enskilt användningsvillkor. En administratör kan ladda upp flera PDF-dokument och tagga dessa dokument med ett motsvarande språk (upp till 108). När slutanvändare loggar in kan vi titta på sina webbläsare språkinställning och visa matchande dokumentet. Om det finns ingen matchning, så visas standarddokument, vilket är det första dokumentet som har överförts.

**F: När är användningsvillkoren?**<br />
S: Användningsvillkoren tillämpas under inloggningen går.

**F: Vilka program kan jag rikta användningsvillkor till?**<br />
S: Du kan skapa en princip för villkorlig åtkomst för företagsprogram som använder modern autentisering. Mer information finns i [enterprise applications](./../manage-apps/view-applications-portal.md) (företagsprogram).

**F: Kan jag lägga till flera användningsvillkor till en viss användare eller app?**<br />
S: Ja, genom att skapa flera principer för villkorlig åtkomst som dessa grupper eller program. Om en användare som ingår i omfånget för flera användningsvillkor godkänner de ett användningsvillkor i taget.

**F: Vad händer om en användare nekar användningsvillkoren?**<br />
S: Användaren är blockerad från att få åtkomst till programmet. Användaren behöver logga in igen och acceptera villkoren för att få åtkomst.

**F: Är det möjligt att unaccept användningsvillkoren som godkändes tidigare?**<br />
S: Du kan [granska tidigare godkända användningsvillkor](#how-users-can-review-their-terms-of-use), men det finns för närvarande inte ett sätt att unaccept.

**F: Vad händer om jag använder också Intune villkoren?**<br />
S: Om du har konfigurerat båda Azure AD-användningsvillkor och [Intune villkor](/intune/terms-and-conditions-create), användaren uppmanas att godkänna båda. Mer information finns i den [välja rätt villkor lösning för din organisation blogginlägget](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Kräv användningsvillkor godkännas före åtkomst till molnappar](require-tou.md)
- [Best practices for conditional access in Azure Active Directory](best-practices.md) (Metodtips för villkorlig åtkomst i Azure Active Directory)
