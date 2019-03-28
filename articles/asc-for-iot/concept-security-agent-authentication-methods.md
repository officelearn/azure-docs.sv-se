---
title: Autentiseringsmetoder för ASC för förhandsversionen av IoT | Microsoft Docs
description: Läs mer om de olika autentiseringsmetoderna som är tillgängliga när du använder ASC för IoT-tjänsten.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 23bc4d0df1c8124ec225ac31239c7acb3f1ab546
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541819"
---
# <a name="security-agent-authentication-methods"></a>Security-agenten autentiseringsmetoder 

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln beskrivs de olika autentiseringsmetoderna som du kan använda med AzureIoTSecurity agent för att autentisera med IoT Hub.

För varje enhet har integrerats i ASC för IoT i IoT Hub krävs en modul för maskinvarusäkerhet. ASC för IoT som för att autentisera enheten kan använda någon av två metoder. Välj den metod som passar bäst för din befintliga IoT-lösning. 

> [!div class="checklist"]
> * Modulen säkerhetsalternativ
> * Enhetsinställningar

## <a name="authentication-methods"></a>Autentiseringsmetoder

De två metoderna för att AzureIoTSecurity agenten ska utföra autentisering:

 - **Modulen** autentiseringsläge<br>
   Modulen är autentiserad oberoende av enhetstvillingen.
   Den information som krävs för den här typen av autentisering har definierats i av filen Authentication.config för C# och LocalConfiguration.json för C.
        
 - **Enheten** autentiseringsläge<br>
    I den här metoden autentiseras säkerhetsagenten först mot enheten. När den första autentiseringen ASC för IoT-agenten utför **Rest** anrop till IoT-hubben med hjälp av Rest-API med autentiseringsinformationen för enheten. ASC för IoT-agenten begär sedan autentiseringsmetoden för nätverkssäkerhet modulen och data från IoT Hub. I det sista steget utför ASC för IoT-agenten en autentisering mot ASC för IoT-modulen.    

Se [Security agent installationsparametrar](#security-agent-installation-parameters) att lära dig hur du konfigurerar.
                                
## <a name="authentication-methods-known-limitations"></a>Autentiseringsmetoder som är kända begränsningar

- **Modulen** autentiseringsläge stöder endast symmetrisk nyckelautentisering.
- CA-signerat certifikat stöds inte av **enhet** autentiseringsläge.  

## <a name="security-agent-installation-parameters"></a>Parametrar för installation av agent

När [distribuerar en säkerhetsagenten](select-deploy-agent.md), autentisering-information om måste anges som argument.
De här argumenten finns dokumenterade i följande tabell.


|Parameter|Beskrivning|Alternativ|
|---------|---------------|---------------|
|**Identitet**|Autentiseringsläge| **Modulen** eller **enhet**|
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
- [Distribuera säkerhetsagenten](select-deploy-agent.md)
- [Rå säkerhetsdata för åtkomst](how-to-security-data-access.md)
