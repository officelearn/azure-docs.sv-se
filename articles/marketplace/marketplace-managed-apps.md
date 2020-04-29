---
title: Publicerings guide för hanterings program för Azure-program Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera ett hanterat program på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084880"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Azure-program: publicerings krav för Managed Application-erbjudande

I den här artikeln förklaras kraven för typen av hanterade program, vilket är ett sätt att publicera ett erbjudande för Azure-program på Azure Marketplace. Hanterade program är Transact-erbjudanden som distribueras och debiteras via Azure Marketplace. Anropet till åtgärden som en användare ser är "Hämta nu".

Använd typen av erbjudande för hanterade program när följande villkor är uppfyllda:

- Du distribuerar en prenumerations-baserad lösning för din kund med antingen en virtuell dator eller en hel IaaS lösning.
- Du eller din kund kräver att lösningen hanteras av en partner.

>[!NOTE]
>En partner kan till exempel vara en SI-eller Managed Service Provider (MSP).  

## <a name="managed-application-offer"></a>Erbjudande för hanterat program

|Krav |Information  |
|---------|---------|
|Distribuerad till en kunds Azure-prenumeration | Hanterade appar måste distribueras i kundens prenumeration och kan hanteras av en tredje part. |
|Fakturering och mätning    |  Resurserna kommer att tillhandahållas i kundens Azure-prenumeration. De virtuella datorerna betala per användning (PAYGO) kommer att meddelas med kunden via Microsoft, debiteras via kundens Azure-prenumeration (PAYGO). <br> Om du använder en egen licens, medan Microsoft fakturerar infrastruktur kostnader i kund prenumerationen, kommer du att använda licens avgifterna för din kund direkt.        |
|Azure-kompatibel virtuell hård disk (VHD)    |   Virtuella datorer måste byggas på Windows eller Linux.<ul> <ul> <li>Mer information om hur du skapar en Linux-VHD finns i [Linux-distributioner](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)som har godkänts på Azure.</li> <li>Mer information om hur du skapar en virtuell Windows-hårddisk finns i [skapa ett erbjudande för Azure-program](./partner-center-portal/create-new-azure-apps-offer.md).</li> </ul> |

>[!NOTE]
> Hanterade appar måste kunna distribueras via Marketplace. Om kund kommunikationen är en risk bör du kontakta intresserade kunder när du har aktiverat delning av lead.  

>[!Note]
>Partner kanal för Cloud solution providers (CSP) är nu tillgängligt. Mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner Channels finns i [moln lösnings leverantörer](./cloud-solution-providers.md).

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har gjort det, [Läs mer](https://azuremarketplace.microsoft.com/sell) om Azure Marketplace.
- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
- [Skapa ett Azure Application-erbjudande](./partner-center-portal/create-new-azure-apps-offer.md) för mer information.
