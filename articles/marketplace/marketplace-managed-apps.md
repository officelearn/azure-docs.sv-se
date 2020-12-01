---
title: Hanterings guide för Azure Applications-hanterade program – Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera ett hanterat program på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: d4fb3354b7035149b80191528b2f5335b593b764
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433547"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Publicerings guide för Azure-hanterade program

Ett Azure- *hanterat program* erbjuder ett sätt att publicera ett Azure-program på Azure Marketplace. Hanterade program är Transact-erbjudanden som distribueras och debiteras via Azure Marketplace. Det List alternativ som en användare ser är att *Hämta det nu*.

I den här artikeln beskrivs kraven för typen av erbjudande för hanterade program.

Använd det hanterade programmets erbjudande typ under följande omständigheter:

- Du distribuerar en prenumerations-baserad lösning för kunden genom att antingen använda en virtuell dator (VM) eller en hel infrastruktur som en tjänst (IaaS)-baserad lösning.
- Du eller din kund kräver att lösningen hanteras av en partner.

>[!NOTE]
>En partner kan till exempel vara en System Integrator eller en hanterad tjänst leverantör (MSP).  

## <a name="managed-application-offer-requirements"></a>Krav för hanterade program erbjudanden

|Krav |Information  |
|---------|---------|
|En Azure-prenumeration | Hanterade program måste distribueras till en kunds prenumeration, men de kan hanteras av en tredje part. |
|Fakturering och mätning    |  Resurserna finns i en kunds Azure-prenumeration. Virtuella datorer som använder betalnings modellen betala per användning samverkar med kunden via Microsoft och faktureras via kundens Azure-prenumeration. <br><br> För att få en egen licens för virtuella datorer, fakturerar Microsoft alla infrastruktur kostnader som uppstår i kund prenumerationen, men du debiteras licens avgifterna för Transact-programvaran med kunden direkt.        |
|En Azure-kompatibel virtuell hård disk (VHD)    |   Virtuella datorer måste byggas på Windows eller Linux.<br><br>Mer information om hur du skapar en Linux-VHD finns i [Linux-distributioner](../virtual-machines/linux/endorsed-distros.md)som har godkänts på Azure.<br><br>Mer information om hur du skapar en virtuell Windows-hårddisk finns i [skapa ett erbjudande för Azure-program](./create-new-azure-apps-offer.md). |

---

> [!NOTE]
> Hanterade program måste kunna distribueras via Azure Marketplace. Om kund kommunikationen är ett problem kan du kontakta intresserade kunder när du har aktiverat delning av lead.  

> [!Note]
> En partner kanal för en moln lösnings leverantör (CSP) är nu tillgänglig. Mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner Channels finns i [moln lösnings leverantörer](./cloud-solution-providers.md).

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, lär du dig hur du kan [Utöka din moln verksamhet med Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registrera dig och börja arbeta i Partner Center:

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
- Mer information finns i [skapa ett erbjudande för Azure-program](./create-new-azure-apps-offer.md) .
