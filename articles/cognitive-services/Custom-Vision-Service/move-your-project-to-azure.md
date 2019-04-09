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
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056885"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Så här flyttar du begränsad utvärderingsversion projektet till Azure

Som Custom Vision Service är klar flytten till Azure, går stöd för begränsad utvärderingsversion projekt utanför Azure ut. Det här dokumentet visar hur du använder API: er för Custom Vision för att kopiera projektet begränsad utvärderingsversion till en Azure-resurs.

Stöd för att visa begränsad utvärderingsversion projekt på den [Custom Vision webbplats](https://customvision.ai) upphörde den 25 mars 2019. Det här dokumentet nu visar hur du använder anpassade Vision API: er med en [migrering python-skriptet](https://github.com/Azure-Samples/custom-vision-move-project) på GitHub) att duplicera projektet till en Azure-resurs.

Mer information, inklusive viktiga tidsgränser i begränsad utvärderingsversion utfasning-processen finns i [viktig](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) eller e-postmeddelanden som skickas till innehavare av begränsad utvärderingsversion projekt.

Den [migreringsskriptet](https://github.com/Azure-Samples/custom-vision-move-project) kan du skapa ett projekt genom att hämta och överföra alla taggar, regioner, och bilder i din aktuella iteration. Den lämnar du med ett nytt projekt i din nya prenumeration som du kan sedan träna.

## <a name="prerequisites"></a>Förutsättningar

- Du behöver ett giltig Azure-prenumeration som är associerade med Microsoft-konto eller Azure Active Directory (AAD)-konto som du vill använda för att logga in på den [Custom Vision webbplats](https://customvision.ai). 
    - Om du inte har ett Azure-konto, [skapa ett konto](https://azure.microsoft.com/free/) utan kostnad.
    - En introduktion till Azure begreppet prenumerationerna och resurserna finns på [utvecklarguiden för Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions).
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Skapa anpassade Vision resurser i Azure portal

Om du vill använda Custom Vision Service med Azure behöver du skapa anpassade Vision Inlärnings- och Förutsägelsetransaktioner resurser i den [Azure-portalen](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Flera projekt kan kopplas till en enskild resurs. Mer information om [priser och begränsningar](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) är tillgänglig. Om du vill fortsätta använda Custom Vision Service utan kostnad, kan du välja F0 nivå i Azure-portalen. 

> [!NOTE]
> När du flyttar ditt Custom Vision-projekt till en Azure-resurs, ärver det underliggande [behörigheter]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) för den Azure-resursen. Om andra användare i din organisation är ägare till Azure-resursen ditt projekt är i, de kommer att kunna komma åt ditt projekt på den [Custom Vision webbplats](https://customvision.ai). På samma sätt kan tas dina resurser bort dina projekt.  

## <a name="find-your-limited-trial-project-information"></a>Hitta begränsad utvärderingsversion projektinformation

Om du vill flytta ditt projekt, måste den _projektet ID_ och _utbildning nyckeln_ för projektet som du försöker migrera. Om du inte har den här informationen kan du gå [ https://limitedtrial.customvision.ai/projects ](https://limitedtrial.customvision.ai/projects) att hämta ID och nyckel för varje projekt. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Använda Python-exempelkoden för att kopiera projektet till Azure

Följ den [exempel kod instruktioner](https://github.com/Azure-Samples/custom-vision-move-project)med begränsad utvärderingsversion nyckeln och projektet ID som materialet som ”källa” och nyckeln från den nya Azure-resurs som du skapade som ”mål”.

Som standard finns alla begränsad utvärderingsversion projekt i södra centrala USA Azure-region.

## <a name="next-steps"></a>Nästa steg

Projektet har nu flyttats till en Azure-resurs. Du behöver uppdatera dina Inlärnings- och Förutsägelsetransaktioner nycklar i alla program som du har skrivit.

Visa ditt projekt på den [Custom Vision webbplats](https://customvision.ai), logga in med samma konto som du använde för att logga in på Azure portal. Om du inte ser ditt projekt, bekräfta att du är i samma katalog i den [Custom Vision webbplats](https://customvision.ai) som den katalog där resurserna finns i Azure-portalen. I både Azure-portalen och CustomVision.ai kan välja du din katalog från listrutan Användare-menyn i det övre högra hörnet på skärmen.