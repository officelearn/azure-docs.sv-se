---
title: Flytta en begränsad utvärderingsversion projektet till Azure
titlesuffix: Azure Cognitive Services
description: Lär dig hur du flyttar en begränsad utvärderingsversion-projektet till Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 76207e83e31ba3919da80e4ecc99435e88c76c66
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56654591"
---
# <a name="how-to-move-your-limited-trial-project-to-azure-using-the-customvisionai-site"></a>Hur du flyttar begränsad utvärderingsversion projektet till Azure med hjälp av webbplatsen CustomVision.ai


Eftersom Custom Vision Service är nu i [förhandsversion av Azure](https://azure.microsoft.com/services/preview/),-support för för begränsad utvärderingsversion projekt utanför Azure. Det här dokumentet får du lära dig hur du använder den [Custom Vision webbplats](https://customvision.ai) att flytta projektet begränsad utvärderingsversion som ska associeras med en Azure-resurs. 

> [!NOTE]
> När du flyttar dina Custom Vision-projekt till en Azure-resurs, de underliggande Ärv [behörigheter]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) för den Azure-resursen. Om andra användare i din organisation är ägare till Azure-resursen ditt projekt är i, de kommer att kunna komma åt ditt projekt på den [Custom Vision webbplats](https://customvision.ai). På samma sätt kan tas dina resurser bort dina projekt.  


En introduktion till Azure begreppet prenumerationerna och resurserna finns på [utvecklarguiden för Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)


## <a name="prerequisites"></a>Förutsättningar

Du behöver ett giltig Azure-prenumeration som är associerade med samma Microsoft-konto eller Azure Active Directory (AAD)-konto som du använder för att logga in på den [Custom Vision webbplats](https://customvision.ai). 

Om du inte har ett Azure-konto, [skapa ett konto](https://azure.microsoft.com/free/) utan kostnad.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Skapa anpassade Vision resurser i Azure portal
Om du vill använda Custom Vision Service med Azure behöver du skapa anpassade Vision Inlärnings- och Förutsägelsetransaktioner resurser i den [Azure-portalen](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

 Att flytta projektet använder det här [Custom Vision webbplats](https://customvision.ai) upplevelse, måste du skapa dina resurser i regionen södra centrala USA, eftersom alla projekt i begränsad utvärderingsversion finns i södra centrala USA. 

Flera projekt kan kopplas till en enskild resurs. Mer information om [priser och begränsningar](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) är tillgänglig. Om du vill fortsätta använda Custom Vision Service utan kostnad, kan du välja F0 nivå i Azure-portalen. 


## <a name="move-your-limited-trial-project-to-an-azure-resource"></a>Flytta projektet begränsad utvärderingsversion till en Azure-resurs

1.  I din webbläsare, navigerar du till den [Custom Vision webbplats](https://customvision.ai) och välj __logga in__. Öppna projektet som du vill migrera till ett Azure-konto. 
2.  Öppna inställningssidan för ditt projekt genom att klicka på kugghjulsikonen i det övre högra hörnet på skärmen. 

    ![Projektinställningar är kugghjulsikonen längst upp till höger på projektsidan för.](./media/move-your-project-to-azure/settings-icon.png)


3. Klicka på __flytta till Azure__.

    ![Flytta till Azure-knappen är längst ned till vänster på sidan Inställningar.](./media/move-your-project-to-azure/move-to-azure.jpg)


4. I listrutan på den __flytta till Azure__ knapp, Välj Azure-resursen du vill flytta ditt projekt. Klicka på __flytta__. 

5. Om du inte ser Azure-resursen du skapade tidigare för Custom Vision Service, kan det vara i en annan katalog. Följ anvisningarna nedan om du vill flytta ditt projekt till en resurs i en annan katalog. 

    ![Projektfönstret för migrering.](./media/move-your-project-to-azure/Project_Migration_Window.jpg)


## <a name="move-your-limited-trial-project-to-an-azure-resource-in-another-directory"></a>Flytta projektet begränsad utvärderingsversion till en Azure-resurs i en annan katalog 

> [!NOTE]
> I både Azure-portalen och CustomVision.ai kan välja du din katalog från listrutan Användare-menyn i det övre högra hörnet på skärmen.   


1. Identifiera vilken katalog som din Azure-resurs som finns i. Du hittar den katalog som visas under ditt användarnamn längst upp till höger på Azure portal menyraden. 

    ![Din katalog visas under ditt användarnamn längst upp till höger på Azure portal menyraden. .](./media/move-your-project-to-azure/identify_directory.jpg)

2. Hitta resurs-ID för din resurs för Custom Vision-utbildning. Du hittar detta i Azure portal genom att öppna resursen Custom Vision-utbildning och välja ”egenskaper” under avsnittet ”resurshantering”. Resurs-ID blir det. 

    ![Hitta din resurs-ID i i Azure portal genom att öppna resursen Custom Vision-utbildning och välja ”egenskaper” under avsnittet ”resurshantering”.](./media/move-your-project-to-azure/resource_ID_azure_portal.jpg)


3. Du kan också du kan hitta resurs-ID för din anpassade visionen resursen direkt på webbplatsen för Custom Vision [inställningssidan]( https://www.customvision.ai/projects#/settings). Du måste växla till din Azure-resurs är i samma katalog.

    ![Resurs-ID anges för varje resurs på inställningssidan på Custom Vision-webbplatsen.](./media/move-your-project-to-azure/resource_ID_CVS_portal.jpg)

4. Nu när du har din resurs-ID kan du gå tillbaka till Custom Vision-projektet som du försöker flytta från en begränsad utvärderingsversion till en Azure-resurs. Påminnelse, du kan behöva gå tillbaka till din ursprungliga directory för att hitta den. Följ anvisningarna [ovan](#move-your-limited-trial-project-to-an-azure-resource) att öppna sidan dina Projektinställningar och välj __flytta till Azure__. 


5. Markera kryssrutan för ”flytt till en annan Azure-katalog”? i flytten till Azure-fönstret. Välj den katalog som du vill flytta ditt projekt och ange resurs-ID för den resurs som du flyttar ditt projekt. Klicka på __flytta__. 



5. Kom ihåg att projektet är nu i en annan katalog. För att hitta projektet, behöver du växla till samma katalog på webbportalen Custom Vision som ditt projekt i. I Azure-portalen och [Custom Vision webbplats](https://customvision.ai), du kan välja din katalog från den nedrullningsbara menyn i det övre högra hörnet på skärmen. 


