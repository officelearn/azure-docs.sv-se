---
title: Skriva kod för att spåra begäranden med Azure Application Insights | Microsoft Docs
description: Skriva kod för att spåra begäranden med Application Insights så att du kan hämta profiler för din reqeusts
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 20f408d9dd32c3fd7a0e319e4051483e3aa54dd9
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54083166"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Skriva kod för att spåra begäranden med Application Insights

Om du vill se profiler för ditt program på sidan prestanda måste Application Insights spåra begäranden för ditt program. Application Insights kan automatiskt spåra förfrågningar för program som bygger på ramverk som redan är utrustade, t.ex. ASP.net och ASP.Net Core. Men för andra program som arbetsroller i Azure Cloud Service och Service Fabric tillståndslösa API: er, behovet av att skriva kod för att berätta för Application Insights var dina begäranden börjar och slutar. När du har skrivit den här koden begäranden telemetri skickas till Application Insights och telemetrin visas på sidan prestanda och profiler kommer att samlas in för dessa begäranden. 

Här följer de steg du måste vidta för att manuellt spåra begäranden:


  1. Lägg till följande kod tidigt i program-livstid:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Läs mer om den här nyckeln globala instrumentation är konfigurerad, [används Service Fabric med Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. För alla typer av kod som du vill att instrumentera, lägga till en `StartOperation<RequestTelemetry>` **USING** instruktionen runt den som visas i följande exempel:

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
