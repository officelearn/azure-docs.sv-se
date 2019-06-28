---
title: ta med fil
description: ta med fil
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67325803"
---
1. Logga in på [Azure Portal].

2. Klicka på **Skapa en resurs**.

3. I sökrutan skriver **Webbapp**.
    
4. I resultatlistan väljer **Webbapp** från Marketplace.

5. Välj din **prenumeration** och **resursgrupp** (Välj en befintlig resursgrupp _eller_ skapa en ny (med samma namn som din app)).

6. Välj ett unikt **namn** på din webbapp.

7. Välj standard **publicera** beroende på **kod**.

8. I den **körningsstack**, du måste välja en version under **ASP.NET** eller **noden**. Om du skapar en .NET-serverdel kan du välja en version under ASP.NET. Annars om du arbetar med en nod baserat program, Välj en av versionen från noden.

9. Välja rätt **operativsystemet**, Linux eller Windows. 

10. Välj den **Region** där du vill att den här appen som ska distribueras. 

11. Välj lämplig **Apptjänstplan** och därefter **granska och skapa**. 

12. Under **Resursgrupp**, väljer du en befintlig resursgrrupp _eller_ skapar en ny (med samma namn som din app).

13. Klicka på **Skapa**. Vänta några minuter på att tjänsten ska distribueras innan du fortsätter. Titta på meddelandeikonen (klockan) i portalrubriken för statusuppdateringar.

14. När distributionen är klar klickar du på den **distributionsinformation** och sedan klicka på den resurstyp **Microsoft.Web/sites**. Det går du till App Service Web App som du nyss skapade. 

15. Klicka på den **Configuration** bladet under **inställningar** och i den **programinställningar**, klickar du på den **nya programinställning** knappen.

16. I den **Lägg till/redigera programinställning** anger **namn** som **MobileAppsManagement_EXTENSION_VERSION** och värdet som **senaste** och Tryck på OK.

Du är allt klart att använda den nya App Service Web app som en mobil app.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/