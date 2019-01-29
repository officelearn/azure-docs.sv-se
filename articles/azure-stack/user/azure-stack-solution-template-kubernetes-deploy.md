---
title: Distribuera Kubernetes till Azure Stack | Microsoft Docs
description: Lär dig hur du distribuerar Kubernetes i Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 08bebd8165bf57e3b493578ed782624714c57ab0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190283"
---
# <a name="deploy-kubernetes-to-azure-stack"></a>Distribuera Kubernetes till Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

> [!Note]  
> Kubernetes på Azure Stack är en förhandsversion.

Du kan följa stegen i den här artikeln för att distribuera och konfigurera resurser för Kubernetes i en enda, samordnad åtgärd. Stegen använder en lösningsmall för Azure Resource Manager. Du behöver för att samla in nödvändig information om installationen av Azure Stack, generera mallen och sedan distribuera till molnet. Azure Stack-mallen använder inte samma hanterade AKS-tjänsten erbjuds i globala Azure.

## <a name="kubernetes-and-containers"></a>Kubernetes och behållare

Du kan installera Kubernetes med Azure Resource Manager-mallar som genererats av ACS-Engine på Azure Stack. [Kubernetes](https://kubernetes.io) är ett system med öppen källkod för att automatisera distribution, skalning och hantering av program i behållare. En [behållare](https://www.docker.com/what-container) är i en bild. Behållaravbildningen liknar en virtuell dator, men till skillnad från en virtuell dator, behållaren innehåller endast de resurser den behöver för att köra ett program, till exempel kod, runtime för att köra koden, specifika bibliotek och inställningar.

Du kan använda Kubernetes:

- Utveckla mycket skalbar och kan uppgraderas, program som kan distribueras på några sekunder. 
- Förenkla utformningen av programmet och förbättra tillförlitligheten genom olika Helm-program. [Helm](https://github.com/kubernetes/helm) är ett verktyg med öppen källkod paketering som hjälper dig att installera och hantera livscykeln för Kubernetes-program.
- Enkelt övervaka och diagnostisera hälsotillståndet för dina program med skala och uppgradera funktioner.

Du debiteras endast för beräkning användningen enligt de noder som har stöd för ditt kluster. Mer information finns i [användning och fakturering i Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback).

## <a name="deploy-kubernetes"></a>Distribuera Kubernetes

Stegen för att distribuera ett Kubernetes-kluster på Azure Stack beror på identity management-tjänsten. Kontrollera identitetshanteringslösning som används av din installation av Azure Stack. Kontakta Azure Stack-administratören för att verifiera identity management-tjänsten.

- **Azure Active Directory (Azure AD)**  
Anvisningar om hur du installerar klustret när du använder Azure AD finns i [distribuera Kubernetes i Azure Stack med Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md).

- **Active Directory Federation Services (AD FS)**  
Anvisningar om hur du installerar klustret när du använder AD FS finns i [distribuera Kubernetes i Azure Stack med hjälp av Active Directory Federation Services (AD FS)](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Ansluta till ditt kluster

Nu är du redo att ansluta till klustret. Huvudservern finns i klusterresursgruppen och har namnet `k8s-master-<sequence-of-numbers>`. Använda en SSH-klient för att ansluta till huvuddatabasen. Du kan använda i bakgrunden **kubectl**, Kubernetes kommandoradsklient att hantera kluster. Anvisningar finns i [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Du kan också hitta den **Helm** pakethanterare som är användbara för att installera och distribuera appar till ditt kluster. Anvisningar för hur du installerar och använder Helm med ditt kluster finns i [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Nästa steg

[Lägga till ett Kubernetes i Marketplace (för Azure Stack-operatör)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Distribuera Kubernetes i Azure Stack med Azure Active Directory (AD Azure)](azure-stack-solution-template-kubernetes-azuread.md)

[Distribuera Kubernetes i Azure Stack med hjälp av Active Directory Federation Services (AD FS)](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes på Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
