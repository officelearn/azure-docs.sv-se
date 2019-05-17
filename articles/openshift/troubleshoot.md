---
title: Felsöka Azure Red Hat OpenShift | Microsoft Docs
description: Felsöka och lösa vanliga problem med Azure Red Hat OpenShift
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 9ef32273c007e7319b7f6df99f3d904f805c31f2
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550915"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Felsökning för Azure Red Hat OpenShift

Den här artikeln beskriver några vanliga problem som uppstod när du skapar eller hanterar Microsoft Azure Red Hat OpenShift kluster.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Ett nytt försök görs skapandet av en misslyckad kluster

Om hur du skapar en Azure Red Hat OpenShift klustret med hjälp av den `az` CLI-kommando misslyckas, försöker skapa fortsätter att misslyckas.
Använd `az openshift delete` om du vill ta bort det misslyckade klustret, sedan skapa ett helt nytt kluster.

## <a name="untrusted-azure-red-hat-openshift-server-certificate"></a>Ej betrodda Azure Red Hat OpenShift-servercertifikat

OpenShift-konsolen använder ett självsignerat certifikat. När du uppmanas till detta i din webbläsare kringgå varningen och godkänna ”ej betrodd” certifikatet.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Dolda Azure Red Hat OpenShift klusterresursgrupp

För närvarande den `Microsoft.ContainerService/openShiftManagedClusters` resurs som skapas automatiskt av Azure CLI (`az openshift create` kommandot) är dold i Azure-portalen. I den **resursgrupp** vyn, kontrollera **Visa dolda typer** att visa resursgruppen.

![Skärmbild av kryssrutan dolda typ i portalen](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Skapa ett kluster resultat i fel som ingen registrerad resursprovider hittades

Om du skapar en kluster-resultaten i ett fel som `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, du har en del av förhandsversionen och nu behöver [köp Azure-dator reserverade instanser](https://aka.ms/openshift/buy) att använda den allmänt tillgängliga produkten. En reservation minskar dina utgifter per förväg betala för fullständigt hanterade Azure-tjänster. Referera till [ *vad är Azure reservationer* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) mer information om reservationer och hur de spara pengar.

## <a name="next-steps"></a>Nästa steg

- Prova den [Hjälpcenter för Red Hat OpenShift](https://help.openshift.com/) mer om OpenShift felsökningsinformation.

- Få svar på [vanliga frågor och svar om Azure Red Hat OpenShift](openshift-faq.md).