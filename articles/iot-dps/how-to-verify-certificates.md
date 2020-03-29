---
title: Verifiera X.509 CA-certifikat med Azure IoT Hub Device Provisioning Service
description: Så här gör du bevisbeklädnad för X.509 CA-certifikat med DPS (Azure IoT Hub Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b008c4ebc83200043d51fc8ef367f1983c549949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973449"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Så här gör du bevisbeklädnad för X.509 CA-certifikat med enhetens etableringstjänst

Ett verifierat X.509 Certificate Authority (CA) certifikat är ett CA-certifikat som har laddats upp och registrerats till din etableringstjänst och har gått igenom bevis på innehav med tjänsten. 

Bevis på innehav omfattar följande steg:
1. Få en unik verifieringskod som genereras av etableringstjänsten för ditt X.509-certifikatutfärdarcertifikat. Du kan göra detta från Azure Portal.
2. Skapa ett X.509-verifieringscertifikat med verifieringskoden som ämne och signera certifikatet med den privata nyckeln som är associerad med X.509 CA-certifikatet.
3. Ladda upp det signerade verifieringscertifikatet till tjänsten. Tjänsten validerar verifieringscertifikatet med den offentliga delen av certifikatutfärdarcertifikatet som ska verifieras, vilket bevisar att du har certifikatutfärdarcertifikatets privata nyckel.

Verifierade certifikat spelar en viktig roll när du använder registreringsgrupper. Verifiera certifikatägarskap ger ett extra säkerhetslager genom att se till att uppladdaren av certifikatet har certifikatets privata nyckel. Verifiering förhindrar att en skadlig aktör sniffar på trafiken från att extrahera ett mellanliggande certifikat och använder det certifikatet för att skapa en registreringsgrupp i sin egen etableringstjänst, vilket effektivt kapar dina enheter. Genom att bevisa ägarskap för roten eller ett mellanliggande certifikat i en certifikatkedja bevisar du att du har behörighet att generera lövcertifikat för de enheter som ska registreras som en del av den registreringsgruppen. Därför måste rot- eller mellanliggande certifikat som konfigurerats i en registreringsgrupp antingen vara ett verifierat certifikat eller måste summera till ett verifierat certifikat i certifikatkedjan som en enhet visar när den autentiserar med tjänsten. Mer information om registreringsgrupper finns i [X.509-certifikat](concepts-security.md#x509-certificates) och [Kontrollera enhetsåtkomst till etableringstjänsten med X.509-certifikat](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registrera den offentliga delen av ett X.509-certifikat och få en verifieringskod

Så här registrerar du ett CA-certifikat med etableringstjänsten och får en verifieringskod som du kan använda under bevisbekrätande, gör du så här. 

1. I Azure-portalen navigerar du till **etableringstjänsten** och öppnar Certifikat från menyn till vänster. 
2. Klicka på **Lägg till** om du vill lägga till ett nytt certifikat.
3. Ange ett eget visningsnamn för ditt certifikat. Bläddra till filen .cer eller .pem som representerar den offentliga delen av ditt X.509-certifikat. Klicka på **Överför**.
4. När du har fått ett meddelande om att certifikatet har laddats upp klickar du på **Spara**.

    ![Överför certifikat](./media/how-to-verify-certificates/add-new-cert.png)  

   Certifikatet visas i listan **Certifikatutforskaren.** Observera att **status** för det här certifikatet är *overifierat*.

5. Klicka på certifikatet som du lade till i föregående steg.

6. Klicka på **Generera verifieringskod** **i Certifikatinformation**.

7. Etableringstjänsten skapar en **verifieringskod** som du kan använda för att verifiera certifikatägarskapet. Kopiera koden till Urklipp. 

   ![Verifiera certifikat](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Signera verifieringskoden digitalt för att skapa ett verifieringscertifikat

Nu måste du signera *verifieringskoden* med den privata nyckeln som är associerad med ditt X.509 CA-certifikat, som genererar en signatur. Detta kallas [Bevis på innehav](https://tools.ietf.org/html/rfc5280#section-3.1) och resulterar i ett undertecknat verifieringscertifikat.

Microsoft tillhandahåller verktyg och exempel som kan hjälpa dig att skapa ett signerat verifieringscertifikat: 

- **Azure IoT Hub C SDK** tillhandahåller PowerShell-skript (Windows) och Bash (Linux) som hjälper dig att skapa certifikatutfärdare och lövcertifikat för utveckling och för att utföra bevis på innehav med hjälp av en verifieringskod. Du kan hämta [filerna](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) som är relevanta för ditt system till en arbetsmapp och följa instruktionerna i readme för hantering av [certifikatutfärdare](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) för att utföra bevis på en certifikatutfärdares certifikat. 
- **Azure IoT Hub C# SDK** innehåller [exempel på gruppcertifikatverifiering](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), som du kan använda för att göra bevis på innehav.
 
> [!IMPORTANT]
> Förutom att utföra bevisbekrätande, powershell och bash skript som tidigare nämnts också kan du skapa rotcertifikat, mellanliggande certifikat och blad certifikat som kan användas för att autentisera och etablera enheter. Dessa certifikat bör endast användas för utveckling. De bör aldrig användas i en produktionsmiljö. 

PowerShell- och Bash-skripten som finns i dokumentationen och SDK:er är beroende av [OpenSSL](https://www.openssl.org/). Du kan också använda OpenSSL eller andra verktyg från tredje part för att hjälpa dig att göra bevis på innehav. Mer information om verktyg som medföljer SDK:erna finns [i Så här använder du verktyg som finns i SDK:erna](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Ladda upp det signerade verifieringscertifikatet

1. Ladda upp den resulterande signaturen som ett verifieringscertifikat till etableringstjänsten i portalen. I **Certifikatinformation** på Azure-portalen använder du ikonen _Utforskaren_ **bredvid filfältet Verifieringscertifikat .pem eller .cer** för att överföra det signerade verifieringscertifikatet från systemet.

2. När certifikatet har laddats upp klickar du på **Verifiera**. **Statusen** för certifikatet **_ändras_** till Verifierad i listan **Certifikatutforskaren.** Klicka på **Uppdatera** om den inte uppdateras automatiskt.

   ![Ladda upp certifikatverifiering](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du använder portalen för att skapa en registreringsgrupp finns i [Hantera enhetsregistreringar med Azure Portal](how-to-manage-enrollments.md).
- Mer information om hur du använder tjänstenSDK:er för att skapa en registreringsgrupp finns i [Hantera enhetsregistreringar med tjänst-SDK:er](how-to-manage-enrollments-sdks.md).










