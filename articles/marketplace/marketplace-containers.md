---
title: Publicerings guide för container erbjudanden i Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera behållar erbjudanden i Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 99aecee930e5d77302ad54babd927588519e33fd
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160468"
---
# <a name="publishing-guide-for-container-offers"></a>Publicerings guide för container erbjudanden

Med container får du hjälp att publicera behållar avbildningen på Azure Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Container erbjudanden är transaktions erbjudanden som distribueras och faktureras via Azure Marketplace. Anropet till åtgärden som en användare ser är "Hämta nu".

Använd *behållarens* erbjudande typ när din lösning är en Docker-behållar avbildning som har kon figurer ATS som en Kubernetes-baserad Azure Container Service-instans. 

> [!NOTE]
> Exempel på Kubernetes-baserade Azure Container Service-instanser är Azure Kubernetes service eller Azure Container Instances, valet av Azure-kunder för en Kubernetes-baserad container körning.  

Microsoft har för närvarande stöd för kostnads fria och BYOL-licensierings modeller.

## <a name="container-offer-requirements"></a>Krav för container-erbjudande

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Stöder antingen den kostnads fria eller BYOL fakturerings modellen.<br><br> |  
| Avbildning som skapats från en Dockerfile | Behållar avbildningar måste baseras på Docker-avbildnings specifikationen och skapas från en Dockerfile.<br> <br>Mer information om hur du skapar Docker-avbildningar finns i avsnittet "användning" i [Dockerfile-referensen](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Vara värd för en Azure Container Registry-lagringsplats | Behållar avbildningar måste finnas i en Azure Container Registry-lagringsplats.<br> <br>Mer information om hur du arbetar med Azure Container Registry finns i [snabb start: skapa ett privat behållar register med hjälp av Azure Portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).<br><br> |  
| Bildtaggning | Behållar avbildningar måste innehålla minst en tagg (maximalt antal Taggar: 16).<br><br>Mer information om att tagga en bild finns på `docker tag` sidan på [Docker-dokumentations](https://docs.docker.com/engine/reference/commandline/tag) webbplatsen.<br><br> |  

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, lär du dig hur du kan [Utöka din moln verksamhet med Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registrera dig och börja arbeta i Partner Center:

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
- Mer information finns i [skapa ett erbjudande för Azure-behållare](./partner-center-portal/create-azure-container-offer.md) .
