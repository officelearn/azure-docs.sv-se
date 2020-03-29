---
title: Skydda kommunikationen mellan OPC UA-enheter med OPC Vault – Azure | Microsoft-dokument
description: Så här registrerar du OPC UA-program och hur du utfärdar signerade programcertifikat för dina OPC UA-enheter med OPC Vault.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: af5e511cbf273bc4e4fa0a08d089a955426fe75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75454199"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Använda tjänsten för hantering av OPC Vault-certifikat

I den här artikeln beskrivs hur du registrerar program och hur du utfärdar signerade programcertifikat för dina OPC UA-enheter.

## <a name="prerequisites"></a>Krav

### <a name="deploy-the-certificate-management-service"></a>Distribuera certifikathanteringstjänsten

Distribuera först tjänsten till Azure-molnet. Mer information finns [i Distribuera certifikathanteringstjänsten OPC Vault](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Skapa certifikatet utfärdare av certifikatutfärdare

Om du inte har gjort det ännu skapar du certifikatet utfärdare. Mer information finns i [Skapa och hantera Utfärdarcertifikatet för OPC Vault](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Säkra OPC UA-program

### <a name="step-1-register-your-opc-ua-application"></a>Steg 1: Registrera din OPC UA-applikation 

> [!IMPORTANT]
> Rollen Författare krävs för att registrera ett program.

1. Öppna certifikattjänsten `https://myResourceGroup-app.azurewebsites.net`på och logga in.
2. Gå till **Registrera ny**. För en programregistrering måste en användare ha minst rollen Writer tilldelad.
2. I anmälningsformuläret följer namnkonventioner i OPC UA. I följande skärmbild visas till exempel inställningarna för [OPC UA Reference Server-exemplet](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) i OPC UA .NET Standard-stacken:

   ![Skärmbild av registrering av UA-referensserver](media/howto-opc-vault-secure/reference-server-registration.png "Registrering av UA-referensserver")

5. Välj **Registrera om** du vill registrera programmet i certifikattjänstprogramdatabasen. Arbetsflödet guidar direkt användaren till nästa steg för att begära ett signerat certifikat för programmet.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Steg 2: Skydda ditt program med ett certifikatutfärdare signerat programcertifikat

Skydda ditt OPC UA-program genom att utfärda ett signerat certifikat baserat på en begäran om certifikatsignering (CSR). Alternativt kan du begära ett nytt nyckelpar, som innehåller en ny privat nyckel i PFX- eller PEM-format. Information om vilken metod som stöds för ditt program finns i dokumentationen för din OPC UA-enhet. I allmänhet rekommenderas CSR-metoden, eftersom den inte kräver att en privat nyckel överförs över en tråd.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Begär ett nytt certifikat med ett nytt nyckelpar

1. Gå till **Program**.
3. Välj **Ny begäran** för ett listat program.

   ![Skärmbild av begär nytt certifikat](media/howto-opc-vault-secure/request-new-certificate.png "Begär nytt certifikat")

3. Välj **Begär nytt KeyPair och certifikat om** du vill begära en privat nyckel och ett nytt signerat certifikat med den offentliga nyckeln för ditt program.

   ![Skärmbild av Generera ett nytt KeyPair och -certifikat](media/howto-opc-vault-secure/generate-new-key-pair.png "Generera nytt nyckelpar")

4. Fyll i formuläret med ett ämne och domännamnen. För den privata nyckeln väljer du PEM eller PFX med lösenord. Välj **Generera nytt KeyPair** för att skapa certifikatbegäran.

   ![Skärmbild av information om visa certifikatbegäran](media/howto-opc-vault-secure/approve-reject.png "Godkänna certifikat")

5. Godkännande kräver en användare med rollen Godkännare och med signeringsbehörigheter i Azure Key Vault. I det vanliga arbetsflödet bör rollerna Godkännare och Beställare tilldelas olika användare. Välj **Godkänn** eller **Avvisa** om du vill starta eller avbryta det faktiska skapandet av nyckelparet och signeringsåtgärden. Det nya nyckelparet skapas och lagras säkert i Azure Key Vault, tills det hämtas av certifikatutfärdaren. Det resulterande certifikatet med offentlig nyckel signeras av certifikatutfärdaren. Dessa åtgärder kan ta några sekunder att slutföra.

   ![Skärmbild av Visa information om certifikatbegäran, med godkännandemeddelande längst ned](media/howto-opc-vault-secure/view-key-pair.png "Visa nyckelpar")

7. Den resulterande privata nyckeln (PFX eller PEM) och certifikat (DER) kan laddas ner härifrån i det format som valts som binär filhämtning. En base64-kodad version är också tillgänglig, till exempel för att kopiera och klistra in certifikatet på en kommandorad eller textpost. 
8. När den privata nyckeln har hämtats och lagrats på ett säkert sätt kan du välja **Ta bort privat nyckel**. Certifikatet med den offentliga nyckeln är fortfarande tillgängligt för framtida bruk.
9. På grund av användningen av ett certifikatutfärdarsignerat certifikat bör certifikatlistan för certifikatutfärdare och återkallade certifikat (CRL) också hämtas här.

Nu beror det på OPC UA-enheten hur man använder det nya nyckelparet. Certifikatutfärdarcertifikatet och certifikatutfärdaren `trusted` kopieras vanligtvis till en mapp, medan de `own` offentliga och privata nycklarna till programcertifikatet tillämpas på en mapp i certifikatarkivet. Vissa enheter kanske redan stöder server push för certifikatuppdateringar. Se dokumentationen för din OPC UA-enhet.

#### <a name="request-a-new-certificate-with-a-csr"></a>Begära ett nytt certifikat med en kundtjänstrepresentant 

1. Gå till **Program**.
3. Välj **Ny begäran** för ett listat program.

   ![Skärmbild av begär nytt certifikat](media/howto-opc-vault-secure/request-new-certificate.png "Begär nytt certifikat")

3. Välj **Begär nytt certifikat med signeringsbegäran om** du vill begära ett nytt signerat certifikat för ditt program.

   ![Skärmbild av Generera ett nytt certifikat](media/howto-opc-vault-secure/generate-new-certificate.png "Generera nytt certifikat")

4. Ladda upp CSR genom att välja en lokal fil eller genom att klistra in en base64-kodad CSR i formuläret . Välj **Generera nytt certifikat**.

   ![Skärmbild av information om visa certifikatbegäran](media/howto-opc-vault-secure/approve-reject-csr.png "Godkänna kundtjänstrepresentant")

5. Godkännande kräver en användare med rollen Godkännare och med signeringsbehörigheter i Azure Key Vault. Välj **Godkänn** eller **Avvisa** om du vill starta eller avbryta den faktiska signeringsåtgärden. Det resulterande certifikatet med offentlig nyckel signeras av certifikatutfärdaren. Den här åtgärden kan ta några sekunder att slutföra.

   ![Skärmbild av Visa information om certifikatbegäran, med godkännandemeddelande längst ned](media/howto-opc-vault-secure/view-cert-csr.png "Visa certifikat")

6. Det resulterande certifikatet (DER) kan laddas ner härifrån som binär fil. En base64-kodad version är också tillgänglig, till exempel för att kopiera och klistra in certifikatet på en kommandorad eller textpost. 
10. När certifikatet har hämtats och lagrats på ett säkert sätt kan du välja **Ta bort certifikat**.
11. På grund av användningen av ett certifikatutfärdarsignerat certifikat bör certifikatutfärdaren och referenslaboratoriet också hämtas här.

Nu beror det på OPC UA-enheten hur du använder det nya certifikatet. Certifikatutfärdarcertifikatet och certifikatutfärdaren `trusted` kopieras vanligtvis till en mapp, medan programcertifikatet tillämpas på en `own` mapp i certifikatarkivet. Vissa enheter kanske redan stöder server push för certifikatuppdateringar. Se dokumentationen för din OPC UA-enhet.

### <a name="step-3-device-secured"></a>Steg 3: Enheten säkrad

OPC UA-enheten är nu redo att kommunicera med andra OPC UA-enheter som skyddas av certifikatutfärdare signerade certifikat, utan ytterligare konfiguration.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att säkra OPC UA-enheter kan du:

> [!div class="nextstepaction"]
> [Köra en säker certifikathanteringstjänst](howto-opc-vault-secure-ca.md)
