---
title: Med den privilegierade slutpunkten i Azure-stacken | Microsoft Docs
description: "Visar hur du använder Privilegierade slutpunkten (program) i Azure-stacken (för operatör Azure-stacken)."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: mabrigg
ms.openlocfilehash: 34ad4d7038202bd5efa2b3c210571268a39bf278
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Med den privilegierade slutpunkten i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du bör använda administratörsportal PowerShell eller Azure Resource Manager API: erna för de flesta dagliga hanteringsuppgifter som operatör Azure stacken. Men för vissa mindre vanliga åtgärder, måste du använda den *Privilegierade endpoint* (program). Detta program är en förkonfigurerad PowerShell fjärrkonsolen som ger dig bara tillräckligt med kapacitet för att utföra en obligatorisk uppgift. Slutpunkten använder [PowerShell JEA (Just tillräckligt Administration)](https://docs.microsoft.com/en-us/powershell/jea/overview) att exponera en begränsad uppsättning cmdlets för. Ett lågprivilegierat konto används för att komma åt detta program och anropa begränsad uppsättning cmdlets. Det krävs inga administratörskonton. För ytterligare säkerhet tillåts skript inte.

Du kan använda detta program för att utföra uppgifter, till exempel följande:

- Använda lågnivå-aktiviteter, till exempel [att samla in diagnostikloggar](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- För många efter distributionen datacenter integration uppgifter för integrerad system, till exempel lägga till Domain Name System (DNS) vidarebefordrare efter distributionen kan ställa in Microsoft Graph integration, Active Directory Federation Services (AD FS) integration certifikatet rotation, osv.
- Arbeta med stöd för att få tillfälligt, övergripande åtkomst för avancerad felsökning av ett integrerat system.

Detta program loggar varje åtgärd (och dess motsvarande utdata) som du utför i PowerShell-sessionen. Det ger fullständig genomskinlighet och fullständig granskning av åtgärder. Du kan behålla dessa loggfiler för framtida revisioner.

> [!NOTE]
> I den Azure Stack Development Kit (ASDK), kan du köra några av kommandona som är tillgängliga i detta program direkt från en PowerShell-session på development kit värden. Dock kanske du vill testa vissa åtgärder med hjälp av detta program, till exempel Logginsamling, eftersom det här är den enda tillgängliga metoden för att utföra vissa åtgärder i en integrerad system-miljö.

## <a name="access-the-privileged-endpoint"></a>Komma åt den privilegierade slutpunkten

Du kommer åt detta program via PowerShell-fjärrsession på den virtuella datorn som är värd för detta program. I ASDK, den virtuella datorn med namnet **AzS ERCS01**. Om du använder ett integrerat system, det finns tre instanser av detta program, varje körning i en virtuell dator (*prefixet*-ERCS01, *prefixet*-ERCS02, eller *prefixet*- ERCS03) på olika värdar för återhämtning. 

Innan du börjar den här proceduren för ett integrerat system, kontrollera att du kan komma åt detta program efter IP-adress eller via DNS. Efter den första distributionen av Azure-stacken, du kan komma åt detta program endast efter IP-adress eftersom DNS-integrering inte har ännu ställts in. Maskinvaruleverantören OEM ger dig en JSON-fil med namnet **AzureStackStampDeploymentInfo** som innehåller program IP-adresser.

Vi rekommenderar att du ansluter till detta program bara från maskinvara livscykel värden eller en dedikerad, säker dator som en [arbetsstation för privilegierad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

1. Åtkomst till din arbetsstation för privilegierad åtkomst.

    - Kör följande kommando för att lägga till detta program som en betrodd värd på din maskinvara livscykel värd eller en arbetsstation för privilegierad åtkomst på ett integrerat system.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Om du kör ADSK, kan du logga in på development kit värden.

2. Öppna en upphöjd Windows PowerShell-sessionen på din maskinvara livscykel värden eller en arbetsstation för privilegierad åtkomst. Kör följande kommandon för att upprätta en fjärrsession på den virtuella datorn som är värd för detta program:
 
    - På ett integrerat system:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      Den `ComputerName` parametern kan vara antingen IP-adressen eller DNS-namnet på en av de virtuella datorerna som är värd för detta program. 
    - Om du kör ADSK:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   När du uppmanas, Använd följande autentiseringsuppgifter:

      - **Användarnamnet**: Ange CloudAdmin-konto i formatet ** &lt; *Azure Stack domän*&gt;\cloudadmin**. (För ASDK, användarnamnet är **azurestack\cloudadmin**.)
      - **Lösenordet**: Ange samma lösenord som du angav under installationen för AzureStackAdmin domänadministratörskontot.
    
3.  När du har anslutit uppmaningen ändras till **[*IP-adress eller ERCS VM*]: PS >** eller **[azs ercs01]: PS >**, beroende på miljön. Härifrån kan köra `Get-Command` att visa listan över tillgängliga cmdlets.

    Många av dessa cmdlets är endast avsett för integrerat system-miljöer (till exempel cmdlets för datacenter-integrering). Följande cmdlets i ASDK, har verifierats:

    - Clear-Host
    - Stäng PrivilegedEndpoint
    - Avsluta-PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Mått-objekt
    - New-CloudAdminUser
    - Standard ut
    - Remove-CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - Test-AzureStack
    - Stop-AzureStack
    - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Tips om att använda Privilegierade slutpunkten 

Som nämnts ovan är detta program är en [PowerShell JEA](https://docs.microsoft.com/en-us/powershell/jea/overview) slutpunkt. Och ger en stark säkerhetsskiktet minskar en slutpunkt för JEA några av de grundläggande PowerShell-funktionerna, till exempel skript eller TABB slutförande. Om du av någon typ av skriptåtgärd åtgärden misslyckas med fel **ScriptsNotAllowed**. Detta är förväntat.

Så exempelvis köra för att få en lista över parametrar för en viss cmdlet kan du följande kommando:

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

Du kan också använda den [Import-PSSession](https://docs.microsoft.com/en-us/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) för att importera alla program-cmdletar i den aktuella sessionen på din lokala dator. På så sätt, är alla cmdletar och funktioner för detta program nu tillgängliga på den lokala datorn, tillsammans med flikavslutande med mera, i allmänhet skript. 

Om du vill importera program sessionen på den lokala datorn, gör du följande steg:

1. Åtkomst till din arbetsstation för privilegierad åtkomst.

    - Kör följande kommando för att lägga till detta program som en betrodd värd på din maskinvara livscykel värd eller en arbetsstation för privilegierad åtkomst på ett integrerat system.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Om du kör ADSK, kan du logga in på development kit värden.

2. Öppna en upphöjd Windows PowerShell-sessionen på din maskinvara livscykel värden eller en arbetsstation för privilegierad åtkomst. Kör följande kommandon för att upprätta en fjärrsession på den virtuella datorn som är värd för detta program:
 
    - På ett integrerat system:
      ````PowerShell
        $cred = Get-Credential

        $session = New-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      Den `ComputerName` parametern kan vara antingen IP-adressen eller DNS-namnet på en av de virtuella datorerna som är värd för detta program. 
    - Om du kör ADSK:
     
      ````PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   När du uppmanas, Använd följande autentiseringsuppgifter:

      - **Användarnamnet**: Ange CloudAdmin-konto i formatet ** &lt; *Azure Stack domän*&gt;\cloudadmin**. (För ASDK, användarnamnet är **azurestack\cloudadmin**.)
      - **Lösenordet**: Ange samma lösenord som du angav under installationen för AzureStackAdmin domänadministratörskontot.

3. Importera program-session till din lokala dator
    ````PowerShell 
        Import-PSSession $session
    ````
4. Du kan nu använda fliken slutförande och göra skript som vanligt på din lokala PowerShell-session med alla funktioner och -cmdlets för detta program, utan att minska säkerhetstillståndet i Azure-stacken. Ha det så kul!


## <a name="close-the-privileged-endpoint-session"></a>Stäng den privilegierade endpoint-sessionen

 Som tidigare nämnts kan loggar detta program varje åtgärd (och dess motsvarande utdata) som du utför i PowerShell-sessionen. Du bör stänga sessionen med hjälp av den `Close-PrivilegedEndpoint` cmdlet. Denna cmdlet korrekt stängs slutpunkten och överför filerna till en extern filresurs för kvarhållning.

Att stänga sessionen slutpunkt:

1. Skapa en extern resurs som kan nås av detta program. Du kan bara skapa en filresurs på development kit värden i en utvecklingsmiljö av kit.
2. Kör den `Close-PrivilegedEndpoint` cmdlet. 
3. Du uppmanas att ange en sökväg som ska lagra loggfil betyg. Ange den filresurs som du skapade tidigare, i formatet & #92; & #92; *servername*& #92;* resursnamn*. Om du inte anger en sökväg cmdlet misslyckas och förblir sessionen öppen. 

    ![Stäng PrivilegedEndpoint cmdlet utdata som visas anger du målsökväg betyg](media/azure-stack-privileged-endpoint/closeendpoint.png)

När betyg loggfilerna har överförts till filresursen, bort de automatiskt från detta program. Om du stänger session för program med hjälp av cmdlets `Exit-PSSession` eller `Exit`, eller stänger du bara PowerShell-konsolen, betyg loggar inte överföra till en filresurs. De finns kvar i detta program. Nästa gång du kör `Close-PrivilegedEndpoint` och inkludera en filresurs, betyg loggar från den tidigare har också överför.

## <a name="next-steps"></a>Nästa steg
[Diagnostikverktyg för Azure Stack](azure-stack-diagnostics.md)