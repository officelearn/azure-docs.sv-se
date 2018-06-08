---
title: 'Felsöka: Skapa och ansluta till en arbetsyta i Machine Learning | Microsoft Docs'
description: Lösningar på vanliga problem med att skapa och ansluta till en Azure Machine Learning-arbetsytan
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
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
ms.openlocfilehash: 262c9af4e0f3ee34dc89986affacb6c0d8a0d801
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835730"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>Felsökningsguide: Skapa och anslut till en Machine Learning-arbetsyta
Den här guiden ger lösningar för några vanliga påträffade utmaningar när du ställer in Azure Machine Learning arbetsytor.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Arbetsytesägare
Om du vill öppna en arbetsyta i Machine Learning Studio, måste du vara inloggad på Account du använde för att skapa arbetsytan eller måste du få en inbjudan från ägare att ansluta till arbetsytan. Du kan hantera arbetsytan, vilket ger möjlighet att konfigurera åtkomst från Azure-portalen.

Mer information om hur du hanterar en arbetsyta finns [hantera en Azure Machine Learning-arbetsytan].

[Hantera en Azure Machine Learning-arbetsytan]: manage-workspace.md

## <a name="allowed-regions"></a>Tillåtna regioner
Machine Learning är tillgänglig i ett begränsat antal regioner. Om din prenumeration inte innehåller något av dessa regioner kan du se ett felmeddelande, ”du har inga prenumerationer i tillåtna regioner”.

Om du vill begära att en region som ska läggas till din prenumeration, skapa en ny Microsoft supportförfrågan från Azure portal, väljer **fakturering** som problemtyp, och följ anvisningarna för att skicka din begäran.

## <a name="storage-account"></a>Lagringskonto
Tjänsten Machine Learning måste ett storage-konto för att lagra data. Du kan använda ett befintligt lagringskonto eller skapa ett nytt lagringskonto när du skapar den nya Machine Learning-arbetsytan (om du har en kvot för att skapa ett nytt lagringskonto).

När den nya Machine Learning-arbetsytan har skapats kan logga du in till Machine Learning Studio med hjälp av Microsoft-konto som du använde för att skapa arbetsytan. Om du får felmeddelandet Använd ”arbetsytan kunde inte hittas” (ungefär som följande skärmbild), följande steg att ta bort cookies i webbläsaren.

![Det gick inte att hitta arbetsytan][screen3]

**Ta bort cookies i webbläsaren**

1. Om du använder Internet Explorer klickar du på den **verktyg** i det övre högra hörnet och välj **Internetalternativ**.  

![Internet-alternativ][screen4]

2. Under den **allmänna** klickar du på **ta bort...**

![Fliken Allmänt][screen5]

3. I den **ta bort webbhistorik** dialogrutan Kontrollera **Cookies och webbplatsen data** är markerad och klicka på **ta bort**.

![Ta bort cookies][screen6]

Starta om webbläsaren efter cookies tas bort och går sedan till den [Microsoft Azure Machine Learning](https://studio.azureml.net) sidan. När du ombeds ange ett användarnamn och lösenord anger du samma Microsoft-konto som du använde för att skapa arbetsytan.

## <a name="comments"></a>Kommentarer

Vårt mål är att göra Machine Learning-upplevelse som sömlös som möjligt. Kontrollera efter eventuella kommentarer och problem på den [Azure Machine Learning-forum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) som hjälper oss att hantera du bättre.

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
