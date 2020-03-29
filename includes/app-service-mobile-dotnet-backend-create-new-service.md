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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67325803"
---
1. Logga in på [Azure-portalen].

2. Klicka på **Skapa en resurs**.

3. Skriv **Web App**i sökrutan .
    
4. Välj **Web App** på Marketplace i resultatlistan.

5. Välj din **prenumerations-** och **resursgrupp** (välj en befintlig resursgrupp _eller_ skapa en ny (med samma namn som appen)).

6. Välj ett unikt **namn på** din webbapp.

7. Välj **standardpubliceringsalternativet** som **kod**.

8. I **runtime-stacken**måste du välja en version under **ASP.NET** eller **Node**. Om du skapar en .NET-backend väljer du en version under ASP.NET. Annars om du riktar in dig på ett nodbaserat program väljer du en av versionen från Nod.

9. Välj rätt **operativsystem**, antingen Linux eller Windows. 

10. Välj den **region** där du vill att den här appen ska distribueras. 

11. Välj lämplig **appserviceplan** och tryck på **Granska och skapa**. 

12. Under **Resursgrupp**, väljer du en befintlig resursgrrupp _eller_ skapar en ny (med samma namn som din app).

13. Klicka på **Skapa**. Vänta några minuter på att tjänsten ska distribueras innan du fortsätter. Titta på meddelandeikonen (klockan) i portalrubriken för statusuppdateringar.

14. När distributionen är klar klickar du på avsnittet **Distributionsinformation** och klickar sedan på resursen för typ **Microsoft.Web/sites**. Den navigerar dig till apptjänstens webbapp som du just skapade. 

15. Klicka på **konfigurationsbladet** under **Inställningar** och klicka på knappen **Ny programinställning** i **programinställningarna.**

16. På sidan Ange **Namn** som **MobileAppsManagement_EXTENSION_VERSION** och Värde som **senaste** och tryck på OK på **sidan Lägg till/redigera programinställning.**

Du är redo att använda den här nyskapade App Service Web-appen som en mobilapp.

<!-- URLs. -->
[Azure-portal]: https://portal.azure.com/