---
title: Använd paketinsamling för att göra proaktiv nätverksövervakning med aviseringar - Azure Functions
titleSuffix: Azure Network Watcher
description: I den här artikeln beskrivs hur du skapar en aviseringsutlöst paketfångst med Azure Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: ea506e137d71fc3124a4f93f1e97750a08dd4284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842945"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Använd paketinsamling för proaktiv nätverksövervakning med aviseringar och Azure-funktioner

Network Watcher packet capture skapar insamlingssessioner för att spåra trafik in och ut ur virtuella datorer. Hämtningsfilen kan ha ett filter som har definierats för att spåra endast den trafik som du vill övervaka. Dessa data lagras sedan i en lagringsblob eller lokalt på gästdatorn.

Den här funktionen kan startas på distans från andra automatiseringsscenarier, till exempel Azure Functions. Paketinsamling ger dig möjlighet att köra proaktiva infångningar baserat på definierade nätverksavvikelser. Andra användningsområden är att samla in nätverksstatistik, få information om nätverksintrång, felsöka klient-server kommunikation och mycket mer.

Resurser som distribueras i Azure kör 24/7. Du och din personal kan inte aktivt övervaka status för alla resurser dygnet runt. Vad händer till exempel om ett problem uppstår klockan 02.00?

Genom att använda Network Watcher, aviseringar och funktioner inifrån Azure-ekosystemet kan du proaktivt svara med data och verktyg för att lösa problem i nätverket.

![Scenario][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

* Den senaste versionen av [Azure PowerShell](/powershell/azure/install-Az-ps).
* En befintlig instans av Network Watcher. Om du inte redan har [en, skapa en instans av Network Watcher](network-watcher-create.md).
* En befintlig virtuell dator i samma region som Network Watcher med [tillägget Windows](../virtual-machines/windows/extensions-nwa.md) eller Linux för [virtuell dator.](../virtual-machines/linux/extensions-nwa.md)

## <a name="scenario"></a>Scenario

I det här exemplet skickar den virtuella datorn fler TCP-segment än vanligt och du vill bli aviserad. TCP-segment används som ett exempel här, men du kan använda alla varningsvillkor.

När du får ett meddelande vill du ta emot data på paketnivå för att förstå varför kommunikationen har ökat. Sedan kan du vidta åtgärder för att återställa den virtuella datorn till regelbunden kommunikation.

Det här scenariot förutsätter att du har en befintlig instans av Network Watcher och en resursgrupp med en giltig virtuell dator.

Följande lista är en översikt över arbetsflödet som äger rum:

1. En avisering utlöses på den virtuella datorn.
1. Aviseringen anropar din Azure-funktion via en webhook.
1. Din Azure-funktion bearbetar aviseringen och startar en nätverksbevakningspaketsession.
1. Paketinsamlingen körs på den virtuella datorn och samlar in trafik.
1. Paketinfångstfilen överförs till ett lagringskonto för granskning och diagnos.

För att automatisera den här processen skapar och ansluter vi en avisering på vår virtuella dator för att utlösa när incidenten inträffar. Vi skapar också en funktion för att ringa in i Network Watcher.

Det här scenariot gör följande:

* Skapar en Azure-funktion som startar en paketfångst.
* Skapar en aviseringsregel på en virtuell dator och konfigurerar aviseringsregeln för att anropa Azure-funktionen.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Det första steget är att skapa en Azure-funktion för att bearbeta aviseringen och skapa en paketfångst.

1. I [Azure-portalen](https://portal.azure.com)väljer du Skapa > en **Create a resource** > **resursberäkningsfunktionsapp**.**Function App**

    ![Skapa en funktionsapp][1-1]

2. På **bladet Funktionsapp** anger du följande värden och väljer sedan **OK** för att skapa appen:

    |**Inställning** | **Värde** | **Detaljer** |
    |---|---|---|
    |**Appnamn**|PacketCaptureExample|Namnet på funktionsappen.|
    |**Prenumeration**|[Din prenumeration] Prenumerationen som funktionsappen ska skapas för.||
    |**Resursgrupp**|PacketCaptureRG|Resursgruppen som ska innehålla funktionsappen.|
    |**Värdplan**|Förbrukningsplan| Vilken typ av abonnemang som funktionsappen använder. Alternativen är Förbrukning eller Azure App Service-plan. |
    |**Location**|USA, centrala| Den region där funktionsappen ska skapas.|
    |**Lagringskonto**|{autogenererad}| Lagringskontot som Azure Functions behöver för allmän lagring.|

3. På bladet **PacketCaptureExample Function Apps** väljer du**Funktionsanpassad** >**+** **Functions** > funktion .

4. Välj **HttpTrigger-Powershell**och ange sedan den återstående informationen. Om du vill skapa funktionen väljer du **Slutligen Skapa**.

    |**Inställning** | **Värde** | **Detaljer** |
    |---|---|---|
    |**Scenario**|Experimentell|Typ av scenario|
    |**Namnge din funktion**|AlertPacketCapturePowerShell|Funktionens namn|
    |**Auktorisationsnivå**|Funktion|Auktoriseringsnivå för funktionen|

![Exempel på funktioner][functions1]

> [!NOTE]
> PowerShell-mallen är experimentell och har inte fullt stöd.

Anpassningar krävs för det här exemplet och förklaras i följande steg.

### <a name="add-modules"></a>Lägga till moduler

Om du vill använda PowerShell-cmdlets i Network Watcher laddar du upp den senaste PowerShell-modulen till funktionsappen.

1. På din lokala dator med de senaste Azure PowerShell-modulerna installerade kör du följande PowerShell-kommando:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    Det här exemplet ger dig den lokala sökvägen till dina Azure PowerShell-moduler. Dessa mappar används i ett senare steg. De moduler som används i det här scenariot är:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![PowerShell-mappar][functions5]

1. Välj **Funktionsappinställningar** > **Gå till App Service Editor**.

    ![Funktionsappinställningar][functions2]

1. Högerklicka på mappen **AlertPacketCapturePowershell** och skapa sedan en mapp med namnet **azuremodules**. 

4. Skapa en undermapp för varje modul som du behöver.

    ![Mapp och undermappar][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Högerklicka på undermappen **Az.Network** och välj sedan **Ladda upp filer**. 

6. Gå till dina Azure-moduler. Markera alla filer i mappen i den lokala mappen **Az.Network.** Välj sedan **OK**. 

7. Upprepa dessa steg för **Az.Accounts** och **Az.Resources**.

    ![Överföra filer][functions6]

1. När du är klar bör varje mapp ha PowerShell-modulfilerna från den lokala datorn.

    ![PowerShell-filer][functions7]

### <a name="authentication"></a>Autentisering

Om du vill använda PowerShell-cmdlets måste du autentisera. Du konfigurerar autentisering i funktionsappen. Om du vill konfigurera autentisering måste du konfigurera miljövariabler och överföra en krypterad nyckelfil till funktionsappen.

> [!NOTE]
> Det här scenariot innehåller bara ett exempel på hur du implementerar autentisering med Azure-funktioner. Det finns andra sätt att göra detta.

#### <a name="encrypted-credentials"></a>Krypterade autentiseringsuppgifter

Följande PowerShell-skript skapar en nyckelfil som heter **PassEncryptKey.key**. Det ger också en krypterad version av lösenordet som levereras. Det här lösenordet är samma lösenord som har definierats för Azure Active Directory-programmet som används för autentisering.

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

Skapa en mapp med namnet **Keys** under **AlertPacketCapturePowerShell**i apptjänstredigeraren för funktionsappen . Ladda sedan upp filen **PassEncryptKey.key** som du skapade i det tidigare PowerShell-exemplet.

![Nyckel för funktioner][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Hämta värden för miljövariabler

Det slutliga kravet är att ställa in de miljövariabler som är nödvändiga för att komma åt värdena för autentisering. I följande lista visas de miljövariabler som skapas:

* AzureClientID

* AzureTenant (azuretenant)

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

Klient-ID:et är program-ID för ett program i Azure Active Directory.

1. Om du inte redan har ett program att använda kör du följande exempel för att skapa ett program.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Lösenordet som du använder när du skapar programmet ska vara samma lösenord som du skapade tidigare när du sparade nyckelfilen.

1. Välj **Prenumerationer**i Azure-portalen . Välj den prenumeration som ska användas och välj sedan **Åtkomstkontroll (IAM)**.

    ![Funktioner IAM][functions9]

1. Välj det konto som ska användas och välj sedan **Egenskaper**. Kopiera program-ID:t.

    ![Program-ID för funktioner][functions10]

#### <a name="azuretenant"></a>AzureTenant (azuretenant)

Hämta klient-ID:et genom att köra följande PowerShell-exempel:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Värdet för variabeln AzureCredPassword-miljö är det värde som du får från att köra följande PowerShell-exempel. Det här exemplet är samma som visas i avsnittet **Krypterade autentiseringsuppgifter.** Det värde som behövs är utdata för variabeln. `$Encryptedpassword`  Det här är tjänstens huvudlösenord som du krypterade med powershell-skriptet.

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

### <a name="store-the-environment-variables"></a>Lagra miljövariabler

1. Gå till funktionsappen. Välj sedan **Funktionsappinställningar** > **Konfigurera appinställningar**.

    ![Konfigurera appinställningar][functions11]

1. Lägg till miljövariabler och deras värden i appinställningarna och välj sedan **Spara**.

    ![Appinställningar][functions12]

### <a name="add-powershell-to-the-function"></a>Lägga till PowerShell i funktionen

Det är nu dags att ringa samtal till Network Watcher inifrån Azure-funktionen. Beroende på kraven kan implementeringen av den här funktionen variera. Det allmänna flödet av koden är dock följande:

1. Processinmatningsparametrar.
2. Fråga befintliga paketinsamlingar för att verifiera gränser och lösa namnkonflikter.
3. Skapa en paketfångst med lämpliga parametrar.
4. Avsökningspaket fånga regelbundet tills det är klart.
5. Meddela användaren att paketfångsten är klar.

Följande exempel är PowerShell-kod som kan användas i funktionen. Det finns värden som måste ersättas för **subscriptionId,** **resourceGroupName**och **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Accounts\Az.Accounts.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Network\Az.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Resources\Az.Resources.psd1" -Global

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
            Connect-AzAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}

                #Get existing packetCaptures
                $packetCaptures = Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>Hämta funktions-URL:en 
1. När du har skapat funktionen konfigurerar du aviseringen så att den anropar webbadressen som är kopplad till funktionen. Om du vill hämta det här värdet kopierar du funktions-URL:en från funktionsappen.

    ![Söka efter funktions-URL:en][functions13]

2. Kopiera funktions-URL:en för funktionsappen.

    ![Kopiera funktionens URL][2]

Om du behöver anpassade egenskaper i nyttolasten för webhook POST-begäran läser du [Konfigurera en webhook på en Azure-måttavisering](../azure-monitor/platform/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Konfigurera en avisering på en virtuell dator

Aviseringar kan konfigureras för att meddela enskilda personer när ett visst mått korsar ett tröskelvärde som har tilldelats det. I det här exemplet finns aviseringen på de TCP-segment som skickas, men aviseringen kan utlösas för många andra mått. I det här exemplet är en avisering konfigurerad för att anropa en webhook för att anropa funktionen.

### <a name="create-the-alert-rule"></a>Skapa varningsregeln

Gå till en befintlig virtuell dator och lägg sedan till en aviseringsregel. Mer detaljerad dokumentation om konfiguration av aviseringar finns på [Skapa aviseringar i Azure Monitor för Azure-tjänster - Azure portal](../monitoring-and-diagnostics/insights-alerts-portal.md). Ange följande värden i **bladet Aviseringsregel** och välj sedan **OK**.

  |**Inställning** | **Värde** | **Detaljer** |
  |---|---|---|
  |**Namn**|TCP_Segments_Sent_Exceeded|Namnet på varningsregeln.|
  |**Beskrivning**|TCP-segment som skickats överskred tröskelvärdet|Beskrivning av varningsregeln.|
  |**Mått**|TCP-segment som skickats| Måttet som ska användas för att utlösa aviseringen. |
  |**Villkor**|Större än| Villkoret som ska användas vid utvärdering av måttet.|
  |**Tröskel**|100| Värdet för måttet som utlöser aviseringen. Det här värdet ska anges till ett giltigt värde för din miljö.|
  |**Period**|Under de senaste fem minuterna| Bestämmer i vilken period du vill söka efter tröskelvärdet för måttet.|
  |**Webhook (olika)**|[webhook URL från funktionsapp]| Webhook-URL:en från funktionsappen som skapades i föregående steg.|

> [!NOTE]
> TCP-segmentmåttet är inte aktiverat som standard. Läs mer om hur du aktiverar ytterligare mått genom att besöka [Aktivera övervakning och diagnostik](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Granska resultaten

Efter villkoren för aviseringsutlösare skapas en paketfångst. Gå till Network Watcher och välj sedan **Paketfångst**. På den här sidan kan du välja fillänken för paketinspelning för att hämta paketfångsten.

![Visa paketfångst][functions14]

Om hämtningsfilen lagras lokalt kan du hämta den genom att logga in på den virtuella datorn.

Instruktioner om hur du hämtar filer från Azure-lagringskonton finns i [Komma igång med Azure Blob-lagring med .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som du kan använda är [Storage Explorer](https://storageexplorer.com/).

När din inspelning har hämtats kan du visa den med vilket verktyg som helst som kan läsa en **.cap-fil.** Följande är länkar till två av dessa verktyg:

- [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
- [Wireshark](https://www.wireshark.org/)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visar dina paketinfångningar genom att besöka [Analys av paketfångst med Wireshark](network-watcher-deep-packet-inspection.md).


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
