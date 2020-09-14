---
title: Certifikat rotation för Azure Database for MariaDB
description: Lär dig mer om kommande ändringar av rot certifikat ändringar som påverkar Azure Database for MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: ba5a1a0eb61ca086c3cd1ea66acddc18e950871d
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057286"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>Förstå ändringarna i rot certifikat utfärdarens ändring för Azure Database for MariaDB

Azure Database for MariaDB kommer att ändra rot certifikatet för klient programmet/driv rutinen som är aktive rad med SSL, använda för att [ansluta till databas servern](concepts-connectivity-architecture.md). Rot certifikatet som för närvarande är tillgängligt upphör att gälla den 26 oktober 2020 (10/26/2020) som en del av standard underhåll och rekommenderade säkerhets metoder. Den här artikeln innehåller mer information om kommande ändringar, vilka resurser som kommer att påverkas och de steg som krävs för att säkerställa att programmet upprätthåller anslutningen till databas servern.

## <a name="what-update-is-going-to-happen"></a>Vilken uppdatering ska ske?

I vissa fall använder program en lokal certifikat fil som genererats från en betrodd certifikat utfärdare (CA) certifikat fil för att ansluta på ett säkert sätt. För närvarande kan kunder bara använda det fördefinierade certifikatet för att ansluta till en Azure Database for MariaDB-server som finns [här](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). [Certifikat utfärdare (ca) webbläsarens forum](https://cabforum.org/)   publicerade nyligen rapporter om flera certifikat som utfärdats av ca-leverantörer som icke-kompatibla.

Som enligt branschens krav på efterlevnad började CA-leverantörer återkallade CA-certifikat för icke-kompatibla ca: er, vilket kräver att servrar använder certifikat som utfärdats av kompatibla ca: er och signeras av CA-certifikat från dessa kompatibla certifikat utfärdare. Eftersom Azure Database for MariaDB för närvarande använder ett av dessa icke-kompatibla certifikat, som klient program använder för att verifiera sina SSL-anslutningar, måste vi se till att lämpliga åtgärder vidtas (beskrivs nedan) för att minimera den potentiella påverkan på dina MariaDB-servrar.


Det nya certifikatet kommer att användas från och med den 26 oktober 2020 (10/26/2020). Om du använder antingen CA-validering eller fullständig verifiering av Server certifikatet vid anslutning från en MySQL-klient (sslmode = verify-ca eller sslmode = verify-full) måste du uppdatera program konfigurationen före den 26 oktober 2020 (10/26/2020).

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Hur gör jag för att veta om min databas kommer att påverkas?

Alla program som använder SSL/TLS och kontrollerar att rot certifikatet måste uppdatera rot certifikatet för att kunna ansluta till Azure Database for MariaDB. Om du inte använder SSL/TLS för närvarande påverkas inte din program tillgänglighet. Du kan kontrol lera om klient programmet försöker använda SSL-läge med den fördefinierade betrodda certifikat utfärdaren (CA) [här](concepts-ssl-connection-security.md#default-settings).

Om du vill undvika att programmets tillgänglighet avbryts på grund av att certifikaten har återkallats, eller om du vill uppdatera ett certifikat som har återkallats, kan du läsa avsnittet [**Vad behöver jag för att underhålla anslutningen**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Vad behöver jag för att underhålla anslutningen

Om du vill undvika att programmets tillgänglighet avbryts på grund av att certifikaten har återkallats, eller om du vill uppdatera ett certifikat som har återkallats, följer du stegen nedan:

*   Hämta **BaltimoreCyberTrustRoot**  &  **DigiCertGlobalRootG2** ca från länkarna nedan:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Generera ett kombinerat CA-certifikatarkiv med både **BaltimoreCyberTrustRoot** -och **DigiCertGlobalRootG2** -certifikat ingår.
    *   För Java-användare (MariaDB Connector/J) kör du:

          ```azurecli-interactive
          keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Ersätt sedan den ursprungliga nyckel lagrings filen med den nya som genererade:
        *   System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file"); 
        *   System. setProperty ("javax. net. SSL. trustStorePassword", "lösen ord");
    *   För .NET-användare (MariaDB Connector/NET, MariaDBConnector) ser du till att **BaltimoreCyberTrustRoot** och **DigiCertGlobalRootG2** båda finns i Windows certifikat Arkiv, betrodda rot certifikat utfärdare. Importera certifikatet som saknas om det inte finns några certifikat.

        ![Azure Database for MariaDB .net-certifikat](media/overview/netconnecter-cert.png)

    *   För .NET-användare på Linux med hjälp av SSL_CERT_DIR kontrollerar du att **BaltimoreCyberTrustRoot** och **DigiCertGlobalRootG2** båda finns i katalogen som anges av SSL_CERT_DIR. Om det inte finns några certifikat, skapar du den certifikat fil som saknas.

    *   För andra (MariaDB-klient/MariaDB Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift)-användare kan du slå samman två CA-certifikatfiler som detta format nedan</b>

        </br>-----BEGIN CERTIFICATE-----
 </br>(Rot-CA1: BaltimoreCyberTrustRoot. CRT. pem)
 </br>-----SLUT CERTIFIKAT-----
 </br>-----BEGIN CERTIFICATE-----
 </br>(Rot-CA2: DigiCertGlobalRootG2. CRT. pem)
 </br>-----SLUT CERTIFIKAT-----

*   Ersätt den ursprungliga PEM-filen för rot certifikat utfärdaren med den kombinerade rot-CA-filen och starta om programmet/klienten.
*   I framtiden kan du efter det nya certifikatet som distribuerats på Server sidan Ändra CA PEM-filen till DigiCertGlobalRootG2. CRT. pem.

## <a name="what-can-be-the-impact"></a>Vad kan vara påverkan på?
Om du använder det Azure Database for MariaDB utfärdade certifikatet som dokumenteras här kan programmets tillgänglighet avbrytas eftersom databasen inte går att komma åt. Beroende på ditt program kan du få en rad fel meddelanden, inklusive men inte begränsat till:
*   Ogiltigt certifikat/återkallat certifikat
*   Anslutningens tidsgräns uppnåddes
*   Fel om tillämpligt

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. om jag inte använder SSL/TLS måste jag fortfarande uppdatera rot certifikat utfärdaren?
Inga åtgärder krävs om du inte använder SSL/TLS. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. om jag använder SSL/TLS måste jag starta om min databas server för att uppdatera rot certifikat utfärdaren?
Nej, du behöver inte starta om databas servern för att börja använda det nya certifikatet. Detta är en ändring på klient sidan och inkommande klient anslutningar måste använda det nya certifikatet för att säkerställa att de kan ansluta till databas servern.

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020-10262020"></a>3. Vad händer om jag inte uppdaterar rot certifikatet före den 26 oktober 2020 (10/26/2020)?
Om du inte uppdaterar rot certifikatet före den 30 november 2020 kommer dina program som ansluter via SSL/TLS och verifiering för rot certifikatet inte att kunna kommunicera med MariaDB-databas servern och programmet kommer att uppleva anslutnings problem till MariaDB-databasservern.

### <a name="4-do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>4. måste jag planera ett underhålls avbrott för den här ändringen?<BR>
Nej. Eftersom ändringen bara finns på klient sidan för att ansluta till databas servern, finns det inga underhålls avbrott som krävs för den här ändringen.

### <a name="5--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020-10262020"></a>5. Vad händer om jag inte kan få en schemalagd stillestånds tid för den här ändringen före den 26 oktober 2020 (10/26/2020)?
Eftersom klienterna som används för att ansluta till servern måste uppdatera certifikat informationen enligt beskrivningen i avsnittet åtgärda [här](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity), behöver vi inte något drift stopp för-servern i det här fallet.

###  <a name="6-if-i-create-a-new-server-after-nov-30th-will-i-be-impacted"></a>6. om jag skapar en ny server efter den 30 november kommer jag att påverkas?
För servrar som skapats efter den 26 oktober 2020 (10/26/2020) kan du använda det nyligen utfärdade certifikatet för dina program för att ansluta med SSL.

### <a name="7-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>7. hur ofta uppdaterar Microsoft sina certifikat eller vad är förfallo principen?
Dessa certifikat som används av Azure Database for MariaDB tillhandahålls av betrodda certifikat utfärdare (CA). Stöd för dessa certifikat på Azure Database for MariaDB är knutet till stöd för dessa certifikat av CA. Men i så fall kan det vara oförutsedda buggar i dessa fördefinierade certifikat, vilket måste åtgärdas tidigast.

### <a name="8-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>8. om jag använder Läs repliker behöver jag bara utföra den här uppdateringen på huvud servern eller alla Läs repliker?
Eftersom den här uppdateringen är en ändring på klient sidan, om klienten som används för att läsa data från replik servern, måste du även tillämpa ändringarna för dessa klienter. 

### <a name="9-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>9. har vi en fråga på Server sidan för att kontrol lera om SSL används?
För att kontrol lera om du använder SSL-anslutning för att ansluta till servern, se [SSL-verifiering](howto-configure-ssl.md#verify-the-ssl-connection).

### <a name="10-what-if-i-have-further-questions"></a>10. Vad händer om jag har fler frågor?
Om du har frågor kan du få svar från community-experter i [Microsoft Q&A](mailto:AzureDatabaseformariadb@service.microsoft.com). [Kontakta oss](mailto:AzureDatabaseformariadb@service.microsoft.com) om du har en Support plan och behöver teknisk hjälp
