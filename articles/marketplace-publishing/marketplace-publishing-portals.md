---
title: Översikt över de olika portalerna som behövs för att skapa ett erbjudande för Marketplace | Microsoft Docs
description: Översikt över de olika portalerna som behövs för att skapa ett erbjudande för Marketplace
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 89ce82b3-c28a-4b0d-b37a-db3112160a4e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: d1cc0efa1da90d90bd035eaa495a1336b6ff96c2
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074197"
---
# <a name="portals-you-will-need"></a>Portaler som du behöver

Innan du börjar processen för att publicera ett erbjudande, ska vi hjälper dig att betydelsen av de olika portalerna som du behöver. Nedan visas kort sammanfattning om portaler – Developer Center, Azure-Publiceringsportalen och Azure portal – i den ordning som du kommer att interagera med dem.                                                                            

## <a name="developer-center"></a>Developer Center
[http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)

### <a name="description"></a>Beskrivning
Skapa ett konto på Microsoft Developer Center är en gång. Se till att företaget inte redan har ett Developer Center-konto innan du försöker skapa en. Under processen samlar vi in Bankkontouppgifter, skatteinformation och företagets adressuppgifter.

> [!NOTE]
> Om du publicerar endast kostnadsfria erbjudanden (eller erbjuder bring-your-own-license) behöver inte skatte- och bank information.
> 
> 

### <a name="identityaccount-used"></a>Identitet/konto som används
Vi rekommenderar det här värdet är en distributionslista eller en säkerhetsgrupp (till exempel azurepublishing @*partnercompany*.com). Lista eller säkerhet distributionsgrupp **måste** registreras som ett Microsoft-konto.

> [!TIP]
> Vi rekommenderar att du använder en distributionslista eller en säkerhetsgrupp eftersom det eliminerar beroendet av varje enskild person, även om ett enskilt konto kan användas samt.
> 
> 

## <a name="publishing-portal"></a>Publisher-portalen
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### <a name="description"></a>Beskrivning

Det här värdet är-portalen som du använder för att arbeta med erbjudandet och publicera den (marknadsföring, prissättning, publicera certifikat om tillämpligt, osv.).

### <a name="identityaccount-used"></a>Identitet/konto som används
I ovanstående lista eller säkerhet distributionsgruppen måste användas för första gången för att logga in på publiceringsportalen. Senare kan kan andra användare läggas till som medadministratörer. Den här listan är hur den hämtar mappad till registreringsdata Developer Center.

## <a name="azure-portal"></a>Azure Portal
[Azure Portal](https://portal.azure.com)

### <a name="description"></a>Beskrivning
Det här värdet är där du kan visa dina mellanlagrad och publicerade erbjudanden på Azure Marketplace (gäller för virtuella datorer, lösningsmallar och Azure Resource Manager-baserade utvecklartjänster)-portalen.

### <a name="identityaccount-used"></a>Identitet/konto som används
När du mellanlagring ett erbjudande i publiceringsportalen, måste ett prenumerations-ID vara godkänd. Samma prenumeration (det finns ett användarnamn och lösenord som är associerade med den) måste användas för att logga in på den här portalen för att testa det mellanlagrade erbjudandet.

## <a name="see-also"></a>Se också
* [Komma igång: Hur du publicerar ett erbjudande för Azure Marketplace](marketplace-publishing-getting-started.md)

