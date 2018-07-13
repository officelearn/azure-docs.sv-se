---
title: Konfigurera SSL för en tjänst i molnet | Microsoft Docs
description: Lär dig hur du anger en HTTPS-slutpunkt för en webbroll och överför ett SSL-certifikat för att skydda dina program. De här exemplen använder Azure portal.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 371ba204-48b6-41af-ab9f-ed1d64efe704
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jeconnoc
ms.openlocfilehash: e3e7d271375cd9c3f49d8fedd963b5234dab7902
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001532"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Konfigurera SSL för ett program i Azure

SSL-kryptering (Secure Socket Layer) är den vanligaste metoden för att skydda data som skickas över Internet. Den här vanliga åtgärden diskuterar hur man anger en HTTPS-slutpunkt för en webbroll och överför ett SSL-certifikat för att skydda ett program.

> [!NOTE]
> Procedurerna i det här steget gäller för Azure Cloud Services; App Services, se [detta](../app-service/app-service-web-tutorial-custom-ssl.md).
>

Den här uppgiften använder en Produktionsdistribution. Information om hur du använder en mellanlagrings-distribution finns i slutet av det här avsnittet.

Läs [detta](cloud-services-how-to-create-deploy-portal.md) om du inte har skapat en tjänst i molnet.

## <a name="step-1-get-an-ssl-certificate"></a>Steg 1: Hämta ett SSL-certifikat
Om du vill konfigurera SSL för ett program, måste du först hämta ett SSL-certifikat som har signerats av en certifikatutfärdare (CA), en betrodd tredje part som utfärdar certifikat för detta ändamål. Om du inte redan har en, måste du skaffa ett från ett företag som säljer SSL-certifikat.

Certifikatet måste uppfylla följande krav för SSL-certifikat i Azure:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatet måste skapas för nyckelutbyte, kan exporteras till en Personal Information Exchange (.pfx)-fil.
* Certifikatets ämnesnamn måste matcha den domän som används för att få åtkomst till Molntjänsten. Du kan inte hämta ett SSL-certifikat från en certifikatutfärdare (CA) för cloudapp.net-domän. Du måste skaffa ett anpassat domännamn som ska användas när komma åt tjänsten. När du begär ett certifikat från en Certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som används för att komma åt ditt program. Exempel: om ditt domännamn är **contoso.com** du vill begära ett certifikat från din Certifikatutfärdare för ***. contoso.com** eller **www.contoso.com**.
* Certifikatet måste använda minst 2048-bitars kryptering.

För testning, kan du [skapa](cloud-services-certs-create.md) och använda ett självsignerat certifikat. Ett självsignerat certifikat har autentiserats inte via en Certifikatutfärdare och kan använda cloudapp.net domänen som URL för webbplatsen. Till exempel följande uppgift använder ett självsignerat certifikat som CN-namn (CN) som används i certifikatet är **sslexample.cloudapp.net**.

Därefter måste du inkludera information om certifikatet i tjänstdefinitionen och konfigurationsfiler för tjänsten.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Steg 2: Ändra service definition och konfigurationsfiler
Programmet måste konfigureras för att använda certifikat och en HTTPS-slutpunkt måste läggas till. Tjänstdefinition och konfigurationsfiler för tjänsten måste därför uppdateras.

1. Öppna tjänstdefinitionsfilen (CSDEF) i din utvecklingsmiljö, lägga till en **certifikat** avsnittet inom den **WebRole** avsnittet och inkluderar följande information om certifikatet (och mellanliggande certifikat):

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

   Den **certifikat** avsnittet definierar namnet på vår certifikat, dess plats och namnet på arkivet var de finns.

   Behörigheter (`permisionLevel` attribut) kan anges till något av följande värden:

   | Behörigheten värde | Beskrivning |
   | --- | --- |
   | limitedOrElevated |**(Standard)**  Alla processer som rollen kan komma åt den privata nyckeln. |
   | upphöjd |Endast utökade processer kan komma åt den privata nyckeln. |

2. Lägg till i din tjänstdefinitionsfilen en **InputEndpoint** element i den **slutpunkter** avsnitt för att aktivera HTTPS:

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

3. Lägg till i din tjänstdefinitionsfilen en **bindning** element i den **platser** avsnittet. Det här elementet lägger till en HTTPS-bindning för att mappa slutpunkten till din webbplats:

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

   Alla nödvändiga ändringar i tjänstdefinitionsfilen har uppfyllts. men du behöver att lägga till certifikatinformationen i tjänstekonfigurationsfilen.
4. Lägg till i din tjänstkonfigurationsfilen (CSCFG), ServiceConfiguration.Cloud.cscfg, en **certifikat** värdet med ditt certifikat. Följande kodexempel innehåller information om den **certifikat** avsnittet, förutom tumavtrycksvärde.

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

(Det här exemplet används **sha1** för algoritmen tumavtryck. Ange ett värde för ditt certifikat tumavtrycksalgoritm.)

Nu när tjänsten definitions- och konfigurationsfiler för tjänsten har uppdaterats, paketera din distribution för att ladda upp till Azure. Om du använder **cspack**, Använd inte den **/generateConfigurationFile** flagga, som som skriver över den certifikatinformation som du har infogat.

## <a name="step-3-upload-a-certificate"></a>Steg 3: Ladda upp ett certifikat
Ansluta till Azure-portalen och...

1. I den **alla resurser** avsnittet i portalen väljer du din molntjänst.

    ![Publicera din molntjänst](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Klicka på **certifikat**.

    ![Klicka på ikonen för certifikat](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Klicka på **överför** överst i området för certifikat.

    ![Klicka på menyalternativet ladda upp](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Ange den **filen**, **lösenord**, klicka sedan på **överför** längst ned i posten dataområdet.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Steg 4: Anslut till rollinstansen med hjälp av HTTPS
Nu när distributionen är igång och körs i Azure kan ansluta du till den med hjälp av HTTPS.

1. Klicka på den **webbplatsens URL** så att du öppnar webbläsaren.

   ![Klicka på webbplatsens URL](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. Ändra länken så att använda i din webbläsare **https** i stället för **http**, och sedan gå till sidan.

   > [!NOTE]
   > Om du använder ett självsignerat certifikat när du bläddrar till en HTTPS-slutpunkt som är associerat med det självsignerade certifikatet som du kan se ett certifikat-fel i webbläsaren. Med hjälp av ett certifikat som signerats av en betrodd certifikatutfärdare eliminerar det här problemet. under tiden kan ignorera du felet. (Ett annat alternativ är att lägga till det självsignerade certifikatet i certifikatarkivet för användarens certifikat från betrodd utfärdare.)
   >
   >

   ![Förhandsversion av plats](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Om du vill använda SSL för en mellanlagrings-distribution i stället för en Produktionsdistribution måste du först fastställa den URL som används för mellanlagring distributionen. När din molntjänst har distribuerats, URL-Adressen till mellanlagringsmiljön bestäms av den **distributions-ID** GUID i följande format: `https://deployment-id.cloudapp.net/`  
   >
   > Skapa ett certifikat med nätverksnamn (CN) som är lika med GUID-baserad URL (till exempel **328187776e774ceda8fc57609d404462.cloudapp.net**). Använda portalen för att lägga till certifikatet i mellanlagrad Molntjänsten. Lägg sedan till certifikatinformationen till dina CSDEF och CSCFG-filer, Paketera om ditt program och uppdatera din stegvis distribution om du vill använda det nya paketet.
   >

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration för din molntjänst](cloud-services-how-to-configure-portal.md).
* Lär dig hur du [distribuera en molntjänst](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera en [domännamn](cloud-services-custom-domain-name-portal.md).
* [Hantera din molntjänst](cloud-services-how-to-manage-portal.md).
