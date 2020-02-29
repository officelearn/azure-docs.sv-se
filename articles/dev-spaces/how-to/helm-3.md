---
title: Konfigurera ditt Azure dev Spaces-kluster för att använda Helm 3 (för hands version)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Lär dig hur du konfigurerar dina dev Spaces-kluster för att använda Helm 3
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 9e3f3ff90d36215c386bf1d8b8ec1edd54ebfb6a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202265"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Konfigurera ditt Azure dev Spaces-kluster för att använda Helm 3 (för hands version)

Azure dev Spaces använder Helm 2 som standard för att installera användar tjänster i dev Spaces i ditt AKS-kluster. Du kan aktivera Azure dev Spaces för att använda Helm 3 i stället för Helm 2 och installera användar tjänster i dev Spaces. Oavsett vilken version av Helm Azure dev Spaces använder för att installera användar tjänster kan du fortsätta att använda Helm 2-eller 3-klienten för att hantera dina egna versioner i samma kluster.

När du aktiverar Helm 3 fungerar Azure dev Spaces annorlunda när du installerar användar tjänster i dev Spaces på följande sätt:

* En till-dator distribueras inte längre till klustret i namn området *azds* .
* Helm lagrar information om utgåvor i namn området där en tjänst installeras i stället för *azds* -namnrymden.
* Helm 3 versions information finns kvar i namn området där en tjänst installeras efter att en kontroll enhet har tagits bort.
* Du kan direkt interagera med alla versioner som hanteras av Azure dev Spaces i klustret med hjälp av Helm 3-klienten.

I den här guiden får du lära dig hur du aktiverar Helm 3 för Azure dev Spaces för att installera användar tjänster i dev-utrymmen.

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="before-you-begin"></a>Innan du börjar

### <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat][azure-cli].

### <a name="register-the-helm3preview-preview-feature"></a>Registrera funktionen för förhands granskning av Helm3Preview

Om du vill aktivera Azure dev Spaces för att använda Helm 3 för att installera användar tjänster i dev-utrymmen, aktiverar du först funktionen *Helm3Preview* i din prenumeration med kommandot *AZ funktions register* :

> [!WARNING]
> Alla AKS-kluster som du aktiverar för Azure dev Spaces med funktions flaggan *Helm3Preview* kommer att använda den här för hands versionen. Aktivera inte för hands versions funktioner för produktions prenumerationer om du vill fortsätta att aktivera fullt stöd för Azure dev Spaces i AKS-kluster. Använd en separat test-eller utvecklings-Azure-prenumeration för att testa för hands versions funktioner.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Det tar några minuter för registreringen att slutföras. Kontrol lera registrerings status med kommandot *AZ-funktionen show* :

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

När ett *tillstånd* har *registrerats*uppdaterar du registreringen av *Microsoft. DevSpaces* med *AZ Provider register*:

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när den här funktionen är i för hands version:

* Du kan inte använda den här funktionen på AKS-kluster med befintliga arbets belastningar. Du måste skapa ett nytt AKS-kluster.

## <a name="create-your-cluster"></a>Skapa ditt kluster

Skapa ett nytt AKS-kluster i en region som har denna förhands gransknings funktion. Kommandona nedan skapar en resurs grupp med namnet *MyResourceGroup* och ett nytt AKS-kluster med namnet *MyAKS* i regionen *södra centrala USA* :

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Aktivera Azure dev Spaces

Använd kommandot *use-dev-Spaces* för att aktivera dev Spaces i ditt AKS-kluster och följ anvisningarna. Kommandot nedan aktiverar dev-utrymmen i *MyAKS* -klustret i gruppen *MyResourceGroup* och skapar ett standard dev-utrymme.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Kontrol lera att dev Spaces körs Helm 3

Kontrol lera att till gång inte körs genom att lista distributionerna i *azds* -namnrymden:

```cmd
kubectl get deployment -n azds
```

Bekräfta *till gång-Deploy* körs inte i namn området azds. Några exempel:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbets utveckling genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Grupp utveckling i Azure dev Spaces][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md