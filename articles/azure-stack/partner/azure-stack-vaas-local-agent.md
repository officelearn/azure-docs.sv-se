---
title: Distribuera lokal agent | Microsoft Docs
description: Distribuera lokal agent för Azure Stack-verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 23bbcbf6947100db26f31562c44f8073e16e986f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239349"
---
# <a name="deploy-the-local-agent"></a>Distribuera lokal agent

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Lär dig hur du använder verifieringen som en lokal agent Service (VaaS) för att kontrollera om maskinvaran. Lokal agent måste distribueras på Azure Stack-lösningen som ska verifieras innan du kör verifieringstester.

> [!Note]  
> Du måste se till att datorn som lokal agent körs inte förlorar ut bundna åtkomst till internet. Den här datorn måste vara tillgänglig endast för användare som du har behörighet för att använda VaaS för din klient.

För att distribuera lokal agent:

1. Installera lokal agent
2. Utföra hälsokontroller
3. Kör lokal agent

## <a name="download-and-start-the-local-agent"></a>Ladda ned och starta lokal agent

Ladda ned agenten till en dator som uppfyller kraven i ditt datacenter som inte är en del av Azure Stack-system, men en som har åtkomst till alla Azure Stack-slutpunkter.

### <a name="machine-prerequisites"></a>Förutsättningar för dator

Kontrollera att datorn uppfyller följande kriterier:

- Åtkomst till alla Azure Stack-slutpunkter
- .NET 4.6 och PowerShell 5.0 installerat
- Minst 8 GB RAM-minne
- Minst 8-kärniga processorer
- Minsta 200 GB ledigt diskutrymme
- Stabil nätverksanslutning till internet

Azure Stack är systemet under testet. Datorn får inte vara en del av Azure Stack eller värd i Azure Stack-molnet.

### <a name="download-and-install-the-agent"></a>Ladda ned och installera agenten

1. Öppna Windows PowerShell i en upphöjd kommandotolk på den dator som du använder för att köra testerna.
2. Kör följande kommando för att hämta den lokala agenten:

    ```PowerShell
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. Kör följande kommando för att installera lokal agent beroenden:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **Parametrar**

    | Parameter | Beskrivning |
    | --- | --- |
    | aadServiceAdminUser | Användaren global administratör för Azure AD-klienten. Det kan till exempel vara, vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | Lösenordet för global administratör. |
    | AadTenantId | Azure AD-klient-ID för Azure-konto som har registrerats med verifiering som en tjänst. |
    | ExternalFqdn | Du kan hämta det fullständigt kvalificerade domännamnet från konfigurationsfilen. Anvisningar finns i [arbetsflödets gemensamma parametrar i Azure Stack-validering som en tjänst](azure-stack-vaas-parameters.md). |
    | Region | Regionen som Azure AD-klienten. |

Kommandot hämtar en offentlig avbildning lagringsplats (PIR) avbildning (OS-VHD) och kopiera från en Azure blob storage till Azure Stack-lagring.

![Ladda ned nödvändiga komponenter](media/installingprereqs.png)

> [!Note]
> Om du upplever låg nätverkshastighet när du hämtar dessa avbildningar, ladda ned dem separat till en lokal resurs och ange parametern **- LocalPackagePath** *FileShareOrLocalPath*. Du hittar mer information på din PIR nedladdning i avsnittet [referensen problem med nätverksanslutningen](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) av [felsöka verifiering som en tjänst](azure-stack-vaas-troubleshoot.md).

## <a name="checks-before-starting-the-tests"></a>Kontrollerna innan du påbörjar testerna

Testerna köra fjärråtgärder. Den dator som kör testerna måste ha åtkomst till Azure Stack-slutpunkter, annars fungerar inte testerna. Om du använder VaaS lokal agent kan använda datorn där agenten körs. Du kan kontrollera att datorn har åtkomst till Azure Stack-slutpunkter genom att köra följande kontroller:

1. Kontrollera att bas-URI: N kan nås. Öppna en kommandotolk eller bash-gränssnittet och kör följande kommando, ersätta `<EXTERNALFQDN>` med externa FQDN för din miljö:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Öppna en webbläsare och gå till `https://adminportal.<EXTERNALFQDN>` för att kontrollera att MAS-portalen kan nås.

3. Logga in med Azure AD-tjänsten administratör namn och lösenord värden som anges när du skapar i testet.

4. Kontrollera systemets hälsotillstånd genom att köra den **Test-AzureStack** PowerShell-cmdleten enligt beskrivningen i [kör ett verifieringstest för Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test). Åtgärda eventuella varningar och fel innan du startar alla tester.

## <a name="run-the-agent"></a>Kör agenten

1. Öppna Windows PowerShell i en upphöjd kommandotolk.

2. Kör följande kommando:

    ```PowerShell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ```

      **Parametrar**  
    | Parameter | Beskrivning |
    | --- | --- |
    | VaaSUserId | Användar-ID som används för att logga in på portalen VaaS (till exempel UserName@Contoso.com) |
    | VaaSTenantId | Azure AD-klient-ID för Azure-konto som har registrerats med verifiering som en tjänst. |

    > [!Note]  
    > När du kör agenten, den aktuella arbetskatalogen måste vara platsen för uppgiften motorn värden körbara, **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Om du inte ser några fel rapporterats har lokal agent slutförts. Följande exempel visas i konsolfönstret.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Igång agent](media/startedagent.png)

En agent identifieras unikt genom dess namn. Som standard använder det fullständigt kvalificerade domännamnet namn (FQDN) för den virtuella datorn från där den startades. Du måste minimera fönster för att undvika eventuella oavsiktlig väljer i fönstret som ändrar fokus pausar alla andra åtgärder.

## <a name="next-steps"></a>Nästa steg

- [Felsöka verifiering som en tjänst](azure-stack-vaas-troubleshoot.md)
- [Verifiering som en tjänst viktiga begrepp](azure-stack-vaas-key-concepts.md)
- [Snabbstart: Använda verifieringen som en tjänst-portal för att schemalägga första testet](azure-stack-vaas-schedule-test-pass.md)