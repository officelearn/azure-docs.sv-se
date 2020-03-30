---
title: Lägga till eller ta bort en grupp från en annan grupp - Azure AD
description: Instruktioner om hur du lägger till eller tar bort en grupp från en annan grupp med Hjälp av Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 830bf7134b3a8b0425c53673a1347dd77897a5bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75423067"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Lägga till eller ta bort en grupp från en annan grupp med Azure Active Directory
Den här artikeln hjälper dig att lägga till och ta bort en grupp från en annan grupp med Hjälp av Azure Active Directory.

>[!Note]
>Om du försöker ta bort den överordnade gruppen läser du [Så här uppdaterar eller tar du bort en grupp och dess medlemmar](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Lägga till en grupp i en annan grupp
Du kan lägga till en befintlig säkerhetsgrupp i en annan befintlig säkerhetsgrupp (kallas även kapslade grupper), skapa en medlemsgrupp (undergrupp) och en överordnad grupp. Medlemsgruppen ärver attributen och egenskaperna för den överordnade gruppen, vilket sparar konfigurationstid.

>[!Important]
>Vi stöder för närvarande inte:<ul><li>Lägga till grupper i en grupp som synkroniserats med lokal Active Directory.</li><li>Lägga till säkerhetsgrupper i Office 365-grupper.</li><li>Lägga till Office 365-grupper i säkerhetsgrupper eller andra Office 365-grupper.</li><li>Tilldela appar till kapslade grupper.</li><li>Tillämpa licenser på kapslade grupper.</li><li>Lägga till distributionsgrupper i kapslingsscenarier.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Så här lägger du till en grupp som medlem i en annan grupp

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**och välj sedan **Grupper**.

3. På sidan **Grupper - Alla grupper** söker du efter och väljer den grupp som ska bli medlem i en annan grupp. I den här övningen använder vi **MDM-principen - West-gruppen.**

    >[!Note]
    >Du kan lägga till din grupp som medlem i endast en grupp i taget. Dessutom **filtrerar** rutan Välj grupp visningen baserat på att du matchar din post med någon del av ett användarnamn eller enhetsnamn. Jokertecken stöds dock inte.

    ![Grupper - Sidan Grupper med MDM-princip - Västgrupp markerad](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. På sidan **MDM-princip - Väst - Gruppmedlemskap** väljer du **Gruppmedlemskap**, väljer **Lägg till**, leta reda på den grupp du vill att gruppen ska vara medlem i och välj sedan **Välj**. I den här övningen använder vi **MDM-principen - Gruppen Alla organisationer.**

    **MDM-principen - West-gruppen** är nu medlem i **MDM-principen - Alla organisationsgrupper,** som ärver alla egenskaper och konfiguration av MDM-principen - Alla organisationer.

    ![Skapa ett gruppmedlemskap genom att lägga till grupp i en annan grupp](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. Gå igenom **mdm-principen - väst - gruppmedlemskap** för att se grupp- och medlemsrelationen.

6. Om du vill ha en mer detaljerad vy över grupp- och medlemsrelationen väljer du gruppnamnet (**MDM-principen - All org**) och tar en titt på **MDM-principen - Information om västra** sidan.

## <a name="remove-a-group-from-another-group"></a>Ta bort en grupp från en annan grupp
Du kan ta bort en befintlig säkerhetsgrupp från en annan säkerhetsgrupp. Om du tar bort gruppen tas dock även alla ärvda attribut och egenskaper för medlemmarna bort.

### <a name="to-remove-a-member-group-from-another-group"></a>Så här tar du bort en medlemsgrupp från en annan grupp
1. På sidan **Grupper - Alla grupper** söker du efter och väljer den grupp som ska tas bort som medlem i en annan grupp. För den här övningen använder vi återigen **MDM-principen -** West-gruppen.

2. På översiktssidan **för MDM - Väst** väljer du **Gruppmedlemskap**.

3. Välj **MDM-principen - Alla organisationsgrupper** från **mdm-principen - Väst - Gruppmedlemskap** och välj sedan **Ta bort** från **MDM-principen - Information** om sidan Väst.

    ![Sidan Gruppmedlemskap som visar både medlemmen och gruppinformationen](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>Ytterligare information
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

- [Visa dina grupper och medlemmar](active-directory-groups-view-azure-portal.md)

- [Skapa en basgrupp och lägg till medlemmar](active-directory-groups-create-azure-portal.md)

- [Lägga till eller ta bort medlemmar från en grupp](active-directory-groups-members-azure-portal.md)

- [Redigera dina gruppinställningar](active-directory-groups-settings-azure-portal.md)

- [Använd en grupp för att hantera åtkomst till SaaS-program](../users-groups-roles/groups-saasapps.md)

- [Scenarier, begränsningar och kända problem med hjälp av grupper för att hantera licensiering i Azure Active Directory](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
