---
title: ta med fil
description: ta med fil
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 7fd716be397d9ef6b9d6132cd4470f653f3cea0f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655954"
---
## <a name="for-users-in-your-directory"></a>För användare i katalogen

Följ dessa steg om du vill tillåta användare i katalogen att kunna begära det här åtkomstpaketet. När du definierar begäraneprincipen kan du ange enskilda användare eller oftare grupper av användare. Din organisation kanske till exempel redan har en grupp som **Alla medarbetare**.  Om den gruppen läggs till i principen för användare som kan begära åtkomst kan alla medlemmar i den gruppen begära åtkomst.

1. Klicka på För användare i **katalogen i**avsnittet Användare som kan begära **åtkomst** .

    När du väljer det här alternativet visas nya alternativ för att ytterligare förfina vem i katalogen som kan begära det här åtkomstpaketet.

    ![Åtkomstpaket - Förfrågningar - För användare i din katalog](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Välj något av följande alternativ:

    |  |  |
    | --- | --- |
    | **Specifika användare och grupper** | Välj det här alternativet om du bara vill att de användare och grupper i katalogen som du anger ska kunna begära det här åtkomstpaketet. |
    | **Alla medlemmar (utom gäster)** | Välj det här alternativet om du vill att alla medlemsanvändare i katalogen ska kunna begära det här åtkomstpaketet. Det här alternativet innehåller inga gästanvändare som du kanske har bjudit in till din katalog. |
    | **Alla användare (inklusive gäster)** | Välj det här alternativet om du vill att alla medlemsanvändare och gästanvändare i katalogen ska kunna begära det här åtkomstpaketet. |

    Gästanvändare refererar till externa användare som har bjudits in till din katalog med [Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md). Mer information om skillnaderna mellan medlemsanvändare och gästanvändare finns [i Vilka är standardanvändarbehörigheterna i Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md)

1. Om du har valt **Specifika användare och grupper**klickar du på Lägg till användare och **grupper**.

1. Markera de användare och grupper som du vill lägga till i fönstret Välj användare och grupper.

    ![Åtkomstpaket - Förfrågningar - Välj användare och grupper](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Klicka på **Markera** om du vill lägga till användare och grupper.

1. Hoppa ner till avsnittet [Godkännande.](#approval)

## <a name="for-users-not-in-your-directory"></a>För användare som inte finns i katalogen

 **Användare som inte finns i katalogen** refererar till användare som finns i en annan Azure AD-katalog eller domän. Dessa användare kanske ännu inte har bjudits in till din katalog. Azure AD-kataloger måste konfigureras för att tillåta inbjudningar i **samarbetsbegränsningar**. Mer information finns i [Aktivera B2B externt samarbete och hantera vem som kan bjuda in gäster](../articles/active-directory/b2b/delegate-invitations.md).

> [!NOTE]
> Ett gästanvändarkonto skapas för en användare som ännu inte finns i din katalog vars begäran har godkänts eller godkänts automatiskt. Gästen kommer att bjudas in, men kommer inte att få en inbjudan e-post. Istället får de ett e-postmeddelande när deras åtkomstpakettilldelning levereras. Som standard, senare när gästanvändaren inte längre har några åtkomstpakettilldelningar, eftersom deras senaste tilldelning har upphört att gälla eller annullerats, kommer gästanvändarkontot att blockeras från inloggning och därefter tas bort. Om du vill att gästanvändare ska finnas kvar i katalogen på obestämd tid, även om de inte har några åtkomstpakettilldelningar, kan du ändra inställningarna för rättighetshanteringskonfigurationen. Mer information om gästanvändarobjektet finns i [Egenskaper för en Azure Active Directory B2B-samarbetsanvändare](../articles/active-directory/b2b/user-properties.md).

Gör så här om du vill tillåta användare som inte finns i katalogen att begära det här åtkomstpaketet:

1. Klicka på För användare som **inte finns i katalogen i**avsnittet **Användare som kan begära åtkomst** .

    När du väljer det här alternativet visas nya alternativ.

    ![Åtkomstpaket - Begäranden - För användare som inte finns i katalogen](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Välj något av följande alternativ:

    |  |  |
    | --- | --- |
    | **Specifika anslutna organisationer** | Välj det här alternativet om du vill välja från en lista över organisationer som administratören tidigare har lagt till. Alla användare från de valda organisationerna kan begära det här åtkomstpaketet. |
    | **Alla anslutna organisationer** | Välj det här alternativet om alla användare från alla anslutna organisationer kan begära det här åtkomstpaketet. |
    | **Alla användare (Alla anslutna organisationer + alla nya externa användare)** | Välj det här alternativet om alla användare från alla anslutna organisationer kan begära det här åtkomstpaketet och att B2B tillåter eller nekar listinställningar ska ha företräde för alla nya externa användare. |

    En ansluten organisation är en extern Azure AD-katalog eller domän som du har en relation till.

1. Om du har valt **Specifika anslutna organisationer**klickar du på Lägg till **kataloger** för att välja från en lista över anslutna organisationer som administratören tidigare har lagt till.

1. Skriv namnet eller domännamnet för att söka efter en tidigare ansluten organisation.

    ![Åtkomstpaket - Förfrågningar - Välj kataloger](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Om den organisation som du vill samarbeta med inte finns i listan kan du be administratören att lägga till den som en ansluten organisation. Mer information finns i [Lägga till en ansluten organisation](../articles/active-directory/governance/entitlement-management-organization.md).

1. När du har valt alla anslutna organisationer klickar du på **Välj**.

    > [!NOTE]
    > Alla användare från de valda anslutna organisationerna kan begära det här åtkomstpaketet. Detta inkluderar användare i Azure AD från alla underdomäner som är associerade med organisationen, såvida inte dessa domäner blockeras av Azure B2B-listan. Mer information finns i [Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer](../articles/active-directory/b2b/allow-deny-list.md).

1. Hoppa ner till avsnittet [Godkännande.](#approval)

## <a name="none-administrator-direct-assignments-only"></a>Ingen (endast administratörsdirekt tilldelningar)

Följ dessa steg om du vill kringgå åtkomstbegäranden och tillåta administratörer att direkt tilldela specifika användare till det här åtkomstpaketet. Användarna behöver inte begära åtkomstpaketet. Du kan fortfarande ange livscykelinställningar, men det finns inga inställningar för begäran.

1. Klicka på Ingen i avsnittet **Användare som kan begära åtkomst** **(endast administratörsdirekttilldelningar**.

    ![Åtkomstpaket - Begäranden - Endast administratörs direkta tilldelningar](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    När du har skapat åtkomstpaketet kan du direkt tilldela specifika interna och externa användare till åtkomstpaketet. Om du anger en extern användare skapas ett gästanvändarkonto i katalogen. Information om hur du direkt tilldelar en användare finns i [Visa, lägga till och ta bort tilldelningar för ett åtkomstpaket](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Hoppa ned till avsnittet [Aktivera begäranden.](#enable-requests)

## <a name="approval"></a>Godkännande

I avsnittet Godkännande anger du om ett godkännande krävs när användare begär det här åtkomstpaketet. Godkännandeinställningarna fungerar på följande sätt:

- Endast en av de valda godkännare eller reservgodstagare behöver godkänna en begäran om godkännande i ett steg. 
- Endast en av de valda godkännarna från varje steg behöver godkänna en begäran om tvåstegsgodkännande.
- Godkännaren kan vara en manager, intern sponsor eller extern sponsor beroende på vem principen styr åtkomsten.
- Godkännande från varje vald godkännare krävs inte för godkännande i ett eller två steg.
- Godkännandebeslutet baseras på den som godkänner granskningen först.

En demonstration av hur du lägger till godkännare i en begäraneprincip finns i följande video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

En demonstration av hur du lägger till ett godkännande i flera steg i en begäraneprincip finns i följande video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Följ dessa steg för att ange godkännandeinställningar för begäranden för åtkomstpaketet:

1. Om du vill kräva godkännande för begäranden från de valda användarna anger du **växlingsknappen Kräv godkännande** till **Ja**. Om du vill att begäranden ska godkännas automatiskt anger du växlingsknappen till **Nej**.

1. Om du vill att användarna ska ange en motivering för att begära åtkomstpaketet anger du växlingsknappen **Kräv begäranre** till **Ja**.
    
1. Ta nu reda på om begäranden kräver godkännande i ett eller två steg. Ange **hur många steg** växla till **1** för godkännande i ett steg eller ställ in växlingsknappen till **2** för 2-stegsgodkännande.

    ![Åtkomstpaket - Begäranden - Inställningar för godkännande](./media/active-directory-entitlement-management-request-policy/approval.png)

Följ följande steg för att lägga till godkännare när du har valt hur många steg du behöver: 

### <a name="single-stage-approval"></a>Godkännande i ett steg

1. Lägg till den **första godkännaren:**
    
    Om principen är inställd på att styra åtkomsten för användare i katalogen kan du välja **Hanteraren som godkännare**. Du kan också lägga till en viss användare genom att klicka på **Lägg till godkännare** när du har valt Välj specifika godkännare på den nedrullningsbara menyn.
    
    ![Åtkomstpaket - Begäranden - För användare i katalogen - Första godkännaren](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Om den här principen är inställd på att styra åtkomsten för användare som inte finns i katalogen kan du välja **Extern sponsor** eller **Intern sponsor**. Du kan också lägga till en viss användare genom att klicka på **Lägg till godkännare** eller grupper under Välj specifika godkännare.
    
    ![Åtkomstpaket - Begäranden - För användare utanför katalogen - Första godkännaren](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Om du **valdeHanteraren** som första godkännare klickar du på **Lägg till återställning** för att välja en eller flera användare eller grupper i katalogen som reservhanterare. Återgångsgodkänningsansvariga får begäran om rättighetshanteringen inte kan hitta chefen för den användare som begär åtkomst.

    Chefen hittas av rättighetshantering med attributet **Manager.** Attributet finns i användarens profil i Azure AD. Mer information finns i [Lägga till eller uppdatera en användares profilinformation med Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Om du har valt **Välj specifika godkännare**klickar du på **Lägg till godkännare** för att välja en eller flera användare eller grupper i katalogen som godkännare.

1. I rutan under **Beslut måste göras i hur många dagar?**

    Om en begäran inte godkänns inom denna tidsperiod nekas den automatiskt. Användaren måste skicka en annan begäran om åtkomstpaketet.

1. Om du vill kräva att godkännare anger en motivering för sitt beslut anger du Begär godkännande motivering till **Ja**.

    Motiveringen är synlig för andra godkännare och beställaren.

### <a name="2-stage-approval"></a>Godkännande i två steg

Om du har valt ett godkännande i två steg måste du lägga till en andra godkännare.

1. Lägg till den **andra godkännaren:** 
    
    Om användarna finns i katalogen lägger du till en viss användare som den andra godkännaren genom att klicka på **Lägg till godkännare** under Välj specifika godkännare.

    ![Åtkomstpaket - Begäranden - För användare i katalogen - Andra godkännaren](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Om användarna inte finns i katalogen väljer du **Intern sponsor** eller **Extern sponsor** som den andra godkännaren. När du har valt godkännaren lägger du till reservgodsen.

    ![Åtkomstpaket - Begäranden - För användare utanför katalogen - Andra godkännaren](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Ange hur många dagar den andra godkännaren måste godkänna begäran i rutan under **Beslut måste göras inom hur många dagar?**. 

1. Ange växlingsknappen Kräv godkännare till **Ja** eller **Nej**.

### <a name="alternate-approvers"></a>Alternativa godkännare

Du kan ange alternativa godkännare, på samma sätt som att ange de första och andra godkännare som kan godkänna begäranden. Med alternativa godkännare kommer att bidra till att säkerställa att begärandena godkänns eller nekas innan de löper ut (timeout). Du kan lista alternativa godkännare den första godkännaren och den andra godkännaren för tvåstegsgodkännande. 

Genom att ange alternativa godkännare, i händelse av att de första eller andra godkännarna inte kunde godkänna eller neka begäran, vidarebefordras den väntande begäran till de alternativa godkännarna, enligt det vidarebefordringsschema som du angav under principinställningen. De får ett e-postmeddelande för att godkänna eller neka den väntande begäran.

När begäran har vidarebefordrats till de alternativa godkännarna kan de första eller andra godkännarna fortfarande godkänna eller neka begäran. Alternativa godkännare använder samma My Access-webbplats för att godkänna eller neka den väntande begäran.

Vi kan lista personer eller grupper av personer som godkännare och alternativa godkännare. Se till att du listar olika uppsättningar personer som ska vara de första, andra och alternativa godkännare.
Om du till exempel listade Alice och Bob som den eller de första godkännaren anger Carol och Dave som alternativa godkännare. Så här lägger du till alternativa godkännare i ett åtkomstpaket:

1. Klicka på **Visa avancerade inställningar för begäran**under den första godkännaren, den andra godkännaren eller båda.

    ![Åtkomstpaket - Princip - Visa inställningar för avancerade begäranden](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Ange **Om ingen åtgärd vidtas, vidarebefordra till alternativa godkännare?** växla till **Ja**.

1. Klicka på **Lägg till alternativa godkännare** och välj alternativa godkännare i listan.

    ![Åtkomstpaket - Princip - Lägg till alternativa godkännare](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. I rutan **Vidarebefordra till alternativ godkännare efter hur många dagar** har de dagar som godkännarna måste godkänna eller neka en begäran. Om inga godkännare har godkänt eller nekat begäran före begärans varaktighet upphör begäran att gälla (timeout) och användaren måste skicka en ny begäran om åtkomstpaketet. 

    Begäranden kan endast vidarebefordras till alternativa godkännare en dag efter att begärans varaktighet har nått halveringstiden. I det här exemplet är varaktigheten för begäran 14 dagar. Så når begäran varaktighet halveringstid på dag 7. Så begäran kan inte vidarebefordras tidigare än dag 8. Det går inte heller att vidarebefordra begäranden den sista dagen i begäran. Så i exemplet kan den senaste begäran vidarebefordras dag 13.

## <a name="enable-requests"></a>Aktivera begäranden

1. Om du vill att åtkomstpaketet ska göras omedelbart tillgängligt för användare i begäran om att begära flyttar du växlingsknappen Aktivera till **Ja**.

    Du kan alltid aktivera det i framtiden när du har skapat åtkomstpaketet.

    Om du har valt **Ingen (endast administratörsdirekta tilldelningar)** och du anger aktivera **Nej**kan administratörer inte tilldela det här åtkomstpaketet direkt.

    ![Åtkomstpaket - Principinställning Aktivera principinställning](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Klicka på **Nästa**.
