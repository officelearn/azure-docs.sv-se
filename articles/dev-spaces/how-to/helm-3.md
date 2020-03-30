---
title: Konfigurera azure dev spaces-klustret så att det använder Helm 3 (förhandsversion)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Lär dig hur du konfigurerar ditt Dev Spaces-kluster för att använda Helm 3
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454303"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Konfigurera azure dev spaces-klustret så att det använder Helm 3 (förhandsversion)

Azure Dev Spaces använder Helm 2 som standard för att installera användartjänster i utvecklingsutrymmen i AKS-klustret. Du kan aktivera Azure Dev Spaces för att använda Helm 3 i stället för att Helm 2 installerar användartjänster i utvecklingsutrymmen. Oavsett vilken version av Helm Azure Dev Spaces som används för att installera användartjänster kan du fortsätta att använda Helm 2- eller 3-klienten för att hantera dina egna versioner i samma kluster.

När du aktiverar Helm 3 fungerar Azure Dev Spaces annorlunda när du installerar användartjänster i utvecklingsutrymmen på följande sätt:

* Tiller distribueras inte längre till klustret i *azds-namnområdet.*
* Helm lagrar utgivningsinformation i namnområdet där en tjänst installeras i stället för *azds-namnområdet.*
* Helm 3-utgivningsinformation finns kvar i namnområdet där en tjänst installeras när en styrenhet har tagits bort.
* Du kan interagera direkt med valfri version som hanteras av Azure Dev Spaces i klustret med Helm 3-klienten.

I den här guiden får du lära dig hur du aktiverar Helm 3 för Azure Dev Spaces för att installera användartjänster i utvecklingsutrymmen.

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="before-you-begin"></a>Innan du börjar

### <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat][azure-cli].

### <a name="register-the-helm3preview-preview-feature"></a>Registrera förhandsgranskningsfunktionen Helm3Preview

Om du vill att Azure Dev Spaces ska kunna använda Helm 3 för att installera användartjänster i utvecklingsutrymmen aktiverar du först flaggan *Helm3Preview-funktionen* på din prenumeration med kommandot *az-funktionsregister:*

> [!WARNING]
> Alla AKS-kluster som du aktiverar Azure Dev Spaces med *flaggan Helm3Preview* använder den här förhandsversionen. Om du vill fortsätta att aktivera Azure Dev Spaces som stöds fullt ut i AKS-kluster aktiverar du inte förhandsversionsfunktioner för produktionsprenumerationer. Använd en separat Azure-prenumeration för testning av förhandsversioner av test eller utveckling för att testa förhandsversionsfunktioner.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Det tar några minuter innan registreringen är klar. Kontrollera registreringsstatus med kommandot *az feature show:*

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

När *tillståndet* är *registrerat*uppdaterar du registreringen av *Microsoft.DevSpaces* med *az-providerregistret:*

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när den här funktionen är i förhandsgranskningen:

* Du kan inte använda den här funktionen på AKS-kluster med befintliga arbetsbelastningar. Du måste skapa ett nytt AKS-kluster.

## <a name="create-your-cluster"></a>Skapa ditt kluster

Skapa ett nytt AKS-kluster i en region som har den här förhandsgranskningsfunktionen. Kommandona nedan skapar en resursgrupp med namnet *MyResourceGroup* och ett nytt AKS-kluster med namnet *MyAKS:*

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Aktivera Azure Dev Spaces

Använd kommandot *använd dev-spaces* för att aktivera dev spaces i AKS-klustret och följa anvisningarna. Kommandot nedan aktiverar Dev Spaces i *MyAKS-klustret* i gruppen MyResourceGroup och skapar ett standardutrymme för dev.The below command enables Dev Spaces on the MyAKS cluster in the *MyResourceGroup group* and creates a default dev space.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Kontrollera dev spaces kör Helm 3

Kontrollera att rorkulten inte körs genom att lista distributionerna i *azds-namnområdet:*

```cmd
kubectl get deployment -n azds
```

Bekräfta *att tiller-deploy* inte körs i azds-namnområdet. Ett exempel:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure Dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbete genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Teamutveckling i Azure Dev Spaces][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md