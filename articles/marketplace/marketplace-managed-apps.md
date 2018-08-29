---
title: Erbjudande om Azure-program-hanterat program publicera Guide
description: Den här artikeln beskrivs kraven för att publicera ett hanterat program på Marketplace
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
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: c8ead3dc34faefce0f113dee2074960fddfa11a1
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144599"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure-program: Erbjudande om hanterat program publicera Guide

Ett hanterat program är en av huvudmetoderna för att publicera en lösning i Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Det här är transaktionen erbjudanden som distribueras och faktureras via Marketplace. Anropet till åtgärden som en användare ser är ”Hämta nu”.

Använda Azure-appen: hanterade erbjudande apptyp när följande villkor krävs:
- Du distribuerar antingen en prenumeration-baserad lösning för din kund som använder en virtuell dator eller en hel IaaS-baserad lösning.
- Du eller dina kunder kräver att lösningen hanteras av en partner.

>[!NOTE]
>En partner kan exempelvis vara en SI eller leverantör av hanterade tjänster (MSP).  

## <a name="managed-application-offer"></a>Hanterade program erbjudande

|Krav |Information  |
|---------|---------|
|Distribueras till en kunds Azure-prenumeration | Hanterade appar måste distribueras i kundens prenumeration och kan hanteras av en 3 part | 
|Fakturering och mätning    |  Resurserna som ska etableras i kundens Azure-prenumeration. Betala per användning (betalning per användning) virtuella datorer ska ingå i en transaktion med kunden via Microsoft, faktureras via kundens Azure-prenumeration (betalning per användning) 
När det gäller bring-your-own-license, medan Microsoft att debitera infrastrukturkostnaderna för kundprenumeration ska du transact dina programvarulicenser avgifter för kunden direkt        |
|Azure-kompatibel virtuell hårddisk (VHD)    |   Virtuella datorer måste vara baserade på Windows eller Linux.<ul> <li>Mer information om hur du skapar en Linux-VHD finns i avsnittet Skapa en Azure-kompatibel VHD (Linux-baserade) avsnitt finns på [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Mer information om hur du skapar en virtuell Windows-Hårddisk finns i avsnittet Skapa en Azure-kompatibel VHD (Windows-baserade) avsnitt finns på [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |

>[!NOTE]
> Hanterade appar måste vara distribuerbar via Marketplace. Om kunden kommunikation är ett problem kan bör sedan du kontakta intresserade kunder när du har aktiverat dela leads.  


## <a name="next-steps"></a>Nästa steg
Om du inte redan gjort det, 

- [Registrera](https://azuremarketplace.microsoft.com/sell) i marketplace

Om du är registrerad och skapar ett nytt erbjudande eller arbetar på en befintlig

- [Logga in på partnerportalen i molnet](https://cloudpartner.azure.com) att skapa eller slutföra ditt erbjudande
