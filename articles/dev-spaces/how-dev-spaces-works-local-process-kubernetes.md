---
title: Så här fungerar lokal process med Kubernetes
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Beskriver processer för att använda lokal process med Kubernetes för att ansluta din utvecklings dator till ditt Kubernetes-kluster
keywords: Lokal process med Kubernetes, Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 443783eb7f5359318cf8efbec8b6466a80fa1e85
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84316602"
---
# <a name="how-local-process-with-kubernetes-works"></a>Så här fungerar lokal process med Kubernetes

Lokal process med Kubernetes gör att du kan köra och felsöka kod på din utvecklings dator, medan du fortfarande är ansluten till ditt Kubernetes-kluster med resten av dina program eller tjänster. Om du till exempel har en stor mikrotjänsters arkitektur med många beroende tjänster och databaser, kan det vara svårt att replikera dessa beroenden på din utvecklings dator. Dessutom kan du skapa och distribuera kod till ditt Kubernetes-kluster för varje kod ändring under en inre loop-utveckling kan vara långsam, tids krävande och svårt att använda med en fel sökare.

Lokal process med Kubernetes Undvik att behöva skapa och distribuera din kod till klustret genom att i stället skapa en anslutning direkt mellan utvecklings datorn och klustret. Genom att ansluta din utvecklings dator till klustret medan du felsöker kan du snabbt testa och utveckla din tjänst i kontexten för det fullständiga programmet utan att skapa någon Docker-eller Kubernetes-konfiguration.

Lokal process med Kubernetes omdirigerar trafik mellan ditt anslutna Kubernetes-kluster och din utvecklings dator. Den här trafiken tillåter kod på din utvecklings dator och tjänster som körs i ditt Kubernetes-kluster för att kommunicera som om de befinner sig i samma Kubernetes-kluster. Lokal process med Kubernetes ger också ett sätt att replikera miljövariabler och monterade volymer som är tillgängliga för poddar i ditt Kubernetes-kluster på din utvecklings dator. Genom att ge åtkomst till miljövariabler och monterade volymer på din utvecklings dator kan du snabbt arbeta med din kod utan att behöva replikera dessa beroenden manuellt.

## <a name="using-local-process-with-kubernetes"></a>Använda lokal process med Kubernetes

Om du vill använda en lokal process med Kubernetes behöver du [Visual Studio Code][vs-code] med [Azure dev Spaces][azds-vs-code] och [Azure Kubernetes service][az-aks-vs-code] Extensions installerade och körs på MacOS eller Windows 10 samt att [CLI för Azure dev Spaces är installerat][azds-cli]. Du kan också använda [Visual Studio 2019][visual-studio] som körs på Windows 10 med arbets belastningarna *ASP.net och webb utveckling* och *Azure Development* -arbetsbelastningar installerade och funktionen *AzureDevSpacesTools. LocalKubernetesDebugging* för förhands granskning aktive rad samt [Azure dev Spaces CLI installerat][azds-cli]. När du använder lokal process med Kubernetes för att upprätta en anslutning till ditt Kubernetes-kluster har du möjlighet att omdirigera all trafik till och från en befintlig Pod i klustret till din utvecklings dator.

> [!NOTE]
> När du använder lokal process med Kubernetes uppmanas du att ange namnet på den tjänst som ska omdirigeras till utvecklings datorn. Det här alternativet är ett bekvämt sätt att identifiera en POD för omdirigering. All omdirigering mellan ditt Kubernetes-kluster och din utvecklings dator är för en pod.

När lokal process med Kubernetes upprättar en anslutning till klustret:

* Du uppmanas att konfigurera tjänsten att ersätta i klustret, porten på din utvecklings dator som ska användas för din kod och start uppgiften för din kod som en engångs åtgärd.
* Ersätter behållaren i pod i klustret med en fjärragent-behållare som omdirigerar trafik till utvecklings datorn.
* Kör [kubectl-port – vidarebefordra][kubectl-port-forward] på din utvecklings dator för att vidarebefordra trafik från din utvecklings dator till fjärragenten som körs i klustret.
* Samlar in miljö information från klustret med hjälp av fjärragenten. Den här miljö informationen omfattar miljövariabler, synliga tjänster, volym monteringar och hemliga monteringar.
* Ställer in miljön i Visual Studio eller Visual Studio Code så att tjänsten på din utvecklings dator kan komma åt samma variabler som om den kördes i klustret.  
* Uppdaterar värd filen för att mappa tjänster i klustret till lokala IP-adresser på din utvecklings dator. Dessa värd fil poster tillåter att kod körs på utvecklings datorn för att göra förfrågningar till andra tjänster som körs i klustret. Om du vill uppdatera värd filen ber den lokala processen med Kubernetes dig att be om administratörs åtkomst på din utvecklings dator när du ansluter till klustret.
* Börjar köra och felsöka koden på din utvecklings dator. Vid behov kommer lokala processer med Kubernetes att frigöra de portar som krävs på utvecklings datorn genom att stoppa tjänster eller processer som för närvarande använder dessa portar.

När du har upprättat en anslutning till klustret kan du köra och felsöka kod internt på din dator, utan skapa behållare, och koden kan interagera direkt med resten av klustret. All nätverks trafik som fjärragenten tar emot omdirigeras till den lokala port som anges under anslutningen, så att din interna körnings kod kan acceptera och bearbeta trafiken. Miljövariabler, volymer och hemligheter från klustret görs tillgängliga för kod som körs på din utvecklings dator. På grund av värd fil posterna och vidarebefordran av portar till din Developer-dator genom lokal process med Kubernetes, kan din kod skicka nätverks trafik till tjänster som körs i klustret med hjälp av tjänst namnen från klustret, och trafiken vidarebefordras till tjänsterna som körs i klustret. Trafiken dirigeras mellan din utvecklings dator och klustret hela tiden du är ansluten.

## <a name="diagnostics-and-logging"></a>Diagnostik och loggning

När du använder den lokala processen med Kubernetes för att ansluta till klustret loggas diagnostikloggar från klustret till utvecklings datorns [tillfälliga katalog][azds-tmp-dir]. Med hjälp av Visual Studio Code kan du också använda kommandot *Visa diagnostisk information* för att skriva ut aktuella miljövariabler och DNS-poster från klustret.

## <a name="next-steps"></a>Nästa steg

För att komma igång med att använda lokal process med Kubernetes för att ansluta till din lokala utvecklings dator till klustret, se [Använd lokal process med Kubernetes med Visual Studio Code][local-process-kubernetes-vs-code] och [Använd lokal process med Kubernetes med Visual Studio][local-process-kubernetes-vs].

[azds-cli]: how-to/install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[local-process-kubernetes-vs-code]: how-to/local-process-kubernetes-vs-code.md
[local-process-kubernetes-vs]: how-to/local-process-kubernetes-visual-studio.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[visual-studio]: https://www.visualstudio.com/vs/
[vs-code]: https://code.visualstudio.com/download