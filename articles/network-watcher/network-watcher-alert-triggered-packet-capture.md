---
title: Använd paketinsamling för att göra proaktiv nätverksövervakning med varningar och Azure Functions | Microsoft Docs
description: Den här artikeln beskriver hur du skapar en avisering utlösta paketinsamling med Azure Nätverksbevakaren
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
ms.openlocfilehash: 4c96ca70b9b6a82dcccec443ac0b1e06f96a2396
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Använd paketinsamling för proaktiv nätverksövervakning med varningar och Azure Functions

Nätverket Watcher paketinsamling skapar avbilda sessioner för att spåra trafik till och från virtuella datorer. Avbilda filen kan ha ett filter som definieras för att spåra endast trafik som du vill övervaka. Dessa data lagras sedan i en lagringsblob-eller lokalt på gästdatorn.

Den här funktionen kan startas från en fjärrdator från andra automatiseringsscenarier, till exempel Azure Functions. Paketinsamling ger dig möjlighet att köra proaktiv insamlingar baserat på definierad nätverket avvikelser. Andra användningsområden omfattar att samla in nätverksstatistik för att hämta information om nätverket intrång och felsökning klient-/ serverkommunikation.

Resurser som distribueras i Azure kör 24/7. Du och din personal kan inte aktivt övervaka status för alla resurser 24/7. Till exempel vad händer om ett problem inträffar kl 2?

Genom att använda Nätverksbevakaren, aviseringar och funktioner från i Azure-ekosystemet, kan du proaktivt svara med data och verktyg för att lösa problem i nätverket.

![Scenario][scenario]

## <a name="prerequisites"></a>Förutsättningar

* Den senaste versionen av [Azure PowerShell](/powershell/azure/install-azurerm-ps).
* En befintlig instans av Nätverksbevakaren. Om du inte redan har en, [skapa en instans av Nätverksbevakaren](network-watcher-create.md).
* En befintlig virtuell dator i samma region som Nätverksbevakaren med den [Windows tillägget](../virtual-machines/windows/extensions-nwa.md) eller [Linux-tillägg för virtuell dator](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Scenario

Din virtuella dator skickar flera TCP-segment än vanligt i det här exemplet och du vill bli aviserad om. TCP-segment som används som exempel här, men du kan använda alla aviseringstillståndet.

När du meddelas vill du ta emot paketnivå data för att förstå varför kommunikation har ökat. Du kan sedan vidta åtgärder för att återställa den virtuella datorn till vanlig kommunikation.

Det här scenariot förutsätter att du har en befintlig instans av Nätverksbevakaren och en resursgrupp med en giltig virtuell dator.

I följande lista finns en översikt över arbetsflödet som äger rum:

1. En avisering utlöses på den virtuella datorn.
1. Aviseringen anropar din Azure-funktion via en webhook.
1. Din Azure-funktion bearbetar aviseringen och startar en Nätverksbevakaren paket avbildningssessionen.
1. Paketinsamling körs på den virtuella datorn och samlar in trafik.
1. Filen i paketet har överförts till ett lagringskonto för granskning och diagnos.

Om du vill automatisera processen vi skapa och ansluta en avisering på vår VM att utlösa när händelsen inträffar. Vi kan också skapa en funktion för att anropa Nätverksbevakaren.

Det här scenariot gör följande:

* Skapar en Azure-funktion som startar en paketinsamling.
* Skapar en aviseringsregel på en virtuell dator och konfigurerar varningsregel för att anropa funktionen Azure.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Det första steget är att skapa en Azure-funktion för att bearbeta aviseringen och skapa en paketinsamling.

1. I den [Azure-portalen](https://portal.azure.com)väljer **skapar du en resurs** > **Compute** > **Funktionsapp**.

    ![Skapa en funktionsapp][1-1]

2. På den **Funktionsapp** bladet, ange följande värden och välj sedan **OK** att skapa appen:

    |**Inställning** | **Värde** | **Detaljer** |
    |---|---|---|
    |**Appens namn**|PacketCaptureExample|Namnet på funktionen appen.|
    |**Prenumeration**|[Din prenumeration] Prenumerationen för att skapa funktionen appen.||
    |**Resursgrupp**|PacketCaptureRG|Resursgruppen som innehåller funktionen appen.|
    |**Värdplan**|Förbrukningsplan| Typ av planera din app använder för funktionen. Alternativen är förbrukning eller Azure App Service-plan. |
    |**Plats**|Centrala USA| Den region där du skapar den funktionen.|
    |**Lagringskonto**|{namn} automatiskt| Lagringskontot som Azure Functions måste för allmänna lagring.|

3. På den **PacketCaptureExample funktionen appar** bladet väljer **funktioner** > **anpassad funktionen**  >  **+**.

4. Välj **HttpTrigger Powershell**, och ange sedan återstående information. Slutligen vill skapa funktionen väljer **skapa**.

    |**Inställning** | **Värde** | **Detaljer** |
    |---|---|---|
    |**scenario**|Experimentell|Typen av scenario|
    |**Namnge din funktion**|AlertPacketCapturePowerShell|Namnet på funktionen|
    |**Åtkomstnivå**|Funktion|Åtkomstnivå för funktionen|

![Exempel på funktioner][functions1]

> [!NOTE]
> PowerShell-mallen är experiment och har inte fullständigt stöd.

Anpassningar som krävs för det här exemplet och beskrivs i följande steg.

### <a name="add-modules"></a>Lägg till moduler

Överför den senaste PowerShell-modulen till appen med funktionen för att använda nätverket Watcher PowerShell-cmdlets.

1. Kör följande PowerShell-kommando på den lokala datorn med Azure PowerShell-moduler som är installerad:

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    Det här exemplet får du den lokala sökvägen för dina Azure PowerShell-moduler. Dessa mappar som används i ett senare steg. Moduler som används i det här scenariot är:

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![PowerShell-mappar][functions5]

1. Välj **fungerar appinställningar** > **gå till App Service Editor**.

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

1. När du är klar, var mappen bör ha PowerShell-modulen filer från din lokala dator.

    ![PowerShell-filer][functions7]

### <a name="authentication"></a>Autentisering

Om du vill använda PowerShell-cmdlets, måste du autentisera. Du kan konfigurera autentisering i appen funktion. Om du vill konfigurera autentisering måste du konfigurera miljövariabler och överföra en krypterad nyckelfilen till appen med funktionen.

> [!NOTE]
> Det här scenariot ger bara ett exempel på hur du implementerar autentisering med Azure Functions. Det finns andra sätt att göra detta.

#### <a name="encrypted-credentials"></a>Krypterade autentiseringsuppgifter

Följande PowerShell-skript skapar en nyckelfil som kallas **PassEncryptKey.key**. Det ger också en krypterad version av det lösenord som har angetts. Lösenordet är samma lösenord som har definierats för det Azure Active Directory-program som används för autentisering.

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

I App Service Redigeraren för funktionsapp, skapa en mapp med namnet **nycklar** under **AlertPacketCapturePowerShell**. Sedan ladda upp den **PassEncryptKey.key** -fil som du skapade i föregående exempel PowerShell.

![Funktioner nyckel][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Hämta värden för miljövariabler

Det slutliga kravet är att ställa in miljövariabler som är nödvändiga för att komma åt värden för autentisering. I följande lista visas de miljövariabler som har skapats:

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
   > Det lösenord som du använder för att skapa programmet ska vara samma lösenord som du skapade tidigare när du sparar nyckelfilen.

1. Välj i Azure-portalen **prenumerationer**. Välj prenumerationen du använder och välj sedan **åtkomstkontroll (IAM)**.

    ![Funktioner IAM][functions9]

1. Välj kontot som ska användas och välj sedan **egenskaper**. Kopiera program-ID.

    ![Funktioner program-ID][functions10]

#### <a name="azuretenant"></a>AzureTenant

Hämta klient-ID genom att köra följande PowerShell-exempel:

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Värdet för miljövariabeln AzureCredPassword är det värde som du får från att köra följande PowerShell-exempel. Det här exemplet är samma som visas i den föregående **krypterade autentiseringsuppgifter** avsnitt. Värdet som behövs är resultatet av den `$Encryptedpassword` variabeln.  Det här är lösenordet för tjänstens huvudnamn som du har krypterats med hjälp av PowerShell-skript.

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

### <a name="store-the-environment-variables"></a>Lagra miljövariablerna

1. Gå till funktionsapp. Välj sedan **fungerar appinställningar** > **konfigurera appinställningar**.

    ![Konfigurera appinställningar][functions11]

1. Lägg till miljövariablerna och deras värden i app-inställningar och välj sedan **spara**.

    ![Appinställningar][functions12]

### <a name="add-powershell-to-the-function"></a>Lägg till PowerShell till funktionen

Det är nu att ringa till Nätverksbevakaren från i Azure-funktion. Implementeringen av den här funktionen kan variera beroende på krav. Dock är det allmänna flödet av koden på följande sätt:

1. Processen indataparametrar.
2. Frågan befintliga paket samlar in för att kontrollera gränser och lösa namnkonflikter.
3. Skapa en paketinsamling med lämpliga parametrar.
4. Avsökningen paket avbilda regelbundet tills den är klar.
5. Meddela användaren att hämtningens paket har slutförts.

I följande exempel är PowerShell-kod som kan användas i funktionen. Det finns värden som behöver ersättas för **subscriptionId**, **resourceGroupName**, och **storageAccountName**.

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
1. När du har skapat din funktion kan du konfigurera aviseringen för att anropa den URL som är associerad med funktionen. Kopiera URL som funktionen från din funktion för att få det här värdet.

    ![Hitta funktions-URL][functions13]

2. Kopiera URL-Adressen för funktionen för din funktionsapp.

    ![Kopiera funktions-URL][2]

Om du behöver anpassade egenskaper i nyttolasten för POST-begäran webhook läsa [konfigurera en webhook på en Azure mått avisering](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="configure-an-alert-on-a-vm"></a>Konfigurera en avisering på en virtuell dator

Aviseringar kan konfigureras för att meddela personer när ett specifikt mått överskrider ett tröskelvärde som är tilldelad. Aviseringen är TCP-segment som skickas i det här exemplet, men aviseringen kan aktiveras för många andra mått. I det här exemplet konfigureras en avisering för att anropa en webhook för att anropa funktionen.

### <a name="create-the-alert-rule"></a>Skapa varningsregeln

Gå till en befintlig virtuell dator och sedan lägga till en varningsregel. Mer detaljerad dokumentation om hur du konfigurerar aviseringar finns på [skapa aviseringar i Azure-Monitor för Azure-tjänster - Azure-portalen](../monitoring-and-diagnostics/insights-alerts-portal.md). Ange följande värden i den **varningsregeln** bladet och väljer sedan **OK**.

  |**Inställning** | **Värde** | **Detaljer** |
  |---|---|---|
  |**Namn**|TCP_Segments_Sent_Exceeded|Namnet på regeln.|
  |**Beskrivning**|TCP-segment skickas överskred tröskeln|Beskrivning för regeln.||
  |**Mått**|TCP-segment som skickats| Måttet som du använder för att utlösa aviseringen. |
  |**Villkor**|Större än| Villkoret du vill använda vid utvärdering av måttet.|
  |**Tröskelvärde**|100| Värdet för det mått som utlöser varningen. Det här värdet ska anges till ett giltigt värde för din miljö.|
  |**Period**|Under de senaste fem minuterna| Anger den period som ska sökas efter tröskelvärdet för måttet.|
  |**Webhook**|[Webhooksadressen från funktionsapp]| Webhooksadressen från funktionsapp som skapades i föregående steg.|

> [!NOTE]
> Mått för TCP-segment är inte aktiverad som standard. Mer information om hur du aktiverar fler mått genom att besöka [aktivera övervakning och diagnostik](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Granska resultaten

Efter det att kriterierna för avisering utlösare skapas en paketinsamling. Gå till Nätverksbevakaren och välj sedan **paketinsamling**. Du kan välja paket avbilda filen länk för att hämta paketinsamling på den här sidan.

![Visa paketinsamling][functions14]

Om filen lagras lokalt kan du hämta det genom att logga in till den virtuella datorn.

Anvisningar om att hämta filer från Azure storage-konton finns [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som du kan använda är [Lagringsutforskaren](http://storageexplorer.com/).

När din avbildning har hämtats, du kan visa den med ett verktyg som kan läsa en **CAP** fil. Följande är länkar till två av dessa verktyg:

- [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visar paket-insamlingar genom att besöka [paket avbilda analys med Wireshark](network-watcher-deep-packet-inspection.md).


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
