---
title: Autentiseringsmetoder för Azure Security Center för förhandsversionen av IoT | Microsoft Docs
description: Läs mer om de olika autentiseringsmetoderna som är tillgängliga när du använder Azure Security Center för IoT-tjänsten.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: d1f9d77d1f87e8d201e2cb034401bb3cae14f41b
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862921"
---
# <a name="security-agent-authentication-methods"></a>Security-agenten autentiseringsmetoder 

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln beskrivs de olika autentiseringsmetoderna som du kan använda med AzureIoTSecurity agent för att autentisera med IoT Hub.

En modul för maskinvarusäkerhet krävs för varje enhet publicerats till Azure Security Center (ASC) för IoT i IoT Hub. ASC för IoT som för att autentisera enheten kan använda någon av två metoder. Välj den metod som passar bäst för din befintliga IoT-lösning. 

> [!div class="checklist"]
> * Modulen säkerhetsalternativ
> * Enhetsinställningar

## <a name="authentication-methods"></a>Autentiseringsmetoder

De två metoderna för att AzureIoTSecurity agenten ska utföra autentisering:

 - **Modulen** autentiseringsläge<br>
   Modulen är autentiserad oberoende av enhetstvillingen.
   Använd den här autentiseringstypen om du vill ha säkerhetsagenten att använda en dedikerad autentiseringsmetod via säkerhetsmodul (endast för den symmetriska nyckeln).
        
 - **Enheten** autentiseringsläge<br>
    I den här metoden autentiserar säkerhetsagenten först med enhetens identitet. När den första autentiseringen ASC för IoT-agenten utför en **REST** anrop till IoT-hubben med hjälp av REST-API med autentiseringsinformationen för enheten. ASC för IoT-agenten begär sedan autentiseringsmetoden för nätverkssäkerhet modulen och data från IoT Hub. I det sista steget utför ASC för IoT-agenten en autentisering mot ASC för IoT-modulen.
    
    Använd den här autentiseringstypen om du vill ha säkerhetsagenten om du vill återanvända en befintlig enhet-autentiseringsmetod (självsignerade certifikat eller symmetrisk nyckel). 

Se [Security agent installationsparametrar](#security-agent-installation-parameters) att lära dig hur du konfigurerar.
                                
## <a name="authentication-methods-known-limitations"></a>Autentiseringsmetoder som är kända begränsningar

- **Modulen** autentiseringsläge stöder endast symmetrisk nyckelautentisering.
- CA-signerat certifikat stöds inte av **enhet** autentiseringsläge.  

## <a name="security-agent-installation-parameters"></a>Parametrar för installation av agent

När [distribuerar en säkerhetsagenten](how-to-deploy-agent.md), autentisering-information om måste anges som argument.
De här argumenten finns dokumenterade i följande tabell.


|Parameter|Beskrivning|Alternativ|
|---------|---------------|---------------|
|**identity**|Autentiseringsläge| **Modulen** eller **enhet**|
|**typ**|Autentiseringstyp|**SymmetricKey** eller **SelfSignedCertificate**|
|**filePath**|Absolut fullständig sökväg till filen som innehåller certifikatet eller den symmetriska nyckeln| |
|**gatewayHostname**|FQDN för IoT Hub|Exempel: ContosoIotHub.azure-devices.net|
|**deviceId**|Enhets-ID|Exempel: MyDevice1|
|**certificateLocationKind**|Lagringsplats för certifikat|**Lokalfil** eller **Store**|


När du använder installationsskriptet security-agenten, utförs automatiskt följande konfiguration.

Redigera säkerhetsautentisering för agenten manuellt genom att redigera konfigurationsfilen. 

## <a name="change-authentication-method-after-deployment"></a>Ändra autentiseringsmetod efter distributionen

När du distribuerar en security-agenten med ett skript för installation, skapas automatiskt en konfigurationsfil.

Om du vill ändra autentiseringsmetoder efter distributionen, krävs manuell redigering av konfigurationsfilen.


### <a name="c-based-security-agent"></a>C#-baserade säkerhetsagenten

Redigera _Authentication.config_ med följande parametrar:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>C-baserade security-agenten

Redigera _LocalConfiguration.json_ med följande parametrar:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Se också
- [Säkerhetsöversikt för agenter](security-agent-architecture.md)
- [Distribuera säkerhetsagenten](how-to-deploy-agent.md)
- [Få åtkomst till råsäkerhetsdata](how-to-security-data-access.md)
