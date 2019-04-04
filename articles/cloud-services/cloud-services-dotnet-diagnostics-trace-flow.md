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
ms.openlocfilehash: f0724fd6e5f08f3e09bcb147c12d1657235dc704
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916894"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Spåra flödet för en Cloud Services-program med Azure Diagnostics
Spårning är ett sätt för dig att övervaka körning av ditt program när den körs. Du kan använda den [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace), [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug), och [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) klasser för att samla in information om fel och program som körs i loggar, textfiler och andra enheter för senare analys. Läs mer om spårning av [spårning och instrumentering av program](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Använd spårningsinstruktioner och spåra växlar
Implementera spårning i Cloud Services-program genom att lägga till den [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) programkonfigurationen och anrop till System.Diagnostics.Trace eller System.Diagnostics.Debug i din programkod. Använda konfigurationsfilen *app.config* för worker-roller och *web.config* för web-roller. När du skapar en ny värdbaserad tjänst med hjälp av en mall för Visual Studio, Azure Diagnostics läggs automatiskt till projektet och DiagnosticMonitorTraceListener läggs till i lämpliga konfigurationsfilen för de roller som du lägger till.

Information om placera spårningsinstruktioner som finns i [så här: Lägga till Trace-satser i programkoden](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Genom att placera [Trace växlar](/dotnet/framework/debug-trace-profile/trace-switches) i din kod, du kan styra om spårning av sker och hur omfattande som den är. På så sätt kan du övervaka status för ditt program i en produktionsmiljö. Detta är särskilt viktigt i ett affärsprogram som använder flera komponenter som körs på flera datorer. Mer information finns i [Gör så här: Konfigurera spårning växlar](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurera spårning-lyssnare i Azure-program
Spårning, felsökning och TraceSource, måste du ställa in ”lyssnare” för att samla in och registrera de meddelanden som skickas. Lyssnare samla in, lagra och dirigera spårning av meddelanden. De direkta spårningsdata till en lämplig mål, till exempel en logg, fönster eller textfil. Azure-diagnostik använder den [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) klass.

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

Läs mer om lyssnare [Trace lyssnare](/dotnet/framework/debug-trace-profile/trace-listeners).

När du har slutfört stegen för att lägga till lyssnaren kan du lägga till spårningsinstruktioner som din kod.

### <a name="to-add-trace-statement-to-your-code"></a>Att lägga till spårningsinstruktionen i din kod
1. Öppna en källfil för programmet. Till exempel den <RoleName>.cs fil för web-roll eller worker-roll.
2. Lägg till följande med instruktionen om den inte redan har lagts:
    ```
        using System.Diagnostics;
    ```
3. Lägg till spårningsinstruktioner som där du vill samla in information om tillståndet för ditt program. Du kan använda olika metoder för att formatera utdata från spårningsinstruktionen. Mer information finns i [Gör så här: Lägga till Trace-satser i programkoden](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Spara källfilen.

