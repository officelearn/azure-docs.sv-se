---
title: Distribuera OpenShift container Platform 4. x i Azure
description: Distribuera OpenShift container Platform 4. x i Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f2fb1657c26fce3e1fdd67f36b0c6511b78dd216
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373546"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Distribuera OpenShift container Platform 4. x i Azure

Distribution av OpenShift container Platform (OCP) 4,2 stöds nu i Azure via en IPI-modell (Installer-etablerad infrastruktur).  Landnings sidan för att testa OpenShift 4 är [try.OpenShift.com](https://try.openshift.com/). Om du vill installera OCP 4,2 i Azure går du till sidan [Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  Det krävs autentiseringsuppgifter för Red Hat för att få åtkomst till den här webbplatsen.


## <a name="notes"></a>Obs! 

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
