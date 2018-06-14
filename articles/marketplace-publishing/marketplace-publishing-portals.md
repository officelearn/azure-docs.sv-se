---
title: Översikt över olika portalerna som behövs för att skapa ett erbjudande på Marketplace | Microsoft Docs
description: Översikt över de olika portalerna som behövs för att skapa ett erbjudande på Marketplace
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 89ce82b3-c28a-4b0d-b37a-db3112160a4e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: mbaldwin
ms.openlocfilehash: 663d9a01b80b0c41e37037b0cbec10c93511d24c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
ms.locfileid: "29938652"
---
# <a name="portals-you-will-need"></a>Portaler som du behöver
Innan du startar processen för att publicera ett erbjudande ska få lades till på olika portalerna som du behöver. Nedan finns en kort sammanfattning om portalerna--Developer Center, Azure Publishing Portal och Azure Portal – i den ordning som du kommer att interagera med dem.                                                                            

## <a name="developer-center"></a>Developer Center
[http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)

### <a name="description"></a>Beskrivning
Skapa ditt Microsoft Developer Center-konto är en enstaka uppgift. Se till att företaget inte redan har ett Developer Center-konto innan du försöker skapa en. Vi samlar in bank kontoinformation, information om skatt och företagsinformation för IP-adress för under processen.

> [!NOTE]
> Om du publicerar endast kostnadsfria erbjudanden (eller bring-your-äger-licens erbjuder) behöver inte skatt och bankinformation.
> 
> 

### <a name="identityaccount-used"></a>Identity/konto som används
Vi rekommenderar detta är en distributionslista eller en säkerhetsgrupp (t.ex. azurepublishing @*partnercompany*.com). Distributionsgrupp eller säkerhetsgrupp **måste** registreras som ett Microsoft-konto.

> [!TIP]
> Vi rekommenderar en distributionslista eller säkerhetsgrupp eftersom det tar bort beroendet av varje enskild person, även om ett enskilt konto kan också användas.
> 
> 

## <a name="publishing-portal"></a>Publishing portal
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### <a name="description"></a>Beskrivning
Detta är portalen som du använder att fungera med erbjudandet och publicera den (marknadsföring, prissättning, publicera certifikat om tillämpligt, etc.).

### <a name="identityaccount-used"></a>Identity/konto som används
Ovanstående eller säkerhetsgrupp distributionsgruppen måste användas för första gången du loggar in på publishing portal. Andra användare kan senare kan läggas till som medadministratörer. Detta är hur den hämtar mappad till registreringsdata Developer Center.

## <a name="azure-portal"></a>Azure Portal
[https://portal.azure.com](https://portal.azure.com)

### <a name="description"></a>Beskrivning
Detta är portal där du kan visa din mellanlagrade och publicerade erbjudanden i Azure Marketplace (gäller för virtuella datorer, lösningsmallar och Azure Resource Manager-baserade developer tjänster).

### <a name="identityaccount-used"></a>Identity/konto som används
När du mellanlagring ett erbjudande från publishing portal, måste ett prenumerations-ID vara godkända. Samma prenumeration (det finns ett användarnamn och lösenord som associeras med den) måste användas för att logga in på den här portalen för att testa mellanlagrade erbjudandet.

## <a name="see-also"></a>Se också
* [Komma igång: hur du publicerar ett erbjudande för Azure Marketplace](marketplace-publishing-getting-started.md)

