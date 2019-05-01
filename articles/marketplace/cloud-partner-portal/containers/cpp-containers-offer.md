---
title: Erbjudande om Azure behållare | Azure Marketplace
description: Översikt över processen för att publicera ett erbjudande med behållare på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: pabutler
ms.openlocfilehash: 9617380822f5814d1cbd2bd2b69eac72722fe813
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942739"
---
# <a name="containers"></a>Containrar

<table> <tr> <td>Det här avsnittet beskrivs hur du publicerar en behållaravbildning till den <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.  
Behållaren erbjudandet typ stöder Docker-behållaravbildningar som ska etableras som <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes Service</a> instanser eller <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container Instances</a> och värdbaserad i en <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry </a> lagringsplats. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Erbjudandet komponenter

Det här avsnittet beskriver de element för att publicera en behållare och är avsedd som en vägledning för utgivare på Azure Marketplace. Publicerings uppdelad i följande huvuddelar:

- [Förutsättningar](./cpp-prerequisites.md) – visar en lista över tekniska och affärsmässiga krav innan du skapar eller publicera ett erbjudande med behållare.
- [Skapa erbjudandet](./cpp-create-offer.md) – Listar de steg som krävs för att skapa en ny behållare erbjudandet post med hjälp av Cloud Partner Portal.
- [Förbereda tekniska resurser](./cpp-create-technical-assets.md) – så här skapar du de tekniska resurserna för en lösning som ett erbjudande på Azure Marketplace.
- [Publicera erbjudandet](./cpp-publish-offer.md) – hur du skickar in erbjudande för publicering på Azure Marketplace.

## <a name="container-publishing-process"></a>Process för att publicera behållare

Följande diagram illustrerar de övergripande stegen vid publicering av ett erbjudande för virtuell dator.
![Steg för att publicera ett erbjudande](./media/containers-offer-process.png)

Anvisningar för att publicera ett erbjudande med behållare är:

1. Skapa erbjudandet – ger detaljerad information om erbjudandet. Informationen omfattar: erbjudandet beskrivning, marknadsföringsmaterial, supportinformation och tillgångsspecifikationer.
2. Skapa affärs- och tekniska resurser – skapa företagstillgångar (juridiska dokument och marknadsföringsmaterial) och tekniska resurser för den associerade lösningen (behållare avbildningar som finns i ett Azure Container Registry.
3. Skapa SKU - skapa den SKU: er som är kopplad till erbjudandet. En unik SKU måste anges för varje bild som du planerar att publicera.
4. Certifiera och publicera erbjudande - när erbjudandet och tekniska resurser har slutförts, kan du skicka erbjudandet. Den här publiceringen börjar publiceringsprocessen. Under den här processen testas lösningen verifierats, certifierade, sedan ”lanseras” på Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

Innan du betraktar dessa steg måste du uppfylla de [tekniska krav och affärskrav](./cpp-prerequisites.md) för att publicera en behållare till Microsoft Azure Marketplace.