---
title: Behållare erbjuder publiceringsguide för Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera behållare på Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 80bcf0d5d37a44cee2aab38161abac619542204a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684327"
---
# <a name="containers-offer-publishing-guide"></a>Publiceringsguide för behållare erbjuder

Container erbjuder hjälper dig att publicera din behållaravbildning till Azure Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Det här är transaktionserbjudanden som distribueras och faktureras via Marketplace. Uppmaningen som en användare ser är "Hämta den nu".

Använd erbjudandetypen Behållare när din lösning är en Docker-behållaravbildning som etablerats som en Kubernetes-baserad Azure-behållartjänst.

>[!NOTE]
>Till exempel en Kubernetes-baserad Azure-behållartjänst som Azure Kubernetes Service eller Azure Container Instances, valet av Azure-kunder för en Kubernetes-baserad behållarkörning.  

Microsoft stöder för närvarande gratis och bring-your-own-license (BYOL) licensieringsmodeller.

## <a name="containers-offer"></a>Behållare Erbjudande

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Stöd antingen den kostnadsfria faktureringsmodellen eller BYOL-faktureringsmodellen. |  
| Bild byggd från Dockerfile | Behållaravbildningar måste baseras på Docker-avbildningsspecifikationen och måste byggas från en Dockerfile.<ul> <li>Mer information om hur du skapar docker-bilder finns i avsnittet Användning som finns på [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hosting i ACR | Behållaravbildningar måste finnas i en ACR-databas (Azure Container Registry).<ul> <li>Mer information om hur du arbetar med ACR finns i Snabbstart: Skapa ett behållarregister med hjälp av Azure-portalsidan som finns på [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Bildtaggning | Behållaravbildningar måste innehålla minst 1 tagg (maxtaggar: 16).<ul> <li>Mer information om hur du taggar en bild finns på dockertaggsidan som finns på [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, 

- [Läs mer](https://azuremarketplace.microsoft.com/sell) om marknadsplatsen.

Om du vill registrera dig i Partner center börjar du skapa ett nytt erbjudande eller arbetar på ett befintligt:

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra erbjudandet.
- Mer information [finns i Skapa ett Azure-behållarerbjudande.](./partner-center-portal/create-azure-container-offer.md)
