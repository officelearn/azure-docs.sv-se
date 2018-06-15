---
title: Konfigurera SSL för en molnbaserad tjänst | Microsoft Docs
description: Lär dig hur du anger en HTTPS-slutpunkt för en webbroll och hur man överför ett SSL-certifikat för att skydda ditt program. De här exemplen använder Azure-portalen.
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: 371ba204-48b6-41af-ab9f-ed1d64efe704
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.openlocfilehash: 0e053ad7f1033317948b6ef0856984b21e56e425
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
ms.locfileid: "24859784"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Konfigurera SSL för ett program i Azure

SSL-kryptering (Secure Socket Layer) är den vanligaste metoden för att skydda data som skickas över Internet. Den här vanliga åtgärden diskuterar hur man anger en HTTPS-slutpunkt för en webbroll och överför ett SSL-certifikat för att skydda ett program.

> [!NOTE]
> Procedurerna i det här steget gäller för Azure Cloud Services; App-tjänster, se [detta](../app-service/app-service-web-tutorial-custom-ssl.md).
>

Den här aktiviteten använder en Produktionsdistribution. Information om hur du använder en fristående distribution har angetts i slutet av det här avsnittet.

Läs [detta](cloud-services-how-to-create-deploy-portal.md) första om du inte har skapat en tjänst i molnet.

## <a name="step-1-get-an-ssl-certificate"></a>Steg 1: Hämta ett SSL-certifikat
Om du vill konfigurera SSL för ett program måste du först skaffa ett SSL-certifikat som har signerats av en certifikatet (CA), en betrodd tredje part som utfärdar certifikat för detta ändamål. Om du inte redan har en, måste du skaffa ett från ett företag som säljer SSL-certifikat.

Certifikatet måste uppfylla följande krav för SSL-certifikat i Azure:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatet måste skapas för nyckelutbyte, kan exporteras till en Personal Information Exchange (.pfx)-fil.
* Certifikatets ämnesnamn måste matcha den domän som används för åtkomst till Molntjänsten. Du kan skaffa ett SSL-certifikat från en certifikatutfärdare (CA) för domänen cloudapp.net. Du måste skaffa ett anpassat domännamn som ska användas vid åtkomst till din tjänst. När du begär ett certifikat från en Certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som används för åtkomst till ditt program. Om ditt domännamn är till exempel **contoso.com** du vill begära ett certifikat från Certifikatutfärdaren för ***. contoso.com** eller **www.contoso.com**.
* Certifikatet måste använda minst 2048-bitars kryptering.

För testning, kan du [skapa](cloud-services-certs-create.md) och använda ett självsignerat certifikat. Ett självsignerat certifikat har autentiserats inte via en Certifikatutfärdare och kan använda domänen cloudapp.net som URL för webbplatsen. Till exempel följande uppgift använder ett självsignerat certifikat som nätverksnamn (CN) som används i certifikatet är **sslexample.cloudapp.net**.

Därefter måste du inkludera information om certifikatet i tjänstdefinitionen och konfigurationsfiler för tjänsten.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Steg 2: Ändra tjänsten definitions- och filer
Programmet måste konfigureras för att använda certifikat och en HTTPS-slutpunkt måste läggas till. Därför tjänstdefinitionen och service configuration-filer som måste uppdateras.

1. Öppna tjänstdefinitionsfilen (CSDEF) i din utvecklingsmiljö, lägga till en **certifikat** avsnittet inom den **WebRole** avsnittet och innehålla följande information om certifikat (och mellanliggande certifikat):

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   Den **certifikat** avsnittet definierar namnet på vår certifikat, dess plats och namnet på arkivet där den finns.

   Behörigheter (`permisionLevel` attributet) kan anges som något av följande värden:

   | Behörighetsvärdet | Beskrivning |
   | --- | --- |
   | limitedOrElevated |**(Standard)**  Alla processer som rollen har åtkomst till den privata nyckeln. |
   | upphöjd |Endast utökade processer kan komma åt den privata nyckeln. |

2. Lägg till i din tjänstdefinitionsfilen en **InputEndpoint** element i den **slutpunkter** avsnittet om du vill aktivera HTTPS:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. Lägg till i din tjänstdefinitionsfilen en **bindning** element i den **platser** avsnitt. Det här elementet lägger till en HTTPS-bindning för att mappa slutpunkten till din plats:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   Alla nödvändiga ändringar av tjänstdefinitionsfilen har avslutats. men du måste lägga till certifikatinformationen i tjänstekonfigurationsfilen.
4. Lägg till i din tjänstekonfigurationsfil (CSCFG) ServiceConfiguration.Cloud.cscfg, en **certifikat** värdet med ditt certifikat. Följande kodexempel innehåller information om den **certifikat** avsnittet förutom av tumavtrycksvärde.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(Det här exemplet används **sha1** för algoritmen tumavtryck. Ange ett värde för din certifikatets tumavtryck algoritmen.)

Nu när tjänsten definitions- och konfigurationsfiler för tjänsten har uppdaterats, paketera din distribution för överföring till Azure. Om du använder **cspack**, Använd inte den **/generateConfigurationFile** flagga som skrivs som du har infogat certifikatinformationen.

## <a name="step-3-upload-a-certificate"></a>Steg 3: Överför certifikat
Ansluta till Azure-portalen och...

1. I den **alla resurser** avsnittet Portal, väljer du din tjänst i molnet.

    ![Publicera din molntjänst](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Klicka på **certifikat**.

    ![Klicka på ikonen certifikat](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Klicka på **överför** överst i området för certifikat.

    ![Klicka på menyalternativet Överför](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Ange den **filen**, **lösenord**, klicka på **överför** längst ned i dataområdet transaktionen.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Steg 4: Anslut till rollinstansen med hjälp av HTTPS
Nu när distributionen är igång och körs i Azure, kan du ansluta till den med hjälp av HTTPS.

1. Klicka på den **Webbadress** öppna webbläsaren.

   ![Klicka på webbplatsens URL](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. Ändra länken om du vill använda i din webbläsare **https** i stället för **http**, och går sedan till sidan.

   > [!NOTE]
   > Om du använder ett självsignerat certifikat när du bläddrar till en HTTPS-slutpunkt som är kopplad till det självsignerade certifikatet kan du se ett certifikatfel i webbläsaren. Använder ett certifikat som signerats av en betrodd certifikatutfärdare åtgärdar problemet. under tiden kan du ignorera felet. (Ett annat alternativ är att lägga till det självsignerade certifikatet i certifikatarkivet för användarens certifikat från betrodd utfärdare.)
   >
   >

   ![Platsen preview](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Om du vill använda SSL för en fristående distribution i stället för en Produktionsdistribution måste du först fastställa URL: en som används för distribution av fristående. När Molntjänsten har distribuerats, URL-Adressen till mellanlagringsmiljön bestäms av den **distributions-ID** GUID i det här formatet:`https://deployment-id.cloudapp.net/`  
   >
   > Skapa ett certifikat med nätverksnamn (CN) som är lika med GUID-baserad URL (till exempel **328187776e774ceda8fc57609d404462.cloudapp.net**). Använda portalen för att lägga till certifikatet mellanlagrade Molntjänsten. Sedan lägger du till certifikatinformationen CSDEF- och CSCFG-filer, paketera programmet och uppdatera din stegvis distribution om du vill använda det nya paketet.
   >

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration av Molntjänsten](cloud-services-how-to-configure-portal.md).
* Lär dig hur du [distribuera en tjänst i molnet](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera en [domännamn](cloud-services-custom-domain-name-portal.md).
* [Hantera din molntjänst](cloud-services-how-to-manage-portal.md).
