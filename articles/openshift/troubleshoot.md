---
title: Felsöka Azure Red Hat OpenShift
description: Felsöka och lösa vanliga problem med Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274934"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Felsökning för Azure Red Hat OpenShift

I den hÃ¤r artikeln beskrivs några vanliga problem som uppstått nÃ¤r Microsoft Azure Red Hat OpenShift-kluster skulle hÃ¤r

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Försöker återskapa skapandet av ett misslyckat kluster

Om det misslyckas att skapa `az` ett Azure Red Hat OpenShift-kluster med kommandot CLI fortsätter det att misslyckas att försöka återskapa skapandet.
Används `az openshift delete` för att ta bort det misslyckade klustret och skapa sedan ett helt nytt kluster.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Dold Azure Red Hat OpenShift-klusterresursgrupp

För närvarande `Microsoft.ContainerService/openShiftManagedClusters` är resursen som skapas automatiskt`az openshift create` av Azure CLI ( kommandot) dold i Azure-portalen. Markera **Visa dolda typer** i **gruppvyn Resurs** för att visa resursgruppen.

![Skärmbild av kryssrutan dold text i portalen](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Att skapa ett kluster resulterar i ett fel att ingen registrerad resursprovider hittades

Om du skapar ett kluster `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`resulterar i ett fel som är du en del av förhandsversionen och nu måste [köpa reserverade Azure-datorinstanser](https://aka.ms/openshift/buy) för att använda den allmänt tillgängliga produkten. En reservation minskar dina utgifter genom att betala i förväg för fullständigt hanterade Azure-tjänster. Se [*Vad är Azure-bokningar*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) om du vill veta mer om reservationer och hur de sparar pengar.

## <a name="next-steps"></a>Nästa steg

- Prova [Hjälpcentret](https://help.openshift.com/) för Red Hat OpenShift om du vill ha mer information om felsökning av OpenShift.

- Hitta svar [på vanliga frågor om Azure Red Hat OpenShift](openshift-faq.md).
