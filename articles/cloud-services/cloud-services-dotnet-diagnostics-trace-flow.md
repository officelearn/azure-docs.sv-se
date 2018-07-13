---
title: Spåra flödet i en Cloud Services-program med Azure Diagnostics | Microsoft Docs
description: Lägga till spårning av meddelanden till ett Azure-program för att felsökning, mäta prestanda, övervakning, analysen av nätverkstrafik med mera.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: jeconnoc
ms.openlocfilehash: 2ba97e43616386a0ff8459316bfc4d3ddfe241a0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39000903"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Spåra flödet för en Cloud Services-program med Azure Diagnostics
Spårning är ett sätt för dig att övervaka körning av ditt program när den körs. Du kan använda den [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx), och [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) klasser för att samla in information om fel och program som körs i loggar, textfiler och andra enheter för senare analys. Läs mer om spårning av [spårning och instrumentering av program](https://msdn.microsoft.com/library/zs6s4h68.aspx).

## <a name="use-trace-statements-and-trace-switches"></a>Använd spårningsinstruktioner och spåra växlar
Implementera spårning i Cloud Services-program genom att lägga till den [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) programkonfigurationen och anrop till System.Diagnostics.Trace eller System.Diagnostics.Debug i din programkod. Använda konfigurationsfilen *app.config* för worker-roller och *web.config* för web-roller. När du skapar en ny värdbaserad tjänst med hjälp av en mall för Visual Studio, Azure Diagnostics läggs automatiskt till projektet och DiagnosticMonitorTraceListener läggs till i lämpliga konfigurationsfilen för de roller som du lägger till.

Information om placera spårningsinstruktioner som finns i [så här: Lägg till Trace-satser till programkoden](https://msdn.microsoft.com/library/zd83saa2.aspx).

Genom att placera [Trace växlar](https://msdn.microsoft.com/library/3at424ac.aspx) i din kod, du kan styra om spårning av sker och hur omfattande som den är. På så sätt kan du övervaka status för ditt program i en produktionsmiljö. Detta är särskilt viktigt i ett affärsprogram som använder flera komponenter som körs på flera datorer. Mer information finns i [så här: konfigurera spårning växlar](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurera spårning-lyssnare i Azure-program
Spårning, felsökning och TraceSource, måste du ställa in ”lyssnare” för att samla in och registrera de meddelanden som skickas. Lyssnare samla in, lagra och dirigera spårning av meddelanden. De direkta spårningsdata till en lämplig mål, till exempel en logg, fönster eller textfil. Azure-diagnostik använder den [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) klass.

Innan du slutför följande procedur måste du initiera Azure diagnostikövervakare. För att göra detta, se [aktiverar diagnostik i Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Observera att om du använder mallar som tillhandahålls av Visual Studio, konfigurationen av lyssnaren har lagts till automatiskt åt dig.

### <a name="add-a-trace-listener"></a>Lägg till en trace-lyssnare
1. Öppna filen web.config eller app.config för din roll.
2. Lägg till följande kod i filen. Ändra Versionsattributet om du vill använda det lägre versionsnumret för den sammansättning som du refererar till. Sammansättningsversionen ändras inte nödvändigtvis med varje Azure SDK-version om det finns uppdateringar till den.
   
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
   > Kontrollera att du har en projektreferens till sammansättningen Microsoft.WindowsAzure.Diagnostics. Uppdatera versionsnumret i xml ovan för att matcha versionen av den refererade sammansättningen Microsoft.WindowsAzure.Diagnostics.
   > 
   > 
3. Spara konfigurationsfilen.

Läs mer om lyssnare [Trace lyssnare](https://msdn.microsoft.com/library/4y5y10s7.aspx).

När du har slutfört stegen för att lägga till lyssnaren kan du lägga till spårningsinstruktioner som din kod.

### <a name="to-add-trace-statement-to-your-code"></a>Att lägga till spårningsinstruktionen i din kod
1. Öppna en källfil för programmet. Till exempel den <RoleName>.cs fil för web-roll eller worker-roll.
2. Lägg till följande med instruktionen om den inte redan har lagts:
    ```
        using System.Diagnostics;
    ```
3. Lägg till spårningsinstruktioner som där du vill samla in information om tillståndet för ditt program. Du kan använda olika metoder för att formatera utdata från spårningsinstruktionen. Mer information finns i [så här: Lägg till Trace-satser till programkoden](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Spara källfilen.

