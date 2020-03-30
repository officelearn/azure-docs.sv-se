---
title: Så här skyddar du åtkomst till Azure Data Catalog
description: I den här artikeln beskrivs hur du skyddar en datakatalog och dess datatillgångar i Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b0972be2b8a6e05d3d90cde7354b4890ea95cbe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976766"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Så här skyddar du åtkomst till datakatalog och datatillgångar

> [!IMPORTANT]
> Den här funktionen är endast tillgänglig i standardutgåvan av Azure Data Catalog.

Med Azure Data Catalog kan du ange vem som kan komma åt datakatalogen och vilka åtgärder (registrera, kommentera, bli ägare) som de kan utföra på metadata i katalogen. 

## <a name="catalog-users-and-permissions"></a>Kataloganvändare och behörigheter

Så här ger du en användare eller en grupp åtkomst till en datakatalog och anger behörigheter:

1. Klicka på **Inställningar** i verktygsfältet på [startsidan i datakatalogen.](https://www.azuredatacatalog.com)

   ![Knappen Inställningar för startsidan i Azure Data Catalog](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. Expandera avsnittet **Kataloganvändare** på inställningssidan.

   ![Knappen Lägg till i Azure Data Catalog-användare](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. Klicka på **Lägg till**.

4. Ange det fullständigt kvalificerade **användarnamnet** eller namnet på **säkerhetsgruppen** i Azure Active Directory (AAD) som är associerat med katalogen. Använd komma (',') som avgränsare om du lägger till mer än en användare eller grupp.

   ![Azure Data Catalog användare - användare eller grupper](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. Tryck **på RETUR** eller **TABB** ut ur textrutan. 

6. Bekräfta att alla behörigheter (**Kommentera**, **Registrera**och **Bli ägarskap**) tilldelas dessa användare eller grupper som standard. Det vill än, användaren eller gruppen kan [registrera datatillgångar,]( data-catalog-how-to-register.md) [kommentera datatillgångar]( data-catalog-how-to-annotate.md)och [bli ägare till datatillgångar]( data-catalog-how-to-manage.md). 

   ![Azure Data Catalog-användare - standardbehörigheter](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Om du bara vill ge en användare eller grupp läsbehörighet till katalogen **avmarkerar** du anteckningsalternativet för den användaren eller gruppen. När du gör det kan användaren eller gruppen inte kommentera datatillgångar i katalogen, men de kan visa dem. 

8. Om du vill neka en användare eller grupp att registrera datatillgångar avmarkerar du **registeralternativet** för den användaren eller gruppen.

9. Om du vill neka en användare att bli ägare till en datatillgång avmarkerar du alternativet **ta över ägarskapet** för den användaren eller gruppen. 

10. Om du vill ta bort en användare/grupp från kataloganvändare klickar du på **x** för användaren/gruppen längst ned i listan. 

   ![Azure Data Catalog-kataloganvändare - ta bort användar X-ikonen](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > Vi rekommenderar att du lägger till säkerhetsgrupper i kataloganvändare i stället för att lägga till användare direkt och tilldela behörigheter. Lägg sedan till användare i säkerhetsgrupperna som matchar deras roller och deras nödvändiga åtkomst till katalogen.

## <a name="special-considerations"></a>Särskilda överväganden

- Behörigheterna som tilldelats säkerhetsgrupper är additiva. En användare finns i två grupper. En grupp har kommenterade behörigheter och en annan grupp har inga kommenterade behörigheter. Sedan har användaren kommentera behörigheter. 
- De behörigheter som uttryckligen tilldelas en användare åsidosätter de behörigheter som tilldelats grupper som användaren tillhör. I föregående exempel, säg, du uttryckligen lagt till användaren i kataloganvändare och inte tilldela kommentera behörigheter. Användaren kan inte kommentera datatillgångar även om användaren är medlem i en grupp som har kommenterade behörigheter.

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Azure Data Catalog](data-catalog-get-started.md)
