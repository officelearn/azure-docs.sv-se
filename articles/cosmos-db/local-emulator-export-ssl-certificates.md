---
title: Exportera Azure Cosmos DB emulatorcertifikat
description: När du utvecklar i språk och körningar som inte använder Windows certifikat arkiv måste du exportera och hantera TLS/SSL-certifikaten. Det här inlägget innehåller stegvisa instruktioner.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.openlocfilehash: c72dbf24df850d8b0f7e5f26a873b78f5664c9e0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200946"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Exportera Azure Cosmos DB emulatorcertifikat för användning med Java, Python och Node.js

[**Ladda ned emulatorn**](https://aka.ms/cosmosdb-emulator)

Azure Cosmos DB emulatorn tillhandahåller en lokal miljö som emulerar tjänsten Azure Cosmos DB för utveckling, inklusive användning av TLS-anslutningar. Det här inlägget visar hur du exporterar TLS/SSL-certifikat för användning i språk och körningar som inte integreras med Windows-certifikatarkivet, t. ex. java, som använder ett eget [certifikat Arkiv](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) och python som använder [socket-omslag](https://docs.python.org/2/library/ssl.html) och Node. js som använder [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Du kan läsa mer om emulatorn i [Använda Azure Cosmos DB-emulatorn för utveckling och testning](./local-emulator.md).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Rotera certifikat
> * Exportera TLS/SSL-certifikat
> * Lär dig hur du använder certifikat i Java, Python och Node.js

## <a name="certification-rotation"></a>Rotation av certifikat

Certifikat i den lokala Azure Cosmos DB-emulatorn skapas första gången emulatorn körs. Det finns två certifikat. Ett för att ansluta till den lokala emulatorn och ett för att hantera hemligheter i emulatorn. Det certifikat som du ska exportera är anslutningscertifikatet med namnet "DocumentDBEmulatorCertificate".

Du kan återskapa båda certifikaten genom att klicka på **Återställ data** enligt nedan från den Azure Cosmos DB-emulator som körs i systemfältet i Windows. Om du återskapar certifikaten och har installerat dem i Java-certifikatarkivet eller använt dem någon annanstans måste du uppdatera dem, annars kommer ditt program inte längre att ansluta till den lokala emulatorn.

![Återställa data för den lokala Azure Cosmos DB-emulatorn](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Så här exporterar du Azure Cosmos DB TLS/SSL-certifikat

1. Starta certifikathanteraren i Windows genom att köra certlm.msc och gå till mappen Personligt -> Certifikat. Öppna certifikatet med namnet **DocumentDbEmulatorCertificate**.

    ![Steg 1 för att exportera den lokala Azure Cosmos DB-emulatorn](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Klicka på **Detaljer** och sedan **OK**.

    ![Steg 2 för att exportera den lokala Azure Cosmos DB-emulatorn](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Klicka på **Kopiera till fil...**.

    ![Steg 3 för att exportera den lokala Azure Cosmos DB-emulatorn](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Klicka på **Nästa**.

    ![Steg 4 för att exportera den lokala Azure Cosmos DB-emulatorn](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Klicka på **Nej, exportera inte den privata nyckeln** och sedan på **Nästa**.

    ![Steg 5 för att exportera den lokala Azure Cosmos DB-emulatorn](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Klicka på **Base-64 encoded X.509 (.CER)** och sedan på **Nästa**.

    ![Steg 6 för att exportera den lokala Azure DB Cosmos DB-emulatorn](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Ge certifikatet ett namn. I det här fallet är det **documentdbemulatorcert**. Klicka sedan på **Nästa**.

    ![Steg 7 för att exportera den lokala Azure DB Cosmos DB-emulatorn](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Klicka på **Slutför**.

    ![Steg 8 för att exportera den lokala Azure DB Cosmos DB-emulatorn](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Så här använder du certifikatet i Java

När du kör Java-program eller MongoDB-program som använder Java-klienten är det enklare att installera certifikatet i Java standard certifikat arkivet än att `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` skicka flaggorna. Till exempel är det inkluderade java demo`https://localhost:8081/_explorer/index.html`programmet () beroende av standard certifikat arkivet.

Följ instruktionerna i avsnittet [om hur du lägger till ett certifikat i Java CA certifikatarkiv](https://docs.microsoft.com/azure/java-add-certificate-ca-store) för att importera X.509-certifikatet till standardcertifikatarkivet för Java. Observera att du kommer att arbeta i katalogen %JAVA_HOME% när du kör nyckelverktyget.

När TLS/SSL-certifikatet "CosmosDBEmulatorCertificate" har installerats ska programmet kunna ansluta och använda den lokala Azure Cosmos DB-emulatorn. Om du fortfarande har problem kan du leta efter en lösning i artikeln om [felsökning för SSL/TLS-anslutningar](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). Antagligen har inte certifikatet installerats i certifikatarkivet %JAVA_HOME%/jre/lib/security/cacerts. Om du t.ex. har flera installerade versioner av Java kanske ditt program använder ett annat cacerts-certifikatarkiv än det du uppdaterade.

## <a name="how-to-use-the-certificate-in-python"></a>Så här använder du certifikatet i Python

Som standard försöker [python SDK (version 2.0.0 eller högre)](sql-api-sdk-python.md) för SQL API inte att försöka använda TLS/SSL-certifikatet vid anslutning till den lokala emulatorn. Om du däremot vill använda TLS-verifiering kan du följa exemplen i dokumentationen för [python socket-omslutningar](https://docs.python.org/2/library/ssl.html) .

## <a name="how-to-use-the-certificate-in-nodejs"></a>Så här använder du certifikat i Node.js

Som standard försöker [Node. js SDK (version 1.10.1 eller senare)](sql-api-sdk-node.md) för SQL API inte att försöka använda TLS/SSL-certifikatet vid anslutning till den lokala emulatorn. Om du däremot vill använda TLS-verifiering kan du följa exemplen i [Node. js-dokumentationen](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Roterat certifikat
> * Exportera TLS/SSL-certifikatet
> * Du har lärt dig hur du använder certifikat i Java, Python och Node.js

Om du vill lära dig mer om Azure Cosmos DB fortsätter du till avsnittet om begrepp. 

> [!div class="nextstepaction"]
>[Justerbara datakonsekvensnivåer i Azure Cosmos DB](../cosmos-db/consistency-levels.md)
