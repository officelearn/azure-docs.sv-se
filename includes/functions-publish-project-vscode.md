---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f1553a5c9d55366b2764877b48d0606ff8e0b370
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842187"
---
## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

I det här avsnittet ska du skapa en Function-app och relaterade resurser i din Azure-prenumeration och sedan distribuera din kod. 

1. Välj Azure-ikonen i aktivitets fältet och välj sedan knappen **distribuera till Function-appen** i avsnittet **Azure:** functions.

    ![Publicera ditt projekt till Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Ange följande information i prompten:

    ::: zone pivot="programming-language-csharp,programming-language-powershell"

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----- |
    | Välj prenumeration | Din prenumeration | Visas när du har flera prenumerationer. |
    | Välj Funktionsapp i Azure | + Skapa nytt Funktionsapp | Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure. |
    | Ange ett globalt unikt namn för Function-appen | Unikt namn | Giltiga tecken i ett namn på en funktionsapp är `a-z`, `0-9` och `-`. |
    | Välj en plats för nya resurser | Region | Välj en [region](https://azure.microsoft.com/regions/) nära dig. | 

    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python"

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----- |
    | Välj prenumeration | Din prenumeration | Visas när du har flera prenumerationer. |
    | Välj Funktionsapp i Azure | + Skapa nytt Funktionsapp | Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure. |
    | Ange ett globalt unikt namn för Function-appen | Unikt namn | Giltiga tecken i ett namn på en funktionsapp är `a-z`, `0-9` och `-`. |
    | Välj en körning | Din version | Välj den språk version som du har kört lokalt. |
    | Välj en plats för nya resurser | Region | Välj en [region](https://azure.microsoft.com/regions/) nära dig. | 

    ::: zone-end

    
1.  När det är slutfört skapas följande Azure-resurser i din prenumeration:

    + **[Resurs grupp](../articles/azure-resource-manager/management/overview.md)** : innehåller alla Azure-resurser som skapats. Namnet baseras på namnet på din Function-app.
    + **[Lagrings konto](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : ett standard lagrings konto skapas med ett unikt namn som baseras på namnet på din Function-app.
    + **[Värd plan](../articles/azure-functions/functions-scale.md)** : en förbruknings plan skapas i regionen Västra USA för att vara värd för din server lös Function-app.
    + **Function-app**: ditt projekt distribueras till och körs i den här nya Function-appen.
    + **[Application Insights]()** : en instans, som är ansluten till din Function-app, skapas baserat på funktions namnet.

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. 
    
1. Välj **Visa utdata** i det här meddelandet för att Visa skapande-och distributions resultaten, inklusive de Azure-resurser som du har skapat.

    ![Skapa fullständig avisering](media/functions-publish-project-vscode/function-create-notifications.png)

1. Gå tillbaka till avsnittet **Azure: Functions** i sido fältet och expandera den nya Function-appen under din prenumeration. Expandera **funktioner**, högerklicka på (Windows) eller Ctrl + klicka (MacOS) på **HttpExample**och välj sedan **Kopiera funktions webb adress**.

    ![Kopiera funktions webb adressen för den nya HTTP-utlösaren](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
