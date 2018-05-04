---
title: Lägga till ett certifikat i arkivet för Java-Certifikatutfärdare
description: Lär dig hur du lägger till ett certifikat (certifikatutfärdare) certifikat Java CA (cacerts) certifikatarkivet för Twilio-tjänsten eller Azure Service Bus.
services: ''
documentationcenter: java
author: rmcmurray
manager: mbaldwin
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: 1c432aa9da9637675262313d935edd38bd7b698b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Lägga till ett certifikat i arkivet för Java CA-certifikat
Följande steg visar hur du lägger till ett certifikat (certifikatutfärdare) certifikat Java CA-certifikatarkivet (cacerts). Exempel som används är för CA-certifikat som krävs för Twilio-tjänsten. Informationen som ges senare i avsnittet beskrivs hur du installerar CA-certifikatet för Azure Service Bus. 

Du kan använda keytool för att lägga till CA-certifikatet innan komprimerar din JDK och lägga till din Azure-projekt **approot** mappen, eller om du kan köra en Azure uppstart aktivitet som använder keytool för att lägga till certifikatet. (Det här exemplet förutsätter att du lägger till en CA-certifikat innan komprimerar din JDK.) Även ett specifikt CA-certifikat används i exemplet, men stegen för att få en annan certifikatutfärdare och importerar den till arkivet cacerts skulle vara liknande.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Lägga till ett certifikat i arkivet för cacerts
1. Vid en kommandotolk för administratörer som har angetts till din JDK **jdk\jre\lib\security** mapp, kör du följande för att se vilka certifikat som är installerade:
   
    `keytool -list -keystore cacerts`
   
    Du ombeds ange store-lösenord. Standardlösenordet är **changeit**. (Om du vill ändra lösenordet finns i dokumentationen till keytool på <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.) Det här exemplet förutsätter att certifikatet med MD5 fingeravtryck 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 inte finns och att du vill importera det (det här specifika certifikatet krävs av Twilio API-tjänsten).
2. Hämta certifikatet från listan över certifikat som anges i [GeoTrust rotcertifikat](http://www.geotrust.com/resources/root-certificates/). Högerklicka på länken för certifikat med serienumret 35:DE:F4:CF och spara den till den **jdk\jre\lib\security** mapp. För det här exemplet har sparats till en fil med namnet **Equifax\_Secure\_certifikat\_Authority.cer**.
3. Importera certifikatet via följande kommando:
   
    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`
   
    Om du uppmanas att lita på certifikat med ett fingeravtryck MD5 av 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, svara genom att skriva **y**.
4. Kör följande kommando för att se till att certifikatet som har importerats:
   
    `keytool -list -keystore cacerts`
5. ZIP-JDK och lägga till den i projektet Azure **approot** mapp.

Information om keytool finns <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Azure rotcertifikat
Ditt program som använder Azure-tjänster (till exempel Azure Service Bus) måste lita på Baltimore CyberTrust-rotcertifikatet. (Från 15 April 2013 Azure började migrera från GTE CyberTrust globala roten till Baltimore CyberTrust Root. Den här migreringen tog flera månader att slutföra.)

Baltimore certifikat kan vara installerad i din cacerts store, så Kom ihåg att köra den **keytool-lista** kommandot först för att se om det finns redan.

Om du behöver lägga till Baltimore CyberTrust Root har serienummer 02:00:00:b9 och SHA1 fingeravtryck d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Du kan hämta från <https://cacert.omniroot.com/bc2025.crt>, sparas i en lokal fil med tillägget **.cer**, och sedan importeras med **keytool** som visas i föregående steg.

## <a name="next-steps"></a>Nästa steg
Mer information om rotcertifikat som används av Azure finns [Azure Root Certificate migrering](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Läs mer om Java [Azure för Java-utvecklare](/java/azure).

