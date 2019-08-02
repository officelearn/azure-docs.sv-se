---
title: Redigera och hantera ett befintligt Access-paket i Azure AD-hantering av rättigheter (för hands version) – Azure Active Directory
description: Lär dig hur du redigerar och hanterar ett befintligt Access-paket i Azure Active Directory rättighets hantering (för hands version).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17c85d53d05193313f9e166b88beb2a0f82eb197
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618367"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Redigera och hantera ett befintligt Access-paket i Azure AD-hantering av rättigheter (för hands version)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med ett Access-paket kan du utföra en enstaka konfiguration av resurser och principer som automatiskt administrerar åtkomst för åtkomst paketets livs längd. Som en Access Package Manager kan du när som helst ändra resurserna i ett Access-paket utan att behöva oroa dig för att du ska få åtkomst till de nya resurserna eller ta bort åtkomsten från de tidigare resurserna. Principer kan också uppdateras när som helst, men princip ändringarna påverkar bara nya åtkomster.

I den här artikeln beskrivs hur du redigerar och hanterar befintliga åtkomst paket.

## <a name="add-resource-roles"></a>Lägg till resurs roller

En resurs roll är en samling behörigheter som är kopplade till en resurs. Hur du gör resurser som är tillgängliga för användare att begära är att lägga till resurs roller till ditt åtkomst paket. Du kan lägga till resurs roller för grupper, program och SharePoint-webbplatser.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **resurs roller**på den vänstra menyn.

1. Klicka på **Lägg till resurs roller** för att öppna sidan Lägg till resurs roller för åtkomst till paket.

    ![Åtkomst paket – Lägg till resurs roller](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. Beroende på om du vill lägga till en grupp, ett program eller en SharePoint-webbplats utför du stegen i någon av följande resurs Rolls avsnitt.

### <a name="add-a-group-resource-role"></a>Lägg till en grupp resurs roll

Du kan låta rättighets hantering automatiskt lägga till användare till en grupp när de tilldelas ett åtkomst paket. 

- När en grupp ingår i ett Access-paket och en användare tilldelas till det åtkomst paketet, läggs användaren till i gruppen om den inte redan finns.
- När en användares åtkomst paket tilldelning upphör tas de bort från gruppen, om de inte har en tilldelning till ett annat Access-paket som innehåller samma grupp.

Du kan välja valfri Office 365-grupp eller Azure AD-säkerhetsgrupp.  Administratörer kan lägga till valfri grupp i en katalog. Katalog ägare kan lägga till valfri grupp till katalogen om de är ägare till gruppen. Behåll följande Azure AD-begränsningar i åtanke när du väljer en grupp:

- När en användare, inklusive en gäst, läggs till som en medlem i en grupp kan de Se alla andra medlemmar i gruppen.
- Azure AD kan inte ändra medlemskap för en grupp som har synkroniserats från Windows Server Active Directory att använda Azure AD Connect eller som skapades i Exchange Online som en distributions grupp.  
- Det går inte att uppdatera medlemskapet i dynamiska grupper genom att lägga till eller ta bort en medlem, så dynamiska grupp medlemskap är inte lämpliga för användning med hantering av rättigheter.

1. Öppna fönstret Välj grupper genom att klicka på **grupper** på sidan **Lägg till resurs roller för åtkomst till paket** .

1. Välj de grupper som du vill ta med i åtkomst paketet.

    ![Åtkomst paket – Lägg till resurs roller – Välj grupper](./media/entitlement-management-access-package-edit/group-select.png)

1. Klicka på **Välj**.

1. Välj **ägare** eller **medlem**i listan **roll** .

    Vanligt vis väljer du medlems rollen. Om du väljer ägar rollen så att användarna kan lägga till eller ta bort andra medlemmar eller ägare.

    ![Åtkomst paket – Lägg till resurs roll för en grupp](./media/entitlement-management-access-package-edit/group-role.png)

1. Klicka på **Lägg till**.

    Alla användare med befintliga tilldelningar till åtkomst paketet blir automatiskt medlemmar i den här gruppen när de läggs till.

### <a name="add-an-application-resource-role"></a>Lägg till en program resurs roll

Du kan låta Azure AD automatiskt tilldela användare åtkomst till ett Azure AD Enterprise-program, inklusive både SaaS-program och din organisations program federerade till Azure AD, när en användare tilldelas ett Access-paket. För program som integreras med Azure AD via federerad enkel inloggning utfärdar Azure AD federationsmetadata för användare som är tilldelade till programmet.

Program kan ha flera roller. När du lägger till ett program i ett Access-paket, om det programmet har fler än en roll, måste du ange rätt roll för dessa användare.  Om du utvecklar program kan du läsa mer om hur dessa roller tillhandahålls för dina program i artikeln om hur du [konfigurerar det roll anspråk som utfärdats i SAML](../develop/active-directory-enterprise-app-role-management.md)-token.

När en program roll är en del av ett Access-paket:

- När en användare tilldelas ett åtkomst paket läggs användaren till i program rollen, om den inte redan finns.
- När en användares åtkomst paket tilldelning går ut tas deras åtkomst bort från programmet, om de inte har en tilldelning till ett annat Access-paket som innehåller den program rollen.

Här följer några saker att tänka på när du väljer ett program:

- Program kan också ha grupper som har tilldelats till deras roller också.  Du kan välja att lägga till en grupp i stället för en program roll i ett Access-paket, men programmet kommer inte att vara synligt för användaren som en del av Access-paketet i åtkomst portalen.

1. Öppna fönstret Välj program genom att klicka på **program** på sidan **Lägg till resurs roller för åtkomst till paket** .

1. Välj de program som du vill ta med i åtkomst paketet.

    ![Åtkomst paket – Lägg till resurs roller – Välj program](./media/entitlement-management-access-package-edit/application-select.png)

1. Klicka på **Välj**.

1. Välj en program roll i listan **roll** .

    ![Åtkomst paket – Lägg till resurs roll för ett program](./media/entitlement-management-access-package-edit/application-role.png)

1. Klicka på **Lägg till**.

    Alla användare med befintliga tilldelningar till åtkomst paketet får automatiskt åtkomst till det här programmet när det läggs till.

### <a name="add-a-sharepoint-site-resource-role"></a>Lägg till en resurs roll för SharePoint-webbplats

Azure AD kan automatiskt tilldela användare åtkomst till en SharePoint Online-webbplats eller SharePoint Online-webbplatssamling när de tilldelas ett åtkomst paket.

1. På sidan **Lägg till resurs roller att komma åt paket** klickar du på **SharePoint-webbplatser** för att öppna fönstret Välj SharePoint Online-webbplatser.

1. Välj de SharePoint Online-webbplatser som du vill ta med i åtkomst paketet.

    ![Åtkomst paket – Lägg till resurs roller – Välj SharePoint Online-webbplatser](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Klicka på **Välj**.

1. I listan **roll** väljer du en SharePoint Online-webbplats roll.

    ![Åtkomst paket – Lägg till resurs roll för en SharePoint Online-webbplats](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Klicka på **Lägg till**.

    Alla användare med befintliga tilldelningar till åtkomst paketet får automatiskt åtkomst till den här SharePoint Online-webbplatsen när den läggs till.

## <a name="remove-resource-roles"></a>Ta bort resurs roller

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **resurs roller**på den vänstra menyn.

1. Leta upp den resurs roll som du vill ta bort i listan över resurs roller.

1. Klicka på ellipsen ( **...** ) och sedan på **ta bort resurs roll**.

    Alla användare med befintliga tilldelningar till åtkomst paketet får automatiskt åtkomst till den här resurs rollen när den tas bort.

## <a name="add-a-new-policy"></a>Lägg till en ny princip

Hur du anger vem som kan begära ett Access-paket är att skapa en princip. Du kan skapa flera principer för ett enda åtkomst paket om du vill att olika uppsättningar av användare ska kunna tilldelas tilldelningar med olika inställningar för godkännande och förfallo datum. En enda princip kan inte användas för att tilldela interna och externa användare till samma Access-paket. Du kan dock skapa två principer i samma Access-paket – ett för interna användare och en för externa användare. Om det finns flera principer som gäller för en användare, uppmanas de vid tidpunkten för begäran att välja den princip som de vill tilldelas.

Följande diagram visar den övergripande processen för att skapa en princip för ett befintligt Access-paket.

![Skapa en princip process](./media/entitlement-management-access-package-edit/policy-process.png)

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **principer** och **Lägg sedan till princip**.

1. Ange ett namn och en beskrivning för principen.

    ![Skapa en princip med namn och beskrivning](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. Baserat på ditt val för **användare som kan begära åtkomst**, utför stegen i någon av följande princip avsnitt.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Redigera en befintlig princip

Du kan redigera en princip när du vill. Om du ändrar utgångs datumet för en princip ändras inte förfallo datumet för begär Anden som redan har statusen väntar på godkännande eller godkänt.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **principer** och sedan på den princip som du vill redigera.

    Fönstret **princip information** visas längst ned på sidan.

    ![Åtkomst paket – princip informations fönstret](./media/entitlement-management-access-package-edit/policy-details.png)

1. Redigera principen genom att klicka på **Redigera** .

    ![Åtkomst paket – redigera princip](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Klicka på **Uppdatera**när du är färdig.

## <a name="directly-assign-a-user"></a>Tilldela en användare direkt

I vissa fall kanske du vill tilldela vissa användare direkt till ett Access-paket så att användarna inte behöver gå igenom processen för att begära åtkomst paketet. För att kunna tilldela användare direkt måste åtkomst paketet ha en princip som tillåter administratörs direkta tilldelningar.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på tilldelningar på denvänstra menyn.

1. Klicka på **ny tilldelning** för att öppna Lägg till användare för att komma åt paketet.

    ![Tilldelningar – Lägg till användare till Access-paket](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Klicka på **Lägg till användare** för att välja de användare som du vill tilldela åtkomst paketet till.

1. I listan **Välj princip** väljer du en princip som har inställningen [Ingen (endast administratörers direkta tilldelningar)](#policy-none-administrator-direct-assignments-only) .

    Om det här åtkomst paketet inte har den här typen av princip kan du klicka på **Skapa ny princip** för att lägga till ett.

1. Ange datum och tid då du vill att de valda användarnas tilldelning ska starta och sluta. Om inget slutdatum anges används principens förfallo inställningar.

1. Du kan också ange en motivering för den direkta tilldelningen för post hållning.

1. Klicka på **Lägg till** för att tilldela de valda användarna direkt till åtkomst paketet.

    Efter en liten stund klickar du på **Uppdatera** för att se användarna i listan tilldelningar.

## <a name="view-who-has-an-assignment"></a>Visa vem som har en tilldelning

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på tilldelningar för att visa en lista över aktiva tilldelningar.

1. Klicka på en uppgift om du vill visa mer information.

1. Om du vill se en lista över tilldelningar som inte har alla resurs roller korrekt etablerade klickar du på filter status och väljer **leverera**.

    Du kan se ytterligare information om leverans fel genom att leta upp användarens motsvarande begäran på sidan **begär Anden** .

1. Om du vill se förfallna tilldelningar klickar du på filter status och väljer **upphör att gälla**.

1. Klicka på **Hämta**om du vill ladda ned en CSV-fil för den filtrerade listan.

## <a name="view-requests"></a>Visa begär Anden

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **begär Anden**.

1. Klicka på en enskild begäran om du vill visa mer information.

## <a name="view-a-requests-delivery-errors"></a>Visa leverans fel för en begäran

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **begär Anden**.

1. Välj den begäran som du vill visa.

    Om begäran har några leverans fel kommer status för begäran inte att levereras **och** del statusen kommer att **levereras delvis**.

    Om det finns några leverans fel i förfrågningens informations fönster, finns det ett antal leverans fel.

1. Klicka på antalet för att se alla begärans leverans fel.

## <a name="cancel-a-pending-request"></a>Avbryt en väntande begäran

Du kan bara avbryta en väntande begäran som ännu inte har levererats.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **begär Anden**.

1. Klicka på den begäran som du vill avbryta

1. Klicka på **Avbryt förfrågan**i informations fönstret för begäran.

## <a name="copy-my-access-portal-link"></a>Kopiera min åtkomst Portal länk

De flesta användare i din katalog kan logga in på portalen My Access och automatiskt se en lista över de åtkomst paket som de kan begära. Men för externa affärs partner användare som ännu inte finns i din katalog måste du skicka dem en länk som de kan använda för att begära ett Access-paket. Så länge åtkomst paketet är aktiverat för externa användare och du har en princip för den externa användarens katalog, kan den externa användaren använda länken My Access Portal för att begära åtkomst paketet.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. På sidan Översikt kopierar du **länken min åtkomst Portal**.

    ![Översikt över åtkomst paket – länken min åtkomst Portal](./media/entitlement-management-shared/my-access-portal-link.png)

1. E-posta eller skicka länken till din externa affärs partner. De kan dela länken med sina användare för att begära åtkomst paketet.

## <a name="change-the-hidden-setting"></a>Ändra den dolda inställningen

Åtkomst paketen kan identifieras som standard. Det innebär att om en princip tillåter att en användare begär åtkomst paketet, ser de automatiskt Access-paketet som anges i deras åtkomst Portal.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. På sidan Översikt klickar du på **Redigera**.

1. Ange den **dolda** inställningen.

    Om värdet är **Nej**visas Access-paketet i användarens Mina åtkomst Portal.

    Om det är inställt på **Ja**visas inte Access-paketet i användarens min åtkomst Portal. Det enda sättet som en användare kan visa åtkomst paketet är om de har länken Direct **My Access Portal** till Access-paketet.

## <a name="delete"></a>Ta bort

Ett Access-paket kan bara tas bort om det inte har några aktiva användar tilldelningar.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på tilldelningar på den vänstra menyn och ta bort åtkomsten för alla användare.

1. I den vänstra menyn klickar du på **Översikt** och sedan på **ta bort**.

1. Klicka på **Ja**i det borttagnings meddelande som visas.

## <a name="when-are-changes-applied"></a>När tillämpas ändringar

I hantering av rättigheter bearbetar Azure AD Mass ändringar för tilldelning och resurser i dina åtkomst paket flera gånger per dag. Så om du gör en tilldelning eller ändrar resurs rollerna för ditt åtkomst paket, kan det ta upp till 24 timmar innan ändringen görs i Azure AD, plus den tid det tar att sprida ändringarna till andra Microsoft Online Services eller anslutna SaaS-program na. Om din ändring bara påverkar några få objekt tar ändringen troligen bara några minuter att tillämpa i Azure AD, efter vilka andra Azure AD-komponenter kommer att identifiera ändringen och uppdatera SaaS-programmen. Om din ändring påverkar tusentals objekt tar ändringen längre tid. Om du till exempel har ett Access-paket med 2 program och 100 användar tilldelningar och du bestämmer dig för att lägga till en SharePoint-webbplats roll i åtkomst paketet kan det uppstå en fördröjning tills alla användare är en del av den SharePoint-webbplatsens roll. Du kan övervaka förloppet via Azure AD audit-loggen, Azure AD-etablerings loggen och gransknings loggarna för SharePoint-webbplatsen.

## <a name="next-steps"></a>Nästa steg

- [Lägg till en katalog ägare eller en Access Package Manager](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Begär process och e-postmeddelanden](entitlement-management-process.md)
