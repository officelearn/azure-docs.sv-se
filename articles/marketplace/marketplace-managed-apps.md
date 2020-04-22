---
title: Publiceringsguide för Azure Applications Managed Application Offer
description: I den här artikeln beskrivs kraven för att publicera ett hanterat program på Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 56281ff8faba6b4b950334e2b0018d48c8e7aeb3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687568"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure-program: Publiceringsguide för hanterat programerbjudande

Ett hanterat program är ett av de viktigaste sätten att publicera en lösning på Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Det här är transaktionserbjudanden som distribueras och faktureras via Marketplace. Uppmaningen som en användare ser är "Hämta den nu".

Använd Azure-appen: hanterad apperbjudandetyp när följande villkor krävs:
- Du distribuerar antingen en prenumerationsbaserad lösning för kunden med antingen en virtuell dator eller en hel IaaS-baserad lösning.
- Du eller din kund kräver att lösningen hanteras av en partner.

>[!NOTE]
>En partner kan till exempel vara en SI- eller managed service provider (MSP).  

## <a name="managed-application-offer"></a>Erbjudande om hanterat program

|Krav |Information  |
|---------|---------|
|Distribueras till en kunds Azure-prenumeration | Hanterade appar måste distribueras i kundens prenumeration och kan hanteras av en tredje part. | 
|Fakturering och mätning    |  Resurserna etableras i kundens Azure-prenumeration. Pay-as-you-go (PAYGO) virtuella datorer kommer att överföras med kunden via Microsoft, faktureras via kundens Azure-prenumeration (PAYGO). <br> När det gäller bring-your-own-licens, medan Microsoft fakturerar infrastrukturkostnader som uppstår i kundprenumerationen, kommer du att överföra dina licensavgifter för programvara till kunden direkt.        |
|Azure-kompatibel virtuell hårddisk (VHD)    |   Virtuella datorer måste byggas på Windows eller Linux.<ul> <ul> <li>Mer information om hur du skapar en Virtuell Linux-hårddisk finns i [Linux-distributioner som är godkända på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Mer information om hur du skapar en Virtuell Windows-hårddisk finns i [skapa ett Azure-programerbjudande](./partner-center-portal/create-new-azure-apps-offer.md).</li> </ul> |

>[!NOTE]
> Hanterade appar måste kunna distribueras via Marketplace. Om kundkommunikation är ett problem, bör du nå ut till intresserade kunder efter att du har aktiverat leaddelning.  

>[!Note]
>Opt-in (Cloud Solution Providers) partnerkanal är nu tillgänglig.  Se [Cloud Solution Providers](./cloud-solution-providers.md) för mer information om marknadsföring ditt erbjudande via Microsoft CSP partnerkanaler.

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, 

- [Läs mer](https://azuremarketplace.microsoft.com/sell) om marknadsplatsen.

Om du vill registrera dig i Partner center börjar du skapa ett nytt erbjudande eller arbetar på ett befintligt:

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra erbjudandet.
- Mer information finns i [skapa ett Azure-programerbjudande.](./partner-center-portal/create-new-azure-apps-offer.md)
