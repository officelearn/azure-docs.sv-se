---
title: Felsöka en Programgateway i Azure – ILB ASE | Microsoft Docs
description: Lär dig hur du felsöker en Programgateway med hjälp av en intern belastningsutjämnare med en App Service-miljö i Azure
services: vpn-gateway
documentationCenter: na
author: genlin
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: baed2b23a321c53a614303d3085fbb3a4bf6ad0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831103"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Backend-servercertifikatet är inte vitlistad för en Programgateway med en intern belastningsutjämnare med en App Service Environment

Den här artikeln felsöker följande problem: Ett certifikat är inte godkänd när du skapar en Programgateway med hjälp av ett internt belastningsutjämnaren (ILB) tillsammans med en App Service Environment (ASE) på backend-servern när du använder SSL för slutpunkt till slutpunkt i Azure.

## <a name="symptoms"></a>Symtom

När du skapar en Programgateway med en ILB med en ASE på serverdelen kan backend-servern bli skadad. Det här problemet uppstår om certifikat för serverautentisering för programgatewayen inte matchar det konfigurerade certifikatet på backend-servern. Se följande scenario som exempel:

**Konfiguration för Programgatewayen:**

- **Lyssnare:** Flera platser
- **Port:** 443
- **Värdnamn:** test.appgwtestase.com
- **SSL-certifikat:** CN=test.appgwtestase.com
- **Serverdelspool:** IP-adress eller FQDN
- **IP-adress:**: 10.1.5.11
- **HTTP-inställningar:** HTTPS
- **Port:**: 443
- **Anpassad avsökning:** Hostname – test.appgwtestase.com
- **Certifikat för serverautentisering:** .cer av test.appgwtestase.com
- **Serverdelens hälsotillstånd:** Ohälsosamma – Backend-servercertifikatet är inte godkänd med Application Gateway.

**ASE-konfiguration:**

- **ILB IP-ADRESS:** 10.1.5.11
- **Domännamn:** appgwtestase.com
- **App Service:** test.appgwtestase.com
- **SSL-bindning:** SNI SSL – CN=test.appgwtestase.com

När du har åtkomst till application gateway får följande felmeddelande eftersom backend-servern är felaktiga:

**502 – webbservern tog emot ett ogiltigt svar när den fungerade som gateway eller proxy.**

## <a name="solution"></a>Lösning

När du inte använder ett värdnamn för att få åtkomst till en HTTPS-webbplats, returnerar backend-servern det konfigurerade certifikatet på standardwebbplatsen, om SNI är inaktiverad. För en ILB ASE kommer standardcertifikatet från ILB-certifikat. Om det finns inga konfigurerade certifikat för den interna Belastningsutjämnaren, kommer certifikatet från ASE App certificate.

När du använder ett fullständigt kvalificerat domännamn (FQDN) för att få åtkomst till den interna Belastningsutjämnaren, returneras rätt certifikat som har överförts i HTTP-inställningarna i backend-servern. Om det inte är fallet, bör du följande alternativ:

- Använda FQDN i backend-poolen med application gateway för att peka på IP-adressen för den interna Belastningsutjämnaren. Det här alternativet fungerar bara om du har en privat DNS-zon eller en anpassad DNS konfigurerat. Annars, måste du skapa en A-post för en offentlig DNS.

- Använd överförda certifikat på den interna Belastningsutjämnaren eller standardcertifikatet (ILB-certifikat) i HTTP-inställningarna. Application gateway hämtar certifikatet när den kommer åt den interna belastningsutjämnarens IP för avsökningen.

- Använd ett jokerteckencertifikat på den interna Belastningsutjämnaren och backend-servern, så att certifikatet är gemensamma för alla webbplatser. Den här lösningen är dock bara möjligt med underdomäner och inte om var och en av webbplatser kräver olika värdnamn.

- Rensa den **för App service** alternativ för application gateway om du använder IP-adressen för den interna Belastningsutjämnaren.

Du kan överföra ILB-certifikat i HTTP-inställningarna för att göra avsökningssökvägen fungerar för att minska omkostnaderna. (Det här steget gäller bara för listan över tillåtna program. Den används inte för SSL-kommunikation.) Du kan hämta ILB-certifikat genom att gå till den interna Belastningsutjämnaren med dess IP-adress från din webbläsare på HTTPS och sedan exportera SSL-certifikatet i en Base64-kodad CER-format och ladda upp certifikatet på respektive HTTP-inställningarna.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
