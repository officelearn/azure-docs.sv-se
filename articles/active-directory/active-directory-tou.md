---
title: Användningsvillkor för Azure Active Directory | Microsoft Docs
description: Användningsvillkoren för Azure AD låter dig och ditt företag tillhandahålla användningsvillkor till användarna av Azure AD-tjänsterna.
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
ms.component: compliance
ms.date: 07/31/2018
ms.author: rolyon
ms.openlocfilehash: edf8eccf8c0a1cba70ac178ac5863a68b26f14a3
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622083"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Användningsvillkorsfunktion för Azure Active Directory
Användningsvillkoren för Azure AD tillhandahåller en enkel metod som organisationer kan använda för att presentera information för slutanvändare. Den här presentationen gör att användare kan se relevanta ansvarsfriskrivningar för juridiska krav eller efterlevnadskrav. Den här artikeln beskriver hur du kommer igång med användningsvillkor för Azure AD.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-can-i-do-with-terms-of-use"></a>Vad kan jag göra med användningsvillkoren?
Användningsvillkor för Azure AD gör att du kan göra följande:
- Kräva att anställda eller gäster accepterar dina användningsvillkor innan de får åtkomst.
- Presentera allmänna användningsvillkor för alla användare i din organisation.
- Specifika användningsvillkor baserat på användarattribut (t.ex. läkare jämfört med sjuksköterskor eller inhemska jämfört med internationella medarbetare, via [dynamiska grupper](users-groups-roles/groups-dynamic-membership.md)).
- Presentera specifika användningsvillkor vid åtkomst till affärskritiska program, t.ex. Salesforce.
- Presentera användningsvillkor på olika språk.
- Visa vem som har eller inte har godkänt dina användningsvillkor.
- Visa en granskningslogg av aktivitet för användningsvillkor.

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda och konfigurera Azure AD-villkor för användning, måste du ha:

- Azure AD Premium P1-, P2-, EMS E3- eller EMS E5-prenumeration.
    - Om du inte har en av de här prenumerationerna, kan du [hämta Azure AD Premium](fundamentals/active-directory-get-started-premium.md) eller [aktivera Azure AD Premium-utvärderingsversionen](https://azure.microsoft.com/trial/get-started-active-directory/).
- Ett av följande administratörskonton för den katalog som du vill konfigurera:
    - Global administratör
    - Säkerhetsadministratör
    - Administratör för villkorsstyrd åtkomst

## <a name="terms-of-use-document"></a>Dokument med användningsvillkor

Användningsvillkoren för Azure AD presenteras i PDF-format. PDF-filen kan innehålla valfritt innehåll, som befintliga kontraktdokument, så att du kan samla in slutanvändarens avtal under användare logga in. Rekommenderade teckenstorlek i PDF-filen är 24.

## <a name="add-terms-of-use"></a>Lägg till användningsvillkor
När du har skapat ditt dokument för användningsvillkor använder du följande metod för att lägga till det.

1. Logga in på Azure som en global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.

1. Gå till **användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

    ![Blad för användningsvillkor](media/active-directory-tou/tou-blade.png)

1. Klicka på **Nya villkor**.

    ![Lägga till användningsvillkor](media/active-directory-tou/new-tou.png)

1. Ange **Namn** för användningsvillkoren

2. Ange **Visningsnamn**.  Den här är den rubrik som användarna ser när de loggar in.

3. **Bläddra** till dina färdigställda användningsvillkor i PDF-form och markera dem.

4. **Välj** ett språk för användningsvillkoren.  Via språkalternativet kan du ladda upp flera användningsvillkor, vart och ett med olika språk.  Versionen av användningsvillkoren som en användare ser baseras på deras inställningar för webbläsaren.

5. För **Kräv att användarna expanderar användningsvillkoren** välj antingen aktivera eller inaktivera.  Om den här inställningen är aktiverad måste slutanvändarna läsa villkoren innan de godkänner dem.

6. Under **Villkorlig åtkomst** kan du **Framtvinga** de uppladdade användningsvillkoren genom att välja en mall från listrutan eller en anpassad villkorlig åtkomstprincip.  Anpassade villkorliga åtkomstprinciper möjliggör detaljerade användningsvillkor för ett specifikt molnprogram eller grupp med användare.  Mer information finns i [konfigurera villkorliga åtkomstprinciper](conditional-access/best-practices.md).

    >[!IMPORTANT]
    >Kontrollerna för principer för villkorlig åtkomst (inklusive användningsvillkoren) stöder inte tvingande för tjänstkonton.  Vi rekommenderar att du exkluderar alla tjänstkonton från principen för villkorlig åtkomst.

7. Klicka på **Skapa**.

8. Om du har markerat en mall för villkorlig åtkomst visas en ny skärm där du kan anpassa certifikatutfärdarprincipen.

    Nu ska du inte längre se dina nya användningsvillkor.

    ![Lägga till användningsvillkor](media/active-directory-tou/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Visa rapport över vem som har godkänts och nekats
Bladet för användningsvillkor visar antalet användare som har godkänt och avböjt. Detta antal och vem som accepterat/avböjt lagras under användningsvillkorens livslängd.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

    ![Granska händelse](media/active-directory-tou/view-tou.png)

1. Klicka på numren under **accepterat** eller **avböjt** för att visa användares aktuella status.

    ![Granska händelse](media/active-directory-tou/accepted-tou.png)

## <a name="view-azure-ad-audit-logs"></a>Öppna Azure AD-granskningsloggar
Om du vill visa ytterligare aktivitet innehåller Azure AD-användningsvillkor granskningsloggar. Varje användargodkännande utlöser en händelse i granskningsloggarna som lagras i 30 dagar. Du kan visa dessa loggar i portalen eller hämta dem som en CSV-fil.

Du kommer igång med Azure AD granskningsloggar, Följ stegen nedan:

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

1. Klicka på **Visa granskningsloggar**.

    ![Granska händelse](media/active-directory-tou/audit-tou.png)

1. På skärmen med Azure AD-granskningsloggar kan du filtrera informationen med listrutorna som finns för att rikta in dig mot specifik granskningslogginformation.

    ![Granska händelse](media/active-directory-tou/audit-logs-tou.png)

1. Du kan också klicka på **Hämta** för att ladda ned informationen i en CSV-fil och använda den lokalt.

## <a name="what-terms-of-use-looks-like-for-users"></a>Hur användningsvillkor ser ut för användare
När användningsvillkor skapas och tillämpas, visas användare, som ingår i omfånget, följande skärm när de loggar in.

![Granska händelse](media/active-directory-tou/user-tou.png)

Följande skärmbild visar hur användningsvillkor ser ut på mobila enheter.

![Granska händelse](media/active-directory-tou/mobile-tou.png)

### <a name="how-users-can-review-their-terms-of-use"></a>Hur användarna kan granska deras användningsvillkor
Användare kan granska och se användningsvillkoren som de har godkänt genom att använda följande procedur.

1. Logga in på [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

1. Klicka på ditt namn i övre det övre högra hörnet och välj **Profil** i listrutan.

    ![Profil](media/active-directory-tou/tou14.png)

1. Klicka på **Granska användningsvillkoren** på din profilsida.

    ![Granska händelse](media/active-directory-tou/tou13a.png)

1. Därifrån kan du granska användningsvillkoren som du har accepterat. 

## <a name="edit-terms-of-use-details"></a>Redigera information om användningsvillkor
Du kan redigera vissa detaljer för användningsvillkor, men du kan inte ändra ett befintligt dokument. Följande procedur beskriver hur du redigerar detaljerna.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

1. Välj användningsvillkoren som du vill redigera.

1. Klicka på **redigera villkoren**.

1. I Använd fönstret Redigera villkoren, byta namn, visningsnamn eller kräva att användarna expanderar värden.

    ![Lägga till användningsvillkor](media/active-directory-tou/edit-tou.png)

1. Klicka på **spara** att spara dina ändringar.

    När du sparar ändringarna har användarna måste godkänna de nya villkoren.

## <a name="add-a-terms-of-use-language"></a>Lägga till användningsvillkor Använd språk
Följande procedur beskriver hur du lägger till användningsvillkor Använd språk.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

1. Välj användningsvillkoren som du vill redigera.

1. I den informationsfönstret klickar du på den **språk** fliken.

    ![Lägga till användningsvillkor](media/active-directory-tou/languages-tou.png)

1. Klicka på **Lägg till språk**.

1. Ladda upp din lokaliserade PDF-fil i Använd språk fönstret Lägg till villkor och Välj språk för.

    ![Lägga till användningsvillkor](media/active-directory-tou/language-add-tou.png)

1. Klicka på **Lägg till** att lägga till språk.

## <a name="delete-terms-of-use"></a>Ta bort användningsvillkor
Du kan ta bort gamla användningsvillkor på följande sätt.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

1. Välj de användningsvillkor du vill ta bort.

1. Klicka på **Ta bort villkor**.

1. På meddelandet som visas som frågar om du vill fortsätta klickar du **Ja**.

    ![Lägga till användningsvillkor](media/active-directory-tou/delete-tou.png)

    Nu bör du inte längre se dina användningsvillkor.

## <a name="deleted-users-and-active-terms-of-use"></a>Borttagna användare och aktiva användningsvillkor
Som standard är en borttagen användare i borttagen status i Azure AD i 30 dagar. Under den här perioden kan användaren återställas av en administratör om det behövs.  Efter 30 dagar tas användaren bort permanent.  En global administratör kan också använda Azure Active Directory-portalen för att uttryckligen och [permanent ta bort en användare som nyligen lagts till](fundamentals/active-directory-users-restore.md) innan perioden är slut.  När en användare har tagits bort permanent tas efterföljande data om den användaren bort från de aktiva användningsvillkoren.  Granskningsinformation om borttagna användare finns kvar i spårningsloggen.

## <a name="policy-changes"></a>Principändringar
Principer för villkorlig åtkomst börjar gälla omedelbart. När detta händer börjar administratören Se ”sorgsna moln” eller ”Azure AD-tokenärenden”. Administratören måste logga ut och logga in igen för att uppfylla den nya principen.

>[!IMPORTANT]
> Användare inom området behöver logga ut och logga in för att uppfylla en ny princip om:
> - en villkorlig åtkomstprincip är aktiverad för ett användningsvillkor
> - eller andra användningsvillkor skapas

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Hur ser jag om/när en användare har godkänt användningsvillkoren?**</br>
S: på villkoren i Använd bladet, klickar du på numret under **godkända**. Du kan också visa eller söka efter accept-aktiviteten i Azure AD granskningsloggar. Mer information finns i [Visa rapport över vem som har godkänts och nekats](#view-who-has-accepted-and-declined) och [öppna Azure AD-granskningsloggar](#view-azure-ad-audit-logs).
 
**F: Hur länge lagras informationen?**</br>
S: användare räknas av villkoren i rapporten för användning och som accepterat/avvisade lagras för resten av användningsvillkoren. Azure AD-granskningen loggar lagras i 30 dagar.

**F: Varför visas ett annat antal medgivanden i villkoren för användning rapporten jämfört med Azure AD som granskningsloggar?**</br>
S: användningsvillkoren Använd rapporten sparas livslängden för de användningsvillkoren när Azure AD-granskningen loggar lagras i 30 dagar. Dessutom visar villkoren i Använd rapporten endast användare aktuella medgivande tillstånd. Till exempel om en användare nekar och sedan accepterar villkoren i Använd rapporten visas bara den användaren Godkänn. Om du vill se historiken kan du använda Azure AD granskningsloggar.

**F: om jag redigera information för användningsvillkor behöver användarna godkänna igen?**</br>
S: Ja, om en administratör redigerar detaljerna för användningsvillkor, användarna måste godkänna de nya villkoren krävs.

**F: kan jag uppdatera ett befintligt dokument med användningsvillkor?**</br>
S: för närvarande kan uppdatera du inte ett befintligt dokument med användningsvillkor. För att ändra dokument med ett användningsvillkor, behöver du skapar en ny för användning instans.

**F: om hyperlänkar finns i PDF-dokument med användningsvillkor, kommer användare kunna klickar du på dem?**</br>
S: PDF-filen renderas som standard som JPEG, så att inte klickbara hyperlänkar. Användare har möjlighet att välja **har du problem med att visa? Klicka här**, vilket visas med PDF-filen internt där hyperlänkar stöds.

**F: Kan användningsvillkor ha stöd för flera språk?**</br>
S: Ja.  Det finns för närvarande 18 olika språk som en administratör kan konfigurera för ett enskilt användningsvillkor. 

**F: När tillämpas användningsvillkoren?**</br>
S: Användningsvillkoren tillämpas under inloggningen.

**F: Vilka program kan jag rikta användningsvillkor mot?**</br>
S: Du kan skapa en princip för villkorlig åtkomst för företagsprogram som använder modern autentisering.  Mer information finns i [enterprise applications](./manage-apps/view-applications-portal.md) (företagsprogram).

**F: Kan jag lägga till flera användningsvillkor till en viss användare eller app?**</br>
S: Ja, genom att skapa flera principer för villkorlig åtkomst för dessa grupper eller program. Användare som omfattas av flera användningsvillkor godkänner ett användningsvillkor i taget.
 
**F: Vad händer om en användare nekar användningsvillkoren?**</br>
S: Användare blockeras från åtkomst till programmet. Användaren behöver logga in igen och Godkänn villkoren för att få åtkomst.
 
**F: är det möjligt att unaccept användningsvillkoren som godkändes tidigare?**</br>
S: du kan [granska tidigare godkända användningsvillkor](#how-users-can-review-their-terms-of-use), men det finns för närvarande inte ett sätt att unaccept.

## <a name="next-steps"></a>Nästa steg

- [Best practices for conditional access in Azure Active Directory](conditional-access/best-practices.md) (Metodtips för villkorlig åtkomst i Azure Active Directory)