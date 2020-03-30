---
title: Felsöka en arbetsyta
titleSuffix: ML Studio (classic) - Azure
description: Den här guiden innehåller lösningar för vissa utmaningar som ofta uppstår när du konfigurerar azure machine learning studio (klassiska) arbetsytor.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 58ccd63e16382aca8e16eb67efba951a055eb254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217842"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Felsökningsguide: Skapa och ansluta till en arbetsyta i Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Den här guiden innehåller lösningar för vissa utmaningar som ofta uppstår när du konfigurerar azure machine learning studio (klassiska) arbetsytor.

## <a name="workspace-owner"></a>Ägare av arbetsyta
Om du vill öppna en arbetsyta i Machine Learning Studio (klassisk) måste du vara inloggad på det Microsoft-konto som du använde för att skapa arbetsytan, eller så måste du få en inbjudan från ägaren om att gå med i arbetsytan. Från Azure-portalen kan du hantera arbetsytan, vilket inkluderar möjligheten att konfigurera åtkomst.

Mer information om hur du hanterar en arbetsyta finns i [Hantera en arbetsyta för Azure Machine Learning Studio (klassiskt).]

[Hantera en arbetsyta i Azure Machine Learning Studio (klassisk)]: manage-workspace.md

## <a name="allowed-regions"></a>Tillåtna regioner
Machine Learning är för närvarande tillgängligt i ett begränsat antal regioner. Om din prenumeration inte innehåller något av dessa regioner kan felmeddelandet "Du har inga prenumerationer i de tillåtna regionerna".

Om du vill begära att en region läggs till i din prenumeration skapar du en ny Microsoft-supportbegäran från Azure-portalen, väljer **Fakturering** som problemtyp och följer anvisningarna för att skicka din begäran.

## <a name="storage-account"></a>Lagringskonto
Machine Learning-tjänsten behöver ett lagringskonto för att lagra data. Du kan använda ett befintligt lagringskonto eller skapa ett nytt lagringskonto när du skapar den nya machine learning studio-arbetsytan (klassisk) (om du har kvot för att skapa ett nytt lagringskonto).

När den nya arbetsytan Machine Learning Studio (klassisk) har skapats kan du logga in på Machine Learning Studio (klassisk) med hjälp av det Microsoft-konto som du använde för att skapa arbetsytan. Om felmeddelandet "Workspace Not Found" (liknar följande skärmbild) kan du använda följande steg för att ta bort webbläsarcookies.

![Det gick inte att hitta arbetsytan](media/troubleshooting-creating-ml-workspace/screen3.png)

**Så här tar du bort webbläsarcookies**

1. Om du använder Internet Explorer klickar du på knappen **Verktyg** i det övre högra hörnet och väljer **Internetalternativ**.  

   ![Internetalternativ](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Klicka på Ta **bort under** fliken **Allmänt...**

   ![fliken Allmänt](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Kontrollera att **cookies och webbplatsdata** är markerade i dialogrutan **Ta bort webbhistorik** och klicka på **Ta bort**.

   ![Ta bort cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

När cookies har tagits bort startar du om webbläsaren och går sedan till sidan [Microsoft Azure Machine Learning Studio (klassisk).](https://studio.azureml.net) När du uppmanas att ange ett användarnamn och lösenord anger du samma Microsoft-konto som du använde för att skapa arbetsytan.

## <a name="comments"></a>Kommentarer

Vårt mål är att göra machine learning-upplevelsen så sömlös som möjligt. Lägg upp eventuella kommentarer och problem på [Azure Machine Learning-forumet](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) för att hjälpa oss att hjälpa dig att bättre.
