---
title: Redigera och hantera ett befintligt paket för åtkomst i Azure AD-rättigheten hantering (förhandsversion) – Azure Active Directory
description: Lär dig mer om att redigera och hantera ett befintligt paket för åtkomst i Azure Active Directory rättigheten hantering (förhandsversion).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce51f4df50de50cef06bba699ca7c6f76bc5d166
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833150"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Redigera och hantera ett befintligt paket för åtkomst i Azure AD rättigheten hantering (förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättigheten management är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ett åtkomst-paket kan du göra konfigurationen en gång av resurser och principer som automatiskt tillämpar åtkomst för resten av åtkomst-paketet. Som en åtkomst-Pakethanteraren kan du när som helst ändra resurser i ett paket för åtkomst utan att behöva bekymra dig om etablering användarens åtkomst till de nya resurserna eller ta bort åtkomsten från föregående resurser. Principer kan också uppdateras när som helst, men principändringarna bara påverkar nya åtkomst.

Den här artikeln beskriver hur du redigera och hantera befintliga åtkomst-paket.

## <a name="add-resource-roles"></a>Lägg till resursroller

En resurs roll är en uppsättning behörigheter som är associerade med en resurs. Hur du göra resurser tillgängliga för användare att begära är genom att lägga till resursroller i åtkomst-paketet. Du kan lägga till resursroller för grupper, program och SharePoint-webbplatser.

**Nödvändiga roll:** Användaradministratör, Catalog ägare eller åtkomst-Pakethanteraren

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket** och öppna sedan paketet åtkomst.

1. I den vänstra menyn klickar du på **resursroller**.

1. Klicka på **lägga till resursroller** att öppna Lägg till resurs-roller för att komma åt paketet på sidan.

    ![Komma åt paketet – lägga till resursroller](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. Utför steg beroende på om du vill lägga till ett program, en grupp eller en SharePoint-webbplats på något av avsnitten för resurs-rollen.

### <a name="add-a-group-resource-role"></a>Lägg till en grupp resource roll

Du kan ha rättigheten management automatiskt lägga till användare i en grupp när de tilldelas en åtkomst-paketet. 

- När en grupp är en del av ett paket för åtkomst och en användare är tilldelad till det åtkomst-paketet, användaren läggs till i gruppen, om det inte redan finns.
- När en användares åtkomst pakettilldelningar upphör att gälla, tas de bort från gruppen, såvida de inte för närvarande har en tilldelning till ett annat åtkomst-paket som innehåller samma grupp.

Du kan välja alla Office 365-grupp eller Azure AD-säkerhetsgruppen.  Administratörer kan lägga till en grupp till en katalog; katalogen ägare kan lägga till en grupp till katalogen om de är ägare av gruppen. Tänk på följande begränsningar för Azure AD när du väljer en grupp:

- När en användare, inklusive en gäst, läggs till som en medlem i en grupp, kan de se alla andra medlemmar i gruppen.
- Azure AD kan inte ändra medlemskap i en grupp som synkroniserades från Windows Server Active Directory med Azure AD Connect.  
- Medlemskapet i dynamiska grupper kan inte uppdateras genom att lägga till eller ta bort en medlem, så att dynamiskt gruppmedlemskap inte lämpar sig för användning med rätt management.

1. På den **Lägg till resursroller för att komma åt paketet** klickar du på **grupper** att öppna fönstret Välj grupper.

1. Välj de grupper som du vill ska ingå i åtkomst-paketet.

    ![Komma åt paketet – lägga till resursroller - grupper](./media/entitlement-management-access-package-edit/group-select.png)

1. Klicka på **Välj**.

1. I den **rollen** väljer **ägare** eller **medlem**.

    Vanligtvis väljer du rollen medlem. Om du väljer rollen ägare som gör det möjligt att lägga till eller ta bort andra medlemmar eller ägare.

    ![Komma åt paketet – Lägg till resurs roll för en grupp](./media/entitlement-management-access-package-edit/group-role.png)

1. Klicka på **Lägg till**.

    Alla användare med befintliga tilldelningar av åtkomst-paketet blir automatiskt medlemmar i den här gruppen när den har lagts till.

### <a name="add-an-application-resource-role"></a>Lägg till en programroll för resursen

Du kan ha Azure AD som automatiskt tilldelas användare åtkomst till ett Azure AD-företagsprogram, inklusive både SaaS-program och din organisations program federerat till Azure AD, när en användare har tilldelats ett paket för åtkomst. För program som integreras med Azure AD via federerad enkel inloggning för utfärdar Azure AD federation-token för användare som är tilldelade till programmet.

Program kan ha flera roller. När du lägger till ett program till en åtkomst-paketet, om programmet har mer än en roll, kommer du behöva ange rätt roll för dessa användare.  Om du utvecklar program, kan du läsa mer om hur dessa roller har angetts till dina program i artikeln om hur du [konfigurera rollanspråk som utfärdats i SAML-token](../develop/active-directory-enterprise-app-role-management.md).

När en programroll är en del av en åtkomst-paketet:

- När en användare har tilldelats åtkomst paketet, användaren läggs till i programrollen, om det inte finns redan.
- När en användares åtkomst pakettilldelningar upphör att gälla, tas deras åtkomst bort från programmet, såvida de inte har en tilldelning till ett annat åtkomst-paket som innehåller programmet rollen.

Följande är några saker när du väljer ett program:

- Program kan också ha grupper som tilldelats till deras roller samt.  Du kan välja att lägga till en grupp i stället för en programroll i ett paket för åtkomst, men sedan programmet kommer inte att visas för användaren som en del av åtkomst-paketet i portalen Mina åtkomst.

1. På den **Lägg till resursroller för att komma åt paketet** klickar du på **program** att öppna fönstret väljer program.

1. Välj de program som du vill ska ingå i åtkomst-paketet.

    ![Komma åt paketet – Lägg till resursroller – Välj program](./media/entitlement-management-access-package-edit/application-select.png)

1. Klicka på **Välj**.

1. I den **rollen** väljer du en programroll.

    ![Komma åt paketet – Lägg till resursrollen för ett program](./media/entitlement-management-access-package-edit/application-role.png)

1. Klicka på **Lägg till**.

    Alla användare med befintliga tilldelningar av åtkomst-paketet får automatiskt åtkomst till det här programmet när den har lagts till.

### <a name="add-a-sharepoint-site-resource-role"></a>Lägg till en resurs för SharePoint platsroll

Azure AD kan automatiskt tilldela användare åtkomst till en SharePoint Online-webbplats eller SharePoint Online-webbplatssamling när de tilldelas en åtkomst-paketet.

1. På den **Lägg till resursroller för att komma åt paketet** klickar du på **SharePoint-webbplatser** att öppna fönstret Välj SharePoint Online-platser.

1. Välj SharePoint Online-platser du vill ska ingå i åtkomst-paketet.

    ![Komma åt paketet – Lägg till resursroller – Välj SharePoint Online-platser](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Klicka på **Välj**.

1. I den **rollen** väljer du en SharePoint Online platsroll.

    ![Komma åt paketet – Lägg till resursrollen för en SharePoint Online-webbplats](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Klicka på **Lägg till**.

    Alla användare med befintliga tilldelningar av åtkomst-paketet får automatiskt åtkomst till SharePoint Online-webbplats när den läggs.

## <a name="remove-resource-roles"></a>Ta bort resursroller

**Nödvändiga roll:** Användaradministratör, Catalog ägare eller åtkomst-Pakethanteraren

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket** och öppna sedan paketet åtkomst.

1. I den vänstra menyn klickar du på **resursroller**.

1. I listan över resursroller, hittar du resursrollen som du vill ta bort.

1. Klicka på ellipsen ( **...** ) och klicka sedan på **ta bort resursrollen**.

    Alla användare med befintliga tilldelningar av åtkomst-paketet har automatiskt deras åtkomst återkallas till den här resursrollen när den tas bort.

## <a name="add-a-new-policy"></a>Lägg till en ny princip

Hur du anger som kan begära ett åtkomst-paket är att skapa en princip. Du kan skapa flera principer för ett paket för enkel åtkomst om du vill tillåta olika uppsättningar med användare som ska beviljas tilldelningar med godkännande och inställningar för giltighetstid. En enda princip kan inte användas för att tilldela interna och externa användare till samma åtkomst till paketet. Du kan dock skapa två principer i samma åtkomst-paket – en för interna användare och en för externa användare. Om det finns flera principer som gäller för en användare, uppmanas de vid tidpunkten för deras begäran till väljer du den princip som de som ska tilldelas till.

Följande diagram visar den övergripande processen att skapa en princip för ett befintligt paket för åtkomst.

![Skapa en princip för process](./media/entitlement-management-access-package-edit/policy-process.png)

**Nödvändiga roll:** Användaradministratör, Catalog ägare eller åtkomst-Pakethanteraren

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket** och öppna sedan paketet åtkomst.

1. Klicka på **principer** och sedan **Lägg till princip**.

1. Skriv ett namn och en beskrivning för principen.

    ![Skapa en princip med namn och beskrivning](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. Baserat på ditt val för **användare som kan begära åtkomst**, utföra stegen i följande avsnitt för principen.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Redigera en befintlig princip

Du kan redigera en princip när som helst. Om du ändrar ett sista giltighetsdatum för en princip, ändras inte utgångsdatumet för begäranden som redan finns i en väntar på godkännande eller godkänt tillstånd.

**Nödvändiga roll:** Användaradministratör, Catalog ägare eller åtkomst-Pakethanteraren

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket** och öppna sedan paketet åtkomst.

1. Klicka på **principer** och klicka sedan på den princip som du vill redigera.

    Den **principinformation** öppnas fönstret längst ned på sidan.

    ![Åtkomst - paketet i informationsfönstret för principen](./media/entitlement-management-access-package-edit/policy-details.png)

1. Klicka på **redigera** att redigera principen.

    ![Paket för åtkomst - Redigera princip](./media/entitlement-management-access-package-edit/policy-edit.png)

1. När du är klar klickar du på **uppdatering**.

## <a name="directly-assign-a-user"></a>Tilldela en användare direkt

I vissa fall kan du direkt tilldela specifika användare till en åtkomst-paketet så att användarna inte behöver gå igenom processen för att begära åtkomst till paketet. Om du vill tilldela användare direkt måste åtkomst till paketet ha en princip som administratören direkt tilldelningar.

**Nödvändiga roll:** Användaradministratör, Catalog ägare eller åtkomst-Pakethanteraren

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket** och öppna sedan paketet åtkomst.

1. I den vänstra menyn klickar du på **tilldelningar**.

1. Klicka på **ny tilldelning** att öppna Lägg till användare i åtkomst-paket.

    ![Tilldelningar – Lägg till användare i åtkomst-paket](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Klicka på **lägga till användare** att markera de användare som du vill tilldela åtkomst-paketet till.

1. I den **Välj princip** väljer du en princip som har den [None (administratör direkt tilldelningar endast)](#policy-none-administrator-direct-assignments-only) inställningen.

    Om det här åtkomst-paketet inte har den här typen av princip, kan du klicka **Skapa ny princip** att lägga till ett.

1. Ange datum och tid som du vill att de valda användarna tilldelas börja och sluta. Om ett slutdatum inte är anges så används principinställningar upphör att gälla.

1. Du kan också ange en motivering för din direkttilldelning för arkivering.

1. Klicka på **Lägg till** direkt tilldela de valda användarna till åtkomst-paketet.

    Efter en liten stund klickar du på **uppdatera** att se användarna i listan över tilldelningar.

## <a name="view-who-has-an-assignment"></a>Visa vem som har en tilldelning

**Nödvändiga roll:** Användaradministratör, Catalog ägare eller åtkomst-Pakethanteraren

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket** och öppna sedan paketet åtkomst.

1. Klicka på **tilldelningar** att se en lista över aktiva uppgifter.

1. Klicka på en viss tilldelning för att se ytterligare information.

1. Om du vill se en lista över tilldelningar som inte har alla resursroller som har etablerats korrekt, klicka på filtret status och välj **leverera**.

    Du kan se ytterligare information om leverans fel genom att leta upp motsvarande av användaren på den **begäranden** sidan.

1. Om du vill se har upphört att gälla tilldelningar, klickar du på den filter och välj **har upphört att gälla**.

1. Om du vill hämta en CSV-fil med den filtrerade listan, klickar du på **hämta**.

## <a name="view-requests"></a>Visa begäranden

**Nödvändiga roll:** Användaradministratör, Catalog ägare eller åtkomst-Pakethanteraren

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket** och öppna sedan paketet åtkomst.

1. Klicka på **begäranden**.

1. Klicka på en specifik begäran att se ytterligare information.

## <a name="view-a-requests-delivery-errors"></a>Visa en förfrågan leverans fel

**Nödvändiga roll:** Användaradministratör, Catalog ägare eller åtkomst-Pakethanteraren

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket** och öppna sedan paketet åtkomst.

1. Klicka på **begäranden**.

1. Välj den begäran som du vill visa.

    Om begäran har fel leverans, status för begäran kommer att **Undelivered** och vilken underordnad status kommer att **delvis levereras**.

    Om det finns några delivery-fel i informationsfönstret för denna begäran, kommer det finnas en uppräkning av leverans fel.

1. Klicka på räkna om du vill se alla fel för leverans av på begäran.

## <a name="cancel-a-pending-request"></a>Avbryt en väntande begäran

Du kan bara avbryta en väntande begäran som inte har levererats.

**Nödvändiga roll:** Användaradministratör, Catalog ägare eller åtkomst-Pakethanteraren

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket** och öppna sedan paketet åtkomst.

1. Klicka på **begäranden**.

1. Klicka på begäran som du vill avbryta

1. I informationsfönstret i begäran klickar du på **avbrottsbegäran**.

## <a name="copy-my-access-portal-link"></a>Kopiera Mina åtkomst portal länk

De flesta användare i din katalog kan logga in på portalen Mina åtkomst och automatiskt visa en lista över de kan begära åtkomst-paket. För externa partner företagsanvändare som inte ännu finns i din katalog, kommer du dock behöva skicka dem en länk som de kan använda för att begära ett åtkomst-paket. Så länge paketet åtkomst är aktiverat för externa användare och du har en princip för den externa användaren katalog, kan den externa användaren använda länken Mina åtkomst portal för att begära åtkomst till paketet.

**Nödvändiga roll:** Användaradministratör, Catalog ägare eller åtkomst-Pakethanteraren

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket** och öppna sedan paketet åtkomst.

1. På sidan Översikt kopiera den **portalen Mina åtkomstlänk**.

    ![Översikt över åtkomst paketet - portal Mina åtkomst-länk](./media/entitlement-management-shared/my-access-portal-link.png)

1. E-post eller skicka en länk till din externa affärspartner. De kan dela länken med sina användare att begära åtkomst till paketet.

## <a name="change-the-hidden-setting"></a>Ändra Hidden-inställningen

Åtkomst-paket är synliga som standard. Det innebär att om en princip kan en användare att begära åtkomst-paketet automatiskt visas åtkomst paketet visas i den här åtkomst-portalen.

**Nödvändiga roll:** Användaradministratör, Catalog ägare eller åtkomst-Pakethanteraren

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket** och öppna sedan paketet åtkomst.

1. På sidan Översikt **redigera**.

1. Ange den **Hidden** inställningen.

    Om inställd **nr**, åtkomst paketet visas i min åtkomst användarportalen.

    Om inställd **Ja**, åtkomst paketet inte visas i min åtkomst användarportalen. Det enda sättet som en användare kan visa åtkomst-paketet är om de har direkt **portalen Mina åtkomstlänk** till åtkomst-paketet.

## <a name="delete"></a>Radera

Ett åtkomst-paket kan bara tas bort om det inte använder aktiv användare.

**Nödvändiga roll:** Användaradministratör, Catalog ägare eller åtkomst-Pakethanteraren

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket** och öppna sedan paketet åtkomst.

1. I den vänstra menyn klickar du på **tilldelningar** och ta bort åtkomst för alla användare.

1. I den vänstra menyn klickar du på **översikt** och klicka sedan på **ta bort**.

1. Klicka på Ta borttagningsmeddelandet **Ja**.

## <a name="when-are-changes-applied"></a>När tillämpas ändringarna

I rätt management bearbetas Azure AD samtidigt ändringar för tilldelning och resurser i ditt åtkomst-paket flera gånger per dag. Så om du gör en tilldelning eller ändra resursroller för ditt åtkomst-paket, kan det ta upp till 24 timmar för att ändringen ska göras i Azure AD, samt hur lång tid det tar för att överföra ändringarna till andra Microsoft Online Services eller anslutna SaaS-program s. Om ändringen påverkar bara några objekt, tar ändringen sannolikt bara några minuter att tillämpa i Azure AD, varefter andra Azure AD-komponenter då identifieras som ändra och uppdatera SaaS-program. Om ändringen påverkar tusentals objekt, tar ändringen längre tid. Till exempel om du har ett paket för åtkomst med 2 program och 100 användartilldelningar och du vill lägga till en SharePoint-platsroll i åtkomst-paket, kan det finnas en fördröjning tills alla användare som ingår i den SharePoint-platsrollen. Du kan övervaka förloppet via Azure AD-spårningsloggen, loggen för Azure AD-etablering och granskningsloggar för SharePoint-platsen.

## <a name="next-steps"></a>Nästa steg

- [Begär processen och e-postaviseringar](entitlement-management-process.md)
