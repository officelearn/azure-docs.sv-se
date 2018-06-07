---
title: Azure nätverkstrafik för stacken distribution | Microsoft Docs
description: Den här artikeln beskrivs vad som händer om Azure-stacken distributionsprocesser för nätverk.
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
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: b808875e66e867b84e2971c6a5bd031d108d003b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655426"
---
# <a name="about-deployment-network-traffic"></a>Om distributionen nätverkstrafik
Förstå hur nätverkstrafik flödar under Azure Stack är distribution viktigt att säkerställa en lyckad distribution. Den här artikeln vägleder dig genom förväntade nätverkstrafik under distributionsprocessen att tillhandahålla en förståelse för vad som händer.

Den här bilden visar alla komponenter och anslutningar ingår i distributionen:

![Nätverkstopologi för distribution till Azure Stack](media/deployment-networking/figure1.png)

> [!NOTE]
> Den här artikeln beskrivs kraven för en ansluten distribution, information om andra distributionsmetoder finns [Azure Stack anslutning distributionsmodeller](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>Distributionen VM
Azure Stack-lösningen innehåller en uppsättning servrar som används som värd för Azure-stacken komponenter och en extra server kallas maskinvara livscykel värden (HLH). Den här servern används för att distribuera och hantera livscykeln för din lösning och är värd för distribution av virtuell dator (DVM) under distributionen.

## <a name="deployment-requirements"></a>Krav för distribution
Innan distributionen startar, finns det vissa minimikraven som kan valideras av OEM-tillverkaren så distribution har slutförts. Förstå dessa krav hjälper dig att förbereda miljön och kontrollera att verifieringen lyckas, är dessa:

-   [Certifikat](azure-stack-pki-certs.md)
-   [Azure-prenumeration](https://azure.microsoft.com/free/?b=17.06)
-   Internet-åtkomst
-   DNS
-   NTP

> [!NOTE]
> Den här artikeln fokuserar på de tre sista krav. Mer information om två första finns i länkarna ovan.

## <a name="deployment-network-traffic"></a>Distribution av nätverkstrafik
DVM har konfigurerats med en IP-adress från BMC-nätverket och kräver åtkomst till internet. Även om inte alla BMC nätverkskomponenter kräver extern routning eller åtkomst till Internet, kan även vissa OEM-specifika komponenter genom att använda IP-adresser från det här nätverket kräver detta.

Under distributionen autentiserar DVM mot Azure Active Directory (AD Azure) med ett Azure-konto från prenumerationen. För att kunna göra det, kräver DVM internet-åtkomst till en lista över URL: er och viss port. Du hittar den fullständiga listan i den [publicera slutpunkter](azure-stack-integrate-endpoints.md) dokumentation. DVM ska använda en DNS-server för att vidarebefordra DNS-begäranden som görs av interna komponenter till externa URL: er. Internt DNS vidarebefordrar dessa begäranden till adress för DNS-vidarebefordrare som du tillhandahåller till OEM före distributionen. Detsamma gäller för NTP-server, en tillförlitlig tid-Server krävs för att underhålla synkronisering konsekvens och tid för alla komponenter i Azure-stacken.

Internet-åtkomst som krävs av DVM under distributionen är endast utgående, inga inkommande anrop görs under distributionen. Tänk på att dess IP används som källa och att Azure-stacken stöder inte proxykonfigurationer. Om det behövs, måste du därför att tillhandahålla en transparent proxy- eller NAT för att få åtkomst till internet. När distributionen är klar all kommunikation mellan Azure och Azure-stacken görs via det externa nätverket med hjälp av offentliga virtuella IP-adresser.

Nätverkskonfigurationer Azure Stack-växlar innehåller åtkomstkontrollistor (ACL) som begränsar trafiken mellan vissa nätverk källor och mål. DVM är den enda komponenten med obegränsad åtkomst; även HLH är mycket begränsad. Du kan be OEM-tillverkaren om anpassningsalternativ att underlätta hantering av och åtkomst från nätverket. På grund av dessa ACL: er är det viktigt att undvika att ändra DNS- och NTP-serveradresser vid tidpunkten för distribution. Om du gör det, måste du konfigurera om alla växlar för lösningen.

När distributionen är klar fortsätter de angivna DNS- och NTP-serveradresserna som ska användas av den systemkomponenter direkt. Till exempel om du kontaktar DNS-förfrågningar när distributionen är klar, källan kommer att ändras från DVM IP till en adress från det externa nätverket intervallet.

När Azure stacken har distribuerats, kan OEM-partner använda DVM för ytterligare uppgifter för efter distributionen. Men när alla distributionsuppgifter och konfigurationer efter distributionen har slutförts, bör OEM-tillverkaren ta bort och ta bort DVM från HLH.

## <a name="next-steps"></a>Nästa steg
[Verifiera Azure registrering](azure-stack-validate-registration.md)
