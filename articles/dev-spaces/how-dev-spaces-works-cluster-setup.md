---
title: Så här konfigurerar du ett kluster för Azure Dev Spaces fungerar
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver hur det fungerar att konfigurera ett Azure Kubernetes-tjänstkluster för Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241730"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Så här konfigurerar du ett kluster för Azure Dev Spaces fungerar

Azure Dev Spaces ger dig flera sätt att snabbt iterera och felsöka Kubernetes-program och samarbeta med ditt team i ett AKS-kluster (Azure Kubernetes Service). Ett sätt är att aktivera Azure Dev Spaces i AKS-klustret så att du kan [köra tjänster direkt i klustret][how-it-works-up] och använda ytterligare funktioner för nätverk och [routning][how-it-works-routing]. I den här artikeln beskrivs vad som händer när du förbereder klustret och aktiverar Azure Dev Spaces.

## <a name="prepare-your-aks-cluster"></a>Förbereda AKS-klustret

Om du vill förbereda AKS-klustret för dev spaces kontrollerar du att AKS-klustret finns i en region [som stöds av Azure Dev Spaces][supported-regions] och du kör Kubernetes 1.10.3 eller senare. Om du vill aktivera Azure Dev Spaces i klustret från Azure-portalen navigerar du till klustret, klickar på *Utvecklingsutrymmen,* ändrar *Använd utvecklingsutrymmen* till *Ja*och klickar på *Spara*. Du kan också aktivera Azure Dev Spaces `az aks use-dev-spaces`från Azure CLI genom att köra .

Ett exempel på hur du konfigurerar ett AKS-kluster för Dev Spaces finns i [snabbstarten][quickstart-team]för teamutveckling .

När Azure Dev Spaces är aktiverat i AKS-klustret installeras styrenheten för klustret. Styrenheten finns utanför AKS-klustret. Den driver beteendet och kommunikationen mellan verktygen på klientsidan och AKS-klustret. När den är aktiverad kan du interagera med styrenheten med hjälp av klientverktyget.

Styrenheten utför följande åtgärder:

* Hanterar skapande och val av utvecklingsutrymme.
* Installerar programmets Helm-diagram och skapar Kubernetes-objekt.
* Skapar programmets behållaravbildning.
* Distribuerar ditt program till AKS.
* Bygger inkrementellt när källkoden ändras.
* Hanterar loggar och HTTP-spårningar.
* Framåt stdout och stderr till klientsidan verktyg.
* Konfigurerar routning för program inom ett utrymme samt över överordnade och underordnade utrymmen.

Styrenheten är en separat Azure-resurs utanför klustret och gör följande för resurser i klustret:

* Skapar eller anger ett Kubernetes-namnområde som ska användas som dev space.
* Tar bort alla Kubernetes namnområde med namnet *Azds*, om det finns, och skapar ett nytt.
* Distribuerar en Kubernetes webhook-konfiguration.
* Distribuerar en webhook-åtkomstserver.

Den använder samma tjänsthuvudnamn som AKS-klustret använder för att ringa tjänstanrop till andra Azure Dev Spaces-komponenter.

![Azure Dev Spaces förbereder kluster](media/how-dev-spaces-works/prepare-cluster.svg)

För att kunna använda Azure Dev Spaces måste det finnas minst ett dev-utrymme. Azure Dev Spaces använder Kubernetes namnområden i AKS-klustret för utvecklingsutrymmen. När en styrenhet installeras uppmanas du att skapa ett nytt Kubernetes-namnområde eller välja ett befintligt namnområde som ska användas som ditt första dev-utrymme. Som standard erbjuder styrenheten att uppgradera det befintliga *standardnamnområdet* Kubernetes till ditt första dev-utrymme.

När ett namnområde anges som ett utvecklingsutrymme lägger styrenheten till *azds.io/space=true-etiketten* i namnområdet för att identifiera den som ett utvecklingsutrymme. Det ursprungliga utvecklingsutrymme som du skapar eller anger väljs som standard när du har förberett klustret. När ett utrymme är valt används det av Azure Dev Spaces för att skapa nya arbetsbelastningar.

Du kan använda verktyget på klientsidan för att skapa nya utvecklingsutrymmen och ta bort befintliga utvecklingsutrymmen. På grund av en begränsning i Kubernetes kan *standarddest* utrymme inte tas bort. Styrenheten tar också bort alla befintliga Kubernetes-namnområden med namnet `azds` *azds* för att undvika konflikter med kommandot som används av verktyget på klientsidan.

Kubernetes webhook-åtkomstserver används för att injicera poddar med tre behållare under distributionen för instrumentering: en devspaces-proxy-behållare, en devspaces-proxy-init-behållare och en devspaces-build-behållare. **Alla tre av dessa behållare körs med root-åtkomst i AKS-klustret.** De använder också samma tjänsthuvudnamn som AKS-klustret använder för att ringa tjänstanrop till andra Azure Dev Spaces-komponenter.

![Azure Dev Spaces Kubernetes webhook entréserver](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Devspaces-proxy-behållaren är en sidovagnsbehållare som hanterar all TCP-trafik till och från programbehållaren och hjälper till att utföra routning. Behållaren devspaces-proxy omdirigerar HTTP-meddelanden om vissa blanksteg används. Det kan till exempel hjälpa till att dirigera HTTP-meddelanden mellan program i överordnade och underordnade utrymmen. All icke-HTTP-trafik passerar genom devspaces-proxy oförändrad. Behållaren devspaces-proxy loggar också alla inkommande och utgående HTTP-meddelanden och skickar dem till verktyget på klientsidan som spårningar. Dessa spår kan sedan visas av utvecklaren för att granska beteendet för programmet.

Devspaces-proxy-init-behållaren är en [init-behållare](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) som lägger till ytterligare routningsregler baserat på utrymmeshierarkin i programmets behållare. Den lägger till routningsregler genom att uppdatera programbehållarens */etc/resolv.conf-fil-* och iptables-konfiguration innan den startar. Uppdateringarna till */etc/resolv.conf* möjliggör DNS-lösning av tjänster i överordnade utrymmen. Iptables konfigurationsuppdateringar se till att all TCP-trafik till och från programmets behållare dirigeras genom devspaces-proxy. Alla uppdateringar från devspaces-proxy-init sker utöver de regler som Kubernetes lägger till.

Devspaces-build-behållaren är en init-behållare och har projektets källkod och Docker-socket monterad. Projektets källkod och åtkomst till Docker gör att programbehållaren kan byggas direkt av podden.

> [!NOTE]
> Azure Dev Spaces använder samma nod för att skapa programmets behållare och köra den. Därför behöver Azure Dev Spaces inte ett externt behållarregister för att skapa och köra ditt program.

Kubernetes webhook-åtkomstserver lyssnar efter alla nya poddar som har skapats i AKS-klustret. Om podden distribueras till ett namnområde med *etiketten azds.io/space=true* injicerar den podden med de extra behållarna. Behållaren devspaces-build injiceras endast om programmets behållare körs med hjälp av verktyg på klientsidan.

När du har förberett AKS-klustret kan du använda verktygen på klientsidan för att förbereda och köra koden i utvecklingsutrymmet.

## <a name="client-side-tooling"></a>Verktyg på klientsidan

Med verktyget på klientsidan kan användaren:
* Generera en Dockerfile-, Helm-diagram- och Azure Dev Spaces-konfigurationsfil för programmet.
* Skapa överordnade och underordnade utvecklingsutrymmen.
* Tala om för handkontrollen att bygga och starta programmet.

Medan programmet körs, klientsidan verktyg också:
* Tar emot och visar stdout och stderr från ditt program som körs i AKS.
* Använder [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) för att tillåta webbåtkomst\/till ditt program med http: /localhost.
* Kopplar en felsökare till ditt program som körs i AKS.
* Synkroniserar källkoden till ditt utvecklingsutrymme när en ändring identifieras för inkrementella versioner, vilket möjliggör snabb iteration.
* Gör att du kan ansluta utvecklarmaskinen direkt till AKS-klustret.

Du kan använda verktyget på klientsidan från kommandoraden som en del av kommandot. `azds` Du kan också använda verktygen på klientsidan med:

* Visual Studio-kod med azure [dev spaces-tillägget](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio med [Visual Studio-verktyg för Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder verktyg på klientsidan för att förbereda och köra koden i ditt utvecklingsutrymme finns i [Så här fungerar förbereda ett projekt för Azure Dev Spaces][how-it-works-prep].

Information om hur du kommer igång med Azure Dev Spaces för teamutveckling finns [i teamutvecklingen i snabbstarten för Azure Dev Spaces.][quickstart-team]

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md