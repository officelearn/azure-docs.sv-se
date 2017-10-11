---
title: "Azure Service Fabric omvänd proxy säker kommunikation | Microsoft Docs"
description: "Konfigurera omvänd proxy för att aktivera säker slutpunkt till slutpunkt-kommunikation."
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 568f9638c59282bcd7d3fae058a1588a889c22dc
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Ansluta till en säker tjänst med omvänd proxy

Den här artikeln förklarar hur du upprättar säker anslutning mellan omvänd proxy och tjänster, vilket gör att en säker kanal för slutpunkt till slutpunkt.

Ansluter till säker tjänster stöds endast när omvänd proxy har konfigurerats för att lyssna på HTTPS. Resten av dokumentet förutsätter så är fallet.
Referera till [omvänd proxy i Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) konfigurera omvänd proxy i Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Upprätta säker anslutning mellan omvänd proxy och tjänster 

### <a name="reverse-proxy-authenticating-to-services"></a>Omvänd proxy som autentiserar till tjänster:
Omvänd proxy identifierar sig själv för tjänster med hjälp av dess certifikatet som anges med ***reverseProxyCertificate*** egenskap i den **klustret** [typen avsnittet](../azure-resource-manager/resource-group-authoring-templates.md). Tjänster kan implementera logik för att verifiera certifikatet som presenterades av omvänd proxy. Tjänsterna kan ange godkända klienten certifikatinformation som konfigurationsinställningarna i konfigurationspaketet. Detta kan läsa vid körning och används för att validera certifikatet som presenterades av omvänd proxy. Referera till [hantera applikationsparametrarna](service-fabric-manage-multiple-environment-app-configuration.md) att lägga till konfigurationsinställningarna. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Omvänd proxy verifierar tjänstidentitet via certifikatet som presenterades av tjänsten:
För att utföra valideringen av servercertifikatet för de certifikat som presenteras av tjänsterna omvänd proxy stöder något av följande alternativ: None, ServiceCommonNameAndIssuer och ServiceCertificateThumbprints.
Välj något av följande tre alternativ att ange den **ApplicationCertificateValidationPolicy** i avsnittet Parametrar för ApplicationGateway/http-elementet under [fabricSettings](service-fabric-cluster-fabric-settings.md).

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

Referera till nästa avsnitt för mer information om ytterligare konfiguration för var och en av dessa alternativ.

### <a name="service-certificate-validation-options"></a>Tjänstalternativ för certifikat 

- **Ingen**: omvänd proxy hoppar över kontroll av servercertifikatet via proxy och upprättar säker anslutning. Detta är standardbeteendet.
Ange den **ApplicationCertificateValidationPolicy** med värdet **ingen** i avsnittet Parametrar för ApplicationGateway/http-elementet.

- **ServiceCommonNameAndIssuer**: omvänd proxy kontrollerar certifikatet som presenterades av tjänsten baserat på certifikatets nätverksnamn och omedelbar utfärdaren tumavtryck: Ange den **ApplicationCertificateValidationPolicy** med värdet **ServiceCommonNameAndIssuer** i avsnittet Parametrar för ApplicationGateway/http-elementet.

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

Lista över vanliga tjänstnamn och utfärdaren tumavtryck lägger du till en **ServiceCommonNameAndIssuer-ApplicationGateway/Http** elementet under fabricSettings, enligt nedan. Flera certifikatets unika namn och utfärdaren tumavtrycket par kan läggas till i matriselementet parametrar. 

Om slutpunkten omvänd proxy ansluter till anger ett certifikat som är vanliga namn och utfärdaren tumavtryck matchar någon av de värden som anges här, SSL-kanal upprättas. Vid fel att matcha certifikatinformation misslyckas omvänd proxy klientbegäran med statuskod 502 (felaktig Gateway). HTTP-statusrad innehåller också frasen ”ogiltigt SSL-certifikatet”. 

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


- **ServiceCertificateThumbprints**: omvänd proxy ska verifiera Tjänstcertifikatet via proxy baserat på dess tumavtryck. Du kan välja att gå vägen när tjänsterna som är konfigurerade med self signerade certifikat: Ange den **ApplicationCertificateValidationPolicy** med värdet **ServiceCertificateThumbprints** i avsnittet Parametrar för ApplicationGateway/http-elementet.

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

Ange tumavtryck med en **ServiceCertificateThumbprints** posten under avsnittet Parametrar för ApplicationGateway/http-elementet. Flera tumavtryck kan anges som en kommaavgränsad lista i värdefältet enligt nedan:

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

Om tumavtrycket för certifikatet visas i den här posten config lyckas omvänd proxy SSL-anslutning. Annars avslutar anslutningen och misslyckas klientbegäran med 502 (felaktig Gateway). HTTP-statusrad innehåller också frasen ”ogiltigt SSL-certifikatet”.

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Slutpunkten markeringen logik när tjänster exponera säker samt oskyddat slutpunkter
Service fabric stöder konfiguration av flera slutpunkter för en tjänst. Se [Ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md).

Omvänd proxy väljer en av slutpunkterna att vidarebefordra begäran baserat på de **ListenerName** Frågeparametern. Om detta inte anges välja den valfri slutpunkt från listan över slutpunkter. Nu kan det vara en HTTP- eller HTTPS-slutpunkt. Det kan finnas/krav för scenarier där du vill att fungera i ett ”säkert läge”, dvs omvänd proxy vill du inte säker omvänd proxy vidarebefordrar begäranden till oskyddad slutpunkter. Detta kan uppnås genom att ange den **SecureOnlyMode** konfigurationspost med värdet **SANT** i avsnittet Parametrar för ApplicationGateway/http-elementet.   

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

> 
> När du arbetar i **SecureOnlyMode**om klienten har angett en **ListenerName** motsvarar en HTTP(unsecured) slutpunkt omvänd proxy misslyckas begäran med en (inget hittas) http-status 404.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Konfigurera certifikat för klientautentisering via omvänd proxy
SSL-avslutning sker på omvänd proxy och alla data för klient-certifikatet har gått förlorade. Tjänster för autentisering av klientcertifikat, ange den **ForwardClientCertificate** i avsnittet Parametrar för ApplicationGateway/http-elementet.

1. När **ForwardClientCertificate** är inställd på **FALSKT**, använda omvänd proxy inte begär för klientcertifikatet under dess SSL-handskakning med klienten.
Detta är standardbeteendet.

2. När **ForwardClientCertificate** är inställd på **SANT**, omvänd Proxybegäranden för klientens certifikat under dess SSL-handskakning med klienten.
Sedan vidarebefordras klienten certifikatdata i en anpassad HTTP-huvud med namnet **X-klientcertifikatet**. Huvudets värde är base64-kodade PEM Formatsträngen för klientens certifikat. Tjänsten kan lyckas eller inte begäran med lämpliga statuskoden efter att ha kontrollerat certifikatdata.
Om klienten inte ett certifikat, omvänd proxy vidarebefordrar ett tomt huvud och låta tjänsten hantera fallet.

> Omvänd proxy är enbart vidarebefordrare. Det kommer inte att utföra någon validering av klientens certifikat.


## <a name="next-steps"></a>Nästa steg
* Referera till [konfigurera omvänd proxy för att ansluta till säkra tjänster](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) för Azure Resource Manager mallen exempel för att konfigurera secure omvänd proxy med olika Tjänstcertifikatet verifieringsalternativ.
* Se ett exempel på HTTP-kommunikation mellan tjänster i en [exempelprojektet på GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [RPC-anrop med Reliable Services fjärrkommunikation](service-fabric-reliable-services-communication-remoting.md)
* [Webb-API som använder OWIN i Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md)
