---
title: Konfigurera Azure-resursrollinställningar i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du konfigurerar Azure-resursrollinställningar i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db330a875b8241b642bcbc71fb0866c9833ee7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638688"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Konfigurera Azure-resursrollinställningar i Privilegierad identitetshantering

När du konfigurerar Azure-resursrollinställningar definierar du standardinställningarna som tillämpas på Azure-resursrolltilldelningar i Azure Active Directory (Azure AD) Privilegierad identitetshantering (PIM). Använd följande procedurer för att konfigurera arbetsflödet för godkännande och ange vem som kan godkänna eller neka begäranden.

## <a name="open-role-settings"></a>Öppna rollinställningar

Följ dessa steg för att öppna inställningarna för en Azure-resursroll.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare i rollen [Administratör för privilegierad roll.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure-resurser**.

1. Välj den resurs som du vill hantera, till exempel en prenumeration eller hanteringsgrupp.

    ![Lista resurser som kan hanteras på Azure-resurssidan](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Välj **Rollinställningar**.

    ![Sidan Rollinställningar lista Azure-resursroller](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Välj den roll vars inställningar du vill konfigurera.

    ![Information om rollinställningsinformation med flera tilldelnings- och aktiveringsinställningar](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Välj **Redigera** om du vill öppna fönstret **Rollinställningar.** På den första fliken kan du uppdatera konfigurationen för rollaktivering i Privilegierad identitetshantering.

    ![Sidan Redigera rollinställningar med fliken Aktivering öppen](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Välj fliken **Tilldelning** eller knappen **Nästa: Tilldelning** längst ned på sidan för att öppna fliken tilldelningsinställning. De här inställningarna styr rolltilldelningar som gjorts i gränssnittet för privilegierad identitetshantering.

    ![Fliken Rolltilldelning på sidan Rollinställningar](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Använd fliken **Meddelande** eller knappen **Nästa: Aktivering** längst ned på sidan för att komma till fliken meddelandeinställning för den här rollen. De här inställningarna styr alla e-postmeddelanden som är relaterade till den här rollen.

    ![Fliken Rollmeddelanden på sidan Rollinställningar](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    På fliken **Meddelanden** på sidan Rollinställningar möjliggör Privilegierad identitetshantering detaljerad kontroll över vem som tar emot meddelanden och vilka meddelanden de får.

    - **Stänga av ett e-postmeddelande**<br>Du kan inaktivera specifika e-postmeddelanden genom att avmarkera standardinställningsrutan mottagare och ta bort ytterligare mottagare.  

    - **Begränsa e-postmeddelanden till angivna e-postadresser**<br>Du kan stänga av e-postmeddelanden som skickas till standardmottagare genom att avmarkera standardrutan mottagare. Du kan sedan lägga till ytterligare e-postadresser som ytterligare mottagare. Om du vill lägga till mer än en e-postadress separerar du dem med ett semikolon (;).

    - **Skicka e-postmeddelanden till både standardmottagare och ytterligare mottagare**<br>Du kan skicka e-postmeddelanden till både standardmottagare och ytterligare mottagare genom att markera standardruterutan för mottagare och lägga till e-postadresser för ytterligare mottagare.

    - **Endast kritiska e-postmeddelanden**<br>För varje typ av e-post kan du markera kryssrutan om du bara vill ta emot viktiga e-postmeddelanden. Vad detta innebär är att privilegierad identitetshantering kommer att fortsätta att skicka e-post till de konfigurerade mottagarna endast när e-postmeddelandet kräver en omedelbar åtgärd. E-postmeddelanden där användarna uppmanas att utöka sin rolltilldelning utlöses till exempel inte medan ett e-postmeddelande som kräver att administratörer godkänner en tilläggsbegäran utlöses.

1. Välj knappen **Uppdatera** när som helst för att uppdatera rollinställningarna.

## <a name="assignment-duration"></a>Tilldelningens varaktighet

Du kan välja mellan två tilldelningslängdsalternativ för varje tilldelningstyp (kvalificerad och aktiv) när du konfigurerar inställningar för en roll. Dessa alternativ blir standardtidslängd när en användare tilldelas rollen i Privilegierad identitetshantering.

Du kan välja ett av dessa **berättigade** tilldelningslängdsalternativ:

| | |
| --- | --- |
| **Tillåt permanent berättigat uppdrag** | Resursadministratörer kan tilldela permanent berättigad tilldelning. |
| **Förfalla kvalificerat uppdrag efter** | Resursadministratörer kan kräva att alla kvalificerade tilldelningar har ett angivet start- och slutdatum. |

Och du kan välja ett av dessa **aktiva** tilldelningslängdsalternativ:

| | |
| --- | --- |
| **Tillåt permanent aktiv tilldelning** | Resursadministratörer kan tilldela permanent aktiv tilldelning. |
| **Upphör att gälla aktiv tilldelning efter** | Resursadministratörer kan kräva att alla aktiva tilldelningar har ett angivet start- och slutdatum. |

> [!NOTE]
> Alla tilldelningar som har ett angivet slutdatum kan förnyas av resursadministratörer. Användare kan också initiera självbetjäningsbegäranden för att [utöka eller förnya rolltilldelningar](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Kräv multifaktorautentisering

Privilegierad identitetshantering ger valfri tillämpning av Azure Multi-Factor Authentication för två olika scenarier.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Kräv multifaktorautentisering vid aktiv tilldelning

I vissa fall kanske du vill tilldela en användare eller grupp till en roll för en kort tid (till exempel en dag). I det här fallet behöver de tilldelade användarna inte begära aktivering. I det här fallet kan privilegierad identitetshantering inte framtvinga multifaktorautentisering när användaren använder sin rolltilldelning eftersom de redan är aktiva i rollen från den tidpunkt då den tilldelas.

Om du vill vara säkra på att resursadministratören som uppfyller tilldelningen är den de säger att de är, kan du framtvinga multifaktorautentisering vid aktiv tilldelning genom att markera rutan **Kräv multifaktorautentisering i aktiv tilldelning.**

### <a name="require-multi-factor-authentication-on-activation"></a>Kräv multifaktorautentisering vid aktivering

Du kan kräva att användare som är berättigade till en roll bevisar vem de använder Azure Multi-Factor Authentication innan de kan aktiveras. Multifaktorautentisering säkerställer att användaren är den de säger att de är med rimlig säkerhet. Om du tillämpar det här alternativet skyddas kritiska resurser i situationer där användarkontot kan ha komprometterats.

Om du vill kräva multifaktorautentisering före aktivering kontrollerar du kryssrutan **Kräv multifaktorautentisering vid aktivering.**

Mer information finns i [Multifaktorautentisering och Privilegierad identitetshantering](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximal varaktighet för aktivering

Använd skjutreglaget För maximal varaktighet för **aktivering** för att ange den maximala tid, i timmar, som en roll förblir aktiv innan den upphör att gälla. Det här värdet kan vara från ett till 24 timmar.

## <a name="require-justification"></a>Kräv motivering

Du kan kräva att användarna anger en affärsmotivering när de aktiveras. Om du vill kräva justering markerar du rutan **Kräv justering vid aktiv tilldelning** eller rutan **Kräv justering vid aktivering.**

## <a name="require-approval-to-activate"></a>Kräv godkännande för att aktivera

Om du vill kräva godkännande för att aktivera en roll följer du dessa steg.

1. Markera kryssrutan **Kräv godkännande för att aktivera.**

1. Välj **Välj godkännare** om du vill öppna sidan **Välj en medlem eller grupp.**

    ![Markera ett användar- eller gruppfönster för att välja godkännare](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Markera minst en användare eller grupp och klicka sedan på **Markera**. Du kan lägga till valfri kombination av användare och grupper. Du måste välja minst en godkännare. Det finns inga standardgodkännare.

    Dina val visas i listan över valda godkännare.

1. När du har angett alla dina rollinställningar väljer du **Uppdatera** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-assign-roles.md)
- [Konfigurera säkerhetsaviseringar för Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-configure-alerts.md)
