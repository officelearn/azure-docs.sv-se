---
title: Använda infångade paket för att utföra proaktiv nätverksövervakning med varningar och Azure Functions | Microsoft Docs
description: Den här artikeln beskrivs hur du skapar en avisering utlösta infångade med Azure Network Watcher
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 2035d342a89ace6d286fc205c346591b29646c5d
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270152"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Använda infångade paket för proaktiv nätverksövervakning med varningar och Azure Functions

Network Watcher-infångade skapar avbildning sessioner för att spåra trafik och från virtuella datorer. Avbilda filen kan ha ett filter som har definierats för att spåra bara den trafik som du vill övervaka. Dessa data lagras sedan i en lagringsblob eller lokalt på gästdatorn.

Den här funktionen kan startas via en fjärranslutning från andra automatiseringsscenarier, till exempel Azure Functions. Paketfångsten ger dig möjlighet att köra proaktiv insamlingar baserat på programvarudefinierat nätverk avvikelser. Andra användningsområden är att samla in nätverksstatistik, få information om nätverk intrång och felsökning klient-/ serverkommunikation.

Resurser som distribueras i Azure kör 24/7. Du och din personal kan inte aktivt övervaka status för alla resurser 24/7. Till exempel vad händer om ett problem uppstår på 2 AM?

Genom att använda Network Watcher, aviseringar och funktioner från inom Azure-ekosystemet, kan du proaktivt svara med data och verktyg för att lösa problem i ditt nätverk.

![Scenario][scenario]

## <a name="prerequisites"></a>Förutsättningar

* Den senaste versionen av [Azure PowerShell](/powershell/azure/install-azurerm-ps).
* En befintlig instans av Network Watcher. Om du inte redan har en, [skapa en instans av Network Watcher](network-watcher-create.md).
* En befintlig virtuell dator i samma region som Network Watcher med den [Windows tillägget](../virtual-machines/windows/extensions-nwa.md) eller [Linux VM-tillägget](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Scenario

Din virtuella dator skickar flera TCP-segment än vanligt i det här exemplet och du vill bli aviserad om. TCP-segment som används som exempel här, men du kan använda valfri varningsvillkor.

När du meddelas vill du ta emot paketnivå data för att förstå varför kommunikation har ökat. Sedan kan du vidta åtgärder för att gå tillbaka den virtuella datorn till vanliga kommunikation.

Det här scenariot förutsätter att du har en befintlig instans av Network Watcher och en resursgrupp med en giltig virtuell dator.

I följande lista är en översikt över arbetsflödet som sker:

1. En avisering utlöses på den virtuella datorn.
1. Aviseringen anropar din Azure-funktion via en webhook.
1. Din Azure-funktion som bearbetar aviseringen och startar en Network Watcher packet capture session.
1. Paketfångsten körs på den virtuella datorn och samlar in trafik.
1. Paket-infångade filen har överförts till ett lagringskonto för granskning och diagnos.

Om du vill automatisera den här processen kan vi skapa och ansluta en avisering på våra virtuella datorn för att utlösa när händelsen inträffar. Vi kan också skapa en funktion som ska anropas i Network Watcher.

Det här scenariot gör följande:

* Skapar en Azure-funktion som startar ett infångat paket.
* Skapar en varningsregel för en virtuell dator och konfigurerar varningsregeln så att den anropar Azure-funktion.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Det första steget är att skapa en Azure-funktion för att bearbeta aviseringen och skapa ett infångat paket.

1. I den [Azure-portalen](https://portal.azure.com)väljer **skapa en resurs** > **Compute** > **Funktionsapp**.

    ![Skapa en funktionsapp][1-1]

2. På den **Funktionsapp** bladet anger du följande värden och välj sedan **OK** att skapa appen:

    |**Inställning** | **Värde** | **Detaljer** |
    |---|---|---|
    |**Appens namn**|PacketCaptureExample|Namnet på funktionsappen.|
    |**Prenumeration**|[Din prenumeration] Den prenumeration som du vill skapa funktionsappen.||
    |**Resursgrupp**|PacketCaptureRG|Resursgruppen som innehåller funktionsappen.|
    |**Värdplan**|Förbrukningsplan| Vilken typ av planera din app använder för funktionen. Alternativen är förbrukning eller Azure App Service-plan. |
    |**Plats**|Centrala USA| Den region där du vill skapa funktionsappen.|
    |**Lagringskonto**|{automatiskt genererade}| Det lagringskonto som Azure Functions behöver för allmän lagring.|

3. På den **PacketCaptureExample Funktionsappar** bladet väljer **Functions** > **anpassad funktion**  >  **+**.

4. Välj **HttpTrigger-Powershell**, och ange sedan återstående information. Välj slutligen, om du vill skapa funktionen **skapa**.

    |**Inställning** | **Värde** | **Detaljer** |
    |---|---|---|
    |**Scenario**|Experimentell|Typen av scenario|
    |**Namnge din funktion**|AlertPacketCapturePowerShell|Namnet på funktionen|
    |**Auktorisationsnivå**|Funktion|Åtkomstnivå för funktionen|

![Functions-exempel][functions1]

> [!NOTE]
> PowerShell-mallen är experimentellt och har inte fullständigt stöd.

Anpassningar som krävs för det här exemplet och beskrivs i följande steg.

### <a name="add-modules"></a>Lägg till moduler

Om du vill använda Network Watcher PowerShell-cmdlet, överför du den senaste PowerShell-modulen till funktionsappen.

1. Kör följande PowerShell-kommando på den lokala datorn med Azure PowerShell-moduler som är installerad:

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    Det här exemplet får du den lokala sökvägen för Azure PowerShell-moduler. Dessa mappar som används i ett senare steg. Moduler som används i det här scenariot är:

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![PowerShell-mappar][functions5]

1. Välj **fungera appinställningar** > **gå till App Service Editor**.

    ![Funktionsappinställningar][functions2]

1. Högerklicka på den **AlertPacketCapturePowershell** mapp, och sedan skapa en mapp med namnet **azuremodules**. 

4. Skapa en undermapp för varje modul som du behöver.

    ![Mappen och undermappar][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. Högerklicka på den **AzureRM.Network** undermapp och välj sedan **Överför filer**. 

6. Gå till din Azure-moduler. I lokalt **AzureRM.Network** mapp, markera alla filer i mappen. Välj sedan **OK**. 

7. Upprepa dessa steg för **AzureRM.Profile** och **AzureRM.Resources**.

    ![Överföra filer][functions6]

1. När du är klar, var och en mappen bör ha PowerShell-modulen filer från din lokala dator.

    ![PowerShell-filer][functions7]

### <a name="authentication"></a>Autentisering

Om du vill använda PowerShell-cmdlets, måste du autentisera. Du kan konfigurera autentisering i funktionsappen. Om du vill konfigurera autentisering måste du konfigurera miljövariabler och överför en krypterad nyckelfil i funktionsappen.

> [!NOTE]
> Det här scenariot innehåller bara ett exempel på hur du implementerar autentisering med Azure Functions. Det finns andra sätt att göra detta.

#### <a name="encrypted-credentials"></a>Krypterade autentiseringsuppgifter

Följande PowerShell-skript skapar en nyckelfil som kallas **PassEncryptKey.key**. Det ger också en krypterad version av det lösenord som har angetts. Det här lösenordet är det lösenord som har definierats för det Azure Active Directory-program som används för autentisering.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

I App Service Editor för funktionsappen, skapa en mapp med namnet **nycklar** under **AlertPacketCapturePowerShell**. Ladda sedan upp den **PassEncryptKey.key** -fil som du skapade i föregående PowerShell-exemplet.

![Functions-nyckel][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Hämta värden för miljövariabler

Det slutliga kravet är att ställa in miljövariabler som är nödvändiga för att komma åt värden för autentisering. I följande lista visas de miljövariabler som som har skapats:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

Klient-ID är program-ID för ett program i Azure Active Directory.

1. Om du inte redan har ett program att använda, kör du följande exempel för att skapa ett program.

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Lösenordet som du använder när du skapar programmet ska vara samma lösenord som du skapade tidigare när du sparar filen för nyckeln.

1. I Azure-portalen väljer du **prenumerationer**. Välj prenumerationen som ska användas och välj sedan **åtkomstkontroll (IAM)**.

    ![Functions IAM][functions9]

1. Välj kontot du använder och välj sedan **egenskaper**. Kopiera program-ID.

    ![Functions program-ID][functions10]

#### <a name="azuretenant"></a>AzureTenant

Hämta klient-ID genom att köra följande PowerShell-exempel:

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Värdet för miljövariabeln AzureCredPassword är det värde som du får från att köra följande PowerShell-exempel. Det här exemplet är samma lösenord som visas i det föregående **krypterade autentiseringsuppgifter** avsnittet. Värdet som behövs är utdata från den `$Encryptedpassword` variabeln.  Det här är lösenordet för tjänstens huvudnamn som du har krypterat med hjälp av PowerShell-skript.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>Store miljövariabler

1. Gå till funktionsappen. Välj sedan **fungera appinställningar** > **konfigurera appinställningar**.

    ![Konfigurera appinställningar][functions11]

1. Lägg till miljövariabler och deras värden i appinställningar och välj sedan **spara**.

    ![Appinställningar][functions12]

### <a name="add-powershell-to-the-function"></a>Lägg till PowerShell till funktionen

Nu är det dags att göra anrop i Network Watcher från inom Azure-funktion. Implementeringen av den här funktionen kan variera beroende på kraven. Men finns det allmänna flödet av koden på följande sätt:

1. Process-indataparametrar.
2. Fråga befintliga paket samlar in för att kontrollera gränser och lösa namnkonflikter.
3. Skapa ett infångat paket med lämpliga parametrar.
4. Avsökning paketfångst regelbundet tills det är klart.
5. Meddela användaren att avbildningssessionen paket har slutförts.

I följande exempel är PowerShell-kod som kan användas i funktionen. Det finns värden som måste ersättas för **subscriptionId**, **resourceGroupName**, och **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
            $packetcapturename = "PSAzureFunction"
            $packetCaptureLimit = 10
            $packetCaptureDuration = 10

            #Credentials
            $tenant = $env:AzureTenant
            $pw = $env:AzureCredPassword
            $clientid = $env:AzureClientId
            $keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

            #Authentication
            $secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
            $credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
            Connect-AzureRmAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
                $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

                #Get existing packetCaptures
                $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>Hämta funktions-URL 
1. När du har skapat din funktion kan du konfigurera aviseringen för att anropa den URL som är associerat med hjälp av funktionen. För att få det här värdet kan du kopiera Funktionswebbadressen från din funktionsapp.

    ![Hitta funktions-URL][functions13]

2. Kopiera funktions-URL för din funktionsapp.

    ![Kopiera Funktionswebbadressen][2]

Om du behöver anpassade egenskaper i nyttolasten för POST-begäran för webhook kan referera till [konfigurera en webhook i en Azure metrisk varning](../azure-monitor/platform/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Konfigurera en varning på en virtuell dator

Aviseringar kan konfigureras för att meddela personer när en viss mått överskrider ett tröskelvärde som är tilldelad till den. Aviseringen är på TCP-segment som skickas i det här exemplet, men aviseringen kan utlösas för många andra mått. I det här exemplet konfigureras en avisering för att anropa en webhook för att anropa funktionen.

### <a name="create-the-alert-rule"></a>Skapa varningsregeln

Gå till en befintlig virtuell dator och sedan lägga till en varningsregel. Mer detaljerad dokumentation om hur du konfigurerar aviseringar finns på [skapa aviseringar i Azure Monitor för Azure-tjänster – Azure-portalen](../monitoring-and-diagnostics/insights-alerts-portal.md). Ange följande värden i den **varningsregel** bladet och välj sedan **OK**.

  |**Inställning** | **Värde** | **Detaljer** |
  |---|---|---|
  |**Namn**|TCP_Segments_Sent_Exceeded|Namnet på regeln.|
  |**Beskrivning**|TCP-segment skickas överskridit tröskelvärdet|Beskrivning för regeln.||
  |**Mått**|TCP-segment som skickas| Mått som ska använda för att utlösa aviseringen. |
  |**villkor**|Större än| Villkoret du vill använda vid utvärdering av måttet.|
  |**Tröskelvärde**|100| Värdet för det mått som utlöser aviseringen. Det här värdet sättas till ett giltigt värde för din miljö.|
  |**Period**|Under de senaste fem minuterna| Anger den period som du söker efter tröskelvärdet för måttet.|
  |**Webhook**|[webhook-URL från funktionsapp]| Webhook-URL från funktionsappen som skapades i föregående steg.|

> [!NOTE]
> Mått för TCP-segment är inte aktiverat som standard. Mer information om hur du aktiverar ytterligare mått genom att besöka [aktivera övervakning och diagnostik](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Granska resultaten

Efter det att kriterierna för avisering utlösare skapas ett infångat paket. Gå till Network Watcher och välj sedan **paketfångsten**. Du kan välja packet capture filen länken för att hämta paketfångsten på den här sidan.

![Visa-infångade][functions14]

Du kan hämta den genom att logga in till den virtuella datorn om filen lagras lokalt.

Anvisningar om att hämta filer från Azure storage-konton finns i [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som du kan använda är [Lagringsutforskaren](http://storageexplorer.com/).

När din avbildning har laddats ned kan du visa den med ett verktyg som kan läsa en **.cap** fil. Följande är länkar till två av dessa verktyg:

- [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visar dina infångade paket genom att besöka [Packet capture analys med Wireshark](network-watcher-deep-packet-inspection.md).


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png
