---
title: Skapa & ta bort Azure AD B2C-konsumentanvändarkonton i Azure-portalen
description: Lär dig hur du använder Azure-portalen för att skapa och ta bort konsumentanvändare i din Azure AD B2C-katalog.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c8fad8b5bd2dccba36c0c6888712fa806f3cbfc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187227"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Använda Azure-portalen för att skapa och ta bort konsumentanvändare i Azure AD B2C

Det kan finnas scenarier där du manuellt vill skapa konsumentkonton i din Azure Active Directory B2C -katalog (Azure AD B2C). Även om konsumentkonton i en Azure AD B2C-katalog oftast skapas när användare registrerar sig för att använda ett av dina program, kan du skapa dem programmässigt och med hjälp av Azure-portalen. Den här artikeln fokuserar på Azure-portalen metod för att skapa och ta bort användare.

Om du vill lägga till eller ta bort användare måste ditt konto tilldelas *rollen Användaradministratör* eller *Global administratör.*

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>Typer av användarkonton

Som beskrivs i [Översikt över användarkonton i Azure AD B2C](user-overview.md)finns det tre typer av användarkonton som kan skapas i en Azure AD B2C-katalog:

* Arbete
* Gäst
* Konsumenten

Den här artikeln fokuserar på att arbeta med **konsumentkonton** i Azure-portalen. Information om hur du skapar och tar bort jobb- och gästkonton finns i [Lägga till eller ta bort användare med Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Skapa en konsumentanvändare

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Under **Hantera** väljer du **Användare**.
1. Välj **Ny användare**.
1. Välj **Skapa Azure AD B2C-användare**.
1. Välj en **inloggningsmetod** och ange antingen en **e-postadress** eller ett **användarnamn** för den nya användaren. Inloggningsmetoden som du väljer här måste matcha den inställning som du har angett för din Azure AD B2C-klientens identitetsprovider *för lokalt konto* (se **Hantera** > **identitetsleverantörer** i din Azure AD B2C-klient).
1. Ange ett **namn** för användaren. Detta är vanligtvis användarens fullständiga namn (förnamn och efternamn).
1. (Valfritt) Du kan **blockera inloggning** om du vill fördröja möjligheten för användaren att logga in. Du kan aktivera inloggning senare genom att redigera användarens **profil** i Azure-portalen.
1. Välj **Automatiskt generera lösenord** eller Låt mig skapa **lösenord**.
1. Ange användarens **förnamn** och **efternamn**.
1. Välj **Skapa**.

Om du inte har valt **Blockera inloggning**kan användaren nu logga in med inloggningsmetoden (e-post eller användarnamn) som du har angett.

## <a name="delete-a-consumer-user"></a>Ta bort en konsumentanvändare

1. I din Azure AD B2C-katalog väljer du **Användare**och väljer sedan den användare som du vill ta bort.
1. Välj **Ta bort**och sedan **Ja** för att bekräfta borttagningen.

Mer information om hur du återställer en användare inom de första 30 dagarna efter borttagningen eller för att permanent ta bort en användare finns i [Återställa eller ta bort en nyligen borttagen användare med Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Nästa steg

För automatiserade användarhanteringsscenarier, till exempel migrera användare från en annan identitetsprovider till din Azure AD B2C-katalog, finns i [Azure AD B2C: User migration](user-migration.md).
