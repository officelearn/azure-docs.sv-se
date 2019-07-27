---
title: Flytta ett begränsat utvärderings projekt till Azure
titleSuffix: Azure Cognitive Services
description: Lär dig hur du flyttar ett begränsat utvärderings projekt till Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 22c3767dfac1e377890f1e01517d18263e694854
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560930"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Så här flyttar du ditt begränsade utvärderings projekt till Azure

När Custom Vision Service har flyttat till Azure avslutas stödet för begränsade utvärderings projekt utanför Azure. I det här dokumentet får du se hur du använder Custom Vision API: er för att kopiera ett begränsat utvärderings projekt till en Azure-resurs.

Stöd för visning av begränsade utvärderings projekt på den [Custom vision webbplatsen](https://customvision.ai) avslutades den 25 mars 2019. Det här dokumentet visar nu hur du använder Custom Vision API: er med ett [Python-skript för migrering](https://github.com/Azure-Samples/custom-vision-move-project) på GitHub) för att duplicera projektet till en Azure-resurs.

Mer information, inklusive nyckel deadlines i den begränsade utvärderings versionen, finns i [viktig](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) information eller i e-postkommunikationer som skickas till ägare av begränsade utvärderings projekt.

Med hjälp av [skriptet för migrering](https://github.com/Azure-Samples/custom-vision-move-project) kan du återskapa ett projekt genom att ladda ned och sedan ladda upp alla Taggar, regioner och bilder i den aktuella iterationen. Det låter dig med ett nytt projekt i din nya prenumeration som du sedan kan träna.

## <a name="prerequisites"></a>Förutsättningar

- Du behöver en giltig Azure-prenumeration som är associerad med Microsoft-konto-eller Azure Active Directory-kontot (AAD) som du vill använda för att logga in på [Custom vision webbplats](https://customvision.ai). 
    - Om du inte har ett Azure-konto kan du [skapa ett konto](https://azure.microsoft.com/free/) kostnads fritt.
    - En introduktion till Azure-koncepten för prenumerationer och resurser finns i [Azure Developer Guide.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)
-  [Python](https://www.python.org/downloads/)
- [–](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Skapa Custom Vision resurser i Azure Portal

Om du vill använda Custom Vision Service med Azure måste du skapa Custom Vision utbildning och förutsägelse resurser i [Azure Portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Flera projekt kan associeras med en enda resurs. Mer information om [priser och begränsningar](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) finns tillgängliga. Om du vill fortsätta att använda Custom Vision Service kostnads fritt kan du välja nivån F0 i Azure Portal. 

> [!NOTE]
> När du flyttar Custom Vision-projektet till en Azure-resurs, ärver den underliggande [behörigheter]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) för den Azure-resursen. Om andra användare i din organisation är ägare till den Azure-resurs som ditt projekt finns i, kommer de att kunna komma åt ditt projekt på den [Custom vision webbplatsen](https://customvision.ai). Om du tar bort dina resurser raderas dina projekt på samma sätt.  

## <a name="find-your-limited-trial-project-information"></a>Hitta din begränsade utvärderings projekt information

Om du vill flytta projektet behöver du _projekt-ID_ och _utbildnings nyckel_ för projektet som du försöker migrera. Om du inte har den här informationen kan du [https://limitedtrial.customvision.ai/projects](https://limitedtrial.customvision.ai/projects) gå till för att hämta ID och nyckel för varje projekt. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Använd python-exempel koden för att kopiera projektet till Azure

Följ [anvisningarna i exempel koden](https://github.com/Azure-Samples/custom-vision-move-project)med din begränsade utvärderings nyckel och projekt-ID som "källa"-material och nyckeln från den nya Azure-resurs som du skapade som "mål".

Som standard är alla begränsade utvärderings projekt värdbaserade i USA, södra centrala Azure-regionen.

## <a name="next-steps"></a>Nästa steg

Projektet har nu flyttats till en Azure-resurs. Du måste uppdatera dina utbildnings-och förutsägelse nycklar i alla program som du har skrivit.

Om du vill visa projektet på [Custom vision webbplats](https://customvision.ai)loggar du in med samma konto som du använde för att logga in på Azure Portal. Om du inte ser ditt projekt kontrollerar du att du befinner dig i samma katalog på [Custom vision webbplats](https://customvision.ai) som katalogen där dina resurser finns i Azure Portal. I både Azure Portal-och CustomVision.ai kan du välja din katalog på den nedrullningsbara menyn i det övre högra hörnet på skärmen.