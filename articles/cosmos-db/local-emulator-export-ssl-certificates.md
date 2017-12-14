---
title: Exportera Azure Cosmos DB emulatorn certifikat | Microsoft Docs
description: "När du utvecklar i språk och körningar som inte använder Windows certifikatarkiv måste du exportera och hantera SSL-certifikat. Den här posten ger steg för steg-instruktioner."
services: cosmos-db
documentationcenter: 
keywords: Azure Cosmos DB-emulatorn
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36a2f99b9d5ea4b9e6b313fed79cf7f4dd0e7057
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Exportera Azure Cosmos DB emulatorn certifikat för användning med Java, Python och Node.js

[**Hämta emulatorn**](https://aka.ms/cosmosdb-emulator)

Azure-emulatorn Cosmos DB tillhandahåller en lokal miljö som emulerar Azure DB som Cosmos-tjänsten för utveckling, inklusive dess användning av SSL-anslutningar. Det här exemplet visar hur du exportera SSL-certifikat för användning i språk och körningar som inte integreras med Windows certifikatarkiv, till exempel Java som använder sin egen [certifikatarkiv](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) och Python som använder [socket omslutningar](https://docs.python.org/2/library/ssl.html) och Node.js som använder [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Du kan läsa mer om emulatorn i [Använd Azure Cosmos DB-emulatorn för utveckling och testning](./local-emulator.md).

Den här kursen ingår följande uppgifter:

> [!div class="checklist"]
> * Rotera certifikat
> * Exportera SSL-certifikat
> * Lär dig hur du använder certifikat i Java, Python och Node.js

## <a name="certification-rotation"></a>Certifikatutfärdare rotation

Certifikat i Azure Cosmos DB lokala emulatorn skapas första gången emulatorn körs. Det finns två certifikat. En som används för att ansluta till den lokala emulatorn och ett för att hantera hemligheter i emulatorn. Certifikatet som du vill exportera är anslutningscertifikatet med namnet ”DocumentDBEmulatorCertificate”.

Båda certifikaten kan återskapas genom att klicka på **återställa Data** enligt nedan från Azure Cosmos DB-emulatorn som körs i Windows-enheten. Om du återskapa certifikatet och har installerat dem till certifikatarkivet Java eller använda dem någon annanstans måste du uppdatera dem, annars ditt program inte längre att ansluta till den lokala emulatorn.

![Återställ Azure lokala Cosmos-DB-emulatorn](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-ssl-certificate"></a>Så här exporterar du Azure Cosmos DB SSL-certifikatet

1. Starta Windows Certifikathanteraren genom att köra certlm.msc och navigera till mappen-> personliga certifikat och öppna certifikatet med namnet **DocumentDbEmulatorCertificate**.

    ![Azure DB Cosmos lokala emulatorn exportera steg 1](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Klicka på **information** sedan **OK**.

    ![Azure DB Cosmos lokala emulatorn exportera steg 2](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Klicka på **kopiera till fil...** .

    ![Azure DB Cosmos lokala emulatorn exportera steg 3](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Klicka på **Nästa**.

    ![Azure DB Cosmos lokala emulatorn exportera steg 4](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Klicka på **Nej, exportera inte privat nyckel**, klicka på **nästa**.

    ![Azure DB Cosmos lokala emulatorn exportera steg 5](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Klicka på **Base64-kodad X.509 (. CER)** och sedan **nästa**.

    ![Azure DB Cosmos lokala emulatorn exportera steg 6](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Namnge certifikatet. I det här fallet **documentdbemulatorcert** och klicka sedan på **nästa**.

    ![Azure DB Cosmos lokala emulatorn exportera steg 7](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Klicka på **Slutför**.

    ![Azure DB Cosmos lokala emulatorn exportera steg 8](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Hur du använder certifikat i Java

När du kör Java-program eller MongoDB-program som använder Java-klienten är det lättare att installera certifikatet i certifikatarkivet Java standard än skicka den ”-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword = ”<password>” flaggor. Till exempel ingår [Java Demo programmet](https://localhost:8081/_explorer/index.html) beror på standardcertifikatlagringsplatsen.

Följ instruktionerna i den [att lägga till ett certifikat i Java Certifikatutfärdarens certifikatarkivet](https://docs.microsoft.com/azure/java-add-certificate-ca-store) importera X.509-certifikatet till certifikatarkivet standard Java. Kom kommer ihåg att du att arbeta i katalogen % JAVA_HOME % när du kör keytool.

En gång i ”CosmosDBEmulatorCertificate” SSL-certifikatet har installerats ditt program ska kunna ansluta och använda lokala Azure Cosmos DB-emulatorn. Om du fortfarande har problem kan du vill följa den [felsökning SSL/TLS-anslutningar](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) artikel. Det är mycket troligt att certifikatet inte har installerats i arkivet för %JAVA_HOME%/jre/lib/security/cacerts. För exempel om du har flera installerade versioner av Java ditt program kanske använder en annan cacerts store än det du uppdaterade.

## <a name="how-to-use-the-certificate-in-python"></a>Hur du använder certifikatet i Python

Som standard den [Python SDK(version 2.0.0 or higher)](documentdb-sdk-python.md) för SQL-API som inte kommer försök och använda SSL-certifikatet när du ansluter till lokala emulatorn. Om du kan följa exemplen i men du vill använda SSL-verifiering av [Python socket omslutningar](https://docs.python.org/2/library/ssl.html) dokumentation.

## <a name="how-to-use-the-certificate-in-nodejs"></a>Hur du använder certifikatet i Node.js

Som standard den [Node.js SDK(version 1.10.1 or higher)](documentdb-sdk-node.md) för SQL-API som inte kommer försök och använda SSL-certifikatet när du ansluter till lokala emulatorn. Om du kan följa exemplen i men du vill använda SSL-verifiering av [Node.js dokumentationen](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer du har gjort följande:

> [!div class="checklist"]
> * Roterade certifikat
> * Exportera SSL-certifikatet
> * Lärt dig hur du använder certifikat i Java, Python och Node.js

Du kan nu fortsätta att skapa en Azure Functions HTTP-utlösare med en Azure Cosmos DB indatabindning vägledning.

> [!div class="nextstepaction"]
> [Skapa en Azure-funktion med indata från Azure Cosmos DB](tutorial-functions-http-trigger.md) 
