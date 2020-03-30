---
title: Introduktion till Azure Red Hat OpenShift
description: Lär dig vilka funktioner och fördelar med Microsoft Azure Red Hat OpenShift för att distribuera och hantera behållarbaserade program.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 4be388eec3851154fd0f6af37d03a468fc52197b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76276060"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Med tjänsten Microsoft *Azure Red Hat OpenShift* kan du distribuera fullständigt hanterade [OpenShift-kluster.](https://www.openshift.com/)

Azure Red Hat OpenShift utökar [Kubernetes](https://kubernetes.io/). Att köra behållare i produktion med Kubernetes kräver ytterligare verktyg och resurser, till exempel ett avbildningsregister, lagringshantering, nätverkslösningar och loggnings- och övervakningsverktyg, som alla måste versionsas och testas tillsammans. Att skapa behållarbaserade program kräver ännu mer integrationsarbete med mellanprogram, ramverk, databaser och CI/CD-verktyg. Azure Red Hat OpenShift kombinerar allt detta till en enda plattform, vilket ger enkel drift till IT-team samtidigt som programteamen får vad de behöver för att utföra.

Azure Red Hat OpenShift är gemensamt konstruerad, drivs och stöds av Red Hat och Microsoft för att ge en integrerad supportupplevelse. Det finns inga virtuella datorer att använda och ingen korrigering krävs. Huvud-, infrastruktur- och programnoder korrigeras, uppdateras och övervakas för din räkning av Red Hat och Microsoft. Dina Azure Red Hat OpenShift-kluster distribueras till din Azure-prenumeration och ingår i din Azure-faktura.

Du kan välja egna lösningar för register, nätverk, lagring och CI/CD, eller använda de inbyggda lösningarna för automatiserad källkodshantering, container- och programbyggen, distributioner, skalning, hälsohantering med mera. Azure Red Hat OpenShift ger en integrerad inloggningsupplevelse via Azure Active Directory.

För att komma igång, slutföra [create an Azure Red Hat OpenShift cluster](tutorial-create-cluster.md) tutorial.

## <a name="access-security-and-monitoring"></a>Åtkomst, säkerhet och övervakning

För förbättrad säkerhet och hantering kan du med Azure Active Directory (Azure Active Directory) integreras med Azure Active Directory (Azure AD) och använda Kubernetes rollbaserad åtkomstkontroll (RBAC). Du kan också övervaka hälsotillståndet för ditt kluster och dina resurser.

## <a name="cluster-and-node"></a>Kluster och nod

Azure Red Hat OpenShift-noder körs på virtuella Azure-datorer. Du kan ansluta lagringen till noder och poddar, uppgradera klusterkomponenter och använda GPU:er.

## <a name="virtual-networks-and-ingress"></a>Virtuella nätverk och ingress

Du kan [ansluta ett Azure Red Hat OpenShift-kluster till ett befintligt virtuellt nätverk](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) via peering. I den här konfigurationen kan poddar ansluta till andra tjänster i ett peer-virtuellt nätverk.

Mer information finns i [Ansluta ett klusters virtuella nätverk till ett befintligt virtuellt nätverk.](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network)

## <a name="kubernetes-certification"></a>Kubernetes-certifiering

Azure Red Hat OpenShift-tjänsten har CNCF-certifierats som Kubernetes conformant.

## <a name="next-steps"></a>Nästa steg

Lär dig förutsättningarna för Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Konfigurera din utvecklingsmiljö](howto-setup-environment.md)
