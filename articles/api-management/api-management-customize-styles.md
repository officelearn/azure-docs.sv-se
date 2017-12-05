---
title: "Anpassa sidan formatet på Azure API Management developer-portalen | Microsoft Docs"
description: "Följ stegen i den här snabbstarten att anpassa stil för elementen på Azure API Management developer-portalen."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: f427663ba1c437785c8c521925d9f733c45cb40d
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Anpassa formatet för utvecklare portalens sidor

Det finns tre vanligaste sätt att anpassa Developer-portalen i Azure API Management:
 
* [Redigera innehållet i statiska sidor och layout sidelement](api-management-modify-content-layout.md)
* Uppdatera de formatmallar som används för sidelement över developer-portalen (beskrivs i den här guiden)
* [Ändra mallarna som används för sidor som genereras av portalen](api-management-developer-portal-templates.md) (till exempel API docs produkter, autentisering av användare)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Anpassa formatet för elementen på sidorna i den **Developer** portal
> * Visa din ändring

![Anpassa format](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>Krav

+ Slutför följande Snabbstart: [skapa en instans av Azure API Management](get-started-create-service-instance.md).
+ Dessutom slutföra följande kursen: [Import och publicera din första API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="customize-the-developer-portal"></a>Anpassa Developer-portalen

1. Välj **översikt**.
2. Klicka på den **Developer-portalen** knappen ovanpå på den **översikt** fönster. Du kan också klicka på **Developer-portalen URL** länk.
3. Övre till vänster på skärmen visas en ikon som består av två penslar. Hovra över ikonen för att öppna menyn anpassning av portalen.

    ![Anpassa format](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Välj **formatmallar** från menyn för att öppna fönstret formatmalls-anpassning.

    Alla element som du kan anpassa med **formatmallar** visas på sidan
5. Ange ”rubriker färg” i den **ändra variabelvärden anpassa developer portal utseende:** fält.

    Den  **@headings-color**  elementet visas på sidan. Den här variabeln anger färgen på texten.

    ![Anpassa format](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Klicka på fältet för den  **@headings-color**  variabeln. 
    
    Färgväljaren nedrullningsbara öppnas.
7. Välj en ny färg färgväljare listrutan.

    > [!TIP]
    > Realtid förhandsgranskningen är tillgänglig för alla ändringar. En förloppsindikator längst upp i fönstret anpassning. Efter några sekunder ändras rubriktexten i färg till den valda.

8. Välj **publicera** från längst ned till vänster på anpassning meny.
9. Välj **publicera anpassningar** att göra ändringarna tillgängliga för allmänheten.

## <a name="view-your-change"></a>Visa din ändring

1. Gå till Developer-portalen.
2. Du kan se ändringen som du har gjort.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Anpassa formatet för elementen på sidorna i den **Developer** portal
> * Visa din ändring

> [!div class="nextstepaction"]
> [Anpassa Azure API Management developer-portalen med hjälp av mallar](api-management-developer-portal-templates.md)