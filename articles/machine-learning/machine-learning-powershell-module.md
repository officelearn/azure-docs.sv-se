<properties
    pageTitle="PowerShell-modulen för Machine Learning | Microsoft Azure"
    description="PowerShell-modulen för Azure Machine Learning finns tillgänglig i offentligt förhandsgranskningsläge. Använd PowerShell för att skapa och hantera arbetsytor, experiment, webbtjänster och annat."
    keywords="experiment, linjär regression,machine learning algoritmer, machine learning självstudier, teknik för förutsägbar modellering, dataexperiment"
    services="machine-learning"
    documentationCenter=""
    authors="hning86"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/05/2016"
    ms.author="garye;haining"/>


# PowerShell-modul för Microsoft Azure Machine Learning

PowerShell-modulen för Azure Machine Learning är ett kraftfullt verktyg som gör att du kan använda Windows PowerShell för att hantera arbetsytor, experiment, datauppsättningar, webbtjänster och mycket annat.

Du kan visa dokumentationen och hämta modulen tillsammans med den fullständiga källkoden på [https://aka.ms/amlps](https://aka.ms/amlps). 

## Vad är Machine Learning PowerShell-modulen?

Machine Learning PowerShell-modulen är en. NET-baserad dll-modul som gör att du kan hantera arbetsytor, experiment, datauppsättningar, webbtjänster och slutpunkter för webbtjänster i Azure Machine Learning från Windows PowerShell. Med modulen kan du hämta den fullständiga källkoden som innehåller ett tydligt separerat [C# API-lager](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). Det innebär att du kan referera till denna DLL-fil från ditt eget .NET-projekt och hantera Azure Machine Learning via .NET-kod. Dessutom är DLL-filerna beroende av underliggande REST API:er som du kan använda direkt från din favoritklient.

## Vad kan jag göra med PowerShell-modulen?

Här är några av de uppgifter du kan utföra med PowerShell-modulen. Titta närmare på den [fullständiga dokumentationen](https://aka.ms/amlps) för dessa och många andra funktioner.

- Etablera en ny arbetsyta med ett hanteringscertifikat ([New AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- Exportera och importera en JSON-fil som representerar ett experimentdiagram ([Export AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) och [Importera AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- Kör ett experiment ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- Skapa en webbtjänst utanför en förutsägbart experiment ([New AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- Skapa en ny slutpunkt för en publicerad webbtjänst ([Lägg till AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- Anropa en RRS- och/eller en BES-webbtjänstslutpunkt ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) och [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Här är ett snabbt exempel på hur man kan använda PowerShell för att köra ett befintligt experiment:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Ett mer detaljerat användningsfall finns i den här artikeln om att använda PowerShell-modulen för att automatisera en ofta begärd uppgift: [Skapa många Machine Learning-modeller och webbtjänstslutpunkter från ett experiment med PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md).

## Hur kommer jag igång?

För att komma igång med Machine Learning PowerShell hämtar du [versionspaketet](https://github.com/hning86/azuremlps/releases) från GitHub och följer [installationsanvisningarna](https://github.com/hning86/azuremlps/blob/master/README.md). Du behöver avblockera den nedladdade/ouppackade DLL-filen och sedan importera den till din PowerShell-miljö. De flesta cmdletar kräver att du anger arbetsyte-ID, autentiseringstoken för arbetsytan och Azure-regionen som arbetsytan finns i. Det enklaste sättet att ange dessa är via en config.json-fil av standardtyp, vilket beskrivs i detalj i installationsanvisningarna. Naturligtvis kan du också klona git-trädet och ändra/kompilera koden lokalt med hjälp av Visual Studio.

## Nästa steg

PowerShell-modulen fortsätter att förbättras och expanderas under denna förhandsgranskningsperiod. Håll ett öga på [bloggen om Cortana Intelligence och Machine Learning](https://blogs.technet.microsoft.com/machinelearning/) för mer nyheter och information.



<!--HONumber=Sep16_HO3-->


