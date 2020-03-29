---
title: Spåra flödet i Cloud Services-programmet med Azure Diagnostics
titleSuffix: Azure Cloud Services
description: Lägg till spårningsmeddelanden i ett Azure-program för att hjälpa till att felsöka, mäta prestanda, övervakning, trafikanalys med mera.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: tagore
ms.openlocfilehash: 47a33ba27dd6d2df626d93695c421303bace6a0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386518"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Spåra flödet för ett Cloud Services-program med Azure Diagnostics
Spårning är ett sätt för dig att övervaka körningen av ditt program medan det körs. Du kan använda klasserna [System.Diagnostics.Trace,](/dotnet/api/system.diagnostics.trace) [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug)och [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) för att registrera information om fel och programkörning i loggar, textfiler eller andra enheter för senare analys. Mer information om spårning finns i [Spåra och instrumentera program](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Använda spårningssatser och spårningsväxlar
Implementera spårning i ditt Cloud Services-program genom att lägga till [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) i programkonfigurationen och ringa samtal till System.Diagnostics.Trace eller System.Diagnostics.Debug i programkoden. Använd konfigurationsfilen *app.config* för arbetsroller och *web.config* för webbroller. När du skapar en ny värdtjänst med hjälp av en Visual Studio-mall läggs Azure Diagnostics automatiskt till i projektet och DiagnosticMonitorTraceListener läggs till i lämplig konfigurationsfil för de roller som du lägger till.

Information om hur du placerar spårningssatser finns i [Så här lägger du till spårningssatser i programkod](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Genom att placera [spårningsväxlar](/dotnet/framework/debug-trace-profile/trace-switches) i koden kan du styra om spårning sker och hur omfattande den är. På så sätt kan du övervaka status för ditt program i en produktionsmiljö. Detta är särskilt viktigt i ett affärsprogram som använder flera komponenter som körs på flera datorer. Mer information finns i Så här konfigurerar du [spårningsväxlar](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurera spårningsavlyssnaren i ett Azure-program
Spåra, felsöka och TraceSource, kräver att du ställer in "lyssnare" för att samla in och registrera de meddelanden som skickas. Lyssnare samlar in, lagrar och dirigerar spårningsmeddelanden. De dirigerar spårningsutdata till ett lämpligt mål, till exempel en logg,ett fönster eller en textfil. Azure Diagnostics använder klassen [DiagnosticMonitorTraceListener.](/previous-versions/azure/reference/ee758610(v=azure.100))

Innan du slutför följande procedur måste du initiera Diagnostikövervakaren i Azure. Det gör du genom att aktivera [diagnostik i Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Observera att om du använder de mallar som tillhandahålls av Visual Studio läggs lyssnarens konfiguration till automatiskt för dig.

### <a name="add-a-trace-listener"></a>Lägga till en spårningslyssnare
1. Öppna filen web.config eller app.config för din roll.
2. Lägg till följande kod i filen. Ändra attributet Version om du vill använda versionsnumret för den sammansättning som du refererar till. Sammansättningsversionen ändras inte nödvändigtvis med varje Azure SDK-version om det inte finns uppdateringar av den.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Kontrollera att du har en projektreferens till sammansättningen Microsoft.WindowsAzure.Diagnostics. Uppdatera versionsnumret i xml ovan för att matcha versionen av den refererade Microsoft.WindowsAzure.Diagnostics-sammansättningen.
   > 
   > 
3. Spara konfigurationsfilen.

Mer information om lyssnare finns i [Spåra lyssnare](/dotnet/framework/debug-trace-profile/trace-listeners).

När du har slutfört stegen för att lägga till lyssnaren kan du lägga till spårningssatser i koden.

### <a name="to-add-trace-statement-to-your-code"></a>Så här lägger du till spårningssats i koden
1. Öppna en källfil för ditt program. Till exempel \<rollnamnsfilen>.cs för arbetarrollen eller webbrollen.
2. Lägg till följande med hjälp av direktivet om det inte redan har lagts till:
    ```
        using System.Diagnostics;
    ```
3. Lägg till spårningssatser där du vill samla in information om programmets tillstånd. Du kan använda en mängd olika metoder för att formatera utdata för Spårningssatsen. Mer information finns i [Så här lägger du till spårningssatser i programkod](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Spara källfilen.




