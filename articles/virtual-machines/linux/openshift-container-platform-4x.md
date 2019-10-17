---
title: Distribuera OpenShift container Platform 4. x i Azure | Microsoft Docs
description: Distribuera OpenShift container Platform 4. x i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 9f807823b1d0d8079c43b2ba0e074a1c8a91b458
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392688"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Distribuera OpenShift container Platform 4. x i Azure

Distribution av OpenShift container Platform (OCP) 4,2 stöds nu i Azure via en IPI-modell (Installer-etablerad infrastruktur).  Landnings sidan för att testa OpenShift 4 är [try.OpenShift.com](https://try.openshift.com/). Om du vill installera OCP 4,2 i Azure går du till sidan [Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  Det krävs autentiseringsuppgifter för Red Hat för att få åtkomst till den här webbplatsen.


## <a name="notes"></a>Anteckningar 

 - Ett Azure Active Directory (AAD) tjänstens huvud namn (SP) krävs för att installera och köra OCP 4. x i Azure
     - SP måste beviljas API-behörighet för **Application. readwrite. OwnedBy** för Azure Active Directory Graph
     - En AAD-innehavaradministratör måste bevilja administrativt medgivande för att denna API-behörighet ska börja gälla
     - SP måste beviljas **deltagare** och **Administratörs roller för användar åtkomst** till prenumerationen
 - Installations modellen för OCP 4. x skiljer sig från 3. x och det finns inga Azure Resource Manager mallar som är tillgängliga för distribution av OCP 4. x i Azure
 - Om problem påträffas under installations processen, kontakta lämpligt företag (Microsoft eller Red Hat)

| Problembeskrivning | Kontakt punkt |
|-------------------|---------------|
| Specifika Azure-problem (AAD, SP, Azure-prenumeration osv.)                              | Microsoft |
| OpenShift-specifika problem (installations fel/fel, Red Hat-prenumeration osv.) |  Red Hat  |




## <a name="next-steps"></a>Nästa steg

- [Kom igång med OpenShift container Platform](https://docs.openshift.com)
