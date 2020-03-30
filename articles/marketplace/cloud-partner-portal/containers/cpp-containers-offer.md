---
title: Erbjudande om avbildning av Azure Containers | Azure Marketplace
description: Översikt över processen för att publicera ett behållarerbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: 9c0b4ca6e9a26f13d1539845ca9fb43f31a1a9dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281722"
---
# <a name="containers"></a>Containrar

<table> <tr> <td>I det här avsnittet beskrivs hur du publicerar en behållaravbildning till <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.  
Behållaren erbjudandetyp stöder Docker-behållaravbildningar som etablerats som <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes</a> Service-instanser eller <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container Instances</a> och finns i en <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry-databas.</a> </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Erbjud komponenter

Det här avsnittet beskriver elementen för att publicera en behållare och är avsett som en guide för utgivaren till Azure Marketplace. Publiceringen är uppdelad i följande huvuddelar:

- Förutsättningar - [listar](./cpp-prerequisites.md) tekniska och affärsmässiga krav innan du skapar eller publicerar ett behållarerbjudande.
- [Skapa erbjudandet](./cpp-create-offer.md) -listar de steg som krävs för att skapa en ny container erbjudandepost med hjälp av Cloud Partner Portal.
- [Förbered de tekniska tillgångarna](./cpp-create-technical-assets.md) – hur du skapar de tekniska tillgångarna för en behållarlösning som ett erbjudande på Azure Marketplace.
- [Publicera erbjudandet](./cpp-publish-offer.md) – hur du skickar erbjudandet för publicering till Azure Marketplace.

## <a name="container-publishing-process"></a>Publiceringsprocess för behållare

Följande diagram illustrerar stegen på hög nivå för att publicera ett vm-erbjudande.
![Steg för att publicera ett erbjudande](./media/containers-offer-process.png)

Stegen på hög nivå för att publicera ett behållarerbjudande är:

1. Skapa erbjudandet - Ge detaljerad information om erbjudandet. Den här informationen omfattar: erbjudandebeskrivning, marknadsföringsmaterial, supportinformation och tillgångsspecifikationer.
2. Skapa affärs- och tekniska tillgångar – Skapa affärstillgångar (juridiska dokument och marknadsföringsmaterial) och tekniska resurser för den associerade lösningen (behållaravbildningarna finns i ett Azure-behållarregister.
3. Skapa SKU - Skapa de SKU:er som är associerade med erbjudandet. En unik SKU krävs för varje bild som du planerar att publicera.
4. Certifiera och publicera erbjudandet - När erbjudandet och de tekniska tillgångarna har slutförts kan du skicka erbjudandet. Den här inlämningen startar publiceringsprocessen. Under den här processen testas, valideras, certifieras lösningen och "publiceras" på Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

Innan du överväger de här stegen måste du uppfylla de [tekniska kraven och affärskraven](./cpp-prerequisites.md) för att publicera en behållare till Microsoft Azure Marketplace.
