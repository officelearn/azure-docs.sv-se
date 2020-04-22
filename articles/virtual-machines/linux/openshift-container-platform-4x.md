---
title: Distribuera OpenShift Container Platform 4.x i Azure
description: Distribuera OpenShift Container Platform 4.x i Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 14af110b5cf50f167d0c4961e26454bc33c6ed7d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759490"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Distribuera OpenShift Container Platform 4.x i Azure

Distribution av OCP 4.2 (OpenShift Container Platform) stöds nu i Azure via IPI-modellen (Installer-Provisioned Infrastructure).  Målsidan för att prova OpenShift 4 är [try.openshift.com](https://try.openshift.com/). Om du vill installera OCP 4.2 i Azure besöker du sidan [Red Hat OpenShift Cluster Manager.](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)  Red Hat-autentiseringsuppgifter krävs för att komma åt den här webbplatsen.


## <a name="notes"></a>Anteckningar 

 - Ett Azure Active Directory (AAD) Service Principal (SP) krävs för att installera och köra OCP 4.x i Azure
     - SP måste beviljas API-behörigheten **application.readWrite.OwnedBy** för Azure Active Directory Graph
     - En AAD-klientadministratör måste bevilja administratörsgodkännande för att api-behörigheten ska börja gälla
     - SP måste beviljas **deltagar-** och **användaråtkomstadministratörsroller** till prenumerationen
 - Installationsmodellen för OCP 4.x skiljer sig från 3.x och det finns inga Azure Resource Manager-mallar tillgängliga för distribution av OCP 4.x i Azure
 - Om problem uppstår under installationsprocessen kontaktar du lämpligt företag (Microsoft eller Red Hat)

| Problembeskrivning | Kontaktpunkt |
|-------------------|---------------|
| Azure-specifika problem (AAD, SP, Azure-prenumeration, etc.)                              | Microsoft |
| OpenShift-specifika problem (installationsfel / fel, Red Hat-prenumeration, etc.) |  Red Hat  |




## <a name="next-steps"></a>Nästa steg

- [Komma igång med OpenShift Container Platform](https://docs.openshift.com)
