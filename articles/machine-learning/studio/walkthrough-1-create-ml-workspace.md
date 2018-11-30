---
title: 'Steg 1: Skapa en Machine Learning Studio-arbetsyta | Microsoft Docs'
description: 'Steg 1 av utveckla en förutsägelselösning genomgång: Lär dig hur du ställer in en ny Azure Machine Learning Studio-arbetsyta.'
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: b3c97e3d-16ba-4e42-9657-2562854a1e04
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 0990fb6f8166be516b32e1f457f868be16dedcb2
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316976"
---
# <a name="walkthrough-step-1-create-a-machine-learning-studio-workspace"></a>Genomgång steg 1: Skapa en Machine Learning Studio-arbetsyta
Det här är det första steget i den här genomgången [utveckla en lösning för förutsägelseanalys i Azure Machine Learning](walkthrough-develop-predictive-solution.md).

1. **Skapa en Machine Learning-arbetsyta**
2. [Överför befintliga data](walkthrough-2-upload-data.md)
3. [Skapa ett nytt experiment](walkthrough-3-create-new-experiment.md)
4. [Träna och utvärdera modellerna](walkthrough-4-train-and-evaluate-models.md)
5. [Distribuera webbtjänsten](walkthrough-5-publish-web-service.md)
6. [Få åtkomst till webbtjänsten](walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Du måste ha en Microsoft Azure Machine Learning-arbetsyta om du vill använda Machine Learning Studio. Den här arbetsytan innehåller de verktyg du behöver för att skapa, hantera och publicera experiment.  

Administratören för din Azure-prenumeration måste du skapa arbetsytan och sedan lägga till dig som ägare eller deltagare. Mer information finns i [skapa och dela en Azure Machine Learning-arbetsyta](create-workspace.md).

När arbetsytan har skapats kan du öppna Machine Learning Studio ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Om du har fler än en arbetsyta kan du välja arbetsytan i verktygsfältet i det övre högra hörnet i fönstret.

![Välj en arbetsyta i Studio][2]

> [!TIP]
> Om du har gjort en ägare för arbetsytan, kan du dela experiment som du arbetar med genom att bjuda in andra till arbetsytan. Du kan göra detta i Machine Learning Studio på den **inställningar** sidan. Du behöver bara det Microsoft-konto eller organisationskonto för varje användare.
> 
> På den **inställningar** klickar du på **användare**, klicka sedan på **bjuda in fler användare** längst ned i fönstret.
> 
> 

- - -
**Nästa: [överför befintliga data](walkthrough-2-upload-data.md)**

[1]: ./media/walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/walkthrough-1-create-ml-workspace/open-workspace.png
