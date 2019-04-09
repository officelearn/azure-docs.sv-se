---
title: Skapa en Azure Data Catalog
description: En Snabbstart om hur du skapar en Azure Data Catalog.
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: quickstart
ms.date: 04/05/2019
ms.openlocfilehash: 66d178497f3403579990340b88a1153905b2a2bf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271041"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Snabbstart: Skapa en Azure Data Catalog

Azure Data Catalog är en helt hanterad molntjänst som fungerar som ett registrerings- och identifieringssystem för datatillgångar på ett företag. En detaljerad översikt finns i [Vad är Azure Data Catalog?](overview.md)

Den här snabbstarten hjälper dig att komma igång med att skapa en Azure Data Catalog.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha för att komma igång:

* En [Microsoft Azure](https://azure.microsoft.com/) prenumeration.
* Du måste ha en egen [Azure Active Directory-klient](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Om du vill konfigurera Data Catalog, måste du vara ägare eller Medägare av en Azure-prenumeration.

## <a name="create-a-data-catalog"></a>Skapa en datakatalog

Du kan bara etablera en datakatalog per organisation (Azure Active Directory-domän). Därför om ägaren eller medägaren av en Azure-prenumeration som tillhör den här Azure Active Directory-domänen har redan skapat en katalog kan skapa du inte en katalog igen även om du har flera Azure-prenumerationer. Du kan testa om en katalog har skapats av en användare i din Azure Active Directory-domän genom att gå till [startsidan för Azure Data Catalog](http://azuredatacatalog.com) och kontrollera om du ser katalogen. Om en katalog redan har skapats för dig hoppar du över följande procedur och går till nästa avsnitt.

1. Gå till den [Azure-portalen](https://portal.azure.com) > **skapa en resurs** och välj **Data Catalog**.

    ![Skapa Data Catalog](media/data-catalog-get-started/data-catalog-create.png)

2. Ange en **namn** för datakatalogen, den **prenumeration** du vill använda den **plats** för katalogen, och **prisnivån**. Välj sedan **Skapa**.

3. Gå till [startsidan för Azure Data Catalog](http://azuredatacatalog.com) och klicka på **Publicera data**.

   ![Azure Data Catalog – knappen Publicera data](media/data-catalog-get-started/data-catalog-publish-data.png)

   Du kan också öppna startsidan för Data Catalog från den [service-sidan för Data Catalog](https://azure.microsoft.com/services/data-catalog) genom att välja **börjar**.

   ![Azure Data Catalog – landningssida för marknadsföring](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Gå till den **inställningar** sidan.

    ![Azure Data Catalog – etablering av datakatalog](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Expandera **priser** och verifiera din Azure Data Catalog **edition** (kostnadsfri eller Standard).

    ![Azure Data Catalog – välj version](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Om du väljer *Standard* edition som din prisnivå som du kan expandera **säkerhetsgrupper** och aktivera auktorisering av Active Directory-säkerhetsgrupper för att få åtkomst till Data Catalog och aktivera automatisk justering av fakturering.

    ![Säkerhetsgrupper för Azure Data Catalog](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Expandera **Kataloganvändare** och klicka på **Lägg till** för att lägga till användare för datakatalogen. Du läggs automatiskt till den här gruppen.

    ![Azure Data Catalog – användare](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Om du väljer *Standard* edition som din prisnivå som du kan expandera **ordliste-administratörer** och klicka på **Lägg till** att lägga till ordliste-administratörer. Du läggs automatiskt till den här gruppen.

    ![Azure Data Catalog ordliste-administratörer](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Expandera **Katalogadministratörer** och klicka på **Lägg till** för att lägga till ytterligare administratörer för datakatalogen. Du läggs automatiskt till den här gruppen.

    ![Azure Data Catalog – administratörer](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Expandera **Portal rubrik** och Lägg till ytterligare text som ska visas i rubriken för portalen.

    ![Azure Data Catalog-portalen rubrik](media/data-catalog-get-started/data-catalog-portal-title.png)

11. När du har slutfört den **inställningar** sidan, navigera sedan till den **publicera** sidan.

    ![Azure Data Catalog – skapa katalogen](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Hitta en datakatalog på Azure-portalen

1. Öppna en separat flik i webbläsaren eller ett separat webbläsarfönster, gå till [Azure-portalen](https://portal.azure.com) och logga in med samma konto som du använde för att skapa datakatalogen i föregående steg.

2. Välj **alla tjänster** och klicka sedan på **Data Catalog**.

    ![Azure Data Catalog – Bläddra i Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Du datakatalogen som du skapade.

    ![Azure Data Catalog – visa katalog i lista](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Klicka på den katalog som du skapade. Bladet **Datakatalog** visas på portalen.

   ![Azure Data Catalog – blad på portalen](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Du kan visa egenskaperna för datakatalogen och uppdatera dem. Klicka till exempel **Prisnivå** och ändra versionen.

    ![Azure Data Catalog – prisnivå](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar en Azure Data Catalog för organisationen. Du kan nu registrera datakällor i din data catalog.

> [!div class="nextstepaction"]
> [Registrera datakällor i Azure Data Catalog](data-catalog-how-to-register.md)
