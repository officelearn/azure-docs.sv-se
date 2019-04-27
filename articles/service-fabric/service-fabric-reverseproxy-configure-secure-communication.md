---
title: Azure Service Fabric omvänd proxy för säker kommunikation | Microsoft Docs
description: Konfigurera omvänd proxy för att aktivera säker slutpunkt till slutpunkt-kommunikation.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: d8a11a3289037602535d1b5727d041e376012bd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837852"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Ansluta till en säker tjänst med omvänd proxy

Den här artikeln förklarar hur du upprättar säker anslutning mellan omvänd proxy och tjänster, vilket gör att en säker kanal från slutpunkt till slutpunkt. Läs mer om omvänd proxy i [omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy.md)

Ansluter till säker tjänster stöds endast när omvänd proxy är konfigurerad för att lyssna på HTTPS. Den här artikeln förutsätter att detta är fallet.
Referera till [installationsprogrammet omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy-setup.md) konfigurera omvänd proxy i Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Upprätta säker anslutning mellan omvänd proxy och tjänster 

### <a name="reverse-proxy-authenticating-to-services"></a>Omvänd proxy som autentiserar till tjänster:
Omvänd proxy identifierar sig själv för tjänster med hjälp av dess certifikat. För certifikatet för Azure-kluster har angetts med ***reverseProxyCertificate*** -egenskapen i den [ **Microsoft.ServiceFabric/clusters** ](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [resurs Skriv avsnittet](../azure-resource-manager/resource-group-authoring-templates.md) av Resource Manager-mallen. Certifikatet som angetts för fristående kluster med antingen den ***ReverseProxyCertificate*** eller ***ReverseProxyCertificateCommonNames*** -egenskapen i den **Security**delen av ClusterConfig.json. Mer information finns i [aktivera omvänd proxy i fristående kluster](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Tjänster kan implementera logik för att verifiera certifikatet från den omvända proxyn. Tjänsterna kan ange godkända klienten certifikatinformation som konfigurationsinställningar i konfigurationspaketet. Detta kan läsa vid körning och används för att verifiera certifikatet från den omvända proxyn. Referera till [hantera programparametrar](service-fabric-manage-multiple-environment-app-configuration.md) att lägga till konfigurationsinställningarna. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Omvänd proxy som verifierar tjänstidentitet via certifikatet som presenterades av tjänsten:
Omvänd proxy har stöd för följande principer för att utföra valideringen av servercertifikatet för de certifikat som presenteras av tjänster: Ingen, ServiceCommonNameAndIssuer och ServiceCertificateThumbprints.
Välj principen för omvänd proxy för att använda, ange den **ApplicationCertificateValidationPolicy** i den **ApplicationGateway/Http** avsnittet [fabricSettings](service-fabric-cluster-fabric-settings.md).

I nästa avsnitt visar konfigurationsinformation för var och en av dessa alternativ.

### <a name="service-certificate-validation-options"></a>Verifieringsalternativ för Service-certifikat 

- **Ingen**: Omvänd proxy hoppar över kontroll av proxyn Tjänstcertifikatet och upprättar en säker anslutning. Detta är standardbeteendet.
Ange den **ApplicationCertificateValidationPolicy** med värdet **ingen** i den [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) avsnittet.

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

- **ServiceCommonNameAndIssuer**: Omvänd proxy verifierar certifikatet som presenterades av tjänsten baserat på namn och omedelbar utfärdartumavtrycket: Ange den **ApplicationCertificateValidationPolicy** med värdet **ServiceCommonNameAndIssuer** i den [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) avsnittet.

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

   Lista över vanliga tjänstnamn och certifikatutfärdarens tumavtryck lägger du till en [ **ApplicationGateway/Http/ServiceCommonNameAndIssuer** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) avsnittet **fabricSettings**, som visas nedan. Flera certifikat vanliga namn och tumavtrycket par kan läggas till i den **parametrar** matris. 

   Om slutpunkten omvänd proxy ansluter till anger ett certifikat som är vanliga namn och tumavtryck matchar någon av de värden som anges här, SSL-kanal upprättas. 
   Vid fel att matcha certifikatinformationen misslyckas omvänd proxy klientens begäran med statuskoden 502 (felaktig Gateway). HTTP-statusraden innehåller också frasen ”ogiltigt SSL-certifikat”. 

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

- **ServiceCertificateThumbprints**: Omvänd proxy kontrollerar via proxy Tjänstcertifikatet baserat på dess tumavtryck. Du kan välja att gå den här vägen när tjänsterna har konfigurerats med self signerade certifikat: Ange den **ApplicationCertificateValidationPolicy** med värdet **ServiceCertificateThumbprints** i den [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) avsnittet.

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

   Även ange tumavtryck med en **ServiceCertificateThumbprints** post i den **ApplicationGateway/Http** avsnittet. Flera tumavtryck kan anges som en kommaavgränsad lista i värdefältet enligt nedan:

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

   Om tumavtrycket för certifikatet visas i config transaktionen lyckas omvänd proxy SSL-anslutningen. I annat fall avslutas anslutningen och misslyckas klientens begäran med en 502 (felaktig Gateway). HTTP-statusraden innehåller också frasen ”ogiltigt SSL-certifikat”.

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Slutpunkt för val av logik när tjänster uppvisar säker samt oskyddade slutpunkter
Service fabric stöder konfigurera flera slutpunkter för en tjänst. Mer information finns i [ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md).

Omvänd proxy väljer en av slutpunkterna att vidarebefordra begäran baserat på den **ListenerName** frågeparameter i den [tjänsten URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Om den **ListenerName** parametern inte anges, omvänd proxy kan välja valfri slutpunkt från listan över slutpunkter. Beroende på de slutpunkter som konfigurerats för tjänsten, kan slutpunkten valt vara en HTTP eller HTTPS-slutpunkt. Det kan finnas krav eller scenarier där du vill att den omvända proxyn ska fungera i en ”endast säkra läget”. det vill säga att du inte vill säker omvänd proxy som vidarebefordrar begäranden till oskyddade slutpunkter. För att ange omvänd proxy för läget för endast säkra, ange den **SecureOnlyMode** konfigurationspost med värdet **SANT** i den [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) avsnittet.   

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
> Vid användning i **SecureOnlyMode**, om en klient har angett en **ListenerName** motsvarar en HTTP(unsecured) slutpunkt omvänd proxy misslyckas på begäran med ett 404 (hittades inte) HTTP-statuskod.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Konfigurera certifikat för klientautentisering via omvänd proxy
SSL-avslutning sker på den omvända proxyn och alla data för klient-certifikatet går förlorade. För tjänsterna som används för att utföra autentisering med klientcertifikat, ange den **ForwardClientCertificate** i den [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) avsnittet.

1. När **ForwardClientCertificate** är inställd på **FALSKT**, använda omvänd proxy begär inte klientcertifikatet under dess SSL-handskakningen med klienten.
Detta är standardbeteendet.

2. När **ForwardClientCertificate** är inställd på **SANT**, använda omvänd proxy kräver klientens certifikat under dess SSL-handskakningen med klienten.
Sedan vidarebefordras klienten certifikatdata i ett anpassat HTTP-huvud med namnet **X-klientcertifikat**. Huvudets värde är base64-kodad PEM Formatsträngen för klientens certifikat. Tjänsten kan lyckas/misslyckas på begäran med lämplig statuskod efter att ha inspekterat certifikatdata.
Om klienten inte ett certifikat, omvänd proxy vidarebefordrar en tom rubrik och fjärrhantering av tjänsten som hanterar fallet.

> [!NOTE]
> Omvänd proxy är en är antalet vidarebefordrare. Det kommer inte att utföra alla validering av klientens certifikat.


## <a name="next-steps"></a>Nästa steg
* [Installera och konfigurera omvänd proxy i ett kluster](service-fabric-reverseproxy-setup.md).
* Referera till [konfigurera omvänd proxy för att ansluta till säker tjänster](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) för Azure Resource Manager-mallexempel konfigurera secure omvänd proxy med olika Tjänstcertifikatet verifieringsalternativ.
* Se ett exempel på HTTP-kommunikation mellan tjänster i en [exempelprojektet på GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [RPC-anrop med Reliable Services-fjärrkommunikation](service-fabric-reliable-services-communication-remoting.md)
* [Webb-API som använder OWIN i Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md)
