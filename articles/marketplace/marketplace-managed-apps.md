---
title: Publicerings guide för hantering av Azure-program
description: I den här artikeln beskrivs kraven för att publicera ett hanterat program i Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: MaggiePucciEvans
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/14/2018
ms.author: evansma
ms.openlocfilehash: 764212ac148b336b07d29c29a72314c5d889d47c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934660"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure-program: publicerings guiden för hanterade program erbjudanden

Ett hanterat program är ett av de största sätten att publicera en lösning på Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Detta är transaktions erbjudanden som distribueras och faktureras via Marketplace. Anropet till åtgärden som en användare ser är "Hämta nu".

Använd Azure app: den hanterade appens erbjudande typ när följande villkor är uppfyllda:
- Du distribuerar antingen en prenumeration baserad lösning för din kund med antingen en virtuell dator eller en hel IaaS lösning.
- Du eller din kund kräver att lösningen hanteras av en partner.

>[!NOTE]
>En partner kan till exempel vara en SI-eller Managed Service Provider (MSP).  

## <a name="managed-application-offer"></a>Erbjudande för hanterat program

|Krav |Information  |
|---------|---------|
|Distribuerad till en kunds Azure-prenumeration | Hanterade appar måste distribueras i kundens prenumeration och kan hanteras av en tredje part. | 
|Fakturering och mätning    |  Resurserna kommer att tillhandahållas i kundens Azure-prenumeration. De virtuella datorerna betala per användning (PAYGO) kommer att meddelas med kunden via Microsoft, debiteras via kundens Azure-prenumeration (PAYGO). <br> Om du använder en egen licens, medan Microsoft fakturerar infrastruktur kostnader i kund prenumerationen, kommer du att använda licens avgifterna för din kund direkt.        |
|Azure-kompatibel virtuell hård disk (VHD)    |   Virtuella datorer måste byggas på Windows eller Linux.<ul> <ul> <li>Mer information om hur du skapar en Linux-VHD finns i [Linux-distributioner](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)som har godkänts på Azure.</li> <li>Mer information om hur du skapar en virtuell Windows-hårddisk finns i [skapa en Azure-kompatibel virtuell hård disk](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Hanterade appar måste kunna distribueras via Marketplace. Om kund kommunikationen är en risk bör du kontakta intresserade kunder när du har aktiverat delning av lead.  

>[!Note]
>Partner kanal för Cloud solution providers (CSP) är nu tillgängligt.  Se [leverantörer av moln lösningar](./cloud-solution-providers.md) för mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner-kanaler.

## <a name="next-steps"></a>Nästa steg
Om du inte redan har gjort det, 

- [Registrera dig](https://azuremarketplace.microsoft.com/sell) på Marketplace.

Om du har registrerat och skapar ett nytt erbjudande eller arbetar på ett befintligt,

- [Logga in på Cloud Partner Portal](https://cloudpartner.azure.com) för att skapa eller slutföra ditt erbjudande.
