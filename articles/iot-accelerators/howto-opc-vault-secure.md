---
title: Skydda kommunikationen av OPC UA-enheter med OPC Vault – Azure | Microsoft Docs
description: Så här registrerar du OPC UA-program och hur du utfärdar signerade program certifikat för dina OPC UA-enheter med OPC-valvet.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a819e0b5a0da92137451eedbf84fe06d22879568
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973784"
---
# <a name="how-to-use-the-opc-ua-certificate-management-service"></a>Så här använder du tjänsten OPC UA Certificate Management

Den här artikeln förklarar hur du registrerar program och hur du utfärdar signerade program certifikat för OPC UA-enheter.

## <a name="prerequisites"></a>Förutsättningar

### <a name="deploy-the-certificate-management-service"></a>Distribuera certifikat hanterings tjänsten

Först i alla måste tjänsten distribueras till Azure-molnet.
Hitta en artikel som beskriver hur du [distribuerar OPC-valvet certifikat hanterings tjänst](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Skapa certifikat utfärdarens CA-certifikat

Om du inte har gjort det ännu kan du skapa certifikat utfärdarens CA-certifikat.

Hitta en artikel som beskriver hur du [skapar och hanterar utfärdarens certifikat för OPC](howto-opc-vault-manage.md)-valvet.

## <a name="secure-opc-ua-applications"></a>Säkra OPC UA-program

### <a name="step-1-register-your-opc-ua-application"></a>Steg 1: Registrera ditt OPC UA-program 

> [!IMPORTANT]
> Rollen Writer krävs för att registrera ett program.

1. Öppna certifikat tjänsten på `https://myResourceGroup-app.azurewebsites.net` och logga in.
2. Gå till `Register New` sidan.
1. För en program registrering måste en användare ha minst rollen "skrivar" tilldelad.
2. Post formuläret följer namn konventioner i OPC UA World. I bilden nedan visas inställningarna för [OPC UA Reference Server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) som exempel i OPC UA. Netstandard-stack visas:

   ![Registrering av UA-referens Server](media/howto-opc-vault-secure/reference-server-registration.png "Registrering av UA-referens Server")

5. Tryck på `Register` knappen för att registrera programmet i databasen för certifikat tjänst program. Arbets flödet guidar användaren direkt till nästa steg för att begära ett signerat certifikat för programmet.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Steg 2: Skydda ditt program med ett CA-signerat program certifikat

Ett OPC UA-program kan skyddas genom att utfärda ett signerat certifikat baserat på en certifikat signerings förfrågan (CSR) eller genom att begära ett nytt nyckel par, som även innehåller en ny privat nyckel i PFX-eller PEM-format. Följ dokumentationen för din OPC UA-enhet på vilken metod som stöds för ditt program. I allmänhet rekommenderas CSR-metoden, eftersom den inte kräver att en privat nyckel överförs över en tråd.

- Om enheten kräver att ett nytt nyckel par utfärdas följer du [Step3a](##step-3a-request-a-new-certificate-with-a-new-keypair).
- Om enheten har stöd för att utfärda en certifikat signerings förfrågan, följer du [Step3b](#step-3b-request-a-new-certificate-with-a-csr).

### <a name="step-3a-request-a-new-certificate-with-a-new-keypair"></a>Steg 3a: Begär ett nytt certifikat med ett nytt nyckel par

1. Gå till `Applications` sidan.
3. Starta arbets flödet certifikatbegäran genom att välja `New Request` för ett program i listan.

   ![Begär nytt certifikat](media/howto-opc-vault-secure/request-new-certificate.png "Begär nytt certifikat")

3. Tryck på Begär nytt nyckel par och certifikat om du vill begära en privat nyckel och ett nytt signerat certifikat med den offentliga nyckeln för ditt program.

   ![Generera nytt nyckel par](media/howto-opc-vault-secure/generate-new-key-pair.png "Generera nytt nyckel par")

4. Fyll i formuläret med ett ämne, domän namnen och Välj PEM eller PFX med lösen ordet för den privata nyckeln. Tryck på `Generate New Certificate` knappen för att skapa en certifikatbegäran.

   ![Godkänn certifikat](media/howto-opc-vault-secure/approve-reject.png "Godkänn certifikat")

5. Godkännande steget kräver en användare med rollen "god kännare" och med signerings behörighet i Azure Key Vault. I det typiska arbets flödet ska rollen god kännare och beställare tilldelas olika användare.
6. Godkänn eller avvisa certifikatbegäran för att starta eller avbryta den faktiska skapandet av nyckel paret och signerings åtgärden. Det nya nyckel paret skapas och lagras på ett säkert sätt i Azure Key Vault tills det laddas ned av certifikat beställaren. Det resulterande certifikatet med offentlig nyckel signeras av CA: n. Det kan ta några sekunder att slutföra dessa åtgärder.

   ![Visa nyckel par](media/howto-opc-vault-secure/view-key-pair.png "Visa nyckel par")

7. Den resulterande privata nyckeln (PFX eller PEM) och Certificate (DER) kan hämtas härifrån i det format som valts som binär fil hämtning. En Base64-kodad version är också tillgänglig, till exempel för att kopiera klistra in certifikatet till en kommando rad eller text inmatning. 
8. När den privata nyckeln har hämtats och lagrats på ett säkert sätt kan den tas bort från tjänsten `Delete Private Key` med knappen. Certifikatet med den offentliga nyckeln är fortfarande tillgängligt för framtida bruk.
9. På grund av användningen av ett signerat certifikat från en certifikat utfärdare, ska CA-certifikatet och CRL: en även hämtas här.
10. Det beror nu på den OPC UA-enhet som används för att tillämpa det nya nyckel paret. Vanligt vis kopieras certifikat utfärdarens certifikat och CRL till en `trusted` mapp, medan den offentliga och privata nyckeln för program certifikatet används i en `own` mapp i certifikat arkivet. Vissa enheter kanske redan stöder ' Server-push ' för certifikat uppdateringar. Se dokumentationen för din OPC UA-enhet.

### <a name="step-3b-request-a-new-certificate-with-a-csr"></a>Steg 3B: Begär ett nytt certifikat med en CSR 

1. Gå till `Applications` sidan.
3. Starta arbets flödet certifikatbegäran genom att välja `New Request` för ett program i listan.

   ![Begär nytt certifikat](media/howto-opc-vault-secure/request-new-certificate.png "Begär nytt certifikat")

3. Tryck på Begär nytt certifikat med signerings förfrågan för att begära ett nytt signerat certifikat för ditt program.

   ![Skapa nytt certifikat](media/howto-opc-vault-secure/generate-new-certificate.png "Skapa nytt certifikat")

4. Ladda upp CSR genom att välja en lokal fil eller genom att klistra in en Base64-kodad CSR i formuläret. Tryck på `Generate New Certificate` knappen för att skapa en certifikatbegäran.

   ![Godkänn CSR](media/howto-opc-vault-secure/approve-reject-csr.png "Godkänn CSR")

5. Godkännande steget kräver en användare med rollen "god kännare" och med signerings behörighet i Azure Key Vault. Godkänn eller avvisa certifikatbegäran för att starta eller avbryta den faktiska signerings åtgärden. Det resulterande certifikatet med offentlig nyckel signeras av CA: n. Det kan ta några sekunder att slutföra den här åtgärden.

   ![Visa certifikat](media/howto-opc-vault-secure/view-cert-csr.png "Visa certifikat")

6. Det resulterande certifikatet (DER) kan laddas ned härifrån som en binär fil. En Base64-kodad version är också tillgänglig, till exempel för att kopiera klistra in certifikatet till en kommando rad eller text inmatning. 
10. När certifikatet har hämtats och lagrats på ett säkert sätt kan det tas bort från tjänsten med `Delete Certificate` knappen.
11. På grund av användningen av ett signerat certifikat från en certifikat utfärdare, ska CA-certifikatet och CRL: en även hämtas här.
12. Det beror nu på den OPC UA-enhet som används för att tillämpa det nya certifikatet. Vanligt vis kopieras certifikat utfärdarens certifikat och CRL till en `trusted` mapp, medan program certifikatet används i en `own` mapp i certifikat arkivet. Vissa enheter kanske redan stöder ' Server-push ' för certifikat uppdateringar. Se dokumentationen för din OPC UA-enhet.

### <a name="step-4-device-secured"></a>Steg 4: Skyddad enhet

OPC UA-enheten är nu redo att kommunicera med andra OPC UA-enheter som skyddas av CA-signerade certifikat utan ytterligare konfiguration. Ha det så kul!


## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skyddar OPC UA-enheter är du det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Köra en tjänst för säker certifikat hantering](howto-opc-vault-secure-ca.md)