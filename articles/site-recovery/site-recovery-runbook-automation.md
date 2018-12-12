---
title: Lägg till Azure Automation-runbooks i återställningsplaner för Site Recovery | Microsoft Docs
description: Lär dig mer om att utöka återställningsplaner med Azure Automation för haveriberedskap med Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 5587d86cb4b3a213961ce46e77c75e947de2d29e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52866380"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Lägg till Azure Automation-runbooks i återställningsplaner
I den här artikeln beskriver vi hur Azure Site Recovery kan integreras med Azure Automation kan du utöka dina återställningsplaner. Återställningsplaner kan dirigera återställning av virtuella datorer som skyddas med Site Recovery. Återställningsplaner fungerar både för replikering till en sekundär molnet och för replikering till Azure. Återställningsplaner även gör återställningen **konsekvent korrekt**, **upprepningsbara**, och **automatiserade**. Om du växlar över dina virtuella datorer till Azure utökar-integrering med Azure Automation dina återställningsplaner. Du kan använda den för att köra runbooks, som erbjuder kraftfulla automatiserade uppgifter.

Om du är nybörjare på Azure Automation kan du [registrera](https://azure.microsoft.com/services/automation/) och [hämta skriptexempel](https://azure.microsoft.com/documentation/scripts/). Mer information och för att lära dig hur du organiserar återställning till Azure med hjälp av [återställningsplaner](./site-recovery-create-recovery-plans.md), se [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

I den här artikeln beskriver vi hur du kan integrera Azure Automation-runbooks i dina återställningsplaner. Vi kan använda exemplen för att automatisera grundläggande uppgifter som tidigare krävde manuella åtgärder. Vi beskriver också hur du konverterar en återställning med flera steg till en enda musklick återställningsåtgärd.

## <a name="customize-the-recovery-plan"></a>Anpassa återställningsplanen
1. Gå till den **Site Recovery** recovery plan-resursbladet. I det här exemplet har två virtuella datorer har lagts till, för återställning i återställningsplanen. Om du vill börja lägga till en runbook, klickar du på den **anpassa** fliken.

    ![Klicka på knappen Anpassa](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Högerklicka på **grupp 1: starta**, och välj sedan **Lägg till efteråtgärd**.

    ![Högerklicka på grupp 1: Starta och Lägg till efteråtgärd](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Klicka på **Välj ett skript**.

4. På den **åtgärden Uppdatera** bladet namn skriptet **Hello World**.

    ![Bladet Update-åtgärd](media/site-recovery-runbook-automation-new/update-rp.png)

5. Ange ett namn på Automation-konto.
    >[!NOTE]
    > Automation-kontot kan vara i alla Azure-regioner. Automation-kontot måste vara i samma prenumeration som Azure Site Recovery-valvet.

6. Välj en runbook i ditt Automation-konto. Denna runbook är det skript som körs under körningen av återställningsplanen, efter återställningen av den första gruppen.

7. Spara skriptet genom att klicka på **OK**. Skriptet har lagts till i **grupp 1: steg efter**.

    ![Efter åtgärdsgrupp 1:Start](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Överväganden för att lägga till ett skript

* Om alternativ för att **ta bort ett steg** eller **uppdatera skriptet**, högerklicka på skriptet.
* Ett skript kan köras på Azure under redundansväxlingen från en lokal dator till Azure. Den kan även köras på Azure som en primär plats skript före avstängning, vid återställning från Azure till en lokal dator.
* När ett skript körs det lägger in en recovery plan-kontext. I följande exempel visas en sammanhangsvariabel:

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

    I följande tabell visas namn och beskrivning för varje variabel i kontexten.

    | **Variabelnamn** | **Beskrivning** |
    | --- | --- |
    | RecoveryPlanName |Namnet på den plan som körs. Den här variabeln kan du vidta olika åtgärder baserat på namnet på återställningsplanen. Du kan även återanvända skriptet. |
    | FailoverType |Anger om växling vid fel är ett test, planerad eller oplanerad. |
    | FailoverDirection |Anger om återställningen görs till en primär eller sekundär plats. |
    | Grupp-ID |Identifierar gruppnumret i återställningsplanen när planen körs. |
    | VmMap |En matris med alla virtuella datorer i gruppen. |
    | VMMap nyckel |En unik nyckel (GUID) för varje virtuell dator. Det är samma som Azure Virtual Machine Manager (VMM)-ID för den virtuella datorn, om tillämpligt. |
    | SubscriptionId |Azure-prenumerations-ID som den virtuella datorn skapades. |
    | RoleName |Namnet på den virtuella Azure-datorer som återställs. |
    | CloudServiceName |Azure molntjänstens namn som den virtuella datorn skapades. |
    | ResourceGroupName|Azure resursgruppens namn som den virtuella datorn skapades. |
    | RecoveryPointId|Tidsstämpel för när den virtuella datorn återställs. |

* Kontrollera att Automation-kontot har följande moduler:
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Alla moduler som ska ha kompatibla versioner. Ett enkelt sätt att se till att alla moduler som är kompatibla är att använda de senaste versionerna av alla moduler.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Få åtkomst till alla virtuella datorer av VMMap i en loop
Använd följande kod för att bläddra i alla virtuella datorer för Microsoft-VMMap:

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

> [!NOTE]
> De namn och rollen värdena för resursgruppnamn är tomma när skriptet är en åtgärd till en start-grupp. Värden fylls bara om den virtuella datorn i den gruppen lyckas i redundanskluster. Skriptet är efteråtgärd i Start-gruppen.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Använd samma Automation-runbook i flera återställningsplaner

Du kan använda ett enda skript i flera återställningsplaner med externa variabler. Du kan använda [Azure Automation-variabler](../automation/automation-variables.md) att lagra parametrar som du kan skicka för körning av en recovery plan. Lägger till namnet på återställningsplanen som ett prefix i variabeln kan skapa du enskilda variabler för varje återställningsplan. Sedan Använd variablerna som parametrar. Du kan ändra en parameter utan att ändra skriptet, men ändå ändra hur skriptet fungerar.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Använda en enkel strängvariabel i en runbook-skriptet

I det här exemplet är ett skript tar indata för en Nätverkssäkerhetsgrupp (NSG) och tillämpar den på de virtuella datorerna i en återställningsplan.

För skriptet att identifiera vilka recovery plan körs kan använda recovery plan context:

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Om du vill använda en befintlig NSG, måste du känna NSG-namnet och NSG resursgruppens namn. Använd dessa variabler som indata för recovery plan skript. Gör detta genom att skapa två variabler i Automation-konto-tillgångar. Lägg till namnet på återställningsplanen som du skapar parametrarna för som ett prefix till variabelnamnet.

1. Skapa en variabel för att lagra NSG-namnet. Lägga till ett prefix till variabelnamnet med namnet på återställningsplanen.

    ![Skapa en variabel för NSG-namn](media/site-recovery-runbook-automation-new/var1.png)

2. Skapa en variabel för att lagra den NSG resursgruppens namn. Lägga till ett prefix till variabelnamnet med namnet på återställningsplanen.

    ![Skapa en NSG resursgruppens namn](media/site-recovery-runbook-automation-new/var2.png)


3.  Använd följande referenskod för att få variabelvärdena i skriptet:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Använda variabler i runbook för att tillämpa NSG: N till nätverksgränssnittet för den redundansväxlade virtuella datorn:

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

Skapa oberoende variabler för varje återställningsplanen så att du kan återanvända skriptet. Lägg till ett prefix med hjälp av namnet på återställningsplanen. En fullständig, slutpunkt till slutpunkt skriptet i det här scenariot finns [lägga till en offentlig IP- och NSG till virtuella datorer under redundanstest av en återställningsplan för Site Recovery](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Använda en variabel för komplex för att lagra mer information

Tänk dig ett scenario som du vill att ett enda skript för att aktivera en offentlig IP på specifika virtuella datorer. I ett annat scenario kanske du vill tillämpa olika NSG: er på olika virtuella datorer (inte för alla virtuella datorer). Du kan göra ett skript som är återanvändbara för någon återställningsplan. Varje återställningsplanen kan ha en variabel antalet virtuella datorer. Till exempel har en SharePoint-återställning två klientdelar. En grundläggande line-of-business (LOB)-programmet har bara en klientdel. Du kan inte skapa separata variabler för varje återställningsplan.

I följande exempel vi använda en ny teknik och skapa en [komplex variabeln](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) i Azure Automation-konto tillgångar. Du kan göra detta genom att ange flera värden. Du måste använda Azure PowerShell för att slutföra följande steg:

1. Logga in på Azure-prenumerationen i PowerShell:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Skapa komplexa variabeln med namnet på återställningsplanen för att lagra parametrarna:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. I den här variabeln för komplexa, **VMDetails** är VM-ID för den skyddade virtuella datorn. Visa egenskaper för virtuella datorer för att hämta VM-ID i Azure-portalen. I följande skärmbild visas en variabel som lagrar information om två virtuella datorer:

    ![Använda VM-ID som GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Använd den här variabeln i din runbook. Om den angivna VM-GUID hittas i kontexten recovery plan gäller NSG: N på den virtuella datorn:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. Gå igenom de virtuella datorerna i kontexten recovery plan i din runbook. Kontrollera om den virtuella datorn finns i **$VMDetailsObj**. Komma åt egenskaper för variabeln för att tillämpa NSG: N om den finns:

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

Distribuera exempelskript till ditt Automation-konto genom att klicka på den **distribuera till Azure** knappen.

[![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Ett annat exempel finns i följande videoklipp. Den visar hur du återställer en tvålagers-WordPress-program till Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>Ytterligare resurser
* [Azure Automation-tjänsten kör som-konto](../automation/automation-create-runas-account.md)
* [Översikt över Azure Automation](https://msdn.microsoft.com/library/azure/dn643629.aspx "översikt över Azure Automation")
* [Azure Automation-exempelskript](https://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "exempelskript för Azure Automation")

## <a name="next-steps"></a>Nästa steg
[Läs mer](site-recovery-failover.md) om att köra redundansväxlingar.
