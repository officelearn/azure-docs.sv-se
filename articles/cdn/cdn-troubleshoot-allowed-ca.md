---
title: Tillåtna certifikatutfärdare för att aktivera anpassade HTTPS på Azure CDN | Microsoft Docs
description: Om du använder ett eget certifikat för att aktivera HTTPS på en anpassad domän, måste du använda tillåtna certifikatutfärdare (CA) för att skapa den.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 3c41ca7e375324ff784bf7bee347bb56400ddfbd
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238301"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Tillåtna certifikatutfärdare för att aktivera anpassade HTTPS på Azure CDN

För en anpassad domän i Azure Content Delivery Network (CDN) på en **Azure CDN Standard från Microsoft** slutpunkt när du [aktivera HTTPS-funktionen med hjälp av ditt eget certifikat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), måste du använda en tillåten certifikatutfärdare (CA) som kan skapa ett SSL-certifikat. Annars, om du använder en icke-tillåtna Certifikatutfärdare eller ett självsignerat certifikat kan din begäran avvisas.

> [!NOTE]
> Möjlighet att använda ditt eget certifikat för att aktivera anpassade HTTPS är endast tillgänglig med **Azure CDN Standard från Microsoft** profiler. 
>

Följande certifikatutfärdare tillåts när du skapar egna certifikat:

- AddTrust externa Certifikatutfärdare rot
- Asien/Stillahavsområdet rot-CA
- Asien/Stillahavsområdet rotcertifikatutfärdare 2013
- Asien/Stillahavsområdet rotcertifikatutfärdare 2014
- APCA DM3P
- Autopilot Rotcertifikatutfärdare
- Baltimore CyberTrust Root
- Klass 3 primära certifikatutfärdare
- COMODO RSA-certifikatutfärdare
- COMODO RSA validering säker domänserver Certifikatutfärdare
- D-FÖRTROENDE klass 3 Rotcertifikatutfärdare 2 2009
- DigiCert Cloud Services CA 1
- DigiCert globala Rotcertifikatutfärdare
- DigiCert hög säkerhet CA-3
- DigiCert hög säkerhet EV rot-CA
- DigiCert SHA2 hög säkerhet serverns Certifikatutfärdare
- DigiCert SHA2 säker CA-Server
- GlobalSign
- GlobalSign utökad validering CA - SHA256 - G2
- GlobalSign organisation validering CA - G2
- GlobalSign rot-CA
- Microsoft Authenticode rotcertifikatutfärdare
- Microsoft Exchange-tjänster CA 2015
- Microsoft interna företagets rot
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
- Microsoft säker Server 2011 för Certifikatutfärdare
- Microsoft Services Partner rot
- Microsoft tid stämpling Service rot
- Microsoft Windows Hardware Compatibility
- MSIT CA Z2
- MSIT Enterprise CA 1
- MSIT Företagscertifikatutfärdare 3
- Roten Agency
- Symantec klass 3 EV SSL CA - G3
- Symantec klass 3 säker Server CA - G4
- Symantec Enterprise Mobilrot för Microsoft
- Thawte tidsstämplar Certifikatutfärdare
- UTN USERFirst objekt
- VeriSign Class 3 utökad validering SSL Certifikatutfärdare
- VeriSign Class 3 utökad validering SSL SGC Certifikatutfärdare
- VeriSign klass 3 primära certifikatutfärdare - G5
- VeriSign International Server CA - klass 3
- VeriSign tid stämpling Service rot
- VeriSign Universal rotcertifikatutfärdare
- WMSvc-SHA2-DALEDGE1008

