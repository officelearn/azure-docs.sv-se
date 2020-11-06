---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: e9465e014c1b8770f8968cbee89a5b455ad53470
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93425131"
---
## <a name="run-the-function-in-azure"></a>Kör funktionen i Azure

1. Gå tillbaka till avsnittet **Azure: Functions** i sido fältet och expandera den nya Function-appen under din prenumeration. Expandera **funktioner** , högerklicka (Windows) eller <kbd>CTRL-</kbd> klicka (MacOS) på **HttpExample** och välj sedan **Kopiera funktions webb adress**.

    ![Kopiera funktions webb adressen för den nya HTTP-utlösaren](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Klistra in URL: en för HTTP-begäran i webbläsarens Adress fält, Lägg till `name` frågesträngen som `?name=Functions` i slutet av den här URL: en och kör sedan begäran. Den URL som anropar den HTTP-utlösta funktionen ska ha följande format:

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/httpexample?name=Functions
    ```

    I följande exempel visas svaret i webbläsaren till den fjärranslutna GET-begäran som returnerades av funktionen:

    ![Funktionssvar i webbläsaren](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
