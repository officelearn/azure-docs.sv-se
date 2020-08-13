---
title: Få åtkomst till Kubernetes-resurser från Azure Portal (för hands version)
description: Lär dig hur du interagerar med Kubernetes-resurser för att hantera ett Azure Kubernetes service (AKS)-kluster från Azure Portal.
services: container-service
author: laurenhughes
ms.topic: article
ms.date: 08/11/2020
ms.author: lahugh
ms.openlocfilehash: 109192efa19605af003dcfb30592c865ce7495b5
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136990"
---
# <a name="access-kubernetes-resources-from-the-azure-portal-preview"></a>Få åtkomst till Kubernetes-resurser från Azure Portal (för hands version)

Azure Portal innehåller en Kubernetes Resource Viewer (för hands version) för enkel åtkomst till Kubernetes-resurserna i ditt Azure Kubernetes service-kluster (AKS). Genom att Visa Kubernetes-resurser från Azure Portal reduceras kontext växlingen mellan Azure Portal och `kubectl` kommando rads verktyget, vilket effektiviserar upplevelsen med att visa och redigera dina Kubernetes-resurser. Resurs läsaren innehåller för närvarande flera resurs typer, till exempel distributioner, poddar och replik uppsättningar.

Kubernetes från Azure Portal ersätter [instrument panels tillägget AKS][kubernetes-dashboard], som är inställt för utfasning.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill visa Kubernetes-resurser i Azure Portal behöver du ett AKS-kluster. Alla kluster stöds, men om du använder Azure Active Directory (Azure AD)-integration måste klustret använda [AKS-hanterad Azure AD-integrering][aks-managed-aad]. Om klustret använder äldre Azure AD kan du uppgradera klustret i portalen eller med [Azure CLI][cli-aad-upgrade].

## <a name="view-kubernetes-resources"></a>Visa Kubernetes-resurser

Om du vill se Kubernetes-resurserna navigerar du till ditt AKS-kluster i Azure Portal. Navigerings fönstret till vänster används för att komma åt dina resurser. Resurserna omfattar:

- **Namn områden** visar klustrets namn områden. Filtret överst i namn områdes listan är ett snabbt sätt att filtrera och Visa dina namn områdes resurser.
- **Arbets belastningar** visar information om distributioner, poddar, replik uppsättningar och daemon-uppsättningar som distribuerats till klustret. Skärm bilden nedan visar standard systemet poddar i ett exempel AKS-kluster.
- **Tjänster och** ingångar visar alla kluster tjänster och ingresss resurser.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Kubernetes Pod-information som visas i Azure Portal." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>Distribuera ett program

I det här exemplet ska vi använda vårt exempel på AKS-kluster för att distribuera Azures röst program från snabb starten för [AKS][portal-quickstart].

1. Välj **Lägg till** från någon av resursvyer (namnrymd, arbets belastningar eller tjänster och inträngande).
1. Klistra in YAML för Azures röst program från [snabb start för AKS][portal-quickstart].
1. Klicka på **Lägg till** längst ned i yaml-redigeraren för att distribuera programmet. 

När YAML-filen har lagts till visar Resource Viewer både Kubernetes-tjänster som skapades: den interna tjänsten (Azure-rösta) och den externa tjänsten (Azure-rösta-Front) för att få åtkomst till Azures röst program. Den externa tjänsten innehåller en länkad extern IP-adress så att du enkelt kan se programmet i din webbläsare.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Information om Azure röstnings program som visas i Azure Portal." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Övervaka distributions Insights

AKS-kluster med [Azure Monitor för behållare][enable-monitor] aktiverade kan snabbt Visa distributions insikter. I vyn Kubernetes-resurser kan användare se Live-status för enskilda distributioner, inklusive processor-och minnes användning, samt över gång till Azure Monitor för mer detaljerad information. Här är ett exempel på distributions insikter från ett exempel på AKS-kluster:

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Distributions insikter som visas i Azure Portal." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>Redigera YAML

Vyn Kubernetes innehåller även en YAML-redigerare. En inbyggd YAML-redigerare innebär att du kan uppdatera eller skapa tjänster och distributioner inifrån portalen och tillämpa ändringar direkt.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="YAML-redigeraren för en Kubernetes-tjänst visas i Azure Portal.":::

När du har redigerat YAML tillämpas ändringarna genom att välja **Granska + Spara**, bekräfta ändringarna och sedan Spara igen.

>[!WARNING]
> Att utföra direkta produktions ändringar via användar gränssnittet eller CLI rekommenderas inte. du bör utnyttja [bästa metoder för kontinuerlig integrering (CI) och kontinuerlig distribution (CD)](kubernetes-action.md). Hanterings funktionerna i Azure Portal-Kubernetes och YAML-redigeraren är utformade för att lära sig och sätta igång nya distributioner i en utvecklings-och testnings inställning.

## <a name="troubleshooting"></a>Felsökning

Det här avsnittet behandlar vanliga problem och fel söknings steg.

### <a name="unauthorized-access"></a>Obehörig åtkomst

För att få åtkomst till Kubernetes-resurserna måste du ha åtkomst till AKS-klustret, Kubernetes-API: et och Kubernetes-objekten. Se till att du är antingen en kluster administratör eller en användare med rätt behörighet för att komma åt AKS-klustret. Mer information om kluster säkerhet finns i [åtkomst-och identitets alternativ för AKS][concepts-identity].

### <a name="enable-resource-view"></a>Aktivera resursvy

För befintliga kluster kan du behöva aktivera resurs visningen Kubernetes. Om du vill aktivera resursvyn följer du anvisningarna i portalen för klustret.

:::image type="content" source="media/kubernetes-portal/enable-resource-view.png" alt-text="Azure Portal-meddelande om du vill aktivera resursvyn för Kubernetes." lightbox="media/kubernetes-portal/enable-resource-view.png":::

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar hur du kommer åt Kubernetes-resurser för ditt AKS-kluster. Se [distributioner och yaml-manifest][deployments] för att få en djupare förståelse för kluster resurser och yaml-filer som nås med Kubernetes Resource Viewer.

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/insights/container-insights-enable-existing-clusters.md
