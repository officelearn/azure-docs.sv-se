---
title: Openräkningar gå med i Azure Application Insights | Microsoft Docs
description: Innehåller anvisningar om hur du integrerar Open-räkningar genom att gå med i den lokala vidarebefordraren och Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 56e66f17e9ce1d2482463f619e82dfd29d48f191
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67990306"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Samla in distribuerade spår från Go (för hands version)

Application Insights stöder nu distribuerad spårning av Go-program via integration [](https://opencensus.io) med openräkning och vår nya [lokala vidarebefordrare](./opencensus-local-forwarder.md). I den här artikeln får du stegvisa anvisningar genom processen för att ställa in openräkning för go och hämta spårnings data till Application Insights.

## <a name="prerequisites"></a>Förutsättningar

- Du behöver en Azure-prenumeration.
- Go ska installeras, den här artikeln använder hämtningen version 1,11 [Go](https://golang.org/dl/).
- Följ anvisningarna för att installera den [lokala vidarebefordraren som en Windows-tjänst](./opencensus-local-forwarder.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Skapa Application Insights resurs

Först måste du skapa en Application Insights-resurs som ska generera en Instrumentation-nyckel (iKey). IKey används sedan för att konfigurera den lokala vidarebefordraren så att den skickar distribuerade spår från ditt program för openinventerings instrument, till Application Insights.   

1. Välj **skapa en resurs** > **utvecklarverktyg** > **Application Insights**.

   ![lägg till en Application Insights-resurs](./media/opencensus-Go/0001-create-resource.png)

 > [!NOTE]
   >Om det här är första gången du skapar en Application Insights-resurs kan du lära dig mer genom att gå till artikeln [skapa en Application Insights resurs](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   En konfigurationsruta visas. Använd följande tabell när du ska fylla i indatafälten.

    | Inställningar        | Value           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namn som identifierar appen du övervakar |
   | **Resursgrupp**     | myResourceGroup      | Namnet på den nya resursgrupp som är värd för App Insights-data |
   | **Location** | East US | Välj en plats nära dig eller nära där appen finns |

2. Klicka på **Skapa**.

## <a name="configure-local-forwarder"></a>Konfigurera lokal vidarebefordrare

1. Välj **Översikt** > **Essentials** > kopiera appens **instrumenteringsnyckel**.

   ![Skärm bild av Instrumentation-tangenten](./media/opencensus-Go/0003-instrumentation-key.png)

2. `LocalForwarder.config` Redigera filen och Lägg till din Instrumentation-nyckel. Om du följde anvisningarna i den förkraviga filen finns på [](./opencensus-local-forwarder.md)`C:\LF-WindowsServiceHost`

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

3. Starta om den **lokala** program tjänsten för vidarebefordrare.

## <a name="opencensus-go-packages"></a>Openräkning go-paket

1. Installera de öppna inventerings paketen för Go från kommando raden:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Lägg till följande kod i en. go-fil och skapa och kör sedan. (Det här exemplet härleds från den officiella openinventerings vägledningen med tillagd kod som underlättar integreringen med den lokala vidarebefordraren)

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

3. När den enkla go-appen körs går du `http://localhost:50030`till. Varje uppdatering av webbläsaren kommer att generera texten "Hello World" tillsammans med motsvarande span-data som hämtas av den lokala vidarebefordraren.

4. Bekräfta att den **lokala vidarebefordraren** hämtas genom att kontrol lera `LocalForwarder.config` filen. Om du följde stegen i förutsättningen [](https://docs.microsoft.com/azure/application-insights/local-forwarder)finns den i `C:\LF-WindowsServiceHost`.

    I bilden nedan av logg filen kan du se att innan du kör det andra skriptet där vi lade till en exportör `OpenCensus input BatchesReceived` var 0. När vi har börjat köra det uppdaterade `BatchesReceived` skriptet som är lika med antalet värden som vi angav:
    
    ![Nytt App Insights-resursformulär](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Börja övervaka i Azure-portalen

1. Nu kan du öppna sidan Application Insights **Översikt** i Azure Portal för att visa information om ditt program som körs. Välj **Live Metric Stream**.

   ![Skärm bild av översikts fönstret med direkt mått data ström vald i röd ruta](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Om du kör den andra go-appen igen och börjar uppdatera webbläsaren för `http://localhost:50030`, visas real tids spårnings data som de kommer in Application Insights från den lokala vidarebefordraren.

   ![Skärm bild av Live Metric Stream med prestanda data som visas](./media/opencensus-go/0006-stream.png)

3. Gå tillbaka till sidan **Översikt** och välj **program karta** för en visuell layout av beroende relationerna och anropa tiden mellan program komponenterna.

    ![Skärm bild av grundläggande program karta](./media/opencensus-go/0007-application-map.png)

    Eftersom vi bara spårade ett metod anrop är vår program karta inte lika intressant. Men program kartan kan skalas för att visualisera mycket fler distribuerade program:

   ![Programkarta](media/opencensus-go/application-map.png)

4. Välj **Undersök prestanda** för att utföra detaljerad prestanda analys och fastställ rotor saken till långsamma prestanda.

    ![Skärm bild av fönstret prestanda](./media/opencensus-go/0008-performance.png)

5. Om du väljer **exempel** och sedan klickar på något av de exempel som visas i den högra rutan, startas transaktions information från slut punkt till slut punkt. Vår exempel App visar bara oss en enda händelse, ett mer komplext program som gör att du kan utforska slut punkt till slut punkt på nivån för en enskild händelses anrops stack.

     ![Skärm bild av transaktions gränssnitt från slut punkt till slut punkt](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Openräknings spårning för go

Vi omfattade bara grunderna för integrering av openräkning för go med den lokala vidarebefordraren och Application Insights. Den [officiella Openräkningar](https://godoc.org/go.opencensus.io) -användnings vägledningen omfattar mer avancerade ämnen.

## <a name="next-steps"></a>Nästa steg

* [Program karta](./../../azure-monitor/app/app-map.md)
* [Prestanda övervakning från slut punkt till slut punkt](./../../azure-monitor/learn/tutorial-performance.md)
