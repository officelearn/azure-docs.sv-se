---
title: Skriva kod för att spåra begäranden med Azure Application Insights | Microsoft Docs
description: Skriv kod för att spåra begäranden med Application Insights så att du kan hämta profiler för dina begäranden.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 4782e560b580b7f565724dbb35ed9876bffdc256
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730862"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Skriva kod för att spåra begäranden med Application Insights

Om du vill visa profiler för ditt program på sidan prestanda, behöver Azure Application Insights för att spåra begäranden för ditt program. Application Insights kan automatiskt spåra förfrågningar för program som bygger på redan har instrumenterats ramverk. Två exempel är ASP.NET och ASP.NET Core. 

För andra program, till exempel arbetsroller i Azure Cloud Services och Service Fabric tillståndslösa API: er, måste du skriva kod för att berätta för Application Insights där dina begäranden börjar- och slutdatum. När du har skrivit koden skickas begäranden telemetri till Application Insights. Du kan visa telemetri på sidan prestanda och profiler har samlats in för dessa begäranden. 

Om du vill spåra förfrågningar manuellt gör du följande:

  1. Lägg till följande kod tidigt i program-livstid:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Läs mer om den här nyckeln globala instrumentation är konfigurerad, [används Service Fabric med Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. För alla typer av kod som du vill att instrumentera, lägga till en `StartOperation<RequestTelemetry>` **med** instruktionen runt den som visas i följande exempel:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Anropa `StartOperation<RequestTelemetry>` inom en annan `StartOperation<RequestTelemetry>` omfång stöds inte. Du kan använda `StartOperation<DependencyTelemetry>` i den kapslade omfång i stället. Exempel:  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
