---
title: Felsöka Azure Red Hat OpenShift
description: Felsöka och lösa vanliga problem med Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 55360ef295ff80b700b059d053203458f9f384db
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469090"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Fel sökning för Azure Red Hat OpenShift

Den här artikeln beskriver några vanliga problem som uppstår när du skapar eller hanterar Microsoft Azure Red Hat OpenShift-kluster.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Försöker skapa ett misslyckat kluster igen

Om du skapar ett Azure Red Hat OpenShift-kluster med hjälp av `az` CLI-kommandot Miss lyckas återförsöket att försöka skapa fortsätter.
Använd `az openshift delete` för att ta bort det misslyckade klustret och skapa sedan ett helt nytt kluster.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Dolt Azure Red Hat OpenShift-kluster resurs grupp

För närvarande `Microsoft.ContainerService/openShiftManagedClusters` är resursen som skapas automatiskt av Azure CLI ( `az openshift create` kommandot) dold i Azure Portal. I vyn **resurs grupp** markerar du **Visa dolda typer** för att Visa resurs gruppen.

![Skärm bild av kryss rutan dold typ i portalen](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Att skapa ett kluster resulterar i ett fel som inte gick att hitta någon registrerad Resource Provider

Om du skapar ett kluster resulterar det i ett fel som `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` , sedan var du en del av förhands granskningen och måste nu [köpa reserverade instanser för virtuella Azure-datorer](https://aka.ms/openshift/buy) för att kunna använda den allmänt tillgängliga produkten. En reservation minskar dina utgifter genom förskotts betalning för fullständigt hanterade Azure-tjänster. Se [*vad som är Azure reservations*](../cost-management-billing/reservations/save-compute-costs-reservations.md) för att lära dig mer om reservationer och hur de sparar pengar.

## <a name="next-steps"></a>Nästa steg

- Testa [Hjälp Center med Red Hat OpenShift](https://help.openshift.com/) för mer information om OpenShift-felsökning.

- Få svar på [vanliga frågor om Azure Red Hat OpenShift](openshift-faq.md).