---
title: Använd revisioner för att göra bakåtkompatibla ändringar på ett säkert sätt i Azure API Management | Microsoft Docs
description: Följ stegen i den här självstudien för att lära dig hur du gör ändringar som är bakåtkompatibla i API Management.
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
ms.openlocfilehash: 864c269da61bcea885249021a44fe0259ca83e90
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>Använd revideringar för att göra bakåtkompatibla ändringar på ett säkert sätt
När ditt API är klart och börjar användas av utvecklare, måste du vara försiktig när du gör ändringar för det API:et så att du inte stör anropen av API:et. Det är också bra att informera utvecklarna om de ändringar du gjort. Det kan vi göra i Azure API Management med hjälp av **revisioner**. Mer information finns i avsnittet om [versioner & revisioner](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/) och [API-versioner med Azure API Management](https://blogs.msdn.microsoft.com/apimanagement/2017/09/13/api-versioning-with-azure-api-management/).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till en ny version
> * Göra bakåtkompatibla ändringar till din revision
> * Aktualisera dina revisioner och lägga till en ändringsloggpost
> * Gå till utvecklarportalen för att se dina ändringar och ändringsloggen

![Ändringslogg på utvecklarportalen](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Nödvändiga komponenter

+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Lägga till en ny version

1. Välj sidan **API:er**.
2. Välj **konferens-API** från listan över API (eller andra API som du vill lägga till revisioner i).
3. Klicka på fliken **Revisioner** från menyn upptill på sidan.
4. Välj **+ Lägg till revision**

    > [!TIP]
    > Du kan också välja **Lägg till revision** i snabbmenyn (**...**) för API:et.
    
    ![Revisionsmenyn upptill på sidan](media/api-management-getstarted-revise-api/TopMenu.PNG)

5. Ange en beskrivning för din nya revision så att du kommer ihåg vad den ska användas för.
6. Välj **Skapa**
7. Nu skapas en ny version.

    > [!NOTE]
    > Det ursprungliga API:et ligger kvar i **Revision 1**. Det är den här revisionen dina användare fortsätter att anropa tills du väljer att aktualisera en annan revision aktuella.

## <a name="make-non-breaking-changes-to-your-revision"></a>Göra bakåtkompatibla ändringar till din revision

1. Välj **Conference API** (konferens-API) från listan över API.
2. Överst på skärmen väljer du fliken **Design**.
3. Observera att **revisionsväljaren** (direkt ovanför designfliken) visar den aktuella revisionen som **Revision 2**.

    > [!TIP]
    > Använd revisionsväljaren för att växla mellan de revisioner du vill arbeta med.

4. Välj **+ Lägg till åtgärd**.
5. Ange att den nya åtgärden ska vara **POST**, och sätt namn och visningsnamn för åtgärden som **test**
6. **Spara** den nya åtgärden.
7. Vi har nu ändrat till **Revision 2**. Använd **revisionsväljaren** upptill på sidan för att växla tillbaka till **Revision 1**.
8. Observera att den nya åtgärden inte syns i **Revision 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Aktualisera dina revisioner och lägga till en ändringsloggpost
1. Välj fliken **Revisioner** från menyn upptill på sidan.

    ![Revisionsmenyn på revisionsskärmen.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
1. Öppna snabbmenyn (**...**) för **revision 2**.
2. Välj **Make Current** (Gör aktuell). Markera **Post to Public Change log for this API** (Posta till publik ändringslogg för detta API) för att skicka meddelanden om ändringen.
3. Välj **Post to Public Change Log for this API** (Posta till publik ändringslogg för detta API)
4. Ange en beskrivning för din ändring som utvecklare kan se, till exempel **Testning av revisioner. Ny ”teståtgärd” tillagd.**
5. Nu är **Revision 2** aktuell.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Gå till utvecklarportalen för att se dina ändringar och ändringsloggen
1. Välj **API:er** i Azure Portal
2. Välj **Utvecklarportal** i den översta menyn.
3. Välj **API:er** och sedan **konferens-API**.
4. Lägg märke till att din nya **teståtgärd** nu är tillgänglig.
5. Välj **ändringshistoriken för API** under namnet på API:et.
6. Observera att din post i ändringsloggen visas i den här listan.

    ![Utvecklarportalen](media/api-management-getstarted-revise-api/developer_portal.PNG)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till en ny version
> * Göra bakåtkompatibla ändringar till din revision
> * Aktualisera dina revisioner och lägga till en ändringsloggpost
> * Gå till utvecklarportalen för att se dina ändringar och ändringsloggen

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Publicera flera versioner av ditt API](api-management-get-started-publish-versions.md)