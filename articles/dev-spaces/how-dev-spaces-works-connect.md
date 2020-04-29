---
title: Så här ansluter du din utvecklings dator till ditt AKS-kluster
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver processerna som använder Azure dev Spaces för att ansluta din utvecklings dator till ditt Azure Kubernetes service-kluster
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241717"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Så här ansluter du din utvecklings dator till ditt AKS-kluster

Med Azure dev Spaces kan du ansluta din utvecklings dator till ditt AKS-kluster, så att du kan köra och felsöka kod på din utvecklings dator som om den kördes i klustret. Azure dev Spaces omdirigerar trafik mellan ditt anslutna AKS-kluster genom att köra en POD på klustret som fungerar som en fjärragent för att dirigera om trafiken mellan din utvecklings dator och klustret. Den här trafiken tillåter kod på din utvecklings dator och tjänster som körs i ditt AKS-kluster för att kommunicera som om de befann sig i samma AKS-kluster. Med den här anslutningen kan du också köra och felsöka kod med eller utan en behållare på din utvecklings dator. Genom att ansluta din utvecklings dator till klustret kan du snabbt utveckla ditt program och utföra testningen från slut punkt till slut punkt.

## <a name="connecting-to-your-cluster"></a>Ansluter till klustret

Du ansluter till ditt befintliga AKS-kluster med hjälp av [Visual Studio Code][vs-code] med tillägget [Azure dev Spaces][azds-vs-code] installerat på MacOS eller Windows 10. När du upprättar en anslutning har du möjlighet att omdirigera all trafik till och från en ny eller befintlig Pod i klustret till din utvecklings dator.

> [!NOTE]
> När du använder Visual Studio Code för att ansluta till klustret ger tillägget Azure dev Spaces möjligheten att omdirigera en tjänst till din utvecklings dator. Det här alternativet är ett bekvämt sätt att identifiera en POD för omdirigering. All omdirigering mellan ditt AKS-kluster och din utvecklings dator är för en pod.

Att ansluta till klustret kräver inte att du har aktiverat Azure dev Spaces i klustret. I stället, när tillägget Azure dev Spaces upprättar en anslutning till klustret:

* Ersätter behållaren i pod i AKS-klustret med en fjärragent-behållare som omdirigerar trafik till utvecklings datorn. När du omdirigerar en ny Pod skapar Azure dev Spaces en ny Pod i ditt AKS-kluster med fjärragenten.
* Kör [kubectl-port – vidarebefordra][kubectl-port-forward] på din utvecklings dator för att vidarebefordra trafik från din utvecklings dator till fjärragenten som körs i klustret.
* Samlar in miljö information från klustret med hjälp av fjärragenten. Den här miljö informationen omfattar miljövariabler, synliga tjänster, volym monteringar och hemliga monteringar.
* Konfigurerar miljön i Visual Studio Code-terminalen så att tjänsten på din utvecklings dator kan komma åt samma variabler som om den kördes i klustret.  
* Uppdaterar värd filen för att mappa tjänster på ditt AKS-kluster till lokala IP-adresser på din utvecklings dator. Dessa värd fil poster tillåter att kod körs på utvecklings datorn för att göra förfrågningar till andra tjänster som körs i klustret. För att uppdatera värd filen ber Azure dev Spaces att be om administratörs åtkomst på din utvecklings dator när du ansluter till klustret.

Om du har aktiverat Azure dev Spaces i klustret kan du också välja att använda den [omdirigering av trafiken som erbjuds av Azure dev Spaces][how-it-works-routing]. Med hjälp av den omdirigering av trafiken som erbjuds av Azure dev Spaces kan du ansluta till en kopia av tjänsten som körs i ett underordnat dev-utrymme. Genom att använda ett underordnat dev Space kan du undvika att andra arbetar i det överordnade dev-utrymmet eftersom du bara omdirigerar trafik som riktar sig mot den underordnade utrymmes instansen av tjänsten, så att den överordnade utrymmes instansen för tjänsten inte har ändrats.

När du ansluter till klustret dirigeras trafiken till din utvecklings dator oavsett om du har tjänsten som körs på din utvecklings dator.

## <a name="running-code-on-your-development-computer"></a>Köra kod på utvecklings datorn

När du har upprättat en anslutning till ditt AKS-kluster kan du köra valfri kod internt på datorn, utan skapa behållare. All nätverks trafik som fjärragenten tar emot omdirigeras till den lokala port som anges under anslutningen, så att din interna körnings kod kan acceptera och bearbeta trafiken. Miljövariabler, volymer och hemligheter från klustret görs tillgängliga för kod som körs på din utvecklings dator. På grund av värd fil posterna och vidarebefordran av portar som lagts till i din Developer-dator av Azure dev Spaces, kan din kod skicka nätverks trafik till tjänster som körs i klustret med hjälp av tjänst namnen från klustret och trafiken vidarebefordras till tjänsterna som körs i klustret.

Eftersom din kod körs på din utvecklings dator har du möjlighet att använda alla verktyg som du normalt använder för att utveckla och köra koden och felsöka den. Trafiken dirigeras mellan din utvecklings dator och klustret hela tiden du är ansluten. Med den här permanenta anslutningen kan du starta, stoppa och starta om koden så mycket som du behöver utan att behöva upprätta en anslutning igen.

Dessutom erbjuder Azure dev Spaces ett sätt att replikera miljövariabler och monterade filer som är tillgängliga för poddar i ditt AKS-kluster i din utvecklings dator via filen *azds-Local. miljö* . Du kan också använda den här filen för att skapa nya miljövariabler och volym monteringar.

## <a name="additional-configuration-with-azds-localenv"></a>Ytterligare konfiguration med azds-Local. kuvert

Med filen *azds-Local. miljö* kan du replikera miljövariabler och monterade filer som är tillgängliga för din poddar i ditt AKS-kluster. Du kan ange följande åtgärder i en *azds-Local. miljö* -fil:

* Ladda ned en volym och ange sökvägen till den volymen som en miljö variabel.
* Hämta en enskild fil eller en uppsättning filer från en volym och montera den på din utvecklings dator.
* Gör en tjänst tillgänglig oavsett vilket kluster du är ansluten till.

Här är ett exempel på en *azds-Local. miljö* -fil:

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

En standard- *azds-Local. kuvert* -fil skapas inte automatiskt, så du måste skapa filen manuellt i roten för projektet.

## <a name="diagnostics-and-logging"></a>Diagnostik och loggning

När du är ansluten till ditt AKS-kluster loggas diagnostikloggar från klustret till utvecklings datorns [temporära katalog][azds-tmp-dir]. Med hjälp av Visual Studio Code kan du också använda kommandot *Visa diagnostisk information* för att skriva ut aktuella miljövariabler och DNS-poster från ditt AKS-kluster.

## <a name="next-steps"></a>Nästa steg

För att komma igång med att ansluta din lokala utvecklings dator till ditt AKS-kluster, se [ansluta din utvecklings dator till ett AKS-kluster][connect].

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
