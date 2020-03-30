---
title: Lägga till en ansluten organisation i Azure AD-berättigandehantering - Azure Active Directory
description: Lär dig hur du tillåter personer utanför organisationen att begära åtkomstpaket så att du kan samarbeta i projekt.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee370bc9c381eb11ae7cae53b31d0c987c52733c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128616"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Lägga till en ansluten organisation i Azure AD-berättigandehantering

Med Azure Active Directory (Azure AD) berättigandehantering kan du samarbeta med personer utanför organisationen. Om du ofta samarbetar med användare i en extern Azure AD-katalog eller domän kan du lägga till dem som en ansluten organisation. I den här artikeln beskrivs hur du lägger till en ansluten organisation så att du kan tillåta användare utanför organisationen att begära resurser i katalogen.

## <a name="what-is-a-connected-organization"></a>Vad är en ansluten organisation?

En ansluten organisation är en extern Azure AD-katalog eller domän som du har en relation till.

Anta till exempel att du arbetar på Woodgrove Bank och vill samarbeta med två externa organisationer. Dessa två organisationer har olika konfigurationer:

- Graphic Design Institute använder Azure AD och deras användare har ett användarnamn som slutar med *graphicdesigninstitute.com*.
- Contoso använder ännu inte Azure AD. Contoso-användare har ett användarnamn som slutar med *contoso.com*.

I det här fallet kan du konfigurera två anslutna organisationer. Du skapar en ansluten organisation för Graphic Design Institute och en för Contoso. Om du sedan lägger till de två anslutna organisationerna i en princip kan användare från varje organisation med ett användarnamn som matchar principen begära åtkomstpaket. Användare med ett användarnamn som har en *domän med graphicdesigninstitute.com* matchar den grafikdesigninstitutanslutna organisationen och får skicka begäranden. Användare med ett användarnamn som har en *domän med contoso.com* matchar den Contoso-anslutna organisationen och skulle också tillåtas att begära paket. Och eftersom Graphic Design Institute använder Azure AD kan alla användare med ett huvudnamn som matchar en [verifierad domän](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) som läggs till i deras klientorganisation, till exempel *graphicdesigninstitute.example,* också begära åtkomstpaket med samma princip.

![Exempel på ansluten organisation](./media/entitlement-management-organization/connected-organization-example.png)

Hur användare från Azure AD-katalogen eller domänautentiseringen beror på autentiseringstypen. Autentiseringstyperna för anslutna organisationer är:

- Azure AD
- [Direkt federation](../b2b/direct-federation.md)
- [Engångslösenord](../b2b/one-time-passcode.md) (domän)

En demonstration av hur du lägger till en ansluten organisation finns i följande video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Lägga till en ansluten organisation

Om du vill lägga till en extern Azure AD-katalog eller domän som en ansluten organisation följer du instruktionerna i det här avsnittet.

**Viktig roll:** *Global administratör,* *användaradministratör*eller *gäst inbjudna*

1. I Azure-portalen väljer du **Azure Active Directory**och väljer sedan **Identitetsstyrning**.

1. I den vänstra rutan väljer du **Anslutna organisationer**och väljer sedan Lägg **till ansluten organisation**.

    ![Knappen "Lägg till ansluten organisation"](./media/entitlement-management-organization/connected-organization.png)

1. Välj fliken Grunderna och ange sedan ett **visningsnamn** och en beskrivning för organisationen.

    ![Fönstret "Lägg till ansluten organisation" Grunderna](./media/entitlement-management-organization/organization-basics.png)

1. Välj fliken **Katalog + domän** och välj sedan Lägg till katalog + **domän**.

    Fönstret **Välj kataloger + domäner** öppnas.

1. Ange ett domännamn i sökrutan för att söka efter Azure AD-katalogen eller domänen. Var noga med att ange hela domännamnet.

1. Kontrollera att organisationens namn och autentiseringstyp är korrekta. Hur användare loggar in beror på autentiseringstypen.

    ![Fönstret "Välj kataloger + domäner"](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Välj **Lägg till** om du vill lägga till Azure AD-katalogen eller domänen. För närvarande kan du bara lägga till en Azure AD-katalog eller domän per ansluten organisation.

    > [!NOTE]
    > Alla användare från Azure AD-katalogen eller domänen kan begära det här åtkomstpaketet. Detta inkluderar användare i Azure AD från alla underdomäner som är associerade med katalogen, såvida inte dessa domäner blockeras av Azure AD-verksamheten till företag (B2B) tillåt eller neka lista. Mer information finns i [Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer](../b2b/allow-deny-list.md).

1. När du har lagt till Azure AD-katalogen eller domänen väljer du **Välj**.

    Organisationen visas i listan.

    ![Fönstret "Katalog + domän"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Välj fliken **Sponsorer** och lägg sedan till valfria sponsorer för den anslutna organisationen.

    Sponsorer är interna eller externa användare som redan finns i katalogen och som är kontaktpunkten för relationen till den anslutna organisationen. Interna sponsorer är medlemsanvändare i din katalog. Externa sponsorer är gästanvändare från den anslutna organisationen som tidigare bjudits in och som redan finns i katalogen. Sponsorer kan användas som godkännare när användare i den här anslutna organisationen begär åtkomst till det här åtkomstpaketet. Information om hur du bjuder in en gästanvändare till din katalog finns [i Lägga till Azure Active Directory B2B-samarbetsanvändare i Azure-portalen](../b2b/add-users-administrator.md).

    När du väljer **Lägg till/ta bort**öppnas en ruta där du kan välja interna eller externa sponsorer. I fönstret visas en ofiltrerad lista över användare och grupper i katalogen.

    ![Fönstret Sponsorer](./media/entitlement-management-organization/organization-sponsors.png)

1. Välj fliken **Granska + skapa,** granska organisationens inställningar och välj sedan **Skapa**.

    ![Fönstret "Granska + skapa"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Uppdatera en ansluten organisation 

Om den anslutna organisationen ändras till en annan domän, organisationens namn ändras eller om du vill ändra sponsorerna kan du uppdatera den anslutna organisationen genom att följa instruktionerna i det här avsnittet.

**Viktig roll:** *Global administratör,* *användaradministratör*eller *gäst inbjudna*

1. I Azure-portalen väljer du **Azure Active Directory**och väljer sedan **Identitetsstyrning**.

1. I den vänstra rutan väljer du **Anslutna organisationer**och väljer sedan den anslutna organisationen för att öppna den.

1. I den anslutna organisationens översiktsfönster väljer du **Redigera** om du vill ändra organisationens namn eller beskrivning.  

1. I fönstret **Katalog + domän** väljer du Uppdatera katalog + **domän** om du vill ändra till en annan katalog eller domän.

1. I fönstret **Sponsorer** väljer du **Lägg till interna sponsorer** eller **Lägg till externa sponsorer** för att lägga till en användare som sponsor. Om du vill ta bort en sponsor markerar du sponsorn och väljer **Ta bort**i den högra rutan .


## <a name="delete-a-connected-organization"></a>Ta bort en ansluten organisation

Om du inte längre har en relation med en extern Azure AD-katalog eller domän kan du ta bort den anslutna organisationen.

**Viktig roll:** *Global administratör,* *användaradministratör*eller *gäst inbjudna*

1. I Azure-portalen väljer du **Azure Active Directory**och väljer sedan **Identitetsstyrning**.

1. I den vänstra rutan väljer du **Anslutna organisationer**och väljer sedan den anslutna organisationen för att öppna den.

1. I den anslutna organisationens översiktsfönster väljer du **Ta bort** för att ta bort den.

    För närvarande kan du bara ta bort en ansluten organisation om det inte finns några anslutna användare.

    ![Knappen Ta bort ansluten organisation](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Nästa steg

- [Styra åtkomst för externa användare](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Styra åtkomsten för användare som inte finns i katalogen](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
