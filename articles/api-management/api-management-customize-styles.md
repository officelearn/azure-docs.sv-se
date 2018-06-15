---
title: Anpassa sidstilen i utvecklarportalen i Azure API Management | Microsoft Docs
description: Följ stegen i den här snabbstarten för att anpassa stilen för elementen i utvecklarportalen för Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: d1f638c9825ea5eedf6eaee0e0ca2ccfd5a491bc
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "33933719"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Anpassa stilen på utvecklarportalens sidor

Det finns tre vanliga sätt att anpassa utvecklarportalen i Azure API Management:
 
* [Redigera innehållet på statiska sidor och sidlayoutelement](api-management-modify-content-layout.md)
* Uppdatera stilarna som används för sidelement i utvecklarportalen (förklaras i den här guiden)
* [Ändra mallarna som används för sidor som genereras av portalen](api-management-developer-portal-templates.md) (t.ex. API-dokumentation, produkter, användarautentisering)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Anpassa stilen på elementen på sidorna i **utvecklarportalen**
> * Visa din ändring

![anpassa stil](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>Anpassa utvecklarportalen

1. Välj **Översikt**.
2. Klicka på knappen för **utvecklarportalen** upptill i fönstret **Översikt**. Du kan också klicka på länken för **URL:en för utvecklarportalen**.
3. Upptill på den vänstra sidan av skärmen visas en ikon med två penslar. Hovra över ikonen för att öppna menyn för anpassning av portalen.

    ![anpassa stil](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Välj **Stilar** från menyn för att öppna fönstret för anpassning av stilar.

    Alla element som du kan anpassa med **stilar** visas på sidan
5. Ange färg för rubriker i fältet **Change variable values to customize developer portal appearance:** (Anpassa variabelvärden för att anpassa utvecklarportalens utseende).

    Elementen **@headings-color** visas på sidan. Den här variabeln anger färgen på texten.

    ![anpassa stil](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Klicka på fältet för variabeln **@headings-color**. 
    
    En nedrullningsbar meny för val av färg öppnas.
7. Välj en ny färg i den nedrullningsbara menyn för val av färg.

    > [!TIP]
    > Alla ändringar visas i realtid i förhandsgranskningen. En förloppsindikator visas upptill i anpassningsfönstret. Efter några sekunder ändras rubriktexten till den färg du valde.

8. Välj **Publicera** nedtill i den vänstra sidan i fönstret med anpassningsmenyn.
9. Välj **Publicera anpassningar** för att göra ändringarna offentligt tillgängliga.

## <a name="view-your-change"></a>Visa din ändring

1. Gå till utvecklarportalen.
2. Ändringen du gjorde visas.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Anpassa stilen på elementen på sidorna i **utvecklarportalen**
> * Visa din ändring

> [!div class="nextstepaction"]
> [Anpassa utvecklarportalen i Azure API Management med mallar](api-management-developer-portal-templates.md)