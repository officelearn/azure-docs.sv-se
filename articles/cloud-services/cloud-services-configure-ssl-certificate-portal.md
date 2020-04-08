---
title: Konfigurera TLS för en molntjänst | Microsoft-dokument
description: Lär dig hur du anger en HTTPS-slutpunkt för en webbroll och hur du laddar upp ett TLS/SSL-certifikat för att skydda ditt program. Dessa exempel använder Azure-portalen.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/26/2017
ms.author: tagore
ms.openlocfilehash: 4d397279ac7e5949398d695db615d9a003ab7acd
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811677"
---
# <a name="configuring-tls-for-an-application-in-azure"></a>Konfigurera TLS för ett program i Azure

TLS (Transport Layer Security), som tidigare kallades SSL-kryptering (Secure Socket Layer), är den vanligaste metoden för att skydda data som skickas över internet. I den här vanliga uppgiften beskrivs hur du anger en HTTPS-slutpunkt för en webbroll och hur du överför ett TLS/SSL-certifikat för att skydda ditt program.

> [!NOTE]
> Procedurerna i den här uppgiften gäller för Azure Cloud Services. för App Services, se [detta](../app-service/configure-ssl-bindings.md).
>

Den här uppgiften använder en produktionsdistribution. Information om hur du använder en mellanlagringsdistribution finns i slutet av det här avsnittet.

Läs [det här](cloud-services-how-to-create-deploy-portal.md) först om du ännu inte har skapat en molntjänst.

## <a name="step-1-get-a-tlsssl-certificate"></a>Steg 1: Skaffa ett TLS/SSL-certifikat
Om du vill konfigurera TLS för ett program måste du först hämta ett TLS/SSL-certifikat som har signerats av en certifikatutfärdare ( CA), en betrodd tredje part som utfärdar certifikat för detta ändamål. Om du inte redan har ett måste du skaffa ett från ett företag som säljer TLS/SSL-certifikat.

Certifikatet måste uppfylla följande krav för TLS/SSL-certifikat i Azure:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatet måste skapas för nyckelutbyte, som kan exporteras till en fil för personligt informationsutbyte (.pfx).
* Certifikatets ämnesnamn måste matcha den domän som används för att komma åt molntjänsten. Du kan inte hämta ett TLS/SSL-certifikat från en certifikatutfärdarcertifikat för cloudapp.net domänen. Du måste skaffa ett anpassat domännamn som ska användas när du öppnar tjänsten. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamn som används för att komma åt ditt program. Om ditt eget domännamn till exempel **är contoso.com** skulle du begära ett certifikat från certifikatutfärdaren för ***.contoso.com** eller **www\.contoso.com**.
* Certifikatet måste använda minst 2048-bitars kryptering.

I testsyfte kan du [skapa](cloud-services-certs-create.md) och använda ett självsignerat certifikat. Ett självsignerat certifikat autentiseras inte via en certifikatutfärdare och kan använda domänen cloudapp.net som webbadress. Följande uppgift använder till exempel ett självsignerat certifikat där det gemensamma namnet (CN) som används i certifikatet **sslexample.cloudapp.net**.

Därefter måste du inkludera information om certifikatet i tjänstdefinitions- och tjänstkonfigurationsfilerna.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Steg 2: Ändra tjänstdefinitions- och konfigurationsfiler
Programmet måste vara konfigurerat för att certifikatet ska kunna användas och en HTTPS-slutpunkt måste läggas till. Därför måste tjänstdefinitions- och tjänstkonfigurationsfilerna uppdateras.

1. Öppna csdef (service definition file), lägga till ett **certifikatavsnitt** i **avsnittet WebRole** i utvecklingsmiljön och inkludera följande information om certifikatet (och mellanliggande certifikat):

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

   Avsnittet **Certifikat** definierar namnet på vårt certifikat, dess plats och namnet på butiken där det finns.

   Behörigheter`permissionLevel` (attribut) kan anges till ett av följande värden:

   | Behörighetsvärde | Beskrivning |
   | --- | --- |
   | begränsadOrElevated |**(Standard)** Alla rollprocesser kan komma åt den privata nyckeln. |
   | Förhöjda |Endast förhöjda processer kan komma åt den privata nyckeln. |

2. Lägg till ett **InputEndpoint-element** i avsnittet Slutpunkter i **tjänstdefinitionsfilen** för att aktivera HTTPS:

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

3. Lägg till ett **bindningselement** i avsnittet **Platser** i tjänstdefinitionsfilen. Det här elementet lägger till en HTTPS-bindning för att mappa slutpunkten till din webbplats:

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

   Alla nödvändiga ändringar i tjänstdefinitionsfilen har slutförts. men du måste fortfarande lägga till certifikatinformationen i tjänstkonfigurationsfilen.
4. I **tjänstkonfigurationsfilen** (CSCFG), ServiceConfiguration.Cloud.cscfg, lägger du till ett certifikatvärde med certifikatets. Följande kodexempel innehåller information om avsnittet **Certifikat,** med undantag för tumavtrycksvärdet.

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

(I det här exemplet används **sha1** för tumavtrycksalgoritmen. Ange lämpligt värde för certifikatets tumavtrycksalgoritm.)

Nu när tjänstdefinitionen och tjänstkonfigurationsfilerna har uppdaterats paketerar du distributionen för överföring till Azure. Om du använder **cspack**ska du inte använda flaggan **/generateConfigurationFile,** eftersom det kommer att skriva över certifikatinformationen som du just har infogat.

## <a name="step-3-upload-a-certificate"></a>Steg 3: Ladda upp ett certifikat
Anslut till Azure-portalen och...

1. Välj molntjänsten i avsnittet **Alla resurser** på portalen.

    ![Publicera din molntjänst](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Klicka på **Certifikat**.

    ![Klicka på ikonen certifikat](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Klicka på **Ladda upp** högst upp i certifikatområdet.

    ![Klicka på menyalternativet Ladda upp](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Ange **fil**, **lösenord**och klicka sedan på **Ladda** upp längst ned i datainmatningsområdet.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Steg 4: Anslut till rollinstansen med https
Nu när distributionen är igång i Azure kan du ansluta till den med HTTPS.

1. Klicka på **webbadressen** för att öppna webbläsaren.

   ![Klicka på webbadressen](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. Ändra länken så att den använder **https** i stället för **http**i webbläsaren och sedan på sidan.

   > [!NOTE]
   > Om du använder ett självsignerat certifikat kan ett certifikatfel i webbläsaren visas när du bläddrar till en HTTPS-slutpunkt som är associerad med det självsignerade certifikatet. Om du använder ett certifikat som signerats av en betrodd certifikatutfärdare elimineras problemet. Under tiden kan du ignorera felet. (Ett annat alternativ är att lägga till det självsignerade certifikatet i användarens certifikatutfärdarkiv för betrodd certifikatutfärdare.)
   >
   >

   ![Förhandsgranskning av webbplats](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Om du vill använda TLS för en mellanlagringsdistribution i stället för en produktionsdistribution måste du först bestämma webbadressen som används för mellanlagringsdistributionen. När molntjänsten har distribuerats bestäms URL:en till mellanlagringsmiljön av **GUID för distribution** i ID i det här formatet:`https://deployment-id.cloudapp.net/`  
   >
   > Skapa ett certifikat med det gemensamma namnet (CN) som är lika med den GUID-baserade URL:en (till exempel **328187776e774ceda8fc57609d404462.cloudapp.net**). Använd portalen för att lägga till certifikatet i din stegvisa molntjänst. Lägg sedan till certifikatinformationen i CSDEF- och CSCFG-filerna, paketera om programmet och uppdatera den stegvisa distributionen så att det nya paketet används.
   >

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration av molntjänsten](cloud-services-how-to-configure-portal.md).
* Lär dig hur du [distribuerar en molntjänst](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera ett [eget domännamn](cloud-services-custom-domain-name-portal.md).
* [Hantera din molntjänst](cloud-services-how-to-manage-portal.md).



