---
title: Tillåtna certifikatutfärdare för att aktivera anpassad HTTPS på Azure CDN | Microsoft Docs
description: Om du använder ett eget certifikat för att aktivera HTTPS på en anpassad domän måste du använda en tillåtna certifikatutfärdare (CA) för att skapa den.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 11651c2721756a4f750a5a5e78f86fdbd363fb9d
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462597"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Tillåtna certifikatutfärdare för att aktivera anpassad HTTPS på Azure CDN

För en anpassad Azure Content Delivery Network (CDN)-domän på en **Azure CDN Standard från Microsoft** slutpunkt när du [aktivera HTTPS-funktionen med hjälp av ditt eget certifikat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), måste du använda en tillåtna certifikatutfärdare (CA) som kan skapa ett SSL-certifikat. Annars, om du använder en Certifikatutfärdare som inte är tillåtna eller ett självsignerat certifikat kan din begäran kommer att avvisas.

> [!NOTE]
> Alternativet att använda ditt eget certifikat för att aktivera anpassad HTTPS är endast tillgängligt med **Azure CDN Standard från Microsoft** profiler. 
>

Följande CA: er tillåts när du skapar dina egna certifikat:

- AddTrust externa Certifikatutfärdare rot
- AlphaSSL rot-CA
- AME Infra CA 01
- AME Infra CA 02
- Ameroot
- APCA-DM3P
- Autopilot-Rotcertifikatutfärdare
- Baltimore CyberTrust Root
- Klass 3 primära certifikatutfärdare
- COMODO RSA-certifikatutfärdare
- COMODO RSA verifiering säker domänserver CA
- D-TRUST klass 3 Rotcertifikatutfärdare 2 2009
- DigiCert Cloud Services CA-1
- DigiCert Global Root CA
- DigiCert hög Assurance CA-3
- DigiCert hög Assurance EV rot-CA
- DigiCert SHA2 utökad validering Server CA
- DigiCert SHA2 höga säkerhet Server CA
- DigiCert SHA2 säker CA-Server
- DST rotcertifikatutfärdaren X3
- D-trust klass 3 Rotcertifikatutfärdare 2 2009
- Kryptering överallt DV TLS CA
- Entrust-certifikat från rotcertifikatutfärdare
- Entrust rotcertifikatutfärdare - G2
- Entrust.net Certification Authority (2048)
- GeoTrust Global CA
- GeoTrust primära certifikatutfärdare
- GeoTrust primära certifikatutfärdaren - G2
- Geotrust RSA CA 2018
- GlobalSign
- Utökad validering CA - SHA256 - G2 GlobalSign
- GlobalSign organisation verifiering CA - G2
- GlobalSign Root CA
- Go Daddy rotcertifikatutfärdare - G2
- Go Daddy säker certifikatutfärdare - G2
- QuoVadis Root CA2 G3
- RapidSSL RSA CA 2018
- Symantec-klass 3 EV SSL CA - G3
- Symantec klass 3 säker Server CA - G4
- Symantec Enterprise Mobilrot för Microsoft
- Thawte primära rot-CA
- Thawte primära rot-CA - G2
- Thawte primära rot-CA - G3
- Thawte RSA CA 2018
- Thawte tidsstämplar CA
- TrustAsia TLS RSA CA
- VeriSign Class 3 utökad validering SSL CA
- VeriSign Class 3 utökad validering SSL SGC CA
- VeriSign klass 3 primära certifikatutfärdare - G5
- VeriSign International Server CA - klass 3
- VeriSign tid stämpling Service rot
- VeriSign Universal rotcertifikatutfärdare


