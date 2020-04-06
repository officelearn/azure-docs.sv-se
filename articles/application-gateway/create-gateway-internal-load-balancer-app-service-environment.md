---
title: Felsöka en programgateway i Azure – ILB ASE | Microsoft-dokument
description: Lär dig hur du felsöker en programgateway med hjälp av en intern belastningsutjämnare med en App Service-miljö i Azure
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: 4edeea749ba22bef173c15f3a0855679b784ce33
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668560"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Backend-servercertifikat är inte vitlistat för en programgateway med hjälp av en intern belastningsutjämnare med en apptjänstmiljö

Felsöker följande problem: Ett certifikat är inte vitlistat när du skapar en programgateway med hjälp av en intern belastningsutjämnare (ILB) tillsammans med en App Service Environment (ASE) i serverdelen när du använder TLS från slutna till slutna enheter i Azure.

## <a name="symptoms"></a>Symtom

När du skapar en programgateway med hjälp av en ILB med en ASE i serverdelen kan backend-servern bli felaktig. Det här problemet uppstår om autentiseringscertifikatet för programgatewayen inte matchar det konfigurerade certifikatet på backend-servern. Se följande scenario som ett exempel:

**Konfiguration av programgateway:**

- **Lyssnare:** Flera webbplatser
- **Hamn:** 443
- **Värdnamn:** test.appgwtestase.com
- **SSL-certifikat:** CN=test.appgwtestase.com
- **Backend Pool:** IP-adress eller FQDN
- **IP-adress:**: 10.1.5.11
- **HTTP-inställningar:** HTTPS
- **Hamn:**: 443
- **Anpassad avsökning:** Värdnamn – test.appgwtestase.com
- **Autentiseringscertifikat:** .cer för test.appgwtestase.com
- **Backend Hälsa:** Felfritt – Servercertifikatet för server med server som inte är vitlistad med Application Gateway.

**ASE-konfiguration:**

- **ILB IP:** 10.1.5.11
- **Domännamn:** appgwtestase.com
- **App-tjänst:** test.appgwtestase.com
- **SSL-bindning:** SNI SSL – CN=test.appgwtestase.com

NÃ¤s nÃ¤nde nÃ¤s fiã¤ nÃ¤nde nÃ¤s gateway visas fÃ¶ndesÃ¶re nÃ¤1s fÃ¶1 visas fÃ¶ã¶¶ar av att serverdelsservern är felaktig

**502 – Webbservern fick ett ogiltigt svar när den agerade som en gateway- eller proxyserver.**

## <a name="solution"></a>Lösning

När du inte använder ett värdnamn för att komma åt en HTTPS-webbplats returnerar backend-servern det konfigurerade certifikatet på standardwebbplatsen om SNI är inaktiverat. För en ILB ASE kommer standardcertifikatet från ILB-certifikatet. Om det inte finns några konfigurerade certifikat för ILB kommer certifikatet från ASE-appcertifikatet.

När du använder ett fullständigt kvalificerat domännamn (FQDN) för att komma åt ILB returnerar backend-servern rätt certifikat som överförs i HTTP-inställningarna. Om så inte är fallet bör du överväga följande alternativ:

- Använd FQDN i backend-poolen i programgatewayen för att peka på IP-adressen för ILB. Det här alternativet fungerar bara om du har en privat DNS-zon eller en anpassad DNS konfigurerad. Annars måste du skapa en A-post för en offentlig DNS.

- Använd det överförda certifikatet på ILB eller standardcertifikatet (ILB-certifikatet) i HTTP-inställningarna. Programgatewayen hämtar certifikatet när den kommer åt ILB:s IP för avsökningen.

- Använd ett jokerteckencertifikat på ILB och backend-servern, så att certifikatet är vanligt för alla webbplatser. Denna lösning är dock endast möjlig när det gäller underdomäner och inte om var och en av webbplatserna kräver olika värdnamn.

- Avmarkera alternativet **Använd för app-tjänsten** för programgatewayen om du använder IP-adressen för ILB.

Om du vill minska omkostnaderna kan du ladda upp ILB-certifikatet i HTTP-inställningarna så att avsökningssökvägen fungerar. (Det här steget är bara för vitlistning. Det kommer inte att användas för TLS-kommunikation.) Du kan hämta ILB-certifikatet genom att komma åt ILB med dess IP-adress från din webbläsare på HTTPS och sedan exportera TLS/SSL-certifikatet i ett Base-64-kodat CER-format och ladda upp certifikatet på respektive HTTP-inställningar.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.
