---
title: "Starta och stoppa klusternoder för att testa Azure mikrotjänster | Microsoft Docs"
description: "Lär dig hur du använder fel injection för att testa ett Service Fabric-program genom att starta och stoppa klusternoder."
services: service-fabric
documentationcenter: .net
author: LMWF
manager: rsinha
editor: 
ms.assetid: f4e70f6f-cad9-4a3e-9655-009b4db09c6d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: 850fbc0c74811ec942292da64064dec867cd1b9e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Ersätta noden starta och stoppa noden API: er med API: T för noden övergång

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Vad stoppa nod och starta nod API: er?

Stoppa nod-API (hanterade: [StopNodeAsync()][stopnode], PowerShell: [stoppa ServiceFabricNode][stopnodeps]) stoppar en Service Fabric-nod.  En Service Fabric-nod är process, inte en virtuell dator eller en dator – den virtuella datorn eller datorn fortfarande körs.  För resten av dokumentet innebär ”nod” Service Fabric-noden.  Stoppa en nod placeras i en *stoppats* tillstånd där den inte är medlem i klustret och kan inte vara värd för tjänster, vilket simulera en *ned* nod.  Detta är användbart för fel i systemet för att testa ditt program.  Starta nod-API (hanterade: [StartNodeAsync()][startnode], PowerShell: [Start ServiceFabricNode][startnodeps]]) ångrar stoppa noden API, som ger noden till normalt läge.

## <a name="why-are-we-replacing-these"></a>Varför Vi ersätter dessa?

Enligt beskrivningen tidigare, en *stoppats* Service Fabric-noden är en nod som avsiktligt mål med stoppa noden API.  En *ned* nod är en nod som anges av någon annan anledning (t.ex. den virtuella datorn eller datorn är inaktiverat).  Med API stoppa noden systemet inte avslöja information att skilja mellan *stoppats* noder och *ned* noder.

Dessutom kan är vissa fel som returneras av API: erna inte så beskrivande de kunde vara.  Till exempel anropar API: et för stoppa nod på en redan *stoppats* nod returneras felet *InvalidAddress*.  Det här upplevelsen kan förbättras.

En nod har stoppats för varaktighet är också ”oändlig” tills starta nod-API anropas.  Vi har hittat detta kan orsaka problem och kan vara problematiskt.  Till exempel har vi sett problem där en användare anropas stoppa noden API: N på en nod och sedan glömt om den.  Var senare oklart om noden var *ned* eller *stoppats*.


## <a name="introducing-the-node-transition-apis"></a>Introduktion till API: er för noden övergång

Vi har åtgärdas problemen ovan i en ny uppsättning API: er.  Den nya noden övergången API (hanterade: [StartNodeTransitionAsync()][snt]) kan användas för att överföra en Service Fabric-nod en *stoppats* tillstånd, eller att överföra den från en *stoppats* till en normal tillstånd.  Observera att ”Start” i namnet API: et inte refererar till början av en nod.  Den hänvisar till början av en asynkron åtgärd som systemet ska köras för att övergå till antingen noden *stoppats* eller startat tillstånd.

**Användning**

Om noden övergången API inget genereras ett undantag när den anropas sedan systemet har godkänt den asynkrona åtgärden och körs den.  Lyckade anrop innebär inte åtgärden har slutförts ännu.  Anropa API: T för noden övergången förlopp för att få information om det aktuella tillståndet för åtgärden (hanterade: [GetNodeTransitionProgressAsync()][gntp]) med guid som används när du anropar API: T för noden övergången för den här åtgärden.  API: T för noden övergången förlopp returnerar ett NodeTransitionProgress-objekt.  Det här objektet tillstånd egenskapen anger det aktuella tillståndet för åtgärden.  Om tillståndet ”körs” körs igen.  Om den är slutförd, avslutad igen utan fel.  Om det är fel, ett problem uppstod när åtgärden.  Egenskapen resultatet Undantagsegenskapen visar vad problemet är.  Se https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate för mer information om egenskapen State och avsnittet ”exempel” nedan kodexempel.


**Skilja mellan en stoppad nod och en inaktiv nod** om en nod *stoppats* med hjälp av noden övergången API, resultatet av en nod-fråga (hanterade: [GetNodeListAsync()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) visar att den här noden har en *IsStopped* egenskapsvärdet true.  Observera att detta skiljer sig från värdet för den *NodeStatus* -egenskap som talar om *ned*.  Om den *NodeStatus* egenskap har ett värde av *ned*, men *IsStopped* är false, och sedan noden inte har stoppats med hjälp av noden övergången API och *ned* på grund av någon anledning.  Om den *IsStopped* egenskapen är true, och *NodeStatus* egenskapen är *ned*, sedan den stoppades med hjälp av noden övergången API.

Starta en *stoppats* nod med hjälp av noden övergången API returneras den så att den fungerar som en normal medlem i klustret igen.  Visar resultatet av frågan nod API *IsStopped* som FALSKT och *NodeStatus* som något som inte är nere (t.ex. upp).


**Begränsat varaktighet** när noden övergången API för att stoppa en nod, en av de obligatoriska parametrarna *stopNodeDurationInSeconds*, representerar hur lång tid i sekunder som noden *stoppats*.  Det här värdet måste vara i det tillåtna intervallet som har minst 600 och högst 14400.  När denna tid har löpt ut startas noden om sig själv i tillstånd automatiskt.  Finns i exempel 1 nedan ett exempel på användning.

> [!WARNING]
> Undvik att noden övergången API: er och stoppa nod och starta nod API: er.  Rekommendationen är att använda den nod övergång endast API.  > Om en nod har redan har stoppats med hjälp av noden API stoppa den ska startas med starta nod API innan du börjar använda den > noden övergången API: er.

> [!WARNING]
> Flera nod övergången API-anrop kan göras på samma nod parallellt.  I en sådan situation nod övergången API kommer > utlösa en FabricException med ett ErrorCode egenskapsvärde för NodeTransitionInProgress.  När en nod övergång på en viss nod har > är igång kan du vänta tills åtgärden når ett avslutat tillstånd (slutförd, Faulted eller ForceCancelled) innan du startar en > Ny övergång på samma nod.  Parallel-nod övergången anrop på olika noder är tillåtna.


#### <a name="sample-usage"></a>Exempel på användning


**Exempel 1** -i följande exempel används nod övergången API för att stoppa en nod.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until hte desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Exempel 2** -följande exempel startar en *stoppats* nod.  Vissa hjälpmetoder från det första exemplet används.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until hte desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Exempel 3** -följande exempel visar felaktig användning.  Denna användning är felaktig eftersom den *stopDurationInSeconds* ger är större än det tillåtna intervallet.  Eftersom StartNodeTransitionAsync() misslyckas med ett allvarligt fel accepterades inte igen och förloppet API ska inte anropas.  Det här exemplet använder vissa hjälpmetoder från det första exemplet.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**Exempel 4** -i följande exempel visas information om felet som returneras från API: et för noden övergången pågår när åtgärden som initierats av API: T för noden övergången har godkänts, men inte senare vid körning.  I fallet kan misslyckas eftersom API: T för noden övergången försöker starta en nod som inte finns.  Det här exemplet använder vissa hjälpmetoder från det första exemplet.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN#System_Fabric_FabricClient_FaultManagementClient_StopNodeAsync_System_String_System_Numerics_BigInteger_System_Fabric_CompletionMode_
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN#System_Fabric_FabricClient_FaultManagementClient_StartNodeAsync_System_String_System_Numerics_BigInteger_System_String_System_Int32_System_Fabric_CompletionMode_System_Threading_CancellationToken_
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient#System_Fabric_FabricClient_QueryClient_GetNodeListAsync_System_String_
[nodequeryps]: https://docs.microsoft.com/powershell/servicefabric/vlatest/Get-ServiceFabricNode?redirectedfrom=msdn
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartNodeTransitionAsync_System_Fabric_Description_NodeTransitionDescription_System_TimeSpan_System_Threading_CancellationToken_
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_GetNodeTransitionProgressAsync_System_Guid_System_TimeSpan_System_Threading_CancellationToken_
