---
title: Så här fungerar det att ansluta utvecklingsdatorn till AKS-klustret
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver processerna för att använda Azure Dev Spaces för att ansluta utvecklingsdatorn till azure kubernetes-tjänstklustret
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241717"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Så här fungerar det att ansluta utvecklingsdatorn till AKS-klustret

Med Azure Dev Spaces kan du ansluta utvecklingsdatorn till AKS-klustret, så att du kan köra och felsöka kod på utvecklingsdatorn som om den kördes i klustret. Azure Dev Spaces omdirigerar trafik mellan ditt anslutna AKS-kluster genom att köra en pod på klustret som fungerar som en fjärragent för att omdirigera trafik mellan utvecklingsdatorn och klustret. Med den här omdirigeringen av trafiken kan kod på utvecklingsdatorn och tjänster som körs i AKS-klustret kommunicera som om de vore i samma AKS-kluster. Med den här anslutningen kan du också köra och felsöka kod med eller utan en behållare på utvecklingsdatorn. Genom att ansluta utvecklingsdatorn till klustret kan du snabbt utveckla programmet och utföra heltäckande tester.

## <a name="connecting-to-your-cluster"></a>Ansluta till klustret

Du ansluter till ditt befintliga AKS-kluster med [Visual Studio-kod][vs-code] med [Azure Dev Spaces-tillägget][azds-vs-code] installerat som körs på MacOS eller Windows 10. När du upprättar en anslutning kan du dirigera om all trafik till och från en ny eller befintlig pod i klustret till utvecklingsdatorn.

> [!NOTE]
> När du använder Visual Studio-kod för att ansluta till klustret ger Azure Dev Spaces-tillägget dig möjlighet att omdirigera en tjänst till utvecklingsdatorn. Det här alternativet är ett praktiskt sätt att identifiera en pod för omdirigering. All omdirigering mellan AKS-klustret och utvecklingsdatorn är för en pod.

Om du ansluter till klustret krävs inte att du har Azure Dev Spaces aktiverat i klustret. När Azure Dev Spaces-tillägget upprättar en anslutning till klustret:

* Ersätter behållaren i podden i AKS-klustret med en behållare för fjärragent som omdirigerar trafik till utvecklingsdatorn. När du omdirigerar en ny pod skapar Azure Dev Spaces en ny pod i AKS-klustret med fjärragenten.
* Kör [kubectl port-forward][kubectl-port-forward] på utvecklingsdatorn för att vidarebefordra trafik från utvecklingsdatorn till fjärragenten som körs i klustret.
* Samlar in miljöinformation från klustret med hjälp av fjärragenten. Den här miljöinformationen omfattar miljövariabler, synliga tjänster, volymfästen och hemliga fästen.
* Ställer in miljön i Visual Studio-kodterminalen så att tjänsten på utvecklingsdatorn kan komma åt samma variabler som om den kördes i klustret.  
* Uppdaterar din hosts-fil för att mappa tjänster i AKS-klustret till lokala IP-adresser på utvecklingsdatorn. Med de här värdfilposterna kan kod som körs på utvecklingsdatorn göra förfrågningar till andra tjänster som körs i klustret. Om du vill uppdatera hosts-filen frågar Azure Dev Spaces efter administratörsåtkomst på utvecklingsdatorn när du ansluter till klustret.

Om du har Azure Dev Spaces aktiverat i klustret har du också möjlighet att använda [den trafikomdirigering som erbjuds av Azure Dev Spaces][how-it-works-routing]. Med den omdirigering av trafiken som erbjuds av Azure Dev Spaces kan du ansluta till en kopia av tjänsten som körs i ett underordnat utvecklingsutrymme. Med hjälp av ett barn dev utrymme hjälper dig att undvika att störa andra som arbetar i den överordnade dev utrymme eftersom du bara omdirigera trafiken inriktning den underordnade utrymmet instans av din tjänst, lämnar den överordnade utrymme instans av tjänsten oförändrad.

När du har anslutit till klustret dirigeras trafiken till utvecklingsdatorn oavsett om du har tjänsten igång på utvecklingsdatorn.

## <a name="running-code-on-your-development-computer"></a>Köra kod på utvecklingsdatorn

När du har upprättat en anslutning till AKS-klustret kan du köra vilken kod som helst internt på datorn, utan att behålla. All nätverkstrafik som fjärragenten tar emot omdirigeras till den lokala port som anges under anslutningen så att koden som körs internt kan acceptera och bearbeta den trafiken. Miljövariabler, volymer och hemligheter från klustret görs tillgängliga för kod som körs på utvecklingsdatorn. På grund av värdfilposterna och portbefordring som azure Dev Spaces har lagt till på utvecklardatorn av Azure Dev Spaces kan koden skicka nätverkstrafik till tjänster som körs i klustret med hjälp av tjänstnamnen från klustret och att trafiken vidarebefordras till tjänster som körs i klustret.

Eftersom koden körs på din utvecklingsdator har du flexibiliteten att använda alla verktyg som du normalt använder för utveckling för att köra koden och felsöka den. Trafiken dirigeras mellan utvecklingsdatorn och klustret hela tiden du är ansluten. Med den här beständiga anslutningen kan du starta, stoppa och starta om koden så mycket du behöver utan att behöva återupprätta en anslutning.

Dessutom är Azure Dev Spaces ett sätt att replikera miljövariabler och monterade filer som är tillgängliga för poddar i AKS-klustret på utvecklingsdatorn via filen *azds-local.env.* Du kan också använda den här filen för att skapa nya miljövariabler och volymfästen.

## <a name="additional-configuration-with-azds-localenv"></a>Ytterligare konfiguration med azds-local.env

Med *filen azds-local.env* kan du replikera miljövariabler och monterade filer som är tillgängliga för dina poddar i AKS-klustret. Du kan ange följande åtgärder i en *azds-local.env-fil:*

* Hämta en volym och ange sökvägen till den volymen som en miljövariabel.
* Hämta en enskild fil eller uppsättning filer från en volym och montera den på utvecklingsdatorn.
* Gör en tjänst tillgänglig oavsett vilket kluster du är ansluten till.

Här är ett exempel *azds-local.env* fil:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

En *standardfil azds-local.env* skapas inte automatiskt, så du måste skapa filen manuellt i projektets rot.

## <a name="diagnostics-and-logging"></a>Diagnostik och loggning

När du är ansluten till AKS-klustret loggas diagnostikloggar från klustret till utvecklingsdatorns [tillfälliga katalog][azds-tmp-dir]. Med Visual Studio-kod kan du också använda kommandot *Visa diagnostikinformation* för att skriva ut aktuella miljövariabler och DNS-poster från AKS-klustret.

## <a name="next-steps"></a>Nästa steg

Mer om du vill komma igång med att ansluta din lokala utvecklingsdator till AKS-klustret finns i [Anslut utvecklingsdatorn till ett AKS-kluster][connect].

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
