---
title: Introduktion till Azure Red Hat OpenShift | Microsoft Docs
description: Läs om funktioner och fördelar med Microsoft Azure Red Hat OpenShift att distribuera och hantera behållarbaserade program.
services: container-service
author: tylermsft
ms.author: twhitney
ms.service: container-service
manager: jeconnoc
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: bfa3e63a433a6948f05ece7ec3c3a6180933510b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466171"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsofts *Azure Red Hat OpenShift* service kan du distribuera en fullständigt hanterad [OpenShift](https://www.openshift.com/) kluster.

Azure Red Hat OpenShift utökar [Kubernetes](https://kubernetes.io/). Köra behållare i produktion med Kubernetes kräver ytterligare verktyg och resurser, till exempel ett avbildningsregister, lagringshantering, networking lösningar och loggning och övervakningsverktyg, som måste vara versionsnummer och testade tillsammans. Att skapa behållarbaserade program kräver även mer arbete för integrering med mellanprogram, ramverk, databaser och CI/CD-verktyg. Azure Red Hat OpenShift kombinerar alla detta till en enda plattform, föra enkelt i arbetet med att IT-team samtidigt som programmet team vad de behöver för att köra.

Azure Red Hat OpenShift är gemensamt utformat, drivs och stöds av Red Hat och Microsoft för att tillhandahålla en integrerad supportupplevelse. Det finns inga virtuella datorer att fungera och utan uppdatering krävs. Master, infrastruktur och program är uppdaterad, uppdateras och övervakas för din räkning av Red Hat och Microsoft. Azure Red Hat OpenShift-kluster distribueras i din Azure-prenumeration och finns på Azure-fakturan.

Du kan välja egna registret, nätverk, lagring, och CI/CD-lösningar, eller Använd inbyggda lösningar för automatiserade källa kod hanterings-, behållare och bygger, distributioner, skalning, hantering av hälsotillstånd och mer. Azure Red Hat OpenShift ger en integrerad inloggning via Azure Active Directory.

Kom igång genom att slutföra den [skapa ett kluster i Azure Red Hat OpenShift](tutorial-create-cluster.md) självstudien.

## <a name="access-security-and-monitoring"></a>Åtkomst, säkerhet och övervakning

För förbättrad säkerhet och hantering kan Azure Red Hat OpenShift du integrera med Azure Active Directory (Azure AD) och använda Kubernetes rollbaserad åtkomstkontroll (RBAC). Du kan också övervaka hälsotillståndet för ditt kluster och dina resurser.

## <a name="cluster-and-node"></a>Kluster och nod

Azure Red Hat OpenShift-noder kör på Azure virtual machines. Du kan ansluta lagringen till noder och poddar, uppgradera klusterkomponenter och använda GPU:er.

## <a name="virtual-networks-and-ingress"></a>Virtuella nätverk och ingress

Du kan ansluta en Azure Red Hat OpenShift-kluster till ett befintligt virtuellt nätverk via peering. I den här konfigurationen poddar kan ansluta till andra tjänster i ett peer-kopplade virtuella nätverk och lokala nätverk via [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) eller plats-till-plats (S2S) VPN-anslutningar.

Se [ansluta virtuella nätverk i ett kluster till ett befintligt virtuellt nätverk](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) mer information.

## <a name="kubernetes-certification"></a>Kubernetes-certifiering

Azure Red Hat OpenShift-tjänsten har varit CNCF som certifierats för Kubernetes-kompatibel.

## <a name="next-steps"></a>Nästa steg

Lär dig de nödvändiga förutsättningarna för Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Konfigurera din utvecklingsmiljö](howto-setup-environment.md)