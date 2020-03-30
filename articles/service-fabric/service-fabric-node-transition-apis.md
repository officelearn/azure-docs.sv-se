---
title: Starta och stoppa klusternoder
description: Lär dig hur du använder felinsprutning för att testa ett Service Fabric-program genom att starta och stoppa klusternoder.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: 8f2eefec94ad4763a054ee089b17232c41e642dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609799"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Ersätta API:erna för startnod och stoppa nod med NOD Transition API

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Vad gör stop noden och startnoden API:er?

Stop Node API (hanterat: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) stoppar en service fabric-nod.  En Service Fabric-nod är process, inte en virtuell dator eller dator – den virtuella datorn eller datorn körs fortfarande.  För resten av dokumentet betyder "nod" Service Fabric-noden.  Om du stoppar en nod placeras den i ett *stoppat* tillstånd där den inte är medlem i klustret och inte kan vara värd för tjänster, vilket simulerar en *nednod.*  Detta är användbart för att injicera fel i systemet för att testa din ansökan.  Startnod API (hanterad: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) återför Stop Node API, vilket ger noden tillbaka till ett normalt tillstånd.

## <a name="why-are-we-replacing-these"></a>Varför ersätter vi dessa?

Som tidigare beskrivits är en *stoppad* Service Fabric-nod en nod som avsiktligt är riktad med hjälp av API:et för stoppa nod.  En *down* ned-nod är en nod som är nere av någon annan anledning (till exempel är den virtuella datorn eller datorn avstängd).  Med STOP Node API exponerar systemet inte information för att skilja mellan *stoppade* noder och *nedåtnoder.*

Dessutom är vissa fel som returneras av dessa API:er inte så beskrivande som de kan vara.  Om du till exempel anropar API:et för stop nod på en redan *stoppad* nod returneras felet *InvalidAddress*.  Denna erfarenhet skulle kunna förbättras.

Dessutom är varaktigheten en nod stoppas för "oändlig" tills Start Nod API anropas.  Vi har upptäckt att detta kan orsaka problem och kan vara felbenägna.  Vi har till exempel sett problem där en användare har anropat Stop Node-API:et på en nod och sedan glömt bort det.  Senare var det oklart om noden var *nere* eller *stoppad*.


## <a name="introducing-the-node-transition-apis"></a>Vi presenterar API:erna för nodövergång

Vi har tagit itu med dessa problem ovan i en ny uppsättning API:er.  Det nya nodövergångs-API:et (hanterat: [StartNodeTransitionAsync()][snt]) kan användas för att överföra en Service Fabric-nod till ett *stoppat* tillstånd eller för att överföra den från ett *stoppat* tillstånd till ett normalt upptillstånd.  Observera att "Start" i API:ets namn inte refererar till att starta en nod.  Den refererar till början av en asynkron åtgärd som systemet ska köra för att överföra noden till antingen *stoppat* eller startat tillstånd.

**Användning**

Om API:et för nodövergång inte genererar ett undantag när det anropas, har systemet accepterat den asynkrona åtgärden och kör den.  Ett lyckat samtal innebär inte att åtgärden är klar ännu.  Om du vill hämta information om det aktuella tillståndet för åtgärden anropar du API:et för nodövergångsförlopp (hanterat: [GetNodeTransitionProgressAsync()][gntp]) med det guid som används vid anropande nod transition API för den här åtgärden.  Api:et för nodövergångsförlopp returnerar ett nodtransitionprogress-objekt.  Egenskapen State anger åtgärdens aktuella tillstånd.  Om tillståndet är "Körs" körs åtgärden.  Om den är slutförd slutförs åtgärden utan fel.  Om det är fel uppstod ett problem med att utföra åtgärden.  Egenskapen Result's Exception anger vad problemet var.  Mer https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate information om egenskapen State finns i avsnittet "Exempelanvändning" nedan för kodexempel.


**Skilja mellan en stoppad nod och en nedåtnod** Om en nod *stoppas* med API:et för nodövergång visas utdata från en nodfråga (hanterad: [GetNodeListAsync()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) att den här noden har ett *IsStopped-egenskapsvärde* för true.  Observera att detta skiljer sig från värdet för egenskapen *NodeStatus,* som säger *Down*.  Om egenskapen *NodeStatus* har värdet *Ned*, men *IsStopped* är falskt, stoppades inte noden med nodövergångs-API:et och är *nere* på grund av någon annan orsak.  Om egenskapen *IsStopped* är true och egenskapen *NodeStatus* är *Down,* stoppades den med nodövergångs-API:et.

Om du startar en *stoppad* nod med API:et för nodövergång returneras den till att fungera som en normal medlem i klustret igen.  Utdata för nodfrågan API visar *IsStopped* som false och *NodeStatus* som något som inte är Ned (till exempel Upp).


**Begränsad varaktighet** När du använder API:et för nodövergång för att stoppa en nod representerar en av de nödvändiga parametrarna, *stopNodeDurationInSeconds*, hur lång tid i sekunder som ska hålla *noden stoppad*.  Detta värde måste vara i det tillåtna intervallet, som har minst 600, och högst 14400.  När den här tiden har gått ut startar noden om sig själv till uppläge automatiskt.  Se exempel 1 nedan för ett exempel på användning.

> [!WARNING]
> Undvik att blanda nodövergångs-API:er och API:erna för stoppa nod och startnod.  Rekommendationen är att endast använda nodövergångs-API:et.  > Om en nod redan har stoppats med api:et för stoppa nod bör den startas med startnodens API först innan du använder API:erna för > nodövergång.

> [!WARNING]
> Flera nodövergångs-API:er kan inte göras på samma nod parallellt.  I en sådan situation kommer Node Transition API > kasta en FabricException med ett ErrorCode egenskapsvärde för NodeTransitionInProgress.  När en nodövergång på en viss nod har > startats bör du vänta tills åtgärden når ett terminaltillstånd (slutfört, fel eller ForceCancelled) innan du startar en > ny övergång på samma nod.  Parallella nodövergångsanrop på olika noder är tillåtna.


#### <a name="sample-usage"></a>Exempelanvändning


**Exempel 1** - Följande exempel använder API:et för nodövergång för att stoppa en nod.

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

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Prov 2** - Följande exempel startar en *stoppad* nod.  Den använder några hjälpmetoder från det första exemplet.

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

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Exempel 3** - Följande exempel visar felaktig användning.  Den här användningen är felaktig eftersom *stopDurationInSeconds* det ger är större än det tillåtna intervallet.  Eftersom StartNodeTransitionAsync() misslyckas med ett allvarligt fel, accepterades inte åtgärden och status-API:et bör inte anropas.  Det här exemplet använder några hjälpmetoder från det första exemplet.

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

**Prov 4** - Följande exempel visar felinformation som kommer att returneras från API:et för nodövergångsförlopp när åtgärden som initierats av API:et för nodövergång accepteras, men misslyckas senare när den körs.  I fallet misslyckas det eftersom API:et för nodövergång försöker starta en nod som inte finns.  Det här exemplet använder några hjälpmetoder från det första exemplet.

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

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
