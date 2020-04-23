---
title: Publiceringsguide för Azure-program som hanteras | Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera ett hanterat program på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084880"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Azure-program: Hanterade program erbjuder publiceringskrav

I den här artikeln beskrivs kraven för den hanterade programerbjudandetypen, vilket är ett sätt att publicera ett Azure-programerbjudande på Azure Marketplace. Hanterade program är transact-erbjudanden som distribueras och faktureras via Azure Marketplace. Uppmaningen som en användare ser är "Hämta den nu".

Använd den hanterade programerbjudandetypen när följande villkor krävs:

- Du distribuerar en prenumerationsbaserad lösning för din kund med antingen en virtuell dator eller en hel IaaS-baserad lösning.
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
>Opt-in (Cloud Solution Providers) partnerkanal är nu tillgänglig. Mer information om marknadsföring av ditt erbjudande via Microsoft CSP-partnerkanaler finns i [Cloud Solution Providers](./cloud-solution-providers.md).

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har gjort det [kan du läsa](https://azuremarketplace.microsoft.com/sell) mer om Azure Marketplace.
- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra erbjudandet.
- [Skapa ett Azure-programerbjudande](./partner-center-portal/create-new-azure-apps-offer.md) för mer information.
