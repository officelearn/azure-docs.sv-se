---
title: Importera en Azure-funktionsapp som API i Azure API Management | Microsoft Docs
description: Den här självstudien visar hur du importerar en Azure-funktionsapp till Azure API Management som API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: apimpm
ms.openlocfilehash: e86bd797774448d8e4821ff02d358d420a099442
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810787"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Importera en Azure-funktionsapp som API i Azure API Management | Microsoft Docs

Azure API Management har funktioner för att importera Azure-funktionsappar som nya API:er eller lägga till dem till befintliga API:er. Processen genererar automatiskt en värdnyckel i Azure-funktionsappen, som sedan tilldelas till ett namngivet värde i Azure API Management.

Den här artikeln visar hur du importerar en Azure-funktionsapp som API i Azure API Management. Vi beskriver också testprocessen.

Du lär dig hur du:

> [!div class="checklist"]
> * Importera en Azure-funktionsapp som API
> * Lägga till en Azure-funktionsapp till ett API
> * Visa den nya Azure-funktionsappsvärdnyckeln och det namngivna Azure API Management-värdet
> * Testa API:et i Azure Portal
> * Testa API:et i utvecklarportalen

## <a name="prerequisites"></a>Nödvändiga komponenter

* Slutför snabbstarten [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
* Se till att du har en Azure Funktions-app i din prenumeration. Mer information finns i [Skapa en Azure-funktionsapp](../azure-functions/functions-create-first-azure-function.md#create-a-function-app). Det måste innehålla funktioner med HTTP-utlösare och inställningar för auktoriseringsnivå inställda på *Anonym* eller *Funktion*.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-new-api-from-azure-function-app"></a> Importera en Azure-funktionsapp som nytt API

Följ stegen nedan och skapa ett nytt API från en Azure-funktionsapp.

1. I tjänsteinstansen **Azure API Management** väljer du **API:er** i menyn till vänster.

2. I listan **Lägg till ett nytt API** väljer du **Funktionsapp**.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/add-01.png)

3. Klicka på **Bläddra** för att välja funktioner att importera.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/add-02.png)

4. Klicka på avsnittet **Funktionsapp** och välj från listan över tillgängliga funktionsappar.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/add-03.png)

5. Hitta den funktionsapp du vill importera funktioner från, klicka på den och tryck på **Välj**.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/add-04.png)

6. Välj de funktioner du vill importera och klicka på **Välj**.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Du kan endast importera de funktioner som är baserade på HTTP-utlösare och som har auktoriseringsnivåinställningen *Anonym* eller *Funktion*.

7. Växla till vyn **Fullständig** och tilldela **Produkten** till ditt nya API. Redigera de förifyllda fälten om det behövs.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/add-06.png)

8. Klicka på **Skapa**.

## <a name="append-azure-function-app-to-api"></a> Lägg till Azure-funktionsapp till befintligt API

Följ stegen nedan för att lägga till Azure-funktionsapp till befintligt API.

1. I tjänsteinstansen **Azure API Management** väljer du **API:er** i menyn till vänster.

2. Välj ett API som du vill importera en Azure-funktionsapp till. Klicka på **...** och välj **Importera** på snabbmenyn.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/append-01.png)

3. Klicka på rutan **Funktionsapp**.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/append-02.png)

4. I popup-fönstret klickar du på **Bläddra**.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/append-03.png)

5. Klicka på avsnittet **Funktionsapp** och välj från listan över tillgängliga funktionsappar.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/add-03.png)

6. Hitta den funktionsapp du vill importera funktioner från, klicka på den och tryck på **Välj**.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/add-04.png)

7. Välj de funktioner du vill importera och klicka på **Välj**.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/add-05.png)

8. Klicka på **Importera**.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/append-04.png)

## <a name="function-app-import-keys"></a> Genererade värdnyckel för Azure-funktionsapp

Import av en Azure-funktionsapp genererar automatiskt:
* värdnyckel i funktionsappen med namnet apim-{*ditt Azure API Management-tjänsteinstansnamn*},
* namngivet värde i Azure API Management-instans med namnet {*ditt Azure-funktionsappinstansnamn*}-nyckel, vilket innehåller den skapade värdnyckeln.

> [!WARNING]
> Om du tar bort eller ändrar värdet för antingen värdnyckeln för Azure-funktionsappen eller det namngivna Azure API Management-värdet bryts kommunikationen mellan tjänsterna. Värdena synkroniseras inte automatiskt.
>
> Om du behöver rotera värdnyckeln ska du se till att det namngivna värdet i Azure API Management också ändras.

### <a name="access-azure-function-app-host-key"></a>Komma åt värdnyckel för Azure-funktionsapp

1. Öppna instansen av Azure-funktionsappen.

2. Välj **Funktionsappinställningar** i översikten.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/keys-02-a.png)

3. Nyckeln finns i avsnittet **Värdnycklar**.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Komma åt det namngivna värdet i Azure API Management

Öppna instansen av Azure API Management och välj **Namngivna värden** på menyn till vänster. Azure-funktionsappnyckeln finns där.

![Lägg till från funktionsapp](./media/import-function-app-as-api/keys-01.png)

## <a name="test-in-azure-portal"></a> Testa det nya API Management-API:et i Azure-portalen

Du kan anropa åtgärder direkt från Azure-portalen. Azure-portalen är ett praktiskt sätt att visa och testa åtgärderna i ett API.  

1. Välj det API som du skapade i föregående avsnitt.

2. Välj fliken **Test**.

3. Välj en åtgärd.

    Sidan visar fält för frågeparametrar och fält för sidhuvudena. Ett av huvudena är **Ocp-Apim-Subscription-Key**, för prenumerationsnyckeln till den produkt som är associerad med det här API:et. Om du skapade API Management-instansen är du redan administratör, vilket innebär att nyckeln fylls i automatiskt. 

4. Välj **Skicka**.

    Serverdelen svarar med **200 OK** och några data.

## <a name="test-in-developer-portal"></a> Anropa en åtgärd från utvecklarportalen

Du kan även anropa åtgärder från utvecklarportalen för att testa API:er. 

1. Välj det API du skapade i Importera och publicera ett serverdels-API.

2. Välj **Developer portal** (Utvecklarportal).

    Webbplatsen för utvecklarportalen öppnas.

3. Välj det **API** som du skapade.

4. Välj den åtgärd som du vill testa.

5. Välj **Prova**.

6. Välj **Skicka**.
    
    När en åtgärd har anropats visas **svarsstatus**, **svarshuvuden** och eventuellt **svarsinnehåll** på utvecklarportalen.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)
