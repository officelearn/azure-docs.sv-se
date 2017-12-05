---
title: "Gör hårt ändringar på ett säkert sätt i Azure API Management med hjälp av revisioner | Microsoft Docs"
description: "Följ stegen i den här kursen lär dig hur du gör ändringar för hårt med ändringar i API-hantering."
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
ms.openlocfilehash: 50d7ac17faebb34f1a1f9a3259aa0196950391d9
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>Använd ändringar i ändrar hårt på ett säkert sätt
När din API är redo att sätta igång och startar som ska användas av utvecklare, måste vanligtvis du vara noga för att göra ändringar till den API och samtidigt inte att störa anropare av din API. Det är också användbart att låta utvecklare känna till om de ändringar du gjort. Vi kan göra detta i Azure API Management med hjälp av **revisioner**. Mer information finns i [versioner & revisioner](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/) och [API-versioner med Azure API Management](https://blogs.msdn.microsoft.com/apimanagement/2017/09/13/api-versioning-with-azure-api-management/).

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Lägg till en ny version
> * Ändra hårt din omarbetning
> * Gör din omarbetning aktuella och lägger till en ändring loggpost
> * Bläddra developer-portalen om du vill se ändringar och Ändringslogg

![Ändringslogg på Developer-portalen](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Krav

+ Slutför följande Snabbstart: [skapa en instans av Azure API Management](get-started-create-service-instance.md).
+ Dessutom slutföra följande kursen: [Import och publicera din första API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-a-new-revision"></a>Lägg till en ny version

1. Välj **API: er** sidan.
2. Välj **konferens API** från listan över API (eller andra API som du vill lägga till revisioner).
3. Klicka på den **revisioner** fliken på menyn längst upp på sidan.
4. Välj **+ Lägg till Revision**

    > [!TIP]
    > Du kan också välja **lägga till Revision** på snabbmenyn (**...** ) API.
    
    ![Revisioner menyn övre delen av skärmen](media/api-management-getstarted-revise-api/TopMenu.PNG)

5. Ange en beskrivning för en ny revision för att komma ihåg vad den ska användas för.
6. Välj **skapa**
7. Nu skapas en ny version.

    > [!NOTE]
    > Det finns kvar i din ursprungliga API **Revision 1**. Det här är den revisionen användarna fortsätta att anropa förrän du väljer att göra en annan revision aktuella.

## <a name="make-non-breaking-changes-to-your-revision"></a>Ändra hårt din omarbetning

1. Välj **konferens API** från listan över API.
2. Välj den **Design** fliken längst upp på skärmen.
3. Observera att den **revision selector** (direkt ovanför fliken design) visar den aktuella versionen som **version 2**.

    > [!TIP]
    > Använd revision selector för att växla mellan revisioner som du vill arbeta med.

4. Välj **+ Lägg till åtgärden**.
5. Ange ditt nya åtgärden ska kunna **POST**, och namn och visningsnamn för åtgärden som **testa**
6. **Spara** nya igen.
7. Vi har nu gjort en ändring av **version 2**. Använd den **Revision Selector** överst på sidan för att växla tillbaka till **Revision 1**.
8. Observera att nya åtgärden inte visas i **Revision 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Gör din omarbetning aktuella och lägger till en ändring loggpost
1. Välj den **revisioner** fliken på menyn längst upp på sidan.

    ![Menyn revision på skärmen revision.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
1. Öppna snabbmenyn (**...** ) för **version 2**.
2. Välj **se aktuella**. Kontrollera **efter att offentliga ändringsloggen för detta API**, om du vill skicka information om den här ändringen.
3. Välj **Post till offentliga ändringsloggen för detta API**
4. Ange en beskrivning för din ändring som utvecklare visas, till exempel **testning revisioner. Tillagda nya ”test” åtgärden.**
5. **Revision 2** nu är aktuell.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Bläddra developer-portalen om du vill se ändringar och Ändringslogg
1. Välj i Azure-portalen **API: er**
2. Välj **Utvecklarportalen** på den översta menyn.
3. Välj **API: er**, och välj sedan **konferens API**.
4. Lägg märke till din nya **testa** åtgärden är nu tillgänglig.
5. Välj **API ändringshistoriken** från nedan API-namnet.
6. Observera att din ändring loggpost visas i listan.

    ![Utvecklarportalen](media/api-management-getstarted-revise-api/developer_portal.PNG)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägg till en ny version
> * Ändra hårt din omarbetning
> * Gör din omarbetning aktuella och lägger till en ändring loggpost
> * Bläddra developer-portalen om du vill se ändringar och Ändringslogg

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Publicera flera versioner av ditt API](api-management-get-started-publish-versions.md)