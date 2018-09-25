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
ms.date: 09/17/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: e9d9f5b053537b3d6881763bcb39d7894ba78869
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969521"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Tillåtna certifikatutfärdare för att aktivera anpassad HTTPS på Azure CDN

För en anpassad Azure Content Delivery Network (CDN)-domän på en **Azure CDN Standard från Microsoft** slutpunkt när du [aktivera HTTPS-funktionen med hjälp av ditt eget certifikat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), måste du använda en tillåtna certifikatutfärdare (CA) som kan skapa ett SSL-certifikat. Annars, om du använder en Certifikatutfärdare som inte är tillåtna eller ett självsignerat certifikat kan din begäran kommer att avvisas.

> [!NOTE]
> Alternativet att använda ditt eget certifikat för att aktivera anpassad HTTPS är endast tillgängligt med **Azure CDN Standard från Microsoft** profiler. 
>

Följande CA: er tillåts när du skapar dina egna certifikat:

- AddTrust externa Certifikatutfärdare rot
- AlphaSSL rot-CA
- AMN Infra CA 01
- AMN Infra CA 02
- Ameroot
- Asien och Stillahavsområdet rot-CA
- Asien och Stillahavsområdet rotcertifikatutfärdare 2013
- Asien och Stillahavsområdet rotcertifikatutfärdare 2014
- APCA DM3P
- Autopilot-Rotcertifikatutfärdare
- Baltimore CyberTrust Root
- Klass 3 primära certifikatutfärdare
- COMODO RSA-certifikatutfärdare
- COMODO RSA verifiering säker domänserver CA
- D-TRUST klass 3 Rotcertifikatutfärdare 2 2009
- DigiCert molntjänster CA-1
- DigiCert globala Rotcertifikatutfärdare
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
- Entrust.NET certifikatutfärdare (2048)
- GeoTrust globala CA
- GeoTrust primära certifikatutfärdare
- GeoTrust primära certifikatutfärdaren - G2
- Geotrust RSA CA 2018
- GlobalSign
- Utökad validering CA - SHA256 - G2 GlobalSign
- GlobalSign organisation verifiering CA - G2
- GlobalSign rot-CA
- Go Daddy rotcertifikatutfärdare - G2
- Go Daddy säker certifikatutfärdare - G2
- Microsoft Authenticode rotcertifikatutfärdare
- Microsoft Exchange-tjänster CA 2015
- Microsoft internt företagets rotcertifikatutfärdare
- Microsoft IT ITO SSL CA 1
- Microsoft IT SSL SHA1
- Microsoft IT SSL SHA2
- Microsoft IT TLS CA 1
- Microsoft IT TLS CA 2
- Microsoft IT TLS CA 4
- Microsoft IT TLS CA 5
- Microsoft rotcertifikatutfärdare
- Microsoft rotcertifikatutfärdare
- Microsoft rotcertifikatutfärdare 2010
- Microsoft rotcertifikatutfärdare 2011
- Microsoft säker Server CA 2011
- Microsoft Services Partner rot
- Microsoft tid stämpling Service rot
- Microsoft Windows maskinvarukompatibilitet
- MSIT CA Z2
- MSIT Enterprise CA 1
- MSIT Företagscertifikatutfärdare 3
- RapidSSL RSA CA 2018
- Rot myndighet
- Symantec-klass 3 EV SSL CA - G3
- Symantec klass 3 säker Server CA - G4
- Symantec Enterprise Mobilrot för Microsoft
- Thawte primära rot-CA
- Thawte primära rot-CA - G2
- Thawte primära rot-CA - G3
- Thawte RSA CA 2018
- Thawte tidsstämplar CA
- TrustAsia TLS RSA CA
- UTN-USERFirst-Object
- VeriSign Class 3 utökad validering SSL CA
- VeriSign Class 3 utökad validering SSL SGC CA
- VeriSign klass 3 primära certifikatutfärdare - G5
- VeriSign International Server CA - klass 3
- VeriSign tid stämpling Service rot
- VeriSign Universal rotcertifikatutfärdare
- WMSvc-SHA2-DALEDGE1008

