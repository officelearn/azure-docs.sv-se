---
title: Azure Stack-distribution-nätverkstrafik | Microsoft Docs
description: Den här artikeln beskriver vad som händer om Azure Stack distributionsprocesser för nätverk.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 5f4f76f87718ddcc81f8fae8b043b73a4dbd6b0a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189284"
---
# <a name="about-deployment-network-traffic"></a>Om distributionen nätverkstrafik
Förstå hur trafik flödar under Azure Stack är distribution viktigt att säkerställa en lyckad distribution. Den här artikeln beskriver hur du förväntade nätverkstrafik under distributionsprocessen att tillhandahålla en förståelse för vad som händer.

Den här bilden visar alla komponenter och anslutningar som ingår i distributionen:

![Nätverkstopologi för distribution till Azure Stack](media/deployment-networking/figure1.png)

> [!NOTE]
> Den här artikeln beskrivs kraven för en ansluten distribution, mer information om andra metoder för distribution, se [anslutning för Azure Stack distributionsmodeller](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>Distributionen VM
Azure Stack-lösningen innehåller en uppsättning servrar som används som värd för Azure Stack-komponenterna och en extra server kallas maskinvara livscykel värden (HLH). Den här servern används för att distribuera och hantera livscykeln för din lösning och är värd för distribution av virtuell dator (DVM) under distributionen.

## <a name="deployment-requirements"></a>Krav för distribution
Innan distributionen startar, finns det vissa minimikraven som kan valideras av OEM-tillverkaren att se till att distributionen har slutförts. Förstå dessa krav hjälper dig att förbereda miljön och se till att verifieringen lyckas, är dessa:

-   [Certifikat](azure-stack-pki-certs.md)
-   [Azure-prenumeration](https://azure.microsoft.com/free/?b=17.06)
-   Internet-åtkomst
-   DNS
-   NTP

> [!NOTE]
> Den här artikeln fokuserar på de tre sista kraven. Mer information om de första två finns i länkarna ovan.

## <a name="deployment-network-traffic"></a>Nätverkstrafik för distribution
DVM har konfigurerats med en IP-adress från BMC-nätverket och kräver åtkomst till internet. Även om inte alla BMC nätverkskomponenter kräver extern routning eller åtkomst till Internet, kan även vissa OEM-specifika komponenter med IP-adresser från det här nätverket kräver detta.

Under distributionen av autentiseras DVM mot Azure Active Directory (Azure AD) med ett Azure-konto från prenumerationen. För att kunna göra det, kräver DVM internet-åtkomst till en lista över specifika port och URL: er. Du hittar den fullständiga listan i den [publicera slutpunkter](azure-stack-integrate-endpoints.md) dokumentation. DVM ska använda en DNS-server för att vidarebefordra DNS-begäranden som görs av interna komponenter till externa URL: er. Internt DNS vidarebefordrar dessa begäranden till DNS-vidarebefordrare-adress som du tillhandahåller OEM före distributionen. Detsamma gäller för NTP-servern, en tillförlitlig tidsserver krävs att upprätthålla konsekvens och tid synkronisering för alla Azure Stack-komponenter.

Internet-åtkomst som krävs av DVM under distributionen är endast utgående, inga inkommande anrop som görs under distributionen. Tänk på att dess IP används som källa och den Azure-stacken har inte stöd för proxykonfigurationer. Om det behövs, måste du därför att tillhandahålla en transparent proxy eller NAT för att få åtkomst till internet. Under distributionen av startar vissa interna komponenter ansluter till internet via det externa nätverket med hjälp av offentliga virtuella IP-adresser. När distributionen är klar görs all kommunikation mellan Azure och Azure Stack via det externa nätverket med hjälp av offentliga virtuella IP-adresser.

Konfigurationer för nätverk på Azure Stack-växlar innehåller åtkomstkontrollistor (ACL) som begränsar trafik mellan vissa nätverk källor och mål. DVM är den enda komponenten med obegränsad åtkomst; även HLH är mycket begränsad. Du kan ställa OEM-tillverkaren om anpassningsalternativ för att underlätta hantering och åtkomst från ditt nätverk. På grund av dessa ACL: er är det viktigt att undvika att ändra DNS- och NTP-serveradresser vid tidpunkten för distribution. Om du gör det kommer du behöva konfigurera om alla växlar för lösningen.

När distributionen är klar fortsätter de angivna DNS- och NTP-serveradresserna som ska användas av systemets komponenter direkt. Till exempel om du markerar DNS-förfrågningar när distributionen är klar, ändras källan från DVM IP till en adress från det externa nätverket intervallet.

När distributionen är klar fortsätter de angivna DNS- och NTP-serveradresserna som ska användas av systemkomponenter via SDN med hjälp av det externa nätverket. Till exempel om du markerar DNS-förfrågningar när distributionen är klar, ändras källan från DVM IP till en offentlig VIP.

## <a name="next-steps"></a>Nästa steg
[Verifiera Azure-registrering](azure-stack-validate-registration.md)
