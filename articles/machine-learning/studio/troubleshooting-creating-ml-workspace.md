---
title: 'Felsökning: Skapa, ansluta till en Machine Learning Studio-arbetsyta'
titleSuffix: Azure Machine Learning Studio
description: Den här guiden innehåller lösningar för några vanliga påträffade utmaningar när du ställer in Azure Machine Learning Studio-arbetsytor.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: f7b5bdbbf322a0d7ea290f2543af07d5b11f439a
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816138"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-workspace"></a>Felsökningsguide: Skapa och Anslut till en Azure Machine Learning Studio-arbetsyta
Den här guiden innehåller lösningar för några vanliga påträffade utmaningar när du ställer in Azure Machine Learning Studio-arbetsytor.



## <a name="workspace-owner"></a>Arbetsytesägare
Du öppnar en arbetsyta i Machine Learning Studio, måste du vara inloggad på Account du använde för att skapa arbetsytan eller du måste ta emot en inbjudan från ägaren att ansluta till arbetsytan. Du kan hantera arbetsytan, vilket ger möjlighet att konfigurera åtkomst från Azure-portalen.

Läs mer om hur du hanterar en arbetsyta, [hantera en Azure Machine Learning Studio-arbetsyta].

[Hantera en Azure Machine Learning Studio-arbetsyta]: manage-workspace.md

## <a name="allowed-regions"></a>Tillåtna regioner
Machine Learning är för närvarande tillgängligt i ett begränsat antal regioner. Om din prenumeration inte innehåller något av dessa regioner kan du kan se ett felmeddelande, ”du har inga prenumerationer i tillåtna regioner”.

Om du vill begära att en region ska läggas till din prenumeration, skapa en ny supportbegäran för Microsoft från Azure portal, Välj **fakturering** som problemtyp, och följ anvisningarna för att skicka din begäran.

## <a name="storage-account"></a>Lagringskonto
Machine Learning-tjänsten behöver ett lagringskonto för att lagra data. Du kan använda ett befintligt lagringskonto eller du kan skapa ett nytt lagringskonto när du skapar den nya Machine Learning Studio-arbetsytan (om du har en kvot för att skapa ett nytt lagringskonto).

När den nya Machine Learning Studio-arbetsytan har skapats kan logga du in till Machine Learning Studio med hjälp av Microsoft-kontot som du använde för att skapa arbetsytan. Om du får felmeddelandet Använd ”arbetsyta hittades inte” (ungefär som följande skärmbild), följande steg att ta bort cookies i webbläsaren.

![Arbetsytan hittades inte](media/troubleshooting-creating-ml-workspace/screen3.png)

**Att ta bort cookies i webbläsaren**

1. Om du använder Internet Explorer, klickar du på den **verktyg** i det övre högra hörnet och välj **Internetalternativ**.  

   ![Internet-alternativ](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Under den **Allmänt** fliken **ta bort...**

   ![Fliken Allmänt](media/troubleshooting-creating-ml-workspace/screen5.png)

3. I den **ta bort webbhistorik** dialogrutan Kontrollera **Cookies och webbsidesdata** är markerad och klicka på **ta bort**.

   ![Ta bort cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

Efter det raderas cookies, starta om webbläsaren och gå sedan till den [Microsoft Azure Machine Learning Studio](https://studio.azureml.net) sidan. När du ombeds ange ett användarnamn och lösenord anger du samma Microsoft-konto som du använde för att skapa arbetsytan.

## <a name="comments"></a>Kommentarer

Vårt mål är att göra Machine Learning-miljö så smidig som möjligt. Du publicera alla kommentarer och problem med den [Azure Machine Learning-forumet](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) så att vi dig.
