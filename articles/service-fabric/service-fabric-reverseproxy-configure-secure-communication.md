---
title: Azure Service Fabric omvänd proxy säker kommunikation
description: Konfigurera omvänd proxy för att aktivera säker heltäckande kommunikation i ett Azure Service Fabric-program.
author: kavyako
ms.topic: conceptual
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 4cfeaf34a39231ffa91ea970a61f66632bae40c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282255"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Ansluta till en säker tjänst med omvänd proxy

I den här artikeln beskrivs hur du upprättar en säker anslutning mellan omvänd proxy och tjänster, vilket möjliggör en på säker kanal. Mer information om omvänd proxy finns [i Omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy.md)

Anslutning till säkra tjänster stöds endast när omvänd proxy är konfigurerad för att lyssna på HTTPS. Denna artikel förutsätter att så är fallet.
Se [omvänd konfigurationsproxy i Azure Service Fabric](service-fabric-reverseproxy-setup.md) för att konfigurera omvänd proxy i Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Säker anslutningsetablering mellan omvänd proxy och tjänster 

### <a name="reverse-proxy-authenticating-to-services"></a>Omvänd proxyautentisering till tjänster:
Den omvända proxyn identifierar sig för tjänster som använder dess certifikat. För Azure-kluster anges certifikatet med ***egenskapen reverseProxyCertificate*** i avsnittet [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [Resource type](../azure-resource-manager/templates/template-syntax.md) i Resource Manager-mallen. För fristående kluster anges certifikatet med antingen ***ReverseProxyCertificate*** eller ***ReverseProxyCertificateCommonNames-egenskapen*** i avsnittet **Säkerhet** i ClusterConfig.json. Mer information finns i [Aktivera omvänd proxy i fristående kluster](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Tjänster kan implementera logiken för att verifiera certifikatet som visas av den omvända proxyn. Tjänsterna kan ange den accepterade klientcertifikatinformationen som konfigurationsinställningar i konfigurationspaketet. Detta kan läsas vid körning och användas för att validera certifikatet som visas av den omvända proxyn. Se [Hantera programparametrar](service-fabric-manage-multiple-environment-app-configuration.md) för att lägga till konfigurationsinställningarna. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Omvänd proxy som verifierar tjänstens identitet via det certifikat som visas av tjänsten:
Omvänd proxy stöder följande principer för att utföra validering av servercertifikat för de certifikat som visas av tjänster: Ingen, ServiceCommonNameAndIssuer och ServiceCertificateThumbprints.
Om du vill välja principen för omvänd proxy som ska användas anger du **ApplicationCertificateValidationPolicy** i avsnittet **ApplicationGateway/Http** under [fabricSettings](service-fabric-cluster-fabric-settings.md).

I nästa avsnitt visas konfigurationsinformation för vart och ett av dessa alternativ.

### <a name="service-certificate-validation-options"></a>Valideringsalternativ för tjänstcertifikat 

- **Ingen**: Omvänd proxy hoppar över verifieringen av det proportionella tjänstcertifikatet och upprättar den säkra anslutningen. Det här är standardbeteendet.
Ange **ApplicationCertificateValidationPolicy** med värdet **Ingen** i avsnittet [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCommonNameAndIssuer**: Omvänd proxy verifierar certifikatet som visas av tjänsten baserat på certifikatets gemensamma namn och omedelbara utfärdarens tumavtryck: Ange **ApplicationCertificateValidationPolicy** med värdet **ServiceCommonNameAndIssuer** i avsnittet [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Om du vill ange listan över tjänstens gemensamma namn och utfärdartumavtryck lägger du till avsnittet [**ApplicationGateway/Http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) under **fabricSettings**, som visas nedan. Flera certifikat vanliga namn och utfärdare tumavtryck par kan läggas till i **parametrarna** array. 

   Om den omvända slutpunktsproxyn ansluter till presenterar ett certifikat som är vanligt namn och utfärdartumavtryck matchar något av de värden som anges här, upprättas SSL-kanal. 
   Efter att certifikatet inte har matchats misslyckas klientens begäran med statuskoden 502 (Bad Gateway). HTTP-statusraden innehåller också frasen "Ogiltigt SSL-certifikat". 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints**: Reverse proxy verifierar det proxied servicecertifikat baserat på dess tumavtryck. Du kan välja att gå den här vägen när tjänsterna är konfigurerade med självsignerade certifikat: Ange **ApplicationCertificateValidationPolicy** med värde **ServiceCertificateThumbprints** i avsnittet [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Ange också tumavtrycken med en **ServiceCertificateThumbprints-post** i avsnittet **ApplicationGateway/Http.** Flera tumavtryck kan anges som en kommaavgränsad lista i värdefältet, som visas nedan:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Om servercertifikatets tumavtryck visas i den här konfigurationsposten lyckas omvänd proxy SSL-anslutningen. Annars avslutas anslutningen och klientens begäran misslyckas med en 502 (Felaktig gateway). HTTP-statusraden innehåller också frasen "Ogiltigt SSL-certifikat".

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Slutpunktsvallogik när tjänster exponerar säkra och osäkra slutpunkter
Service fabric stöder konfiguration av flera slutpunkter för en tjänst. Mer information finns [i Ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md).

Omvänd proxy väljer en av slutpunkterna för att vidarebefordra begäran baserat på frågeparametern **ListenerName** i [tjänsten URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Om parametern **ListenerName** inte har angetts kan omvänd proxy välja valfri slutpunkt från slutpunktslistan. Beroende på de slutpunkter som konfigurerats för tjänsten kan den valda slutpunkten vara en HTTP- eller HTTPS-slutpunkt. Det kan finnas scenarier eller krav där du vill att den omvända proxyn ska fungera i ett "säkert läge". Det vill ha inte den säkra omvända proxyn för att vidarebefordra begäranden till osäkra slutpunkter. Om du vill ange omvänd proxy till endast säkert läge anger du konfigurationsposten **SecureOnlyMode** med värdet **sant** i avsnittet [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> Om en klient har angett ett ListenerName som motsvarar en HTTP-slutpunkt(oskyddad) när den körs i **SecureOnlyMode**, om en klient har angett ett **ListenerName** som motsvarar en HTTP-slutpunkt(oskyddad) , misslyckas omvänd proxy begäran med en HTTP-statuskod på 404 (hittades inte).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Ställa in klientcertifikatautentisering via omvänd proxy
SSL-avslutning sker vid omvänd proxy och alla klientcertifikatdata går förlorade. Om tjänsterna ska utföra klientcertifikatautentisering anger du inställningen **ForwardClientCertificate** i avsnittet [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

1. När **ForwardClientCertificate** är inställt på **false**kommer omvänd proxy inte att begära klientcertifikatet under SSL-handskakningen med klienten.
Det här är standardbeteendet.

2. När **ForwardClientCertificate** är inställt på **true**begär omvänd proxy klientens certifikat under SSL-handskakningen med klienten.
Klientcertifikatdata vidarebefordras sedan i ett anpassat HTTP-huvud med namnet **X-Client-Certificate**. Huvudvärdet är den base64-kodade PEM-formatsträngen för klientens certifikat. Tjänsten kan lyckas/misslyckas med begäran med lämplig statuskod efter att ha inspekterat certifikatdata.
Om klienten inte visar något certifikat vidarebefordrar omvänd proxy ett tomt huvud och låter tjänsten hantera ärendet.

> [!NOTE]
> Omvänd proxy är bara en vidarebefordrare. Den kommer inte att utföra någon validering av klientens certifikat.


## <a name="next-steps"></a>Nästa steg
* [Konfigurera omvänd proxy på ett kluster](service-fabric-reverseproxy-setup.md).
* Se [Konfigurera omvänd proxy för att ansluta till säkra tjänster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Se ett exempel på HTTP-kommunikation mellan tjänster i ett [exempelprojekt på GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Fjärrproceduranrop med reliable services-ommotning](service-fabric-reliable-services-communication-remoting.md)
* [Webb-API som använder OWIN i reliable services](service-fabric-reliable-services-communication-webapi.md)
* [Hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md)
