---
title: Med den privilegierade slutpunkten i Azure-stacken | Microsoft Docs
description: "Visar hur du använder Privilegierade slutpunkten i Azure-stacken (för operatör Azure-stacken)."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 9769b12064216680bb1b2db8c1fd7449927c7771
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Med den privilegierade slutpunkten i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du bör använda administratörsportal PowerShell eller Azure Resource Manager API: erna för de flesta dagliga hanteringsuppgifter som operatör Azure stacken. Men för vissa mindre vanliga åtgärder, måste du använda den *Privilegierade endpoint*. Den här slutpunkten är en förkonfigurerad PowerShell fjärrkonsolen som ger dig bara tillräckligt med kapacitet för att utföra en obligatorisk uppgift. Slutpunkten utnyttjar PowerShell JEA (Just tillräckligt Administration) för att visa en begränsad uppsättning cmdlets för. Ett lågprivilegierat konto används för att komma åt den privilegierade slutpunkten och anropa begränsad uppsättning cmdlets. Det krävs inga administratörskonton. För ytterligare säkerhet tillåts skript inte.

Du kan använda Privilegierade slutpunkten för att utföra uppgifter, till exempel följande:

- Använda lågnivå-aktiviteter, till exempel [att samla in diagnostikloggar](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- För många efter distributionen datacenter integration uppgifter för integrerad system, till exempel lägga till Domain Name System (DNS) vidarebefordrare efter distributionen kan ställa in integration diagram, integrering med Active Directory Federation Services (AD FS), certifikat rotation, osv.
- Arbeta med stöd för att få tillfälligt, övergripande åtkomst för avancerad felsökning av ett integrerat system. 

Privilegierade slutpunkten loggar varje åtgärd (och dess motsvarande utdata) som du utför i PowerShell-sessionen. Det ger fullständig genomskinlighet och fullständig granskning av åtgärder. Du kan behålla dessa loggfiler för framtida granskning.

> [!NOTE]
> I Azure Stack Development Kit (ASDK), kan du köra vissa kommandon som är tillgängliga i Privilegierade slutpunkten direkt från en PowerShell-session på development kit värden. Du kanske vill testa vissa åtgärder med hjälp av Privilegierade slutpunkten, till exempel Logginsamling, eftersom det här är den enda tillgängliga metoden för att utföra vissa åtgärder i en miljö med integrerade system.

## <a name="access-the-privileged-endpoint"></a>Komma åt den privilegierade slutpunkten

Du har åtkomst till Privilegierade slutpunkten via PowerShell-fjärrsession på den virtuella datorn som är värd för privilegierade slutpunkten. Den här virtuella datorn är i ASDK, med namnet AzS ERCS01. Om du använder ett integrerat system, det finns tre instanser av Privilegierade slutpunkten körs i en virtuell dator (*prefixet*-ERCS01, *prefixet*-ERCS02, eller *prefixet*-ERCS03) på olika värdar för återhämtning. 

Innan du börjar den här proceduren för ett integrerat system, kontrollera att du har åtkomst till en slutpunkt för privilegierade antingen via IP-adress eller DNS. Efter första distributionen av Azure-stacken, du kan komma åt Privilegierade slutpunkten endast efter IP-adress eftersom DNS-integrering inte har ännu ställts in. Maskinvaruleverantören OEM ger dig en JSON-fil med namnet ”AzureStackStampDeploymentInfo” som innehåller IP-adresser Privilegierade slutpunkt.

Vi rekommenderar att du ansluter till Privilegierade slutpunkten endast från maskinvara livscykel värden eller en dedikerad, låst dator som en [arbetsstation för privilegierad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

1. Gör något av följande beroende på miljön:

    - Kör följande kommando för att lägga till Privilegierade slutpunkten som en betrodd värd på din maskinvara livscykel värd eller en arbetsstation för privilegierad åtkomst på ett integrerat system.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Om du kör ADSK, kan du logga in på development kit värden.

2. Öppna en upphöjd Windows PowerShell-sessionen på din maskinvara livscykel värden eller en arbetsstation för privilegierad åtkomst. Kör följande kommandon för att upprätta en fjärrsession på den virtuella datorn som är värd för privilegierade slutpunkten:
 
    - På ett integrerat system:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      Den `ComputerName` parametern kan vara antingen IP-adressen eller DNS-namnet på en av de virtuella datorerna som är värd för en privilegierad slutpunkt. 
    - Om du kör ADSK:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   När du uppmanas, Använd följande autentiseringsuppgifter:

      - **Användarnamnet**: Ange CloudAdmin-konto i formatet  **&lt;* Azure Stack domän*&gt;\cloudadmin**. (För ASDK, användarnamnet är **azurestack\cloudadmin**.)
      - **Lösenordet**: Ange samma lösenord som du angav under installationen för AzureStackAdmin domänadministratörskontot.
    
3.  När du har anslutit uppmaningen ändras till  **[*IP-adress eller ERCS VM*]: PS > ** eller **[azs ercs01]: PS >**, beroende på miljön. Härifrån kan köra `Get-Command` att visa listan över tillgängliga cmdlets.

    ![Get-Command-cmdlet utdata med en lista över tillgängliga kommandon](media/azure-stack-privileged-endpoint/getcommandoutput.png)

    Många av dessa cmdlets är endast avsett för integrerat system-miljöer (till exempel cmdlets för datacenter-integrering). Följande cmdlets i ASDK, har verifierats:

    - Rensa värden
    - Stäng PrivilegedEndpoint
    - Avsluta-PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Mått-objekt
    - Ny CloudAdminUser
    - Standard ut
    - Ta bort CloudAdminUser
    - Select-Object
    - Ange CloudAdminUserPassword
    - Stoppa AzureStack
    - Get-ClusterLog

4.  Eftersom skript inte tillåts, kan du inte använda flikavslutande parametervärden. Kör följande kommando för att hämta listan över parametrar för en viss cmdlet:

    ````PowerShell
    Get-Command <cmdlet_name> -Syntax
    ```` 
    Om du av någon typ av skriptåtgärd åtgärden misslyckas med fel **ScriptsNotAllowed**. Detta är förväntat.

## <a name="close-the-privileged-endpoint-session"></a>Stäng den privilegierade endpoint-sessionen

 Som tidigare nämnts loggar Privilegierade slutpunkten varje åtgärd (och dess motsvarande utdata) som du utför i PowerShell-sessionen. Du bör stänga sessionen med hjälp av den `Close-PrivilegedEndpoint` cmdlet. Denna cmdlet korrekt stängs slutpunkten och överför filerna till en extern filresurs för kvarhållning.

Att stänga sessionen slutpunkt:

1. Skapa en extern filresurs som kan nås av Privilegierade slutpunkten. Du kan bara skapa en filresurs på development kit värden i en utvecklingsmiljö av kit.
2. Kör den `Close-PrivilegedEndpoint` cmdlet. 
3. Du uppmanas att ange en sökväg som ska lagra loggfil betyg. Ange den filresurs som du skapade tidigare, i formatet &#92; &#92; *servername*&#92; *resursnamn*. Om du inte anger en sökväg cmdlet misslyckas och förblir sessionen öppen. 

    ![Stäng PrivilegedEndpoint cmdlet utdata som visas anger du målsökväg betyg](media/azure-stack-privileged-endpoint/closeendpoint.png)

När betyg loggfilerna har överförts till filresursen, bort de automatiskt från Privilegierade slutpunkten. Om du stänger den privilegierade endpoint-sessionen med hjälp av cmdlets `Exit-PSSession` eller `Exit`, eller stänger du bara PowerShell-konsolen, betyg loggar inte överföra till en filresurs. De finns kvar i Privilegierade slutpunkten. Nästa gång du kör `Close-PrivilegedEndpoint` och inkludera en filresurs, betyg loggar från den tidigare har också överför.

## <a name="next-steps"></a>Nästa steg
[Diagnostikverktyg för Azure Stack](azure-stack-diagnostics.md)







