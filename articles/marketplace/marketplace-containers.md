---
title: Containers erbjuder publicerings guide för Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera behållare i Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: ffdc6b22209c1d334ac7b75c7079e755a7405154
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823001"
---
# <a name="containers-offer-publishing-guide"></a>Publicerings guide för behållare

Med container får du hjälp att publicera behållar avbildningen på Azure Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Detta är transaktions erbjudanden som distribueras och faktureras via Marketplace. Anropet till åtgärden som en användare ser är "Hämta nu".

Använd containerns erbjudande typ när din lösning är en Docker-behållar avbildning som tillhandahålls som en Kubernetes-baserad Azure Container Service.

>[!NOTE]
>Till exempel en Kubernetes-baserad Azure Container Service som Azure Kubernetes service eller Azure Container Instances, valet av Azure-kunder för en Kubernetes-baserad container Runtime.  

Microsoft har för närvarande stöd för kostnads fria och BYOL-licensierings modeller.

## <a name="containers-offer"></a>Container erbjudande

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Stöder antingen den kostnads fria eller BYOL fakturerings modellen. |  
| Avbildning skapad från Dockerfile | Behållar avbildningar måste baseras på Docker-avbildnings specifikationen och måste skapas från en Dockerfile.<ul> <li>Mer information om hur du skapar Docker-avbildningar finns i avsnittet användning på [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Värd i ACR | Behållar avbildningar måste finnas i en Azure Container Registry-databas (ACR).<ul> <li>Mer information om hur du arbetar med ACR finns i snabb start: skapa ett behållar register med hjälp av Azure Portal sidan som finns på [docs.Microsoft.com/Azure/Container-Registry/container-Registry-get-started-Portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Bild taggning | Behållar avbildningar måste innehålla minst en tagg (maximalt antal Taggar: 16).<ul> <li>Om du vill ha mer information om att tagga en bild kan du gå till Docker-taggnings sidan som finns på [docs.Docker.com/Engine/Reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, 

- [Registrera dig](https://azuremarketplace.microsoft.com/sell) på Marketplace.

Om du har registrerat och skapar ett nytt erbjudande eller arbetar på ett befintligt,

- [Logga in på Cloud Partner Portal](https://cloudpartner.azure.com) för att skapa eller slutföra ditt erbjudande.
- Se [behållare](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) för mer information.
