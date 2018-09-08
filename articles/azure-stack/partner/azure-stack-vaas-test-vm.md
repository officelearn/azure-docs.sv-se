---
title: Distribuera lokal agent och testa avbildningen virtuella datorer i Azure Stack-validering som en tjänst | Microsoft Docs
description: Distribuera lokal agent och testa avbildningen virtuella datorer för Azure Stack-verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 78136ab00dcba2f8a99df36ba99d384b49995882
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159222"
---
# <a name="deploy-the-local-agent-and-test-virtual-machines"></a>Distribuera de lokala virtuella datorerna för agenten och testning

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Lär dig hur du använder verifieringen som en lokal agent service (VaaS) för att kontrollera om maskinvaran. Lokal agent måste distribueras på Azure Stack-lösningen som ska verifieras innan du kör verifieringstester.

> [!Note]  
> Du måste se till att den dator där lokal agent körs inte förlorar åtkomst till Internet. Datorn måste bara vara tillgänglig för användare som har behörighet att använda Azure Stack VaaS.

Så här testar dina virtuella datorer:

1. Installera lokal agent
2. Mata in fel i systemet
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
        Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.3.2.0.nupkg" -outfile "OnPremAgent.zip"
        Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent.3.2.0 -Force
        Set-Location VaaSOnPremAgent.3.2.0\lib\net46
    ````

3. Kör följande kommando för att installera lokal agent beroenden:

    ```PowerShell  
        $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-VaaSPrerequisites -AadTenantId <AadTenantId> `
        -ServiceAdminCreds <ServiceAdminCreds> `
        -ArmEndpoint https://adminmanagement.<ExternalFqdn> `
        -Region <Region>
    ````

    **Parametrar**

    | Parameter | Beskrivning |
    | --- | --- |
    | aadServiceAdminUser | Användaren global administratör för Azure AD-klienten. Det kan till exempel vara, vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | Lösenordet för global administratör. |
    | AadTenantId | Azure AD-klient-ID för Azure-konto som har registrerats med verifiering som en tjänst. |
    | ExternalFqdn | Du kan hämta det fullständigt kvalificerade domännamnet från konfigurationsfilen. Anvisningar finns i [testa parametrar för verifiering som en tjänst Azure Stack](azure-stack-vaas-parameters-test.md). |
    | Region | Regionen som Azure AD-klienten. |

Kommandot hämtar en offentlig avbildning lagringsplats (PIR) avbildning (OS-VHD) och kopiera från en Azure blob storage till Azure Stack-lagring. 

![Ladda ned nödvändiga komponenter](media/installingprereqs.png)

> [!Note]  
> Om du upplever låg nätverkshastighet när du hämtar dessa avbildningar, ladda ned dem separat till en lokal resurs och ange parametern **- LocalPackagePath** *FileShareOrLocalPath*. Du hittar mer information på din PIR nedladdning i avsnittet [referensen problem med nätverksanslutningen](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) av [felsöka verifiering som en tjänst](azure-stack-vaas-troubleshoot.md).

## <a name="fault-injection"></a>Felinmatning

Microsoft har utformat Azure Stack för återhämtning och ska kunna hanteras flera typer av program- och fel. Felinmatning ökar frekvensen för fel i systemet. Denna ökning kan du få problem med tidigare så att du kan minska antalet incidenter som påverkar systemet.

Kör följande kommandon för att mata in fel i systemet.

1. Öppna Windows PowerShell i en upphöjd kommandotolk.

2. Kör följande kommando:

    ```PowerShell  
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-ServiceFabricSDK Install-ServiceFabricSDK
    ```

## <a name="run-the-agent"></a>Kör agenten

1. Öppna Windows PowerShell i en upphöjd kommandotolk.

2. Kör följande kommando:

    ````PowerShell  
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ````

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

En agent identifieras unikt genom dess namn. Som standard använder det fullständigt kvalificerade domännamnet namn (FQDN) för den virtuella datorn från där den startades. Du måste minimera fönster för att undvika eventuella oavsiktlig klick i fönstret som ändrar fokus pausar alla andra åtgärder.

## <a name="next-steps"></a>Nästa steg

- [Verifiera en ny Azure Stack-lösning](azure-stack-vaas-validate-solution-new.md)  
- Om du har långsam eller tillfälligt ansluten till Internet, kan du hämta PIR-avbildning. Mer information finns i [referensen problem med nätverksanslutningen](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity).
- Mer information om [Azure Stack-verifiering som en tjänst](https://docs.microsoft.com/azure/azure-stack/partner).
