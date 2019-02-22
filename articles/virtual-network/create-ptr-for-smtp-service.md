---
title: Konfigurera zoner för omvänd sökning för en SMTP-banderollskontroll i Azure
titlesuffix: Azure Virtual Network
description: Beskriver hur du konfigurerar zoner för omvänd sökning för en SMTP-banderollskontroll i Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 203c3c5f371af7de891f0949a35378294bb50a0e
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652390"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Konfigurera zoner för omvänd sökning för en SMTP-banderollskontroll

Den här artikeln beskriver hur du använder en omvänd zon i Azure DNS och skapa en post för omvänd DNS (PTR) för att kontrollera om SMTP-banderollen.

## <a name="symptom"></a>Symtom

Om du använder en SMTP-server i Microsoft Azure kan du få följande felmeddelande visas när skicka eller ta emot ett meddelande från fjärr-e-servrar:

**554: Ingen PTR-post**

## <a name="solution"></a>Lösning

För en virtuell IP-adress i Azure skapas omvända poster i Microsoft ägs domän zoner, inte anpassad domän zoner.

Om du vill konfigurera PTR-poster i Microsoft ägs zoner, Använd egenskapen - värdet för ReverseFqdn för PublicIpAddress-resursen. Mer information finns i [konfigurera omvänd DNS för tjänster som hanteras i Azure](../dns/dns-reverse-dns-for-azure-services.md).

När du konfigurerar PTR-poster kan du kontrollera att IP-adressen och omvänt fullständigt domännamn som äger prenumerationen. Om du försöker ställa in en omvänd FQDN som inte tillhör prenumerationen visas följande felmeddelande visas:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) Värdet för ReverseFqdn matchar fqdn för en offentlig ip-resurs under prenumerationen;
    2) Värdet för ReverseFqdn matchar fqdn (via CName-poster kedja) för alla offentliga ip-resurs under prenumerationen;
    3) Det matchar ip-adressen (via CName- och A-poster kedja) för en statisk offentlig ip-resurs under prenumerationen.

Om du manuellt ändra din SMTP-banderollen för att matcha våra standard omvänd FQDN, remote e-postserver kan fortfarande misslyckas eftersom det förväntar dig SMTP-banderoll värden så att de matchar MX-post för domänen.
