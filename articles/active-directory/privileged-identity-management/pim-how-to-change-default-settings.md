---
title: Konfigurera Azure AD-rollinställningar i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du konfigurerar Azure AD-rollinställningar i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd525973a8c8ba5bb3103126b4ddb5b6973590a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205032"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Konfigurera Azure AD-rollinställningar i privilegierad identitetshantering

En privilegierad rolladministratör kan anpassa PIM (Privileged Identity Management) i sin Azure Active Directory-organisation (Azure AD), inklusive att ändra upplevelsen för en användare som aktiverar en kvalificerad rolltilldelning.

## <a name="determine-your-version-of-pim"></a>Bestäm din version av PIM

Från och med november 2019 uppdateras Azure AD-rollerna i Privileged Identity Management till en ny version som matchar upplevelserna för Azure-resursroller. Detta skapar ytterligare funktioner samt [ändringar i det befintliga API:et](azure-ad-roles-features.md#api-changes). Medan den nya versionen distribueras, vilka procedurer som du följer i den här artikeln beror på vilken version av privilegierad identitetshantering du har för närvarande. Följ stegen i det här avsnittet för att avgöra vilken version av privilegierad identitetshantering du har. När du känner till din version av Privilegierad identitetshantering kan du välja de procedurer i den här artikeln som matchar den versionen.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är i [rollen Privilegierad rolladministratör.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Öppna **Azure AD-privilegierad identitetshantering**. Om du har en banderoll högst upp på översiktssidan följer du instruktionerna på fliken **Ny version** i den här artikeln. Annars följer du instruktionerna på fliken **Föregående version.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Följ stegen i den här artikeln för att godkänna eller neka begäranden om Azure AD-roller.

# <a name="new-version"></a>[Ny version](#tab/new)

## <a name="open-role-settings"></a>Öppna rollinställningar

Följ dessa steg för att öppna inställningarna för en Azure AD-roll.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare i rollen [Administratör för privilegierad roll.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
Gt
1. Öppna Azure **AD Privileged Identity Management** &gt; **Azure AD-roller** &gt; **Rollinställningar**.

    ![Sidan Rollinställningar lista Azure AD-roller](./media/pim-how-to-change-default-settings/role-settings.png)

1. Välj den roll vars inställningar du vill konfigurera.

    ![Information om rollinställningsinformation med flera tilldelnings- och aktiveringsinställningar](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Välj **Redigera** om du vill öppna sidan Rollinställningar.

    ![Redigera sidan rollinställningar med alternativ för att uppdatera tilldelnings- och aktiveringsinställningar](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    I fönstret Rollinställning för varje roll finns det flera inställningar som du kan konfigurera.

## <a name="assignment-duration"></a>Tilldelningens varaktighet

Du kan välja mellan två tilldelningslängdsalternativ för varje tilldelningstyp (kvalificerad och aktiv) när du konfigurerar inställningar för en roll. Dessa alternativ blir standardtidslängd när en användare tilldelas rollen i Privilegierad identitetshantering.

Du kan välja ett av dessa **berättigade** tilldelningslängdsalternativ:

| | |
| --- | --- |
| **Tillåt permanent berättigat uppdrag** | Globala administratörer och privilegierade rolladministratörer kan tilldela permanent berättigad tilldelning. |
| **Förfalla kvalificerat uppdrag efter** | Globala administratörer och privilegierade rolladministratörer kan kräva att alla kvalificerade tilldelningar har ett angivet start- och slutdatum. |

Och du kan välja ett av dessa **aktiva** tilldelningslängdsalternativ:

| | |
| --- | --- |
| **Tillåt permanent aktiv tilldelning** | Globala administratörer och privilegierade rolladministratörer kan tilldela permanent aktiv tilldelning. |
| **Upphör att gälla aktiv tilldelning efter** | Globala administratörer och privilegierade rolladministratörer kan kräva att alla aktiva tilldelningar har ett angivet start- och slutdatum. |

> [!NOTE]
> Alla tilldelningar som har ett angivet slutdatum kan förnyas av globala administratörer och privilegierade rolladministratörer. Användare kan också initiera självbetjäningsbegäranden för att [utöka eller förnya rolltilldelningar](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Kräv multifaktorautentisering

Privilegierad identitetshantering ger valfri tillämpning av Azure Multi-Factor Authentication för två olika scenarier.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Kräv multifaktorautentisering vid aktiv tilldelning

I vissa fall kanske du vill tilldela en användare till en roll för en kort tid (en dag, till exempel). I det här fallet behöver de tilldelade användarna inte begära aktivering. I det här fallet kan privilegierad identitetshantering inte framtvinga multifaktorautentisering när användaren använder sin rolltilldelning eftersom de redan är aktiva i rollen från den tidpunkt då den tilldelas.

Om du vill vara kontrollerar att administratören som fullgör tilldelningen är den de säger att de är, kan du framtvinga multifaktorautentisering vid aktiv tilldelning genom att markera rutan **Kräv multifaktorautentisering i aktiv tilldelning.**

### <a name="require-multi-factor-authentication-on-activation"></a>Kräv multifaktorautentisering vid aktivering

Du kan kräva att användare som är berättigade till en roll bevisar vem de använder Azure Multi-Factor Authentication innan de kan aktiveras. Multifaktorautentisering säkerställer att användaren är den de säger att de är med rimlig säkerhet. Om du tillämpar det här alternativet skyddas kritiska resurser i situationer där användarkontot kan ha komprometterats.

Om du vill kräva multifaktorautentisering före aktivering kontrollerar du kryssrutan **Kräv multifaktorautentisering vid aktivering på fliken** Tilldelning i **rollinställningen Redigera**.

Mer information finns i [Multifaktorautentisering och Privilegierad identitetshantering](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximal varaktighet för aktivering

Använd skjutreglaget För maximal varaktighet för **aktivering** för att ange den maximala tid, i timmar, som en roll förblir aktiv innan den upphör att gälla. Det här värdet kan vara från ett till 24 timmar.

## <a name="require-justification"></a>Kräv motivering

Du kan kräva att användarna anger en affärsmotivering när de aktiveras. Om du vill kräva justering markerar du rutan **Kräv justering vid aktiv tilldelning** eller rutan **Kräv justering vid aktivering.**

## <a name="require-approval-to-activate"></a>Kräv godkännande för att aktivera

Om du anger flera godkännare slutförs godkännandet så snart en av dem godkänner eller nekar. Du kan inte kräva godkännande från minst två användare. Så här kräver du godkännande för att aktivera en roll.

1. Markera kryssrutan **Kräv godkännande för att aktivera.**

1. Välj **Välj godkännare**.

    ![Markera ett användar- eller gruppfönster för att välja godkännare](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Markera minst en användare och klicka sedan på **Markera**. Du måste välja minst en godkännare. Det finns inga standardgodkännare.

    Dina val visas i listan över valda godkännare.

1. När du har angett alla dina rollinställningar väljer du **Uppdatera** för att spara ändringarna.

# <a name="previous-version"></a>[Tidigare version](#tab/previous)

## <a name="open-role-settings"></a>Öppna rollinställningar

Följ dessa steg för att öppna inställningarna för en Azure AD-roll.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure AD-roller**.

1. Välj **Inställningar**.

    ![Azure AD-roller - Inställningar](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Välj **Roller**.

1. Välj den roll vars inställningar du vill konfigurera.

    ![Azure AD-roller - Inställningar Roller](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    På inställningssidan för varje roll finns det flera inställningar som du kan konfigurera. Dessa inställningar påverkar endast användare som är **kvalificerade** tilldelningar, inte **permanenta** tilldelningar.

## <a name="activations"></a>Aktiveringar

Använd skjutreglaget **Aktiveringar** för att ange den maximala tid, i timmar, som en roll förblir aktiv innan den upphör att gälla. Det här värdet kan vara mellan 1 och 72 timmar.

## <a name="notifications"></a>Meddelanden

Använd växeln **Meddelanden** för att ange om administratörer ska få e-postmeddelanden när roller aktiveras. Det här meddelandet kan vara användbart för att upptäcka obehöriga eller olagliga aktiveringar.

När den är inställd på **Aktivera**skickas meddelanden till:

- Administratör för privilegierad roll
- Säkerhetsadministratör
- Global administratör

Mer information finns [i E-postmeddelanden i Privilegierad identitetshantering](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Biljett till incident/begäran

Använd **biljettväxeln Incident/Request** för att kräva att berättigade administratörer inkluderar ett biljettnummer när de aktiverar sin roll. Denna praxis kan göra rollåtkomstrevisioner mer effektiva.

## <a name="multi-factor-authentication"></a>Multifaktorautentisering

Använd växeln **Multifaktorautentisering** för att ange om användarna ska verifiera sin identitet med MFA innan de kan aktivera sina roller. De behöver bara verifiera sin identitet en gång per session, inte varje gång de aktiverar en roll. Det finns två tips att tänka på när du aktiverar MFA:

- Användare som har Microsoft-konton för @outlook.comsina e-postadresser (vanligtvis , men inte alltid) kan inte registrera sig för Azure Multi-Factor Authentication. Om du vill tilldela roller till användare med Microsoft-konton bör du antingen göra dem permanenta administratörer eller inaktivera multifaktorautentisering för den rollen.
- Du kan inte inaktivera Azure Multi-Factor Authentication för mycket privilegierade roller för Azure AD och Office 365. Den här säkerhetsfunktionen hjälper till att skydda följande roller:  
  
  - Administratör för Azure-informationsskydd
  - Faktureringsadministratör
  - Administratör för molnprogram
  - Administratör för efterlevnad
  - Administratör för villkorsstyrd åtkomst
  - Dynamics 365-administratör
  - Godkännande av kundlåsboxåtkomst
  - Katalogförfattare
  - Exchange-administratör
  - Global administratör
  - Intune-administratör
  - Power BI-administratör
  - Administratör för privilegierad roll
  - Säkerhetsadministratör
  - SharePoint-administratör
  - Skype for Business-administratör
  - Användaradministratör

Mer information finns i [Multifaktorautentisering och Privilegierad identitetshantering](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Kräv godkännande

Om du vill delegera det godkännande som krävs för att aktivera en roll följer du dessa steg.

1. Ange växeln **Kräv godkännande** till **Aktiverad**. Fönstret expanderas med alternativ för att välja godkännare.

    ![Azure AD-roller - Inställningar - Kräver godkännande](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Om du inte anger några godkännare blir administratören för privilegierad roll standard godkännaren och måste sedan godkänna alla aktiveringsbegäranden för den här rollen.

1. Om du vill lägga till godkännare klickar du på **Välj godkännare**.

    ![Azure AD-roller - Inställningar - Kräver godkännande](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Markera en eller flera godkännare utöver administratören för privilegierade roller och klicka sedan på **Välj**. Vi rekommenderar att du lägger till minst två godkännare. Även om du lägger till dig själv som godkännare kan du inte självgodkänna en rollaktivering. Dina val visas i listan över valda godkännare.

1. När du har angett alla dina rollinställningar väljer du **Spara** för att spara ändringarna.

---

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i privilegierad identitetshantering](pim-how-to-add-role-to-user.md)
- [Konfigurera säkerhetsaviseringar för Azure AD-roller i privilegierad identitetshantering](pim-how-to-configure-security-alerts.md)
