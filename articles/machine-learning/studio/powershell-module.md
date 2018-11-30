---
title: PowerShell-modulen för Machine Learning Studio – Azure | Microsoft Docs
description: PowerShell-modulen för Azure Machine Learning finns tillgänglig i offentligt förhandsgranskningsläge. Skapa och hantera arbetsytor, experiment, webbtjänster och annat med hjälp av PowerShell.
keywords: experiment, linjär regression,machine learning algoritmer, machine learning självstudier, teknik för förutsägbar modellering, dataexperiment
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=haining, author=hning86)
ms.author: amlstudiodocs
manager: mwinkle
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.openlocfilehash: 3191ff845f72c87b85fdd414716ed9a00b022d06
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52312035"
---
# <a name="powershell-module-for-azure-machine-learning-studio"></a>PowerShell-modulen för Azure Machine Learning Studio
PowerShell-modulen för Azure Machine Learning är ett kraftfullt verktyg som gör att du kan använda Windows PowerShell för att hantera arbetsytor, experiment, datauppsättningar, klassiska webbtjänster och mycket mer.

Du kan visa dokumentationen och hämta modulen tillsammans med den fullständiga källkoden på [ https://aka.ms/amlps ](https://aka.ms/amlps). 

> [!NOTE]
> Azure Machine Learning PowerShell-modulen är för närvarande i förhandsgranskningsläge. Modulen fortsätter att förbättras och expanderas under denna förhandsgranskningsperiod. Håll ett öga på den [Cortana Intelligence och Machine Learning Blog](https://blogs.technet.microsoft.com/machinelearning/) för nyheter och information.

## <a name="what-is-the-machine-learning-powershell-module"></a>Vad är Machine Learning PowerShell-modulen?
Machine Learning PowerShell-modulen är en. NET-baserad DLL-modul som hjälper dig att hantera Azure Machine Learning-arbetsytor, experiment, datauppsättningar, klassiska webbtjänster och slutpunkter för klassiska webbtjänster från Windows PowerShell. 

Med modulen kan du hämta den fullständiga källkoden som innehåller en korrekt avgränsas [ C# API-läger](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). Du kan referera till denna DLL-fil från din egen .NET-projekt och hantera Azure Machine Learning via .NET-kod. Dessutom beroende DLL-filerna underliggande REST API: er som du kan använda direkt från din favoritklient.

## <a name="what-can-i-do-with-the-powershell-module"></a>Vad kan jag göra med PowerShell-modulen?
Här är några av de uppgifter du kan utföra med PowerShell-modulen. Titta närmare på den [fullständiga dokumentationen](https://aka.ms/amlps) för dessa och många andra funktioner.

* Etablera en ny arbetsyta med ett hanteringscertifikat ([New AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
* Exportera och importera en JSON-fil som representerar ett experimentdiagram ([Export AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) och [Importera AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
* Kör ett experiment ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
* Skapa en webbtjänst utanför en förutsägbart experiment ([New AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
* Skapa en slutpunkt på en publicerad webbtjänst ([Lägg till AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* Anropa en RRS- och/eller en BES-webbtjänstslutpunkt ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) och [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Här är ett snabbt exempel på hur man kan använda PowerShell för att köra ett befintligt experiment:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Ett mer detaljerat användningsfall finns i den här artikeln om hur du använder PowerShell-modulen för att automatisera en ofta begärd uppgift: [skapa många Machine Learning-modeller och webbtjänstslutpunkter från ett experiment med PowerShell](create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Hur kommer jag igång?
För att komma igång med Machine Learning PowerShell hämtar du [versionspaketet](https://github.com/hning86/azuremlps/releases) från GitHub och följer [installationsanvisningarna](https://github.com/hning86/azuremlps/blob/master/README.md). Instruktionerna förklarar hur du avblockera den nedladdade/ouppackade DLL-filen och sedan importera den till din PowerShell-miljö. De flesta cmdletar kräver att du anger arbetsyte-ID, autentiseringstoken för arbetsytan och Azure-regionen som arbetsytan finns i. Det enklaste sättet att ange värdena som är via en config.json-fil. Anvisningarna beskriver också hur du konfigurerar den här filen. 

Om du vill kan du kan klona git-trädet ändrar koden och kompilera den lokalt med hjälp av Visual Studio.

## <a name="next-steps"></a>Nästa steg
Du hittar den fullständiga dokumentationen för PowerShell-modulen på [ https://aka.ms/amlps ](https://aka.ms/amlps). 

Ett utökat exempel på hur du använder modulen i ett verkligt scenario, Kolla in djupgående användningsfall [skapa många Machine Learning-modeller och webbtjänstslutpunkter från ett experiment med PowerShell](create-models-and-endpoints-with-powershell.md).
