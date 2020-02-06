---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 256510f855256e648ae9203f46eb9f66c9ffaed6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029275"
---
## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

I det här avsnittet ska du skapa en Function-app och relaterade resurser i din Azure-prenumeration och sedan distribuera din kod. 

1. Välj Azure-ikonen i aktivitets fältet och välj sedan knappen **distribuera till Function-appen** i avsnittet **Azure:** functions.

    ![Publicera ditt projekt till Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Ange följande information i prompten:

    + **Välj prenumeration**: Välj den prenumeration som ska användas. Du ser inte det här om du bara har en prenumeration.

    + **Välj Funktionsapp i Azure**: Välj `+ Create new Function App` (inte `Advanced`). Den här artikeln stöder inte det [avancerade publicerings flödet](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options). 
    
    >[!IMPORTANT]
    > Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure. 
    
    + **Ange ett globalt unikt namn för Function-appen**: Ange ett namn som är giltigt i en URL-sökväg. Namnet du skriver verifieras för att säkerställa att det är unikt i Azure Functions. 
    
    ::: zone pivot="programming-language-python"
    + **Välj en körning**: Välj den version av python som du har kört lokalt. Du kan kontrol lera din version med hjälp av kommandot `python --version`.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Välj en körning**: Välj den version av Node. js som du har kört lokalt. Du kan kontrol lera din version med hjälp av kommandot `node --version`.
    ::: zone-end

    + **Välj en plats för nya resurser**: om du vill ha bättre prestanda väljer du en [region](https://azure.microsoft.com/regions/) nära dig. 
    
1.  När det är slutfört skapas följande Azure-resurser i din prenumeration:

    + **[Resurs grupp](../articles/azure-resource-manager/management/overview.md)** : innehåller alla Azure-resurser som skapats. Namnet baseras på namnet på din Function-app.
    + **[Lagrings konto](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : ett standard lagrings konto skapas med ett unikt namn som baseras på namnet på din Function-app.
    + **[Värd plan](../articles/azure-functions/functions-scale.md)** : en förbruknings plan skapas i regionen Västra USA för att vara värd för din server lös Function-app.
    + **Function-app**: ditt projekt distribueras till och körs i den här nya Function-appen.
    + **Application Insights**: en instans, som är ansluten till din Function-app, skapas baserat på funktions namnet.

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. 
    
1. Välj **Visa utdata** i det här meddelandet för att Visa skapande-och distributions resultaten, inklusive de Azure-resurser som du har skapat. Om du saknar meddelandet väljer du klock ikonen i det nedre högra hörnet för att se den igen.

    ![Skapa fullständig avisering](media/functions-publish-project-vscode/function-create-notifications.png)
