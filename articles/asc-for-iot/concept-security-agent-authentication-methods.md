---
title: Autentiseringsmetoder för Azure Security Center för IoT| Microsoft-dokument
description: Lär dig mer om de olika autentiseringsmetoder som är tillgängliga när du använder Azure Security Center for IoT-tjänsten.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 16f7f91e02d118d9f9a295ebb79a6cd0187dd9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596467"
---
# <a name="security-agent-authentication-methods"></a>Autentiseringsmetoder för säkerhetsagenter 

I den här artikeln beskrivs de olika autentiseringsmetoder som du kan använda med AzureIoTSecurity-agenten för att autentisera med IoT Hub.

För varje enhet som finns ombord på Azure Security Center för IoT i IoT Hub krävs en säkerhetsmodul. För att autentisera enheten kan Azure Security Center for IoT använda en av två metoder. Välj den metod som fungerar bäst för din befintliga IoT-lösning. 

> [!div class="checklist"]
> * SecurityModule alternativ
> * Enhetsalternativ

## <a name="authentication-methods"></a>Autentiseringsmetoder

De två metoderna för AzureIoTSecurity-agenten för att utföra autentisering:

 - **Säkerhetsmodulautentiseringsläge**<br>
   Agenten autentiseras med hjälp av säkerhetsmodulens identitet oberoende av enhetens identitet.
   Använd den här autentiseringstypen om du vill att säkerhetsagenten ska använda en dedikerad autentiseringsmetod via säkerhetsmodulen (endast symmetrisk nyckel).
        
 - **Autentiseringsläge** för enhet<br>
    I den här metoden autentiserar säkerhetsagenten först med enhetens identitet. Efter den första autentiseringen utför Azure Security Center för IoT-agenten ett **REST-anrop** till IoT-hubben med REST-API:et med enhetens autentiseringsdata. Azure Security Center för IoT-agent begär sedan autentiseringsmetoden för säkerhetsmodulen och data från IoT Hub. I det sista steget utför Azure Security Center för IoT-agenten en autentisering mot Azure Security Center för IoT-modulen.
    
    Använd den här autentiseringstypen om du vill att säkerhetsagenten ska återanvända en befintlig enhetsautentiseringsmetod (självsignerat certifikat eller symmetrisk nyckel). 

Se [Installationsparametrar för säkerhetsagenter](#security-agent-installation-parameters) för att lära dig hur du konfigurerar.
                                
## <a name="authentication-methods-known-limitations"></a>Kända begränsningar för autentiseringsmetoder

- **SecurityModule-autentiseringsläge** stöder endast symmetrisk nyckelautentisering.
- CA-signerat certifikat stöds inte av **enhetsautentiseringsläget.**  

## <a name="security-agent-installation-parameters"></a>Installationsparametrar för säkerhetsagenter

När [en säkerhetsagent distribueras](how-to-deploy-agent.md)måste autentiseringsinformation anges som argument.
Dessa argument dokumenteras i följande tabell.


|Namn på Linux-parameter | Namn på Windows-parameter | Parameter för stenografi |Beskrivning|Alternativ|
|---------------------|---------------|---------|---------------|---------------|
|autentisering-identitet|AutentiseringIdentitet|Aui|Autentiseringsidentitet| **SecurityModule** eller **Enhet**|
|autentiseringsmetod|AuthenticationMethod|Aum|Autentiseringsmetod|**SymmetricKey** eller **SelfSignedCertificate**|
|filsökväg|Filepath|f|Absolut fullständig sökväg för filen som innehåller certifikatet eller den symmetriska nyckeln| |
|värdnamn|Värdnamn|Hn|FQDN för IoT-hubben|Exempel: ContosoIotHub.azure-devices.net|
|enhet-id|DeviceId|Di|Enhets-ID|Exempel: MyDevice1|
|certifikat-plats-typ|CertifikatLokaliseringskinn|Cl|Lagringsplats för certifikat|**LocalFile** eller **Store**|
|


När du använder skriptet för installationssäkerhetsagent utförs följande konfiguration automatiskt. Redigera konfigurationen av säkerhetsagenten manuellt genom att redigera konfigurationsfilen. 

## <a name="change-authentication-method-after-deployment"></a>Ändra autentiseringsmetod efter distribution

När du distribuerar en säkerhetsagent med ett installationsskript skapas en konfigurationsfil automatiskt.

Om du vill ändra autentiseringsmetoder efter distribution krävs manuell redigering av konfigurationsfilen.


### <a name="c-based-security-agent"></a>C#-baserad säkerhetsagent

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

### <a name="c-based-security-agent"></a>C-baserad säkerhetsagent

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

## <a name="see-also"></a>Se även
- [Översikt över säkerhetsagenter](security-agent-architecture.md)
- [Distribuera säkerhetsagent](how-to-deploy-agent.md)
- [Få åtkomst till råsäkerhetsdata](how-to-security-data-access.md)
