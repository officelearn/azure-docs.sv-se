---
title: Anpassa sidformat på api management äldre utvecklarportal
titleSuffix: Azure API Management
description: Följ stegen i den här snabbstarten för att anpassa stilen för elementen i utvecklarportalen för Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 664686511df8f310295a9f6ed6bc689b3a999544
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75430737"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Anpassa stilen på utvecklarportalsidorna

Det finns tre vanligaste sätten att anpassa utvecklarportalen i Azure API Management:
 
* [Redigera innehållet på statiska sidor och sidlayoutelement](api-management-modify-content-layout.md)
* Uppdatera stilarna som används för sidelement i utvecklarportalen (förklaras i den här guiden)
* [Ändra mallarna som används för sidor som genereras av portalen](api-management-developer-portal-templates.md) (t.ex. API-dokumentation, produkter, användarautentisering)

I den här artikeln får du lära dig hur du anpassar elementens stil på sidor i den äldre **utvecklarportalen** och visar dina ändringar.

![anpassa stil](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Krav

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>Anpassa utvecklarportalen

1. Välj **Översikt**.
2. Klicka på knappen **Utvecklarportal (äldre)** högst upp i **översiktsfönstret.**
3. Upptill på den vänstra sidan av skärmen visas en ikon med två penslar. Hovra över ikonen för att öppna menyn för anpassning av portalen.

    ![anpassa stil](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Välj **Stilar** från menyn för att öppna fönstret för anpassning av stilar.

    Alla element som du kan anpassa med **stilar** visas på sidan
5. Ange färg för rubriker i fältet **Change variable values to customize developer portal appearance:** (Anpassa variabelvärden för att anpassa utvecklarportalens utseende).

    Elementet för ** \@rubriker och färg** visas på sidan. Den här variabeln anger färgen på texten.

    ![anpassa stil](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Klicka på fältet för ** \@variabeln rubriker-färg.** 
    
    En nedrullningsbar meny för val av färg öppnas.
7. Välj en ny färg i den nedrullningsbara menyn för val av färg.

    > [!TIP]
    > Alla ändringar visas i realtid i förhandsgranskningen. En förloppsindikator visas upptill i anpassningsfönstret. Efter några sekunder ändras rubriktexten till den färg du valde.

8. Välj **Publicera** nedtill i den vänstra sidan i fönstret med anpassningsmenyn.
9. Välj **Publicera anpassningar** för att göra ändringarna offentligt tillgängliga.

## <a name="view-your-change"></a>Visa din ändring

1. Navigera till utvecklarportalen.
2. Ändringen du gjorde visas.

## <a name="next-steps"></a>Nästa steg

Du kanske också vill lära dig mer [ om hur du anpassar Azure API Management-utvecklarportalen med hjälp av mallar](api-management-developer-portal-templates.md).