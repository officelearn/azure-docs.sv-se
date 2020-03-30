---
title: Distribuera OpenShift Container Platform 4.x i Azure
description: Distribuera OpenShift Container Platform 4.x i Azure.
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
ms.openlocfilehash: 213c02b76f822d134729ebc4c0e6bff40f62089f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035442"
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
