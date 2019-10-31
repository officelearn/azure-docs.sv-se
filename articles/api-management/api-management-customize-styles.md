---
title: Anpassa sid formatet på Azure API Management Legacy Developer-portalen | Microsoft Docs
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
ms.openlocfilehash: 779d1228393cdc88f88820d8a335cb2e32424c14
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176859"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Anpassa stilen på utvecklings portalens sidor

Det finns tre vanligaste sätt att anpassa Developer-portalen i Azure API Management:
 
* [Redigera innehållet på statiska sidor och sidlayoutelement](api-management-modify-content-layout.md)
* Uppdatera stilarna som används för sidelement i utvecklarportalen (förklaras i den här guiden)
* [Ändra mallarna som används för sidor som genereras av portalen](api-management-developer-portal-templates.md) (t.ex. API-dokumentation, produkter, användarautentisering)

I den här artikeln får du lära dig hur du anpassar formatet för element på sidor i den äldre **Developer** -portalen och ser dina ändringar.

![anpassa stil](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Krav

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>Anpassa utvecklarportalen

1. Välj **Översikt**.
2. Klicka på knappen **Developer portal (bakåtkompatibelt)** överst i **översikts** fönstret.
3. Upptill på den vänstra sidan av skärmen visas en ikon med två penslar. Hovra över ikonen för att öppna menyn för anpassning av portalen.

    ![anpassa stil](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Välj **Stilar** från menyn för att öppna fönstret för anpassning av stilar.

    Alla element som du kan anpassa med **stilar** visas på sidan
5. Ange färg för rubriker i fältet **Change variable values to customize developer portal appearance:** (Anpassa variabelvärden för att anpassa utvecklarportalens utseende).

    **\@rubrik-färgs** element visas på sidan. Den här variabeln anger färgen på texten.

    ![anpassa stil](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Klicka på fältet för **\@header-färg-** variabeln. 
    
    En nedrullningsbar meny för val av färg öppnas.
7. Välj en ny färg i den nedrullningsbara menyn för val av färg.

    > [!TIP]
    > Alla ändringar visas i realtid i förhandsgranskningen. En förloppsindikator visas upptill i anpassningsfönstret. Efter några sekunder ändras rubriktexten till den färg du valde.

8. Välj **Publicera** nedtill i den vänstra sidan i fönstret med anpassningsmenyn.
9. Välj **Publicera anpassningar** för att göra ändringarna offentligt tillgängliga.

## <a name="view-your-change"></a>Visa din ändring

1. Gå till Developer-portalen.
2. Ändringen du gjorde visas.

## <a name="next-steps"></a>Nästa steg

Du kanske också vill lära dig mer [ om hur du anpassar Azure API Management-utvecklarportalen med hjälp av mallar](api-management-developer-portal-templates.md).