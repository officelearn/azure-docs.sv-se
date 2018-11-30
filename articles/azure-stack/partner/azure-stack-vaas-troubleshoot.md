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
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c6884ff0949949c03787ff5c0b8c7f8c1397400f
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334031"
---
# <a name="troubleshoot-validation-as-a-service"></a>Felsöka verifiering som en tjänst

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Här följer några vanliga problem som inte är relaterade till programversioner och sina lösningar.

## <a name="local-agent"></a>Lokal agent

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>Portalen visar lokal agent i felsökningsläge

Det här är sannolikt eftersom agenten inte kan skicka pulsslag till tjänsten på grund av en instabil nätverksanslutning. Ett pulsslag skickas var femte minut. Om tjänsten inte får ett pulsslag under 15 minuter, tjänsten tar hänsyn till agenten inaktiva och kommer inte längre att schemalägga testerna på den. Kontrollera felmeddelandet i den *Agenthost.log* fil som finns i katalogen där agenten har startats.

> [!Note]
> Alla tester som redan körs på agenten fortsätter att köras, men om pulsslag inte har återställts innan testet avslutas och sedan växlar agenten att uppdatera status för testning eller ladda upp loggarna. Testet visas alltid som **kör** och måste avbrytas.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Agent-processen på datorn stängdes av vid körning av test. Vad som händer?

Om agenten processen stängs av ungracefully till exempel, datorn startas om, processen avslutades (CTRL + C i fönstret agenten anses avslutning) testet som kördes på den kommer fortsätta att visa som **kör**. Om agenten har startats om kommer agenten att uppdatera status för att testet ska **avbröts**. Om agenten inte startas om och sedan testet visas som **kör** och du måste manuellt avsluta testet.

> [!Note]
> Testerna i ett arbetsflöde är schemalagda att köras sekventiellt. **Väntande** tester körs inte förrän tester i den **kör** tillstånd i samma arbetsflödet är klart.

## <a name="vm-images"></a>VM-avbildningar

### <a name="handle-slow-network-connectivity"></a>Hantera problem med nätverksanslutningen

Du kan hämta PIR avbildningen till en resurs i ditt lokala datacenter. Och du kan kontrollera avbildningen.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Hämta PIR bild till lokala resurser vid långsam nätverkstrafik

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

#### <a name="verifying-pir-image-file-hash-value"></a>Verifiera PIR bild filen hash-värde

Du kan använda **Get-HashFile** cmdlet för att hämta hash-värdet för den nedladdade offentliga avbildningslagringsplatsen bildfiler att kontrollera integriteten för avbildningarna.

| Filnamn | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.VHD | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.VHD | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.VHD | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604 20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>Felet inträffar när du laddar upp avbildningen i den `VaaSPreReq` skript

Kontrollera först att miljön är felfri:

1. Från DVM / hoppa box, kontrollera att du kan logga in på administrationsportalen med hjälp av autentiseringsuppgifter som administratör för.
1. Bekräfta att det finns inga aviseringar eller varningar.

Om miljön är felfri, manuellt Överför 5 VM-avbildningarna som krävs för VaaS testkörningar:

1. Logga in som tjänstadministratör på administrationsportalen för. Du kan hitta admin portal URL: en från FN store eller din fil med tidsstämpel. Anvisningar finns i [miljö parametrar](azure-stack-vaas-parameters.md#environment-parameters).
1. Välj **fler tjänster** > **Resursprovidrar** > **Compute** > **VM-avbildningar**.
1. Välj den **+ Lägg till** längst upp på den **VM-avbildningar** bladet.
1. Ändra eller Kontrollera värdena för följande fält för den första VM-avbildningen:
    > [!IMPORTANT]
    > Inte alla standardvärden är korrekta för det befintliga objektet i Marketplace.

    | Fält  | Värde  |
    |---------|---------|
    | Utgivare | MicrosoftWindowsServer |
    | Erbjudande | WindowsServer |
    | OS-typ | Windows |
    | SKU | 2012-R2-Datacenter |
    | Version | 1.0.0 |
    | OS-disken Blob-URI | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. Välj knappen **Skapa**.
1. Upprepa för återstående VM-avbildningarna.

Egenskaperna för alla 5 VM-avbildningar är följande:

| Utgivare  | Erbjudande  | OS-typ | SKU | Version | OS-disken Blob-URI |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer | Windows | 2012-R2-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |

## <a name="next-steps"></a>Nästa steg

- Granska [viktig information för verifiering som en tjänst](azure-stack-vaas-release-notes.md) för ändringar i den senaste versionen.