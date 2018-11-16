---
title: Använda kubectl med Azure Dev blanksteg | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: article
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: b522ab9177d963f65ac6ea75538ddc46331875da
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706797"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Använda kubectl med ett adressutrymme för Azure-utveckling

Du kan komma åt Kubernetes-kluster i ett adressutrymme för utveckling av Azure och Använd befintliga Kubernetes-verktyg som `kubectl`.

Kör `az aks use-dev-spaces` kommandot att automatiskt lägga till en `kubectl` configuration kontext för dig, så kubectl redan är ansluten till din Azure Dev blanksteg Kubernetes-kluster. Exempel:
- Kontrollera den aktuella kontexten: `kubectl config current-context`
- Lista över alla tillgängliga sammanhang: `kubectl config get-contexts`. 
- Kontext: `kubectl config use-context <context-name>`
- Visa instrumentpanelen för Kubernetes: kör `kubectl proxy`, sedan öppna din webbläsare med den adress som det här kommandot genererar (Lägg till `/ui` i URL: en för att navigera till Kubernetes-instrumentpanelen).
- Lista löpande services i området standard Azure Dev blanksteg med namnet *standard*: `kubectl get services --namespace=default`

