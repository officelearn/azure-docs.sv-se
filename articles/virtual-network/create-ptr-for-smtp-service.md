---
title: Konfigurera zoner för omvänd sökning för en SMTP-banderoll i Azure
titlesuffix: Azure Virtual Network
description: Beskriver hur du konfigurerar zoner för omvänd sökning för en SMTP-banderoll i Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 084fdb7f850f3819738a982127fa98efab114197
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059026"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Konfigurera zoner för omvänd sökning för en SMTP-annons kontroll

Den här artikeln beskriver hur du använder en omvänd zon i Azure DNS och skapar en omvänd DNS-post (PTR) för SMTP-banderollbilden.

## <a name="symptom"></a>Symtom

Om du är värd för en SMTP-server i Microsoft Azure kan du få följande fel meddelande när du skickar eller tar emot ett meddelande från fjärranslutna e-postservrar:

**554: Ingen PTR-post**

## <a name="solution"></a>Lösning

För en virtuell IP-adress i Azure skapas de omvända posterna i Microsofts ägda domän zoner, inte för anpassade domän zoner.

Om du vill konfigurera PTR-poster i Microsofts ägda zoner använder du egenskapen-ReverseFqdn på PublicIpAddress-resursen. Mer information finns i [Konfigurera omvänd DNS för tjänster som finns i Azure](../dns/dns-reverse-dns-for-azure-services.md).

När du konfigurerar PTR-posterna måste du kontrol lera att IP-adressen och det omvända fullständiga domän namnet ägs av prenumerationen. Om du försöker ange ett omvänt fullständigt domän namn som inte tillhör prenumerationen visas följande fel meddelande:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) ReverseFqdn matchar FQDN för en offentlig IP-resurs under prenumerationen.
    2) ReverseFqdn matchar FQDN (via CName-datakedja) för offentliga IP-resurser under prenumerationen.
    3) Den matchar IP-adressen (via CName och en post kedja) för en statisk offentlig IP-resurs under prenumerationen.

Om du ändrar din SMTP-banderoll manuellt så att den matchar vårt standardiserade omvänt fullständigt domän namn kan fjärrservern fortfarande Miss lyckas eftersom den kan förväntar sig att den ska matcha MX-posten för domänen.
