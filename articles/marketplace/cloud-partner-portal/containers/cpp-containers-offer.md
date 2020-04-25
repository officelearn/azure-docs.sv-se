---
title: Erbjudande om Azure containers-avbildning | Azure Marketplace
description: Översikt över processen för att publicera ett behållar erbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: 81908de5c2fb3960684ed6cf37952e8815d8a5d5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148296"
---
# <a name="containers"></a>Containrar

> [!IMPORTANT]
> Från den 13 april 2020 börjar vi flytta hanteringen av dina Azure Container-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett Azure Container-erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) för att hantera dina migrerade erbjudanden.

<table> <tr> <td>I det här avsnittet beskrivs hur du publicerar en behållar avbildning på <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.  
Behållarens erbjudande typ har stöd för Docker-behållar avbildningar som tillhandahålls som <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes-</a> tjänsteinstanser eller <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container instances</a> och finns i en <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry</a> -lagringsplats. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Erbjudande komponenter

I det här avsnittet beskrivs hur du publicerar en behållare och är avsedd som en guide för utgivaren till Azure Marketplace. Publiceringen är uppdelad i följande huvud delar:

- [Krav – visar](./cpp-prerequisites.md) en lista över de tekniska och affärs kraven innan du skapar eller publicerar ett behållar erbjudande.
- [Skapa erbjudandet](./cpp-create-offer.md) – visar en lista över de steg som krävs för att skapa en ny behållare för container erbjudandet med hjälp av Cloud Partner Portal.
- [Förbered de tekniska till gångarna](./cpp-create-technical-assets.md) – så här skapar du tekniska till gångar för en behållar lösning som ett erbjudande på Azure Marketplace.
- [Publicera erbjudandet](./cpp-publish-offer.md) – så här skickar du erbjudandet om publicering till Azure Marketplace.

## <a name="container-publishing-process"></a>Process för publicering av behållare

Följande diagram illustrerar de övergripande stegen för att publicera ett erbjudande för virtuella datorer.
![Steg för att publicera ett erbjudande](./media/containers-offer-process.png)

De övergripande stegen för att publicera ett behållar erbjudande är:

1. Skapa erbjudandet – ange detaljerad information om erbjudandet. Den här informationen omfattar: Beskrivning av erbjudandet, marknadsförings material, supportinformation och till gångs uppgifter.
2. Skapa företags-och teknik till gångar – skapa företags till gångar (juridiska dokument och marknadsförings material) och tekniska till gångar för den associerade lösningen (behållar avbildningarna som finns i en Azure Container Registry.
3. Skapa SKU – skapa de SKU: er som är associerade med erbjudandet. En unik SKU krävs för varje avbildning som du planerar att publicera.
4. Certifiera och publicera erbjudandet – när erbjudandet och de tekniska till gångarna har slutförts kan du skicka in erbjudandet. Den här sändningen startar publicerings processen. Under den här processen testas, verifieras, godkänns, certifieras och aktive ras på Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

Innan du tar hänsyn till de här stegen måste du uppfylla de [tekniska och affärsmässiga kraven](./cpp-prerequisites.md) för att publicera en behållare till Microsoft Azure Marketplace.
