---
title: OpenCensus gå spårning med Azure Application Insights | Microsoft Docs
description: Innehåller instruktioner för att integrera OpenCensus gå spårning med lokala vidarebefordrare och Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8f0dc0db3f50d191b558f69252a4557410c30adc
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "54003775"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Samla in distribuerade spårningar från Go (förhandsversion)

Application Insights nu stöder distribuerad spårning av Go-program via integrering med [OpenCensus](https://opencensus.io) och vår nya [lokala vidarebefordrare](./opencensus-local-forwarder.md). Den här artikeln beskriver steg för steg hur du konfigurerar OpenCensus för Go och få din spårningsdata till Application Insights.

## <a name="prerequisites"></a>Förutsättningar

- Du behöver en Azure-prenumeration.
- Go ska installeras, den här artikeln använder version 1.11 [går hämta](https://golang.org/dl/).
- Följ anvisningarna för att installera den [lokala vidarebefordrare som en Windows-tjänst](./opencensus-local-forwarder.md#windows-service).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Skapa Application Insights-källa

Du måste först skapa en Application Insights-resurs som genererar en instrumentationsnyckeln (ikey). Nyckeln används sedan för att konfigurera den lokala vidarebefordraren för att skicka distribuerade spårningar från programmets OpenCensus som instrumenteras till Application Insights.   

1. Välj **skapa en resurs** > **utvecklarverktyg** > **Application Insights**.

   ![lägg till en Application Insights-resurs](./media/opencensus-Go/0001-create-resource.png)

   En konfigurationsruta visas. Använd följande tabell när du ska fylla i indatafälten.

    | Inställningar        | Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namn som identifierar appen du övervakar |
   | **Programtyp** | Allmänt | Typen av app du övervakar |
   | **Resursgrupp**     | myResourceGroup      | Namnet på den nya resursgrupp som är värd för App Insights-data |
   | **Plats** | Östra USA | Välj en plats nära dig eller nära där appen finns |

2. Klicka på **Skapa**.

## <a name="configure-local-forwarder"></a>Konfigurera lokala vidarebefordrare

1. Välj **Översikt** > **Essentials** > kopiera appens **instrumenteringsnyckel**.

   ![Skärmbild av instrumentationsnyckeln](./media/opencensus-Go/0003-instrumentation-key.png)

2. Redigera din `LocalForwarder.config` filen och Lägg till din instrumentationsnyckel. Om du har följt anvisningarna i den [installationsprogrammets](./opencensus-local-forwarder.md#windows-service) filen finns på `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Starta om programmet **lokala vidarebefordrare** service.

## <a name="opencensus-go-packages"></a>OpenCensus Go-paket

1. Installera öppen insamlade paket för Go från kommandoraden:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Lägg till följande kod till en .go-fil och sedan skapa och kör. (Det här exemplet är härlett från officiella OpenCensus vägledningen med har lagts till kod som möjliggör integrering med lokala vidarebefordraren)

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. När enkel go-app är igång går du till `http://localhost:50030`. Varje uppdatering av webbläsaren genererar den texten ”hello world” tillsammans med motsvarande span data som hämtas av den lokala vidarebefordraren.

4. Att bekräfta att den **lokala vidarebefordrare** plocka upp kontrollen spårningar den `LocalForwarder.config` filen. Om du har följt stegen i den [nödvändiga](https://docs.microsoft.com/azure/application-insights/local-forwarder#windows-service), det ska finnas i `C:\LF-WindowsServiceHost`.

    I nedanstående bild av loggfilen, kan du se att innan du kör skriptet andra där vi har lagt till en exportör `OpenCensus input BatchesReceived` : 0. När vi började köras det uppdaterade skriptet `BatchesReceived` ökar lika med antalet värden som vi angav:
    
    ![Nytt App Insights-resursformulär](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Börja övervaka i Azure-portalen

1. Nu kan du öppna Application Insights **översikt** sida i Azure portal, för att visa information om programmet som körs för närvarande. Välj **Live Metric Stream**.

   ![Skärmbild av översiktsfönstret med live metric stream för valt med röd ram](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Om du kör andra Go-appen igen och starta uppdatering av webbläsaren för `http://localhost:50030`, ser du live spårningsdata när de anländer i Application Insights från lokala vidarebefordrartjänsten.

   ![Skärmbild av live metric stream för med prestandadata som visas](./media/opencensus-go/0006-stream.png)

3. Gå tillbaka till den **översikt** och välj **Programkartan** för en visuell layout av beroenden och anrop tidsinställning mellan programkomponenter.

    ![Skärmbild av program med grundläggande karta](./media/opencensus-go/0007-application-map.png)

    Eftersom vi bara spårning ett metodanrop, inte våra programkartan som intressant. Men programkartan kan skalas för att visualisera mycket mer distribuerade program:

   ![Programkarta](media/opencensus-go/application-map.png)

4. Välj **Undersök prestanda** att utföra detaljerade prestandaanalys och fastställa orsaken till långsamma prestanda.

    ![Skärmbild av fönstret för prestanda](./media/opencensus-go/0008-performance.png)

5. Att välja **exempel** och sedan klicka på någon av de exempel som visas i rutan till höger startar slutpunkt till slutpunkt transaktion information upplevelse. Även om vår exempelapp kommer bara att visa en enda händelse, skulle ett mer komplext program kan du utforska slutpunkt till slutpunkt-transaktion till nivå i en enskild händelse anropsstacken.

     ![Skärmbild av transaktionsgränssnitt som slutpunkt till slutpunkt](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Spårning av OpenCensus för Go

Vi bara beskrivs grunderna för att integrera OpenCensus för Go med lokala vidarebefordrare och Application Insights. Den [officiella vägledning för användning av OpenCensus Go](https://godoc.org/go.opencensus.io) omfattar mer avancerade avsnitt.

## <a name="next-steps"></a>Nästa steg

* [Programkarta](./../../azure-monitor/app/app-map.md)
* [Prestandaövervakning för slutpunkt till slutpunkt](./../../application-insights/app-insights-tutorial-performance.md)
