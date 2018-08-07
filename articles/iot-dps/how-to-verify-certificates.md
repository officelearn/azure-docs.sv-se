---
title: Hur du gör bevis tillgång för X.509 CA-certifikat med Azure IoT Hub Device Provisioning-tjänsten | Microsoft Docs
description: Så här verifierar du X.509 CA-certifikat med DPS-tjänsten
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 709a53c0fc0814262cd7ce8bb0398be82ef81479
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526855"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Hur du gör bevis tillgång för X.509 CA-certifikat med Device Provisioning-tjänsten

En verifierad X.509 certifikatutfärdaren (CA) certifikatet är ett CA-certifikat som har laddats upp och registrerad på din etablering tjänst och har gått igenom bevis tillgång med tjänsten. 

Bevis på tillgång omfattar följande steg:
1. Få en unik verifieringskoden som genereras av etableringstjänsten för ditt X.509 CA-certifikat. Du kan göra detta från Azure-portalen.
2. Skapa ett X.509-certifikat för verifiering med verifieringskoden som dess ämne och registrera certifikatet med den privata nyckeln som är associerade med X.509 CA-certifikat.
3. Ladda upp signerad verifieringscertifikatet till tjänsten. Tjänsten verifierar verifieringscertifikatet med hjälp av den offentliga delen av CA-certifikatet verifieras, alltså bekräfta att du är tillgång CA-certifikatets privata nyckel.

Verifierat certifikat spelar en viktig roll när med registreringsgrupper. Verifiera att du äger certifikat ger ett extra säkerhetslager genom att se till att överföring för certifikatet har tillgång av certifikatets privata nyckel. Verifieringen förhindrar en skadliga aktörer kontroll trafiken från Extrahera ett mellanliggande certifikat och använda certifikatet för att skapa en grupp för registrering i sina egna etableringstjänsten effektivt kapar dina enheter. Genom att bevisa ägarskapet för roten eller ett mellanliggande certifikat i en certifikatkedja, du bevisar att du har behörighet att skapa lövmedlemmar certifikat för de enheter som ska registreras som en del av den gruppen för registrering. För därför, rot- eller mellanliggande certifikat som konfigurerats i en grupp för registrering måste vara ett verifierat certifikat eller måste presenterar samlar upp till ett verifierat certifikat i certifikatkedjan en enhet när den autentiserar med tjänsten. Läs mer om registrering av grupper i [X.509-certifikat](concepts-security.md#x509-certificates) och [Kontrollera Enhetsåtkomst till etableringstjänsten med X.509-certifikat](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registrera den offentliga delen av ett X.509-certifikat och hämta en Verifieringskod

Följ dessa steg för att registrera ett CA-certifikat till din etableringstjänst och få en Verifieringskod som du kan använda under bevis på tillgång. 

1. Navigera till etableringstjänsten i Azure-portalen och öppna **certifikat** på den vänstra menyn. 
2. Klicka på **Lägg till** att lägga till ett nytt certifikat.
3. Ange ett eget namn för certifikatet. Bläddra till CER- eller .pem-fil som representerar den offentliga delen av X.509-certifikat. Klicka på **Överför**.
4. När du får ett meddelande om att ditt certifikat har laddats upp, klickar du på **spara**.

    ![Överför certifikat](./media/how-to-verify-certificates/add-new-cert.png)  

   Ditt certifikat visas i den **Certifikatutforskare** lista. Observera att den **STATUS** av det här certifikatet är *inte verifierad*.

5. Klicka på det certifikat som du lade till i föregående steg.

6. I **certifikatinformation**, klickar du på **generera Verifieringskod**.

7. Etableringstjänsten skapar en **Verifieringskod** som du kan använda för att verifiera ägarskapet för certifikatet. Kopiera koden till Urklipp. 

   ![Verifiera certifikat](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Signera verifieringskoden som du skapar ett verifieringscertifikat

Nu kan du behöva logga in på *Verifieringskod* med den privata nyckeln som är associerade med X.509 CA-certifikat, vilket genererar en signatur. Detta kallas [bevis på tillgång](https://tools.ietf.org/html/rfc5280#section-3.1) och resultat i en signerad verifieringscertifikatet.

Microsoft tillhandahåller verktyg och exempel som kan hjälpa dig att skapa ett signerat verifieringscertifikat: 

- Den **Azure IoT Hub C SDK** innehåller PowerShell (Windows) och Bash (Linux)-skript för att skapa certifikat för Certifikatutfärdaren och löv för utveckling och utföra med en Verifieringskod proof-för-tillgång. Du kan ladda ned den [filer](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) i systemet till en arbetsmapp och följ instruktionerna i den [hantera CA-certifikat readme](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) att utföra bevis på tillgång på ett CA-certifikat. 
- Den **Azure IoT Hub C# SDK** innehåller den [exemplet för verifiering av certifikat i grupp](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples/GroupCertificateVerificationSample), som du kan använda för att göra bevis av tillgång.
 
> [!IMPORTANT]
> Förutom att utföra bevis på tillgång, kan PowerShell och Bash-skript som åberopade tidigare också du skapa rotcertifikat och mellanliggande certifikat löv-certifikat som kan användas för att autentisera och etablera enheter. Dessa certifikat ska användas för endast utveckling. De bör aldrig användas i en produktionsmiljö. 

PowerShell och Bash-skript som finns i dokumentationen och SDK: er som förlitar sig på [OpenSSL](https://www.openssl.org/). Du kan också använda OpenSSL eller andra verktyg från tredje part som hjälper dig bevis av tillgång. Mer information om verktyg som medföljer SDK: erna finns i [hur du använder verktygen i SDK: erna](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Överför det signerade verifieringscertifikatet

1. Ladda upp den resulterande signaturen som ett verifieringscertifikat till etableringstjänsten i portalen. I **certifikatinformation** på Azure-portalen använder du den _Utforskaren_ ikonen bredvid den **verifieringscertifikatet .pem eller .cer-fil** fält som du vill ladda upp den signerade verifieringscertifikat från datorn.

2. När certifikatet har laddats upp, klickar du på **Kontrollera**. Den **STATUS** av ändringarna certifikat **_verifierad_** i den **Certifikatutforskare** lista. Klicka på **uppdatera** om det inte uppdateras automatiskt.

   ![Ladda upp certifikatverifiering](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Nästa steg

- Läs om hur du använder portalen för att skapa en grupp för registrering i [hantera enhetsregistreringar med Azure-portalen](how-to-manage-enrollments.md).
- Läs om hur du använder SDK: er för tjänsten för att skapa en grupp för registrering i [hantera enhetsregistreringar med tjänst-SDK: er](how-to-manage-enrollments-sdks.md).










