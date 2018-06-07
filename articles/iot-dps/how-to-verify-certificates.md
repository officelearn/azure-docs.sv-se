---
title: Hur du gör bevis tillgång för X.509-certifikat med Azure IoT Hub etablering av tjänst | Microsoft Docs
description: Så här kontrollerar du X.509-certifikat med din DP-tjänst
author: bryanla
ms.author: bryanla
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: dfd45a68934250978fa8ac0cc9cebabcf8bad597
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629702"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Hur du gör bevis tillgång för X.509-certifikat med din enhet etablering av tjänst

En verifierad X.509 certifikatutfärdare (CA) certifikatet är ett certifikat som har överförts och registrerad på din etablering tjänst och har gått igenom bevis tillgång till tjänsten. 

Bevis av tillgång omfattar följande steg:
1. Hämta en unik Verifieringskod som skapats av tjänsten etablering för din CA X.509-certifikat. Du kan göra detta från Azure-portalen.
2. Skapa ett X.509-certifikat för verifiering med verifieringskoden som ämnet och registrera certifikatet med den privata nyckeln som associeras med X.509 Certifikatutfärdarens certifikat.
3. Överför det signerade verifieringscertifikatet till tjänsten. Tjänsten verifierar verifieringscertifikatet med hjälp av den offentliga delen av CA-certifikatet verifieras därför bevisa att du är som har tillgång till CA-certifikatets privata nyckel.

Verifierade certifikat spelar en viktig roll när du använder grupper för registrering. Verifiera certifikatet ägarskap ger ett ytterligare säkerhetslager genom att säkerställa att överföring av certifikatet har tillgång för certifikatets privata nyckel. Verifieringen förhindrar en skadlig aktören kontroll trafiken från Extrahera ett mellanliggande certifikat och använder certifikatet för att skapa en grupp för registrering i sina egna etablering service effektivt kapa dina enheter. Du bevisa att du har behörighet att skapa lövmedlemmar certifikat för de enheter som ska registreras som en del av registreringen gruppen genom att bevisa ägarskapet för roten eller ett mellanliggande certifikat i en certifikatkedja. För därför den rot- eller mellanliggande certifikat som konfigurerades i en grupp för registrering måste antingen vara en verifierad certifikat eller måste presenterar sammanslagning upp till ett verifierade certifikat i certifikatkedjan en enhet när den autentiserar med tjänsten. Läs mer om registrering av grupper i [X.509-certifikat](concepts-security.md#x509-certificates) och [Kontrollera Enhetsåtkomst till tjänsten etablering med X.509-certifikat](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registrera den offentliga delen av ett X.509-certifikat och få en Verifieringskod

Följ dessa steg om du vill registrera ett certifikat med etablering tjänsten och få en Verifieringskod som du kan använda under bevis av tillgång. 

1. Navigera till etablering tjänsten i Azure-portalen och öppna **certifikat** i den vänstra menyn. 
2. Klicka på **Lägg till** att lägga till ett nytt certifikat.
3. Ange ett eget namn för certifikatet. Bläddra till filen CER- eller .pem som representerar den offentliga delen av X.509-certifikat. Klicka på **Överför**.
4. När du får ett meddelande om att ditt certifikat har överförts klickar du på **spara**.

    ![Överför certifikat](./media/how-to-verify-certificates/add-new-cert.png)  

   Ditt certifikat visas i den **certifikat Explorer** lista. Observera att den **STATUS** av det här certifikatet är *Ej verifierat*.

5. Klicka på det certifikat som du lade till i föregående steg.

6. I **certifikatinformation**, klickar du på **generera Verifieringskod**.

7. Tjänsten etablering skapar en **Verifieringskod** som du kan använda för att validera certifikatet ägare. Kopiera koden till Urklipp. 

   ![Verifiera certifikat](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Signera Verifieringskod för att skapa ett verifieringscertifikat

Nu kan du behöva logga den *Verifieringskod* med den privata nyckeln som associeras med X.509 Certifikatutfärdarens certifikat, vilket genererar en signatur. Detta kallas [konceptbevis tillgång](https://tools.ietf.org/html/rfc5280#section-3.1) och resulterar i ett signerat verifieringscertifikat.

Microsoft tillhandahåller verktyg och exempel som kan hjälpa dig att skapa ett signerat verifieringscertifikat: 

- Den **Azure IoT-hubb C SDK** ger PowerShell (Windows) och (Linux) för Bash-skript för att skapa CA och lägsta certifikat för utveckling och utföra med en Verifieringskod bevis-för-tillgång. Du kan hämta den [filer](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) i systemet till en arbetsmapp och följ instruktionerna i den [hantera CA-certifikat viktigt](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) att utföra bevis av tillgång på ett CA-certifikat. 
- Den **Azure IoT-hubb C# SDK** innehåller den [grupp certifikat verifiering exempel](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples/GroupCertificateVerificationSample), som du kan använda för att göra bevis av tillgång.
- Du kan följa stegen i den [PowerShell-skript för att hantera CA-signerat X.509-certifikat](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-create-certificates) artikel i dokumentationen för IoT-hubb specifikt skriptet som anges i avsnittet [konceptbevis tillgång till din X.509-certifikatutfärdarcertifikat](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-create-certificates#signverificationcode).
 
> [!IMPORTANT]
> Förutom att utföra bevis av tillgång, kan PowerShell och Bash-skript åberopade tidigare också du skapa rotcertifikat och mellanliggande certifikat löv certifikat som kan användas för att autentisera och etablera enheter. Dessa certifikat ska användas för endast utveckling. De ska aldrig användas i en produktionsmiljö. 

PowerShell och Bash-skript som finns i dokumentationen och SDK: er som förlitar sig på [OpenSSL](https://www.openssl.org/). Du kan också använda OpenSSL eller andra verktyg från tredje part som hjälper dig att göra bevis av tillgång. Mer information om verktygsuppsättning som medföljer SDK: erna finns [hur du använder verktygen i SDK: erna](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Överför det signerade verifieringscertifikatet

1. Ladda upp den resulterande signaturen som ett verifieringscertifikat till etablering tjänsten i portalen. I **certifikatinformation** på Azure-portalen använder den _Utforskaren_ ikonen bredvid den **verifieringscertifikat .pem eller .cer-fil** fältet överför den signerade verifieringscertifikat från systemet.

2. När certifikatet har laddats upp, klickar du på **Kontrollera**. Den **STATUS** av ändringarna certifikat **_verifierad_** i den **certifikat Explorer** lista. Klicka på **uppdatera** om den inte uppdateras automatiskt.

   ![Överför certifikatverifiering](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Nästa steg

- Läs om hur du kan använda portalen för att skapa en grupp för registrering i [hantera enhetsregistrering med Azure-portalen](how-to-manage-enrollments.md).
- Läs om hur du använder SDK: er för tjänsten för att skapa en grupp för registrering i [hantera enhetsregistrering med tjänsten SDK](how-to-manage-enrollments-sdks.md).










