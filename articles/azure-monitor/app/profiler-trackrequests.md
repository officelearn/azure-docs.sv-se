---
title: Skriv kod för att spåra begäranden med Azure Application Insights | Microsoft-dokument
description: Skriv kod för att spåra förfrågningar med Application Insights så att du kan få profiler för dina önskemål.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c59cbe852a91a91c7b3adb4452328700ec718a82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671604"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Skriv kod för att spåra begäranden med Application Insights

Om du vill visa profiler för ditt program på sidan Prestanda måste Azure Application Insights spåra begäranden för ditt program. Application Insights kan automatiskt spåra begäranden för program som bygger på redan instrumenterade ramverk. Två exempel är ASP.NET och ASP.NET Core. 

För andra program, till exempel Azure Cloud Services-arbetsroller och tillståndslösa API:er för tjänstinfrastruktur, måste du skriva kod för att tala om för Application Insights var dina begäranden börjar och slutar. När du har skrivit den här koden skickas begäranden telemetri till Application Insights. Du kan visa telemetrin på sidan Prestanda och profiler samlas in för dessa begäranden. 

Gör så här om du vill spåra begäranden manuellt:

  1. Lägg till följande kod tidigt under programmets livstid:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Mer information om den här globala instrumenteringsnyckelkonfigurationen finns i [Använda tjänst fabric med application insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Lägg till en `StartOperation<RequestTelemetry>` med hjälp av en **sats** runt den för alla kodstycken som du vill instrumentera:

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

        Det `StartOperation<RequestTelemetry>` går `StartOperation<RequestTelemetry>` inte att ringa inom ett annat scope. Du kan `StartOperation<DependencyTelemetry>` använda i det kapslade scopet i stället. Ett exempel:  
        
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
