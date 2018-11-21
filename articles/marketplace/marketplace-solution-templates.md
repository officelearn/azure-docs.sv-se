---
title: Azure-program lösning mall erbjudandet publicera Guide
description: Den här artikeln beskrivs kraven för att publicera en mall i Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: d955f5c11121e8d42bc4b02b75427ab07298e74b
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264190"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure-program: Lösning mall erbjudandet publicera Guide

Lösningsmallar är en av huvudmetoderna för att publicera en lösning i Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Använda Azure-appen: mall erbjudandet typ av lösning när lösningen kräver ytterligare distribution och konfiguration av automation utöver en enstaka virtuell dator. Du kan automatisera etablering av en eller flera virtuella datorer med Azure-appar: lösningsmallar. Du kan också etablera nätverks- och lagringsresurser. Azure-appar: lösningsmallar erbjuder typ har automation fördelar för enskilda virtuella datorer och hela IaaS-baserade lösningar.

Dessa mallar för lösningar är transaktion erbjudanden som distribueras och faktureras via Marketplace. Anropet till åtgärden som en användare ser är ”Hämta nu”.


## <a name="requirements-for-solution-templates"></a>Krav för Lösningsmallar

| **Krav** | **Detaljer**  |
| ---------------  | -----------  |
|Fakturering och mätning    |  Resurserna som ska etableras i kundens Azure-prenumeration. Betala per användning (betalning per användning) virtuella datorer ska ingå i en transaktion med kunden via Microsoft, faktureras via kundens Azure-prenumeration (betalning per användning).  <br/> När det gäller bring-your-own-license (BYOL), medan Microsoft att debitera infrastrukturkostnader som tillkommer i kundens prenumeration, kommer du transact dina programvarulicenser avgifter för kunden direkt.   |
|Azure-kompatibel virtuell hårddisk (VHD)  |   Virtuella datorer måste vara baserade på Windows eller Linux.  Mer information [Se Skapa en Azure-kompatibel VHD](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Kundens användning Attribution | Aktivera kundens användning attribution krävs på alla mallar för lösningar som publicerats på Azure Marketplace. Läs mer på kundens användning attribution och hur du aktiverar det [Azure-partner kundens användning attribution](./azure-partner-customer-usage-attribution.md).  |
|  |  |


## <a name="next-steps"></a>Nästa steg
Om du inte redan gjort det, [registrera](https://azuremarketplace.microsoft.com/sell) i marketplace.

Om du är registrerad och skapar ett nytt erbjudande eller arbetar på en befintlig inloggning på [Cloud Partner Portal](https://cloudpartner.azure.com) att skapa eller slutföra ditt erbjudande.
