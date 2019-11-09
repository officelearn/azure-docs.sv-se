---
title: Felsöka en arbets yta
titleSuffix: ML Studio (classic) - Azure
description: Den här guiden innehåller lösningar för några vanliga utmaningar när du konfigurerar den klassiska versionen av Azure Machine Learning Studio-arbetsytor.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 88f4e1edae84892892d1a8a9e76499d71d551abf
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838026"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Fel söknings guide: skapa och ansluta till en Azure Machine Learning Studio (klassisk)-arbets yta
Den här guiden innehåller lösningar för några vanliga utmaningar när du konfigurerar Azure Machine Learning Studio (klassiska) arbets ytor.



## <a name="workspace-owner"></a>Arbets ytans ägare
Om du vill öppna en arbets yta i Machine Learning Studio (klassisk) måste du vara inloggad på det Microsoft-konto som du använde för att skapa arbets ytan, eller så måste du ta emot en inbjudan från ägaren för att ansluta till arbets ytan. Från Azure Portal kan du hantera arbets ytan, vilket inkluderar möjligheten att konfigurera åtkomst.

Mer information om hur du hanterar en arbets yta finns i [hantera en Azure Machine Learning Studio (klassisk)-arbets yta].

[Hantera en Azure Machine Learning Studio (klassisk)-arbets yta]: manage-workspace.md

## <a name="allowed-regions"></a>Tillåtna regioner
Machine Learning är för närvarande tillgängligt i ett begränsat antal regioner. Om din prenumeration inte innehåller någon av dessa regioner kan du se fel meddelandet "du har inga prenumerationer i de tillåtna regionerna".

Om du vill begära att en region läggs till i din prenumeration skapar du en ny Microsoft-supportbegäran från Azure Portal, väljer **fakturering** som problem typ och följer anvisningarna för att skicka in din begäran.

## <a name="storage-account"></a>Lagringskonto
Den Machine Learning tjänsten behöver ett lagrings konto för att lagra data. Du kan använda ett befintligt lagrings konto, eller så kan du skapa ett nytt lagrings konto när du skapar den nya Machine Learning Studio-arbetsytan (om du har en kvot för att skapa ett nytt lagrings konto).

När den nya Machine Learning Studio-arbetsytan (klassisk) har skapats kan du logga in på Machine Learning Studio (klassisk) med hjälp av Microsoft-konto som du använde för att skapa arbets ytan. Om du stöter på fel meddelandet "arbets ytan hittades inte" (liknar följande skärm bild) kan du använda följande steg för att ta bort dina cookies från webbläsaren.

![Arbets ytan hittades inte](media/troubleshooting-creating-ml-workspace/screen3.png)

**Ta bort cookies från webbläsare**

1. Om du använder Internet Explorer klickar du på knappen **verktyg** i det övre högra hörnet och väljer **Internet alternativ**.  

   ![Internet alternativ](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Klicka på ta bort under fliken **Allmänt** **...**

   ![Fliken Allmänt](media/troubleshooting-creating-ml-workspace/screen5.png)

3. I dialog rutan **ta bort webb historik** kontrollerar du att **cookies och webbplats data** är markerade och klickar på **ta bort**.

   ![Ta bort cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

När cookies har tagits bort startar du om webbläsaren och går sedan till sidan [Microsoft Azure Machine Learning Studio (klassisk)](https://studio.azureml.net) . När du uppmanas att ange ett användar namn och lösen ord anger du samma Microsoft-konto som du använde för att skapa arbets ytan.

## <a name="comments"></a>Kommentarer

Vårt mål är att göra Machine Learnings upplevelsen så sömlös som möjligt. Publicera eventuella kommentarer och problem i Azure Machine Learning- [forumet](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) för att hjälpa oss att få dig bättre.
