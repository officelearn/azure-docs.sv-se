---
title: Felsöka Azure Stack-verifiering som en tjänst | Microsoft Docs
description: Felsöka verifiering som en tjänst för Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 4372da2f1057c06761dd6abaf18c26df6e33640e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235048"
---
# <a name="troubleshoot-validation-as-a-service"></a>Felsöka verifiering som en tjänst

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Här följer några vanliga problem som inte är relaterade till programversioner och sina lösningar.

## <a name="the-portal-shows-local-agent-in-debug-mode"></a>Portalen visar lokal agent i felsökningsläge

Det här är sannolikt eftersom agenten inte kan skicka pulsslag till tjänsten på grund av en instabil nätverksanslutning. Ett pulsslag skickas var femte minut. Om tjänsten inte får ett pulsslag under 15 minuter, tjänsten tar hänsyn till agenten inaktiva och kommer inte längre att schemalägga testerna på den. Kontrollera felmeddelandet i den *Agenthost.log* fil som finns i katalogen där agenten har startats.

> [!Note] 
> Alla tester som redan körs på agenten fortsätter att köras, men om pulsslag inte har återställts innan testet avslutas och sedan växlar agenten att uppdatera status för testning eller ladda upp loggarna. Testet visas alltid som **kör** och måste avbrytas.

## <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Agent-processen på datorn stängdes av vid körning av test. Vad som händer?

Om agenten processen stängs av ungracefully till exempel, datorn startas om, processen avslutades (CTRL + C i fönstret agenten anses avslutning) testet som kördes på den kommer fortsätta att visa som **kör**. Om agenten har startats om kommer agenten att uppdatera status för att testet ska **har avbrutits**. Om agenten inte startas om och sedan testet visas som **kör** och du måste manuellt avsluta testet

> [!Note] 
> Testerna i ett arbetsflöde är schemalagda att köras sekventiellt. **Väntande** tester körs inte förrän tester i den **kör** tillstånd i samma arbetsflödet är klart.

## <a name="handle-slow-network-connectivity"></a>Hantera problem med nätverksanslutningen

Du kan hämta PIR avbildningen till en resurs i ditt lokala datacenter. Och du kan kontrollera avbildningen.

<!-- This is from the appendix to the Deploy local agent topic. -->

### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Hämta PIR bild till lokala resurser vid långsam nätverkstrafik

1. Ladda ned AzCopy från: [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. Extrahera AzCopy.zip och ändra till den katalog som innehåller AzCopy.exe

3. Öppna Windows PowerShell från en upphöjd kommandotolk. Kör följande kommandon:

```PowerShell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare är sökvägen till filresursen eller lokal sökväg.

### <a name="verifying-pir-image-file-hash-value"></a>Verifiera PIR bild filen hash-värde

Du kan använda **Get-HashFile** cmdlet för att hämta hash-värdet för den nedladdade offentliga avbildningslagringsplatsen bildfiler att kontrollera integriteten för avbildningarna.

| Filnamn | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.VHD | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.VHD | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.VHD | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604 20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

## <a name="next-steps"></a>Nästa steg

- Mer information om [Azure Stack-verifiering som en tjänst](https://docs.microsoft.com/azure/azure-stack/partner).
