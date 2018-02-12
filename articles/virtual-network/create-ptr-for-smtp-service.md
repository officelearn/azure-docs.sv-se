---
title: "Konfigurera zoner för omvänd sökning för en kontroll av SMTP-banderoll i Azure | Microsoft Docs"
description: "Beskriver hur du konfigurerar zoner för omvänd sökning för en kontroll av SMTP-banderoll i Azure"
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: WillChen
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/06/2018
ms.author: genli
ms.custom: 
ms.openlocfilehash: 1e95b00ea08105238a860265e46275c24ed7bfbd
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
#  <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Konfigurera zoner för omvänd sökning för en SMTP-banderoll kontroll

Den här artikeln beskriver hur du använder en zon för omvänd i Azure DNS och skapa en post för omvänd DNS (PTR) för SMTP-banderoll kontrollera. 

## <a name="symptom"></a>Symptom

Om du har en SMTP-server i Microsoft Azure får följande felmeddelande när skicka eller ta emot ett meddelande från fjärr-e-servrar:

**554: ingen PTR-post** 

## <a name="solution"></a>Lösning

För en virtuell IP-adress i Azure skapas omvänd poster i Microsoft ägs domän zoner inte domänen zoner.

Om du vill konfigurera PTR-poster i Microsoft ägs zoner, Använd egenskapen - ReverseFqdn på PublicIpAddress-resursen. Mer information finns i [konfigurera omvänd DNS för tjänster i Azure](../dns/dns-reverse-dns-for-azure-services.md). 

När du konfigurerar PTR-poster kan du kontrollera att IP-adressen och den omvända FQDN ägs av prenumerationen. Om du försöker ställa in en omvänd FQDN som inte tillhör prenumerationen får du följande felmeddelande:

    Set-AzureRmPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership: 
                        
    1) ReverseFqdn matchar fqdn för valfri offentlig ip-resurs under prenumerationen; 
    2) ReverseFqdn matchar fqdn (via CName-poster kedja) för alla offentliga ip-resurs under publiceringsinställningsfilen; 
    3) Det matchar ip-adressen (via CName- och A-poster-kedja) för en statisk offentlig ip-resurs under prenumerationen. 

Om du manuellt ändra SMTP-banderollen att matcha vårt omvänd FQDN, remote e-postserver kan fortfarande misslyckas eftersom den kan förvänta sig SMTP-banderoll värden så att den matchar MX-post för domänen.