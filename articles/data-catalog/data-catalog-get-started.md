---
title: Skapa Azure Data Catalog
description: Den här snabbstarten beskriver hur du skapar en Azure-datakatalog med Azure-portalen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 08/01/2019
ms.openlocfilehash: 854760ea0158d356cc5449c9f1fdab6161588640
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "68976865"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Snabbstart: Skapa en Azure-datakatalog

Azure Data Catalog är en helt hanterad molntjänst som fungerar som ett registrerings- och identifieringssystem för datatillgångar på ett företag. En detaljerad översikt finns i [Vad är Azure Data Catalog?](overview.md)

Den här snabbstarten hjälper dig att komma igång med att skapa en Azure Data Catalog.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

För att komma igång måste du ha:

* En [Microsoft Azure](https://azure.microsoft.com/)-prenumeration.
* Du måste ha en egen [Azure Active Directory-klient](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

För att kunna konfigurera datakatalog måste du vara ägare eller delägare i en Azure-prenumeration.

## <a name="create-a-data-catalog"></a>Skapa en datakatalog

Du kan bara etablera en datakatalog per organisation (Azure Active Directory-domän). Om ägaren eller delägaren av en Azure-prenumeration som tillhör den här Azure Active Directory-domänen redan har skapat en katalog, kan du därför inte skapa en katalog igen även om du har flera Azure-prenumerationer. Du kan testa om en katalog har skapats av en användare i din Azure Active Directory-domän genom att gå till [startsidan för Azure Data Catalog](http://azuredatacatalog.com) och kontrollera om du ser katalogen. Om en katalog redan har skapats för dig hoppar du över följande procedur och går till nästa avsnitt.

1. Gå till [Azure-portalen](https://portal.azure.com) > **Skapa en resurs** och välj **Datakatalog**.

    ![Skapa knapp för azure datakatalog](media/data-catalog-get-started/data-catalog-create.png)

2. Ange ett **namn** för datakatalogen, den **prenumeration** du vill använda, **platsen** för katalogen och **prisnivån**. Välj sedan **Skapa**.

3. Gå till [startsidan för Azure Data Catalog](http://azuredatacatalog.com) och klicka på **Publicera data**.

   ![Azure Data Catalog – knappen Publicera data](media/data-catalog-get-started/data-catalog-publish-data.png)

   Du kan också komma till startsidan för datakatalogen på [tjänstsidan för datakatalogen](https://azure.microsoft.com/services/data-catalog) genom att välja **Kom igång**.

   ![Azure Data Catalog – landningssida för marknadsföring](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Gå till sidan **Inställningar.**

    ![Azure Data Catalog – etablering av datakatalog](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Utöka **priser** och verifiera din Azure Data **Catalog-utgåva** (gratis eller standard).

    ![Azure Data Catalog – välj version](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Om du väljer *Standard* edition som prisnivå kan du expandera **säkerhetsgrupper** och aktivera behörighet för Active Directory-säkerhetsgrupper att komma åt Datakatalog och aktivera automatisk justering av fakturering.

    ![Säkerhetsgrupper för Azure Data Catalog](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Expandera **Kataloganvändare** och klicka på **Lägg till** för att lägga till användare för datakatalogen. Du läggs automatiskt till i den här gruppen.

    ![Azure Data Catalog – användare](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Om du väljer *Standard* edition som prisnivå kan du expandera **Ordlistor administratörer** och klicka på **Lägg** till för att lägga till ordlista administratörsanvändare. Du läggs automatiskt till i den här gruppen.

    ![Ordlista administratörer för Azure Data Catalog-katalog](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Expandera **Katalogadministratörer** och klicka på **Lägg till** för att lägga till ytterligare administratörer för datakatalogen. Du läggs automatiskt till i den här gruppen.

    ![Azure Data Catalog – administratörer](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Expandera **portalrubriken** och lägg till ytterligare text som visas i portaltiteln.

    ![Rubrik för Azure Data Catalog-Portal](media/data-catalog-get-started/data-catalog-portal-title.png)

11. När du har slutfört sidan **Inställningar** navigerar du nästa till sidan **Publicera.**

    ![Azure Data Catalog – skapa katalogen](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Hitta en datakatalog på Azure-portalen

1. Öppna en separat flik i webbläsaren eller ett separat webbläsarfönster, gå till [Azure-portalen](https://portal.azure.com) och logga in med samma konto som du använde för att skapa datakatalogen i föregående steg.

2. Markera **Alla tjänster** och klicka sedan på **Datakatalog**.

    ![Azure Data Catalog - bläddra i Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Du ser datakatalogen som du skapade.

    ![Azure Data Catalog – visa katalog i lista](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Klicka på den katalog som du skapade. Bladet **Datakatalog** visas på portalen.

   ![Azure Data Catalog – blad på portalen](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Du kan visa egenskaperna för datakatalogen och uppdatera dem. Klicka till exempel **Prisnivå** och ändra versionen.

    ![Azure Data Catalog – prisnivå](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar en Azure-datakatalog för din organisation. Nu kan du registrera datakällor i datakatalogen.

> [!div class="nextstepaction"]
> [Registrera datakällor i Azure Data Catalog](data-catalog-how-to-register.md)
