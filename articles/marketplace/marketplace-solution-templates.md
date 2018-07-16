---
title: Azure-program lösning mall erbjudandet publicera Guide
description: Den här artikeln beskrivs kraven för att publicera en lösningsmall i Marketplace
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
ms.openlocfilehash: 44d081a0666aa37ec0bf8eeac540b7a7f4f4f904
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059636"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure-program: Lösning mall erbjudandet publicera Guide

Lösningsmallar är en av huvudmetoderna för att publicera en lösning i Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Det här är transaktionen erbjudanden som distribueras och faktureras via Marketplace. Anropet till åtgärden som en användare ser är ”Hämta nu”.

Använda Azure-appen: mall erbjudandet typ av lösning när lösningen kräver ytterligare distribution och konfiguration av automation utöver en enkel virtuell dator. Du kan automatisera etablering av en eller flera virtuella datorer med Azure-appar: lösningsmallar. Du kan också etablera nätverks- och lagringsresurser. Azure-appar: lösningsmallar erbjuder typ har automation fördelar för enskilda virtuella datorer och hela IaaS-baserade lösningar.

## <a name="requirements-for-solution-templates"></a>Krav för Lösningsmallar

|Krav |Information  |
|---------|---------|
|Fakturering och mätning    |  Resurserna som ska etableras i kundens Azure-prenumeration. Betala per användning (betalning per användning) virtuella datorer ska ingå i en transaktion med kunden via Microsoft, faktureras via kundens Azure-prenumeration (betalning per användning) 
När det gäller bring-your-own-license, medan Microsoft att debitera infrastrukturkostnaderna för kundprenumeration ska du transact dina programvarulicenser avgifter för kunden direkt        |
|Azure-kompatibel virtuell hårddisk (VHD)    |   Virtuella datorer måste vara baserade på Windows eller Linux.<ul> <li>Mer information om hur du skapar en Linux-VHD finns i avsnittet Skapa en Azure-kompatibel VHD (Linux-baserade) avsnitt finns på [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Mer information om hur du skapar en virtuell Windows-Hårddisk finns i avsnittet Skapa en Azure-kompatibel VHD (Windows-baserade) avsnitt finns på [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |



## <a name="next-steps"></a>Nästa steg
Om du inte redan gjort det, 

- [Registrera](https://azuremarketplace.microsoft.com/sell) i marketplace

Om du är registrerad och skapar ett nytt erbjudande eller arbetar på en befintlig

- [Logga in på partnerportalen i molnet](https://cloudpartner.azure.com) att skapa eller slutföra ditt erbjudande
