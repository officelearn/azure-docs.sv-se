---
title: Publicerings guide för container erbjudanden i Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera behållar erbjudanden i Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/04/2020
ms.openlocfilehash: c52fabcfc2ff22df2de6dd93f2543d625310baef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484349"
---
# <a name="publishing-guide-for-container-offers"></a>Publicerings guide för container erbjudanden

Med container får du hjälp att publicera behållar avbildningen på Azure Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Container erbjudanden är transaktions erbjudanden som distribueras och faktureras via Azure Marketplace. Det List alternativ som en användare ser är "Hämta nu".

Använd *behållarens* erbjudande typ när din lösning är en Docker-behållar avbildning som har kon figurer ATS som en Kubernetes-baserad Azure Container Service-instans. 

> [!NOTE]
> Exempel på Kubernetes-baserade Azure Container Service-instanser är Azure Kubernetes service eller Azure Container Instances, valet av Azure-kunder för en Kubernetes-baserad container körning.  

Microsoft har för närvarande stöd för kostnads fria och BYOL-licensierings modeller.

## <a name="container-offer-requirements"></a>Krav för container-erbjudande

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Stöder antingen den kostnads fria eller BYOL fakturerings modellen.<br><br> |  
| Avbildning som skapats från en Dockerfile | Behållar avbildningar måste baseras på Docker-avbildnings specifikationen och skapas från en Dockerfile.<br> <br>Mer information om hur du skapar Docker-avbildningar finns i avsnittet "användning" i [Dockerfile-referensen](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Vara värd för en Azure Container Registry-lagringsplats | Behållar avbildningar måste finnas i en Azure Container Registry-lagringsplats.<br> <br>Mer information om hur du arbetar med Azure Container Registry finns i [snabb start: skapa ett privat behållar register med hjälp av Azure Portal](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Bildtaggning | Behållar avbildningar måste innehålla minst en tagg (maximalt antal Taggar: 16).<br><br>Mer information om att tagga en bild finns `docker tag` på sidan på [Docker-dokumentations](https://docs.docker.com/engine/reference/commandline/tag) webbplatsen.<br><br> |  

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, lär du dig hur du kan [Utöka din moln verksamhet med Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registrera dig och börja arbeta i Partner Center:

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
- Mer information finns i [skapa ett erbjudande för Azure-behållare](./partner-center-portal/create-azure-container-offer.md) .
