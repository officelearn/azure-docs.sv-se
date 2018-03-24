---
title: PowerShell-modulen för Machine Learning | Microsoft Docs
description: PowerShell-modulen för Azure Machine Learning finns tillgänglig i offentligt förhandsgranskningsläge. Använda PowerShell för att skapa och hantera arbetsytor, experiment, webbtjänster och mycket mer.
keywords: experiment, linjär regression,machine learning algoritmer, machine learning självstudier, teknik för förutsägbar modellering, dataexperiment
services: machine-learning
documentationcenter: ''
author: hning86
ms.author: haining
manager: mwinkle
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.openlocfilehash: c9553e372f4d1cb5c60935fae5a7af61806ea6d4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>PowerShell-modul för Microsoft Azure Machine Learning
PowerShell-modulen för Azure Machine Learning är ett kraftfullt verktyg som gör att du kan använda Windows PowerShell för att hantera arbetsytor, experiment, datauppsättningar, klassisk webbtjänster och mycket mer.

Du kan visa i dokumentationen och hämta modulen, tillsammans med fullständig källkoden på [ https://aka.ms/amlps ](https://aka.ms/amlps). 

> [!NOTE]
> Azure Machine Learning PowerShell-modulen är för närvarande i förhandsgranskningsläge. Modulen fortsätter att förbättrade och expanderas under den här förhandsversionen. Hålla ett öga på den [Cortana Intelligence och Machine Learning blogg](https://blogs.technet.microsoft.com/machinelearning/) för nyheter och information.

## <a name="what-is-the-machine-learning-powershell-module"></a>Vad är Machine Learning PowerShell-modulen?
Machine Learning PowerShell-modulen är en. NET-baserade dll-modulen som hjälper dig att hantera Azure Machine Learning arbetsytor, experiment, datauppsättningar, klassisk webbtjänster och klassisk slutpunkter för webbtjänster från Windows PowerShell. 

Du kan hämta fullständig källkoden som innehåller ett korrekt genom att avgränsa dem tillsammans med modulen [C#-API-nivå](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). Du kan referera till DLL-filen från .NET projektet och hantera Azure Machine Learning via .NET-kod. Dessutom beroende DLL-filen av underliggande REST API: er som kan användas direkt från din favorit-klienten.

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

En mer detaljerad användningsfall finns i den här artikeln på med PowerShell-modulen för att automatisera en uppgift som begärs ofta: [skapar många Machine Learning-modeller och web service slutpunkter från ett experiment med hjälp av PowerShell](create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Hur kommer jag igång?
För att komma igång med Machine Learning PowerShell hämtar du [versionspaketet](https://github.com/hning86/azuremlps/releases) från GitHub och följer [installationsanvisningarna](https://github.com/hning86/azuremlps/blob/master/README.md). Instruktionerna förklarar hur du avblockerar hämtas/uppackade DLL-filen och importera den till din PowerShell-miljö. De flesta cmdletar kräver att du anger arbetsyte-ID, autentiseringstoken för arbetsytan och Azure-regionen som arbetsytan finns i. Det enklaste sättet att tillhandahålla värdena är via en standardfil config.json. Instruktionerna förklarar också hur du konfigurerar den här filen. 

Om du vill kan du klona git-trädet ändrar koden och kompilera den lokalt med hjälp av Visual Studio.

## <a name="next-steps"></a>Nästa steg
Du kan hitta fullständig dokumentation för PowerShell-modulen på [ https://aka.ms/amlps ](https://aka.ms/amlps). 

Ett utökat exempel på hur du använder modulen i ett verkligt scenario kolla djupgående användningsfall [skapar många Machine Learning-modeller och web service slutpunkter från ett experiment med hjälp av PowerShell](create-models-and-endpoints-with-powershell.md).
