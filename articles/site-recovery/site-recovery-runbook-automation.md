---
title: Lägg till Azure Automation-runbooks i Azure Site Recovery-återställningsplaner | Microsoft Docs
description: Lär dig hur Azure Site Recovery kan du utöka återställningsplaner med Azure Automation. Lär dig mer om att utföra komplicerade uppgifter under återställningen till Azure.
services: site-recovery
documentationcenter: ''
author: ruturaj
manager: gauravd
editor: ''
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 05/02/2018
ms.author: ruturajd@microsoft.com
ms.openlocfilehash: a0a57e4a604a8cadedd5d715acf5f76a147da6d1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896028"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Lägg till Azure Automation-runbooks återställningsplaner
I den här artikeln beskrivs hur Azure Site Recovery kan integreras med Azure Automation kan du utöka din återställningsplaner. Återställningsplaner kan samordna återställning av virtuella datorer som är skyddade med Site Recovery. Återställningsplaner fungerar både för replikering till en sekundär molnet och för replikering till Azure. Återställningsplaner även gör återställningen **konsekvent korrekt**, **repeterbara**, och **automatiserad**. Om du växlar över dina virtuella datorer till Azure utökar-integrering med Azure Automation din återställningsplaner. Du kan använda den för att köra runbooks, som ger kraftfulla automation-aktiviteter.

Om du är nybörjare på Azure Automation kan du [registrering](https://azure.microsoft.com/services/automation/) och [hämta skriptexempel](https://azure.microsoft.com/documentation/scripts/). Mer information och lär dig hur du samordnar återställning till Azure med hjälp av [återställningsplaner](./site-recovery-create-recovery-plans.md), se [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

I den här artikeln beskriver vi hur du kan integrera Azure Automation-runbooks i din återställningsplaner. Vi använder exempel för att automatisera grundläggande uppgifter som tidigare krävde manuella åtgärder. Vi beskriver också hur du konverterar en återställning av flera steg till en enda musklick återställningsåtgärd.

## <a name="customize-the-recovery-plan"></a>Anpassa återställningsplanen
1. Gå till den **Site Recovery** resursbladet plan för återställning. I det här exemplet har två virtuella datorer som lagts till, för återställning i återställningsplanen. Om du vill börja lägga till en runbook, klickar du på den **anpassa** fliken.

    ![Klicka på Anpassa](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Högerklicka på **grupp 1: starta**, och välj sedan **Lägg till post åtgärd**.

    ![Högerklicka på grupp 1: Starta och Lägg till post åtgärd](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Klicka på **väljer du ett skript**.

4. På den **uppdatera åtgärden** bladet namn skriptet **Hello World**.

    ![Bladet Update-åtgärd](media/site-recovery-runbook-automation-new/update-rp.png)

5. Ange namnet på ett Automation-konto.
    >[!NOTE]
    > Automation-kontot kan vara i Azure-region. Automation-kontot måste vara i samma prenumeration som Azure Site Recovery-valvet.

6. Välj en runbook i Automation-kontot. Denna runbook är det skript som körs under körningen av återställningsplanen för efter återställningen av den första gruppen.

7. Spara skriptet genom att klicka på **OK**. Skriptet har lagts till i **grupp 1: efter steg**.

    ![1:Start efter grupp](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Överväganden för att lägga till ett skript

* För alternativ för att **ta bort ett steg** eller **uppdatera skriptet**, högerklicka på skriptet.
* Ett skript kan köras i Azure under växling vid fel från en lokal dator till Azure. Den kan även köras på Azure som en primär plats skriptet innan avslutning, vid återställning från Azure till en lokal dator.
* När ett skript körs den lägger in en återställning plan kontext. I följande exempel visas en kontext variabel:

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
    | RecoveryPlanName |Namnet på den plan som körs. Den här variabeln hjälper dig att vidta olika åtgärder baserat på schemanamn återställning. Du kan också använda skriptet. |
    | FailoverType |Anger om växling vid fel är ett test, planerad eller oplanerad. |
    | FailoverDirection |Anger om återställningen görs till en primär eller sekundär plats. |
    | Grupp-ID |Identifierar gruppnumret i återställningsplanen när planen körs. |
    | VmMap |En matris med alla virtuella datorer i gruppen. |
    | VMMap nyckel |En unik nyckel (GUID) för varje virtuell dator. Det är samma som Azure Virtual Machine Manager (VMM)-ID för den virtuella datorn, om tillämpligt. |
    | SubscriptionId |Azure prenumerations-ID som den virtuella datorn skapades. |
    | RoleName |Namnet på den virtuella Azure-datorn som återställs. |
    | CloudServiceName |Azure-molntjänstnamn som har skapats för den virtuella datorn. |
    | resourceGroupName|Azure resursgruppens namn som har skapats för den virtuella datorn. |
    | RecoveryPointId|Tidsstämpel för när den virtuella datorn återställs. |

* Kontrollera att Automation-kontot har följande moduler:
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Alla moduler som anges i kompatibla versioner. Ett enkelt sätt att se till att alla moduler som är kompatibla är att använda de senaste versionerna av alla moduler.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Åtkomst till alla virtuella datorer av VMMap i en loop
Du kan använda följande kod för att gå över alla virtuella datorer i Microsoft-VMMap:

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
> Resurs grupp namn och rollen namn värdena är tomt när skriptet är en före åtgärd till en start-grupp. Värdena är fyllda endast om den virtuella datorn i den gruppen lyckas växling vid fel. Skriptet är en efter åtgärd i startgruppen.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Använd samma Automation-runbook i flera återställningsplaner

Du kan använda ett enda skript i flera återställningsplaner med externa variabler. Du kan använda [Azure Automation-variabler](../automation/automation-variables.md) att lagra parametrar som du kan skicka för att köra en plan för en återställning. Genom att lägga till återställning schemanamn som prefix till variabeln, kan du skapa enskilda variabler för varje återställningsplan. Använd sedan variablerna som parametrar. Du kan ändra en parameter utan att ändra skriptet, men ändå ändra hur skriptet fungerar.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Använda en enkel string-variabel i en runbook-skript

I det här exemplet ett skript som tar indata för en Nätverkssäkerhetsgrupp (NSG) och gäller för de virtuella datorerna i en återställningsplan.

För skript för att identifiera vilka recovery plan körs använder återställning plan kontext:

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Om du vill använda en befintlig NSG, måste du veta namnet NSG och NSG resursgruppens namn. Använd dessa variabler som indata för återställning plan skript. Gör detta genom att skapa två variabler i Automation-konto tillgångar. Lägg till namnet på återställningsplan som du skapar parametrar för som ett prefix till variabelnamnet.

1. Skapa en variabel för att lagra NSG-namn. Lägga till ett prefix till variabelnamnet med namnet på återställningsplanen.

    ![Skapa en NSG-variabel](media/site-recovery-runbook-automation-new/var1.png)

2. Skapa en variabel för att lagra den NSG resursgruppens namn. Lägga till ett prefix till variabelnamnet med namnet på återställningsplanen.

    ![Skapa en NSG resursgruppens namn](media/site-recovery-runbook-automation-new/var2.png)


3.  Använd följande referenskod för att hämta variabelvärdena i skriptet:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Använda variabler i runbook för att tillämpa NSG: N till nätverksgränssnitt för den virtuella datorn misslyckades över:

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

Skapa oberoende variabler för varje återställningsplanen så att du kan återanvända skriptet. Lägga till ett prefix med schemanamn återställning. En fullständig, slutpunkt-till-slutpunkt skript i det här scenariot finns [lägga till en offentlig IP- och NSG till virtuella datorer under redundanstestningen av en återställningsplan för Site Recovery](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Använda en variabel för komplex för att lagra mer information

Föreställ dig ett scenario som du vill använda ett enda skript för att aktivera en offentlig IP-adress på specifika virtuella datorer. I ett annat scenario kanske du vill tillämpa olika NSG: er på olika virtuella datorer (inte på alla virtuella datorer). Du kan göra ett skript som är återanvändbara för en återställningsplan. Varje återställningsplanen kan ha en variabel antal virtuella datorer. Till exempel har en SharePoint-återställning två frontwebbservrarna. En grundläggande line-of-business (LOB)-programmet har bara en klientdel. Du kan skapa olika variabler för varje återställningsplan.

I följande exempel tar vi en ny teknik och skapa en [komplex variabeln](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) i Azure Automation-konto tillgångar. Det gör du genom att ange flera värden. Du måste använda Azure PowerShell för att slutföra följande steg:

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

3. I den här komplex variabeln **VMDetails** är VM-ID för den skydda virtuella datorn. Visa egenskaper för Virtuella datorer för att få VM-ID i Azure-portalen. Följande skärmbild visar en variabel som lagrar information om två virtuella datorer:

    ![Använd virtuella datorns ID som GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Använd den här variabeln i din runbook. Om det angivna VM-GUID finns i kontexten recovery plan, tillämpa NSG: N på den virtuella datorn:

    ```
    $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. Gå igenom de virtuella datorerna i kontexten recovery plan i din runbook. Kontrollera om den virtuella datorn finns i **$VMDetailsObj**. Om den finns, att komma åt egenskaper för variabeln för att tillämpa NSG: N:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID

            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Du kan använda samma skript för olika återställningsplaner. Ange olika parametrar genom att lagra det värde som motsvarar en återställningsplan i olika variabler.

## <a name="sample-scripts"></a>Exempelskript

Distribuera exempelskript till ditt Automation-konto genom att klicka på den **till Azure** knappen.

[![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Ett annat exempel finns i följande videoklipp. Den visar hur du återställer en två-lagers WordPress-programmet till Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>Ytterligare resurser
* [Azure Automation-tjänsten kör som-konto](../automation/automation-create-runas-account.md)
* [Översikt över Azure Automation](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure Automation-översikt")
* [Azure Automation-exempelskript](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Azure Automation-exempelskript")

## <a name="next-steps"></a>Nästa steg
[Lär dig mer](site-recovery-failover.md) om att köra redundansväxlingar.
