---
title: Lägga till Azure Automation-runbooks i återställningsplaner för webbplatsåterställning
description: Lär dig hur du utökar återställningsplaner med Azure Automation för haveriberedskap med Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: rajanaki
ms.openlocfilehash: ecfe993a137ca63c84438870ec54ac1e6d6707da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257490"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Lägga till Azure Automation Runbook-rutiner i återställningsplaner

I den här artikeln beskrivs hur du integrerar Azure Automation-runbooks, för att utöka Azure Site Recovery-återställningsplaner. [Azure Site Recovery](site-recovery-overview.md) Vi visar dig hur du automatiserar grundläggande uppgifter som annars skulle behöva manuella åtgärder och hur du konverterar en återställning i flera steg till en åtgärd med ett enda klick.

## <a name="recovery-plans"></a>Återställningsplaner 

Du kan använda återställningsplaner när du växlar över lokala datorer eller virtuella Azure-datorer. Återställningsplaner hjälper dig att definiera en systematisk återställningsprocess som definierar hur datorer växlar över och hur de startar och återställs efter redundans. 

Återställning stora appar kan vara komplexa. Återställningsplaner hjälper till att införa ordning så att återställningen är konsekvent korrekt, repeterbar och automatiserad. Du kan automatisera uppgifter i en återställningsplan med skript och Azure Automation-runbooks. Typiska exempel kan vara att konfigurera inställningar på en Azure-dator efter redundans eller konfigurera om en app som körs på den virtuella datorn.

- [Läs mer](recovery-plan-overview.md) om återställningsplaner.
- [Läs mer](../automation/automation-runbook-types.md) om Azure Automation-runbooks.



## <a name="runbooks-in-recovery-plans"></a>Runbooks i återhämtningsplaner

Du lägger till ett Azure Automation-konto och runbooks i en återställningsplan. Runbook anropas när återställningsplanen körs.

- Automation-kontot kan finnas i alla Azure-regioner och måste finnas i samma prenumeration som site recovery-valvet. 
- En runbook kan köras i en återställningsplan under redundans från en primär plats till sekundär, eller under återställning efter fel från den sekundära platsen till den primära.
- Runbooks i en återställningsplan körs seriellt, en efter en, i den inställda ordningen.
- Om runbooks i en återställningsplan konfigurerar virtuella datorer för att starta i olika grupper, fortsätter återställningsplanen endast när Azure rapporterar alla virtuella datorer som körs.
- Återställningsplaner fortsätter att köras, även om ett skript misslyckas.

### <a name="recovery-plan-context"></a>Kontext för återställningsplan

När ett skript körs injicerar det en återställningsplankontext till runbooken. Kontexten innehåller de variabler som sammanfattas i tabellen.

| **Variabelnamn** | **Beskrivning** |
| --- | --- |
| RecoveryPlanName |Namn på återställningsplan. Används i åtgärder som baseras på namnet. |
| Redundanstyp |Anger om det är en test- eller produktionsväxling. 
| RedundansDirektion | Anger om återställningen är till en primär eller sekundär plats. |
| Groupid |Identifierar gruppnumret i återställningsplanen när planen körs. |
| VmMap (VmKarta) |En matris med alla virtuella datorer i gruppen. |
| VMMap-nyckel |En unik nyckel (GUID) för varje virtuell dator. |
| SubscriptionId |Azure-prenumerations-ID där den virtuella datorn skapades. |
| ResourceGroupName | Namn på den resursgrupp där den virtuella datorn finns.
| CloudServiceName |Azure-molntjänstnamnet som den virtuella datorn skapades under. |
| RoleName |Namnet på den virtuella Azure-datorn. |
| Återställningspunkt|Tidsstämpeln för vm-återställningen. |

I följande exempel visas en kontextvariabel:

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

Om du vill komma åt alla virtuella datorer i VMMap i en loop kan du använda följande kod:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


Aman Sharma blogg över på [Harvesting Clouds](http://harvestingclouds.com) har ett användbart exempel på en [återhämtningsplan sammanhang skript](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/).



## <a name="before-you-start"></a>Innan du börjar

- Om du inte har tidigare i Azure Automation kan du [registrera dig](https://azure.microsoft.com/services/automation/) och [hämta exempelskript](https://azure.microsoft.com/documentation/scripts/).
- Kontrollera att Automation-kontot har följande moduler:
    - AzureRM.-profil
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Alla moduler bör vara av kompatibla versioner. Det enklaste sättet är att alltid använda de senaste versionerna av alla moduler.



## <a name="customize-the-recovery-plan"></a>Anpassa återställningsplanen

1. Välj **Återställningsplaner (Site Recovery) i** valvet
2. Om du vill skapa en återställningsplan klickar du på **+Återställningsplan**. [Läs mer](site-recovery-create-recovery-plans.md). Om du redan har en återställningsplan väljer du att öppna den.
3. Klicka på **Anpassa**på sidan återställningsplan.

    ![Klicka på knappen Anpassa](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Klicka på ellipserna (...) bredvid **Grupp 1: Starta** > **Lägg till inläggsåtgärd**.
3. Kontrollera att **skript** är markerat i **åtgärden Infoga**och ange ett namn för skriptet (**Hello World**).
4. Ange ett automatiseringskonto och välj en runbook. Om du vill spara skriptet klickar du på **OK**. Skriptet läggs till **i grupp 1: Eftersteg**.


## <a name="reuse-a-runbook-script"></a>Återanvända ett runbook-skript

Du kan använda ett enda runbook-skript i flera återställningsplaner med hjälp av externa variabler. 

- Du använder [Azure Automation-variabler](../automation/automation-variables.md) för att lagra parametrar för att köra en återställningsplan.
- Genom att lägga till återställningsplanens namn som ett prefix i variabeln kan du skapa enskilda variabler för varje återställningsplan. Använd sedan variablerna som parametrar.
- Du kan ändra en parameter utan att ändra skriptet, men ändå ändra hur skriptet fungerar.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Använda en enkel strängvariabel i ett runbook-skript

I det här exemplet tar ett skript indata från en NSG (Network Security Group) och tillämpar det på de virtuella datorerna i en återställningsplan. 

1. Så att skriptet kan identifiera vilken återställningsplan som körs, använd den här återställningsplanens kontext:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Notera NSG-namn och resursgrupp. Du använder dessa variabler som indata för återställningsplanskript. 
1. I kontot Automations konto finns tillgångar. skapa en variabel för att lagra NSG-namnet. Lägg till ett prefix i variabelnamnet med namnet på återställningsplanen.

    ![Skapa en NSG-namnvariabel](media/site-recovery-runbook-automation-new/var1.png)

2. Skapa en variabel för att lagra resursgruppsnamnet för NSG-resursen. Lägg till ett prefix i variabelnamnet med namnet på återställningsplanen.

    ![Skapa ett NSG-resursgruppnamn](media/site-recovery-runbook-automation-new/var2.png)


3.  I skriptet använder du den här referenskoden för att hämta variabelvärdena:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Använd variablerna i runbooken för att tillämpa NSG på nätverksgränssnittet för den virtuella datorn med fel:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


Skapa oberoende variabler för varje återställningsplan så att du kan återanvända skriptet. Lägg till ett prefix med hjälp av återställningsplanens namn. 

Om du vill ha ett fullständigt, end-to-end-skript för det här scenariot läser du [skriptet](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Använda en komplex variabel för att lagra mer information

I vissa fall kanske du inte kan skapa separata variabler för varje återställningsplan. Tänk dig ett scenario där du vill att ett enda skript ska tilldela en offentlig IP-adress på specifika virtuella datorer. I ett annat scenario kanske du vill använda olika NSG:er på olika virtuella datorer (inte på alla virtuella datorer). Tänk på följande:

- Du kan göra ett skript som kan återanvändas för alla återställningsplan.
- Varje återställningsplan kan ha ett varierande antal virtuella datorer.
- En SharePoint-återställning har till exempel två främre änderna. Ett grundläggande affärsprogram (LOB) har bara en front.the end.
- I det här fallet kan du inte skapa separata variabler för varje återställningsplan.

I följande exempel skapar vi en [komplex variabel](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) i Azure Automation-kontot.

Vi gör detta genom att ange flera värden med Hjälp av Azure PowerShell.

1. Logga in på din Azure-prenumeration i PowerShell:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Om du vill lagra parametrarna skapar du den komplexa variabeln med namnet på återställningsplanen:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. I den här komplexa variabeln är **VMDetails** VM-ID för den skyddade virtuella datorn. Om du vill hämta VM-ID:et i Azure-portalen visar du vm-egenskaperna. Följande skärmbild visar en variabel som lagrar information om två virtuella datorer:

    ![Använda VM-ID som GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Använd den här variabeln i runbooken. Om det angivna VM GUID-gränssnittet finns i återställningsplanens kontext använder du NSG på den virtuella datorn:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. I din runbook går du igenom de virtuella datorerna i återställningsplanens kontext. Kontrollera om den virtuella datorn finns i **$VMDetailsObj**. Om den finns, öppna egenskaperna för variabeln för att tillämpa NSG:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Du kan använda samma skript för olika återställningsplaner. Ange olika parametrar genom att lagra värdet som motsvarar en återställningsplan i olika variabler.

## <a name="sample-scripts"></a>Exempelskript

Om du vill distribuera exempelskript till ditt Automation-konto klickar du på knappen **Distribuera till Azure.**

[![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Den här videon ger ett annat exempel. Det visar hur du återställer ett WordPress-program med två nivåer till Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om ett [Azure Automation Run As-konto](../automation/automation-create-runas-account.md)
- Granska [exempelskript för Azure Automation](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- [Läs mer](site-recovery-failover.md) om att köra redundans.



