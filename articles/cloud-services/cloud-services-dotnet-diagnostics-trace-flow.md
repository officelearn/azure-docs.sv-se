---
title: Spåra flödet i ett Cloud Services program med Azure-diagnostik | Microsoft Docs
description: Lägg till spårnings meddelanden i ett Azure-program för att felsöka, mäta prestanda, övervaka, trafik analys med mera.
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: gwallace
ms.openlocfilehash: e3e34ff9b5ce1c3a7b45468d22faddddf0c9a913
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359146"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Spåra flödet av ett Cloud Services program med Azure-diagnostik
Spårning är ett sätt för dig att övervaka körningen av programmet medan den körs. Du kan använda klasserna [system. Diagnostics. trace](/dotnet/api/system.diagnostics.trace), [system. Diagnostics. debug](/dotnet/api/system.diagnostics.debug)och [system. Diagnostics. TraceSource](/dotnet/api/system.diagnostics.tracesource) för att registrera information om fel och program körning i loggar, textfiler eller andra enheter för senare analys. Mer information om spårning finns i spårnings- [och Instrumentation-program](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Använda spårnings-och spårnings växlar
Implementera spårning i Cloud Services programmet genom att lägga till [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) i program konfigurationen och göra anrop till system. Diagnostics. trace eller system. Diagnostics. debug i din program kod. Använd konfigurations filen *app. config* för Worker-roller och *Web. config* för webb roller. När du skapar en ny värdbaserad tjänst med hjälp av en Visual Studio-mall läggs Azure-diagnostik automatiskt till i projektet och DiagnosticMonitorTraceListener läggs till i rätt konfigurations fil för de roller som du lägger till.

Information om hur du placerar spårnings instruktioner [finns i How to: Lägg till spårnings instruktioner till](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)program koden.

Genom att placera [spårnings växlar](/dotnet/framework/debug-trace-profile/trace-switches) i din kod kan du kontrol lera om spårning sker och hur omfattande det är. På så sätt kan du övervaka programmets status i en produktions miljö. Detta är särskilt viktigt i ett affärs program som använder flera komponenter som körs på flera datorer. Mer information finns i [så här: Konfigurera spårnings](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches)växlar.

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurera spårnings lyssnaren i ett Azure-program
Trace, debug och TraceSource kräver att du konfigurerar "lyssnare" för att samla in och registrera meddelanden som skickas. Lyssnare samlar in, lagrar och dirigerar spårnings meddelanden. De dirigerar spårnings resultatet till ett lämpligt mål, till exempel en logg, ett fönster eller en textfil. Azure-diagnostik använder klassen [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) .

Innan du slutför följande procedur måste du initiera Azure Diagnostic Monitor. Information om hur du gör detta finns [i Aktivera diagnostik i Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Observera att om du använder mallarna som tillhandahålls av Visual Studio läggs konfigurationen av lyssnaren till automatiskt åt dig.

### <a name="add-a-trace-listener"></a>Lägg till en spårnings lyssnare
1. Öppna filen Web. config eller app. config för din roll.
2. Lägg till följande kod i filen. Ändra versions-attributet så att det använder versions numret för den sammansättning som du refererar till. Sammansättnings versionen ändras inte nödvändigt vis med varje Azure SDK-utgåva om det inte finns några uppdateringar.
   
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
   > Se till att du har en projekt referens till sammansättningen Microsoft. WindowsAzure. Diagnostics. Uppdatera versions numret i XML-koden ovan för att matcha versionen för den refererade sammansättningen av Microsoft. WindowsAzure. Diagnostics.
   > 
   > 
3. Spara konfigurations filen.

Mer information om lyssnare finns i [spåra lyssnare](/dotnet/framework/debug-trace-profile/trace-listeners).

När du har slutfört stegen för att lägga till lyssnaren kan du lägga till spårnings uttryck i koden.

### <a name="to-add-trace-statement-to-your-code"></a>Lägga till en spårnings instruktion i koden
1. Öppna en källfil för programmet. Till exempel \<rolename-filen >. cs för arbets rollen eller webb rollen.
2. Lägg till följande using-instruktion om den inte redan har lagts till:
    ```
        using System.Diagnostics;
    ```
3. Lägg till spårnings instruktioner där du vill samla in information om appens tillstånd. Du kan använda olika metoder för att formatera utdata från trace-instruktionen. Mer information finns i [så här: Lägg till spårnings instruktioner till](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)program koden.
4. Spara käll filen.

