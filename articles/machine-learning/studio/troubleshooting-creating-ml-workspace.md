---
title: 'Felsök: Skapa, ansluta till en Machine Learning Studio-arbetsyta – Azure | Microsoft Docs'
description: Den här guiden innehåller lösningar för några vanliga påträffade utmaningar när du ställer in Azure Machine Learning Studio-arbetsytor.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 1a8aec4b-35f9-44e8-9570-2575b8979ab1
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 5e52abeeef7d40ac5594f4edfa2b80ef553081e4
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52312079"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-studio-workspace"></a>Felsökningsguide: skapa och Anslut till en Machine Learning Studio-arbetsyta
Den här guiden innehåller lösningar för några vanliga påträffade utmaningar när du ställer in Azure Machine Learning Studio-arbetsytor.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Arbetsytesägare
Du öppnar en arbetsyta i Machine Learning Studio, måste du vara inloggad på Account du använde för att skapa arbetsytan eller du måste ta emot en inbjudan från ägaren att ansluta till arbetsytan. Du kan hantera arbetsytan, vilket ger möjlighet att konfigurera åtkomst från Azure-portalen.

Läs mer om hur du hanterar en arbetsyta, [hantera en Azure Machine Learning-arbetsyta].

[Hantera en Azure Machine Learning-arbetsyta]: manage-workspace.md

## <a name="allowed-regions"></a>Tillåtna regioner
Machine Learning är för närvarande tillgängligt i ett begränsat antal regioner. Om din prenumeration inte innehåller något av dessa regioner kan du kan se ett felmeddelande, ”du har inga prenumerationer i tillåtna regioner”.

Om du vill begära att en region ska läggas till din prenumeration, skapa en ny supportbegäran för Microsoft från Azure portal, Välj **fakturering** som problemtyp, och följ anvisningarna för att skicka din begäran.

## <a name="storage-account"></a>Lagringskonto
Machine Learning-tjänsten behöver ett lagringskonto för att lagra data. Du kan använda ett befintligt lagringskonto eller du kan skapa ett nytt lagringskonto när du skapar nya Machine Learning-arbetsytan (om du har en kvot för att skapa ett nytt lagringskonto).

När den nya Machine Learning-arbetsytan har skapats kan logga du in till Machine Learning Studio med hjälp av Microsoft-kontot som du använde för att skapa arbetsytan. Om du får felmeddelandet Använd ”arbetsyta hittades inte” (ungefär som följande skärmbild), följande steg att ta bort cookies i webbläsaren.

![Arbetsytan hittades inte][screen3]

**Att ta bort cookies i webbläsaren**

1. Om du använder Internet Explorer, klickar du på den **verktyg** i det övre högra hörnet och välj **Internetalternativ**.  

![Internet-alternativ][screen4]

2. Under den **Allmänt** fliken **ta bort...**

![Fliken Allmänt][screen5]

3. I den **ta bort webbhistorik** dialogrutan Kontrollera **Cookies och webbsidesdata** är markerad och klicka på **ta bort**.

![Ta bort cookies][screen6]

Efter det raderas cookies, starta om webbläsaren och gå sedan till den [Microsoft Azure Machine Learning](https://studio.azureml.net) sidan. När du ombeds ange ett användarnamn och lösenord anger du samma Microsoft-konto som du använde för att skapa arbetsytan.

## <a name="comments"></a>Kommentarer

Vårt mål är att göra Machine Learning-miljö så smidig som möjligt. Du publicera alla kommentarer och problem med den [Azure Machine Learning-forumet](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) så att vi dig.

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
