---
title: "Spåra flödet i Cloud Services-program med Azure-diagnostik | Microsoft Docs"
description: "Lägga till spårning av meddelanden till ett Azure-program för att felsöka mäta prestanda, övervakning, trafik analys och mer."
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: 
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: robb
ms.openlocfilehash: 35b4a4270846c54a1ca760e803ef7adba60cf03b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Spåra flödet av ett Cloud Services-program med Azure-diagnostik
Spårning är ett sätt att övervaka körning av ditt program när den körs. Du kan använda den [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx), och [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) klasser för att registrera information om fel och program som körs i loggar, textfiler eller andra enheter för senare analys. Mer information om spårning finns [spårning och instrumentering program](https://msdn.microsoft.com/library/zs6s4h68.aspx).

## <a name="use-trace-statements-and-trace-switches"></a>Använda trace-satser och spåra växlar
Implementera spårning i tillämpningsprogrammet molntjänster genom att lägga till den [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) programkonfigurationen och anropar System.Diagnostics.Trace eller System.Diagnostics.Debug i din programkod. Använda konfigurationsfilen *app.config* för arbetsroller och *web.config* för webbroller. När du skapar en ny värdbaserad tjänst med en mall för Visual Studio Azure Diagnostics läggs automatiskt till projektet och DiagnosticMonitorTraceListener har lagts till i lämpliga konfigurationsfilen för de roller som du lägger till.

Information om att placera trace-satser finns [så här: Lägg till Trace-satser för programkoden](https://msdn.microsoft.com/library/zd83saa2.aspx).

Genom att placera [Trace växlar](https://msdn.microsoft.com/library/3at424ac.aspx) i koden, du kan styra om spårning sker och hur omfattande är. På så sätt kan du övervaka status för ditt program i en produktionsmiljö. Detta är särskilt viktigt i ett affärsprogram som använder flera komponenter som körs på flera datorer. Mer information finns i [så här: konfigurera spårning växlar](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurera spårningslyssnaren i ett Azure-program
Spåra, felsökning och TraceSource, måste du ställa in ”lyssnare” för att samla in och registrera de meddelanden som skickas. Lyssnare samla in, lagra och vidarebefordra spårning av meddelanden. De direkt spårning utdata till en lämplig mål, till exempel en logg, fönster eller textfil. Azure Diagnostics använder den [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) klass.

Innan du slutför följande procedur måste du initiera Azure diagnostikövervakare. För att göra detta, se [aktiverar diagnostik i Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Observera att om du använder mallar som tillhandahålls av Visual Studio, konfigurationen av lyssnaren läggs automatiskt åt dig.

### <a name="add-a-trace-listener"></a>Lägga till en spårningsavlyssningen
1. Öppna filen web.config eller app.config för din roll.
2. Lägg till följande kod i filen. Ändra attributet Version om du vill använda versionsnumret för sammansättningen som du refererar till. Sammansättningens version ändras inte nödvändigtvis i varje version av Azure SDK om det finns uppdateringar till den.
   
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
   > Kontrollera att du har en projektreferens till sammansättningen Microsoft.WindowsAzure.Diagnostics. Uppdatera versionsnumret i xml-ovan för att matcha versionen av den refererade sammansättningen Microsoft.WindowsAzure.Diagnostics.
   > 
   > 
3. Spara konfigurationsfilen.

Läs mer om lyssnare [Samlingsspårlyssnarna](https://msdn.microsoft.com/library/4y5y10s7.aspx).

När du har slutfört stegen för att lägga till lyssnaren kan du lägga till trace-satser din kod.

### <a name="to-add-trace-statement-to-your-code"></a>Att lägga till spårningsinstruktionen i koden
1. Öppna en källfil för programmet. Till exempel den <RoleName>.cs-fil för arbetsrollen eller webbroll.
2. Lägg till följande med instruktionen om det inte redan lagts:
    ```
        using System.Diagnostics;
    ```
3. Lägg till Trace-satser där du vill samla in information om tillståndet för programmet. Du kan använda olika metoder för att formatera utdata från spårningsinstruktionen. Mer information finns i [så här: Lägg till Trace-satser för programkoden](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Spara källfilen.

