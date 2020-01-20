---
title: Felsökning av Azure Red Hat OpenShift
description: Felsöka och lösa vanliga problem med Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274934"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Fel sökning för Azure Red Hat OpenShift

Den här artikeln beskriver några vanliga problem som uppstår när du skapar eller hanterar Microsoft Azure Red Hat OpenShift-kluster.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Försöker skapa ett misslyckat kluster igen

Om du skapar ett Azure Red Hat OpenShift-kluster med kommandot `az` CLI Miss lyckas, kommer försök att Miss lyckas om du försöker skapa igen.
Använd `az openshift delete` för att ta bort det misslyckade klustret och skapa sedan ett helt nytt kluster.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Dolt Azure Red Hat OpenShift-kluster resurs grupp

För närvarande är `Microsoft.ContainerService/openShiftManagedClusters` resursen som skapas automatiskt av Azure CLI (`az openshift create` kommandot) dold i Azure Portal. I vyn **resurs grupp** markerar du **Visa dolda typer** för att Visa resurs gruppen.

![Skärm bild av kryss rutan dold typ i portalen](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Att skapa ett kluster resulterar i ett fel som inte gick att hitta någon registrerad Resource Provider

Om du skapar ett kluster resulterar det i ett fel som `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`. därefter är du en del av förhands granskningen och måste nu [köpa reserverade instanser för virtuella Azure-datorer](https://aka.ms/openshift/buy) för att kunna använda den allmänt tillgängliga produkten. En reservation minskar dina utgifter genom förskotts betalning för fullständigt hanterade Azure-tjänster. Se [*vad som är Azure reservations*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) för att lära dig mer om reservationer och hur de sparar pengar.

## <a name="next-steps"></a>Nästa steg

- Testa [Hjälp Center med Red Hat OpenShift](https://help.openshift.com/) för mer information om OpenShift-felsökning.

- Få svar på [vanliga frågor om Azure Red Hat OpenShift](openshift-faq.md).
