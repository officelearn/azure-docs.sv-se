---
title: Konfigurera zoner för omvänd sökning för en SMTP-bannerkontroll
titlesuffix: Azure Virtual Network
description: Beskriver hur du konfigurerar zoner för omvänd sökning för en SMTP-bannerkontroll i Azure
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
ms.openlocfilehash: 6c37ba61005c9936e6421d06369d1f52b93ac264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201704"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Konfigurera zoner för omvänd sökning för en SMTP-bannerkontroll

I den här artikeln beskrivs hur du använder en omvänd zon i Azure DNS och skapar en omvänd DNS-post (PTR) för SMTP Banner Check.

## <a name="symptom"></a>Symptom

Om du är värd för en SMTP-server i Microsoft Azure kan följande felmeddelande visas när du skickar eller tar emot ett meddelande från fjärrutskicksservrar:

**554: Ingen PTR-post**

## <a name="solution"></a>Lösning

För en virtuell IP-adress i Azure skapas de omvända posterna i Microsoft-ägda domänzoner, inte anpassade domänzoner.

Om du vill konfigurera PTR-poster i Microsoft-ägda zoner använder du egenskapen -ReverseFqdn på publicipAddress-resursen. Mer information finns i [Konfigurera omvänd DNS för tjänster som finns i Azure](../dns/dns-reverse-dns-for-azure-services.md).

När du konfigurerar PTR-posterna kontrollerar du att IP-adressen och den omvända FQDN ägs av prenumerationen. Om du försöker ange ett omvänt FQDN som inte tillhör prenumerationen visas följande felmeddelande:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) ReverseFqdn matchar fqdn för alla offentliga ip-resurser under prenumerationen.
    2) ReverseFqdn matchar fqdn (via CName-postkedjan) för alla offentliga ip-resurser under prenumerationen.
    3) Den matchas till IP-adressen (via CName och A records chain) för en statisk offentlig IP-resurs under prenumerationen.

Om du ändrar din SMTP-banner manuellt för att matcha vår standard omvänd FQDN kan fjärrutskicksservern fortfarande misslyckas eftersom det kan förvänta sig att SMTP-bannervärden matchar MX-posten för domänen.
