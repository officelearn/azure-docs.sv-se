---
title: "Lägga till ett certifikat i arkivet för Java CA | Microsoft Docs"
description: "Lär dig hur du lägger till ett certifikat (certifikatutfärdare) certifikat Java CA (cacerts) certifikatarkivet för Twilio-tjänsten eller Azure Service Bus."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: b6e1a305e19415ab1c4b4c208dac98ad1e2689c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Lägga till ett certifikat i arkivet för Java CA-certifikat
Följande steg visar hur du lägger till ett certifikat (certifikatutfärdare) certifikat Java CA-certifikatarkivet (cacerts). Exempel som används är för CA-certifikat som krävs för Twilio-tjänsten. Informationen som ges senare i avsnittet beskrivs hur du installerar CA-certifikatet för Azure Service Bus. 

Du kan använda keytool för att lägga till CA-certifikatet innan komprimerar din JDK och lägga till din Azure-projekt **approot** mappen, eller om du kan köra en Azure uppstart aktivitet som använder keytool för att lägga till certifikatet. Det här exemplet förutsätter att du ska lägga till ett certifikat innan JDK som zippade. Även ett specifikt CA-certifikat ska användas i exemplet, men stegen för att få en annan certifikatutfärdare och importerar den till arkivet cacerts skulle vara liknande.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Lägga till ett certifikat i arkivet för cacerts
1. Vid en kommandotolk för administratörer som har angetts till din JDK **jdk\jre\lib\security** mapp, kör du följande för att se vilka certifikat som är installerade:
   
    `keytool -list -keystore cacerts`
   
    Du kan uppmanas att lagra lösenord. Standardlösenordet är **changeit**. (Om du vill ändra lösenordet finns i dokumentationen till keytool på <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.) Det här exemplet förutsätter att certifikatet med MD5 fingeravtryck 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 inte finns och att du vill importera det (det här specifika certifikatet krävs av tjänsten Twilio-API).
2. Hämta certifikatet från listan över certifikat som anges i [GeoTrust rotcertifikat](http://www.geotrust.com/resources/root-certificates/). Högerklicka på länken för certifikat med serienumret 35:DE:F4:CF och spara den till den **jdk\jre\lib\security** mapp. För det här exemplet har sparats till en fil med namnet **Equifax\_Secure\_certifikat\_Authority.cer**.
3. Importera certifikatet via följande kommando:
   
    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`
   
    När du uppmanas att lita på certifikat, om certifikatet har MD5 fingeravtryck 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, svara genom att skriva **y**.
4. Kör följande kommando för att se till att certifikatet som har importerats:
   
    `keytool -list -keystore cacerts`
5. ZIP-JDK och lägga till den i projektet Azure **approot** mapp.

Information om keytool finns <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Azure rotcertifikat
Ditt program som använder Azure-tjänster (till exempel Azure Service Bus) måste lita på Baltimore CyberTrust-rotcertifikatet. (Från 15 April 2013 Azure började migrera från GTE CyberTrust globala roten till Baltimore CyberTrust Root. Den här migreringen tog flera månader att slutföra.)

Baltimore certifikat kan vara installerad i din cacerts store, så Kom ihåg att köra den **keytool-lista** kommandot först för att se om det finns redan.

Om du behöver lägga till Baltimore CyberTrust Root har serienummer 02:00:00:b9 och SHA1 fingeravtryck d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Du kan hämta från <https://cacert.omniroot.com/bc2025.crt>, sparas i en lokal fil med tillägget **.cer**, och sedan importeras med **keytool** som ovan.

## <a name="next-steps"></a>Nästa steg
Mer information om rotcertifikat som används av Azure finns [Azure Root Certificate migrering](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Läs mer om Java [Azure för Java-utvecklare](/java/azure).

