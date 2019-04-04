---
title: Med hjälp av privilegierad slutpunkt i Azure Stack | Microsoft Docs
description: Visar hur du använder den privilegierade slutpunkten (program) i Azure Stack (för Azure Stack-operatör).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: ef75b161bcdb9e1b9658612b783dff46d1fa2502
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484346"
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Med hjälp av privilegierad slutpunkt i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Som Azure Stack-operatör bör du använda administratörsportalen, PowerShell eller Azure Resource Manager-API:er för de flesta dagliga hanteringsuppgifter. Men för vissa mindre vanliga åtgärder, som du behöver använda den *privilegierad slutpunkt* (program). Detta program är en förkonfigurerad PowerShell fjärrkonsolen ger tillgång till tillräckligt funktioner för att utföra en viss uppgift. Slutpunkten använder [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/powershell/jea/overview) att exponera endast en begränsad uppsättning cmdlets. Ett lågprivilegierat konto används för att komma åt detta program och anropa begränsad uppsättning cmdlets. Det krävs inga administratörskonton. För ytterligare säkerhet tillåts skript inte.

Du kan använda detta program för att utföra uppgifter, till exempel följande:

- Arbetsuppgifter på låg nivå, till exempel [samla in diagnostikloggar](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Att utföra många aktiviteter för efter distributionen datacenter-integrering för integrerade system, till exempel lägga till System DNS (Domain Name) vidarebefordrare efter distributionen kan ställa in Microsoft Graph-integrering, Active Directory Federation Services (AD FS)-integrering certifikatrotering, osv.
- Att arbeta med stöd för att hämta tillfälligt, övergripande åtkomst för dig att felsöka ett integrerat system.

Detta program loggar alla åtgärder (och dess motsvarande utdata) som du utför i PowerShell-session. Detta ger fullständig genomskinlighet och fullständig granskning av åtgärder. Du kan behålla loggfilerna för framtida revisioner.

> [!NOTE]
> I den Azure Stack Development Kit (ASDK), kan du köra några av kommandona som är tillgängliga i detta program direkt från en PowerShell-session på development kit värden. Dock kanske du vill testa vissa åtgärder med hjälp av detta program, till exempel Logginsamling, eftersom det här är den enda tillgängliga metoden för att utföra vissa åtgärder i en miljö med integrerade system.

## <a name="access-the-privileged-endpoint"></a>Få åtkomst till privilegierad slutpunkt

Du kommer åt detta program via en PowerShell-fjärrsession på den virtuella datorn som är värd för detta program. I ASDK denna virtuella dator heter **AzS ERCS01**. Om du använder ett integrerat system, det finns tre instanser av detta program, varje körs i en virtuell dator (*prefixet*-ERCS01, *prefixet*-ERCS02, eller *prefixet*- ERCS03) på olika värdar för återhämtning. 

Innan du påbörjar den här proceduren för ett integrerat system, kontrollera att du kan komma åt detta program efter IP-adress eller via DNS. Efter den första distributionen av Azure Stack kan du komma åt detta program bara av IP-adress eftersom DNS-integrering inte har ännu ställts in. Maskinvaruleverantören OEM får du en JSON-fil med namnet **AzureStackStampDeploymentInfo** som innehåller program-IP-adresser.


> [!NOTE]
> Av säkerhetsskäl kräver att du ansluter till detta program från en förstärkt virtuell dator som kör ovanpå maskinvara livscykel värden eller från en dedikerad, säker dator, till exempel en [arbetsstation för privilegierad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). Den ursprungliga konfigurationen av maskinvara livscykel värden får inte ändras från den ursprungliga konfigurationen, inklusive installera ny programvara och bör inte användas att ansluta till detta program.

1. Upprätta förtroende.

    - Kör följande kommando från en upphöjd Windows PowerShell-session för att lägga till detta program som en betrodd värd på den strikta virtuella datorn körs på maskinvara livscykel värden eller den privilegierade åtkomst-arbetsstationen på ett integrerat system.

      ```powershell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - Om du kör ASDK, kan du logga in på värden development kit.

2. Öppna en Windows PowerShell-session på den strikta virtuella datorn körs på maskinvara livscykel värd eller Privileged Access Workstation. Kör följande kommandon för att upprätta en fjärrsession på den virtuella datorn som är värd för detta program:
 
   - På ett integrerat system:
     ```powershell
       $cred = Get-Credential

       Enter-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     Den `ComputerName` parameter kan vara antingen IP-adressen eller DNS-namnet på en av de virtuella datorerna som är värd för detta program. 
   - Om du kör ASDK:
     
     ```powershell
       $cred = Get-Credential

       Enter-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     När du uppmanas, Använd följande autentiseringsuppgifter:

     - **Användarnamnet**: Ange CloudAdmin-konto i formatet  **&lt; *Azure Stack-domänen*&gt;\cloudadmin**. (För ASDK, användarnamnet är **azurestack\cloudadmin**.)
     - **Lösenord**: Ange samma lösenord som angavs under installationen för AzureStackAdmin domänadministratörskontot.

     > [!NOTE]
     > Om det inte går att ansluta till slutpunkten ERCS prova steg ett och två igen med IP-adressen för en ERCS VM som du redan inte har försökt att ansluta.

3. När du har anslutit prompten ändras till **[*IP-adress eller ERCS VM namnge*]: PS >** eller **[azs-ercs01]: PS >**, beroende på miljön. Härifrån kan köra `Get-Command` att visa listan över tillgängliga cmdlet: ar.

   Många av dessa cmdletar är avsedda enbart för integrerat system-miljöer (till exempel de cmdletar som rör datacenter-integrering). Följande cmdletar har verifierats i ASDK:

   - Clear-värd
   - Close-PrivilegedEndpoint
   - Avsluta-PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Measure-Object
   - New-CloudAdminUser
   - Out-Default
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Tips för att använda privilegierad slutpunkt 

Som nämnts ovan är detta program är en [PowerShell JEA](https://docs.microsoft.com/powershell/jea/overview) slutpunkt. Samtidigt som det ger en stark säkerhetslager, minskar en JEA-slutpunkt några av de grundläggande funktionerna i PowerShell, till exempel skript- eller fliken slutförande. Om du försöker alla typer av åtgärder för skriptet misslyckas med fel **ScriptsNotAllowed**. Detta är förväntat.

Därför exempelvis köra för att få en lista med parametrar för en viss cmdlet kan du följande kommando:

```powershell
    Get-Command <cmdlet_name> -Syntax
```

Du kan också använda den [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) cmdlet för att importera alla program-cmdletar till den aktuella sessionen på den lokala datorn. Då kan är alla cmdletar och funktioner i detta program nu tillgängliga på den lokala datorn, tillsammans med tabbifyllning med mera, i allmänhet skript. 

Om du vill importera program-session på den lokala datorn, gör du följande:

1. Upprätta förtroende.

    -Kör följande kommando från en upphöjd Windows PowerShell-session för att lägga till detta program som en betrodd värd på strikt virtuell dator som körs på maskinvara livscykel värden eller Privileged Access Workstation på ett integrerat system.

      ```powershell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - Om du kör ASDK, kan du logga in på värden development kit.

2. Öppna en Windows PowerShell-session på den strikta virtuella datorn körs på maskinvara livscykel värd eller Privileged Access Workstation. Kör följande kommandon för att upprätta en fjärrsession på den virtuella datorn som är värd för detta program:
 
   - På ett integrerat system:
     ```powershell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     Den `ComputerName` parameter kan vara antingen IP-adressen eller DNS-namnet på en av de virtuella datorerna som är värd för detta program. 
   - Om du kör ASDK:
     
     ```powershell
      $cred = Get-Credential

      $session = New-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     När du uppmanas, Använd följande autentiseringsuppgifter:

     - **Användarnamnet**: Ange CloudAdmin-konto i formatet  **&lt; *Azure Stack-domänen*&gt;\cloudadmin**. (För ASDK, användarnamnet är **azurestack\cloudadmin**.)
     - **Lösenord**: Ange samma lösenord som angavs under installationen för AzureStackAdmin domänadministratörskontot.

3. Importera program-sessionen till den lokala datorn
    ```powershell 
        Import-PSSession $session
    ```
4. Du kan nu använda tabbifyllning och göra skript som vanligt på din lokala PowerShell-session med alla funktioner och cmdletar för detta program, utan att minska säkerhetspositionen för Azure Stack. Ha det så kul!


## <a name="close-the-privileged-endpoint-session"></a>Stäng privilegierad slutpunkt-sessionen

 Såsom nämnts tidigare loggar detta program alla åtgärder (och dess motsvarande utdata) som du utför i PowerShell-session. Du måste stänga sessionen med hjälp av den `Close-PrivilegedEndpoint` cmdlet. Denna cmdlet korrekt stängs slutpunkten och överför filerna till en extern filresurs för kvarhållning.

Att Stäng endpoint-sessionen:

1. Skapa en extern filresurs som kan nås av detta program. Du kan bara skapa en filresurs på development kit värden i en utvecklingsmiljö av kit.
2. Kör den `Close-PrivilegedEndpoint` cmdlet. 
3. Du uppmanas att ange en sökväg som du vill lagra loggfilen avskrift. Ange den filresurs som du skapade tidigare, i formatet &#92;&#92;*servername*&#92;*resursnamn*. Om du inte anger en sökväg kommer cmdleten misslyckas och förblir sessionen öppen. 

    ![Stäng PrivilegedEndpoint cmdlet-utdata som visas anger du målsökväg avskrift](media/azure-stack-privileged-endpoint/closeendpoint.png)

När avskrift filerna har överförts till filresursen, är de automatiskt bort från detta program. 

> [!NOTE]
> Om du stänger den program-sessionen med hjälp av cmdlets `Exit-PSSession` eller `Exit`, eller du stänga PowerShell-konsolen, avskrift-loggar överförs inte till en filresurs. De finns kvar i detta program. Nästa gång du kör `Close-PrivilegedEndpoint` och inkludera en filresurs, avskrift loggarna från föregående spårningssession också överför. Använd inte `Exit-PSSession` eller `Exit` att Stäng program-session; Använd `Close-PrivilegedEndpoint` i stället.


## <a name="next-steps"></a>Nästa steg

[Diagnostiska verktyg för Azure Stack](azure-stack-diagnostics.md)
