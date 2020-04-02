---
title: Konfigurera ett labb för att använda Fjärrskrivbordsgateway i Azure DevTest Labs
description: Lär dig hur du konfigurerar ett labb i Azure DevTest Labs med en fjärrskrivbordsgateway för att säkerställa säker åtkomst till labb-virtuella datorer utan att behöva exponera RDP-porten.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2cdafa9a36a5f906151ca6946e18ef82bc7f1e01
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529426"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Konfigurera ditt labb i Azure DevTest Labs för att använda en fjärrskrivbordsgateway
I Azure DevTest Labs kan du konfigurera en fjärrskrivbordsgateway för ditt labb för att säkerställa säker åtkomst till labbets virtuella datorer (VMs) utan att behöva exponera RDP-porten. Labbet är en central plats där labbanvändarna kan visa och ansluta till alla virtuella datorer som de har åtkomst till. Knappen **Anslut** på sidan **Virtuell dator** skapar en maskinspecifik RDP-fil som du kan öppna för att ansluta till datorn. Du kan anpassa och skydda RDP-anslutningen ytterligare genom att ansluta labbet till en fjärrskrivbordsgateway. 

Den här metoden är säkrare eftersom labbanvändaren autentiserar direkt till gateway-datorn eller kan använda företagsautentiseringsuppgifter på en domänansluten gateway-dator för att ansluta till sina datorer. Labbet stöder också användning av tokenautentisering till gateway-datorn som tillåter användare att ansluta till sina virtuella labbdatorer utan att RDP-porten exponeras för internet. Den här artikeln går igenom ett exempel på hur du konfigurerar ett labb som använder tokenautentisering för att ansluta till labbdatorer.

## <a name="architecture-of-the-solution"></a>Lösningens arkitektur

![Lösningens arkitektur](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. Åtgärden [Hämta RDP-filinnehåll](/rest/api/dtl/virtualmachines/getrdpfilecontents) anropas när du väljer knappen Anslut.1. **Connect** 
1. Åtgärden Hämta RDP-filinnehåll `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` anropar för att begära en autentiseringstoken.
    1. `{gateway-hostname}`är gateway-värdnamnet som anges på sidan **Labbinställningar** för ditt labb i Azure-portalen. 
    1. `{lab-machine-name}`är namnet på den maskin som du försöker ansluta.
    1. `{port-number}`är den port där anslutningen måste göras. Vanligtvis denna port är 3389. Om labbet VM använder den [delade IP-funktionen](devtest-lab-shared-ip.md) i DevTest Labs kommer porten att vara annorlunda.
1. Fjärrskrivbordsgatewayen skjuter `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` upp anropet från till en Azure-funktion för att generera autentiseringstoken. DevTest Labs-tjänsten innehåller automatiskt funktionsnyckeln i begäranden. Funktionsnyckeln ska sparas i labbets nyckelvalv. Namnet på den hemligheten som ska visas som **gateway token hemlighet** på sidan **Labbinställningar** för labbet.
1. Azure-funktionen förväntas returnera en token för certifikatbaserad tokenautentisering mot gateway-datorn.  
1. Åtgärden Hämta RDP-filinnehåll returnerar sedan den fullständiga RDP-filen, inklusive autentiseringsinformationen.
1. Du öppnar RDP-filen med ditt önskade RDP-anslutningsprogram. Kom ihåg att inte alla RDP-anslutningsprogram stöder tokenautentisering. Autentiseringstoken har ett utgångsdatum som anges av funktionsappen. Gör anslutningen till labbdatorn innan token upphör att gälla.
1. När fjärrskrivbordsgatewaydatorn autentiserar token i RDP-filen vidarebefordras anslutningen till labbdatorn.

### <a name="solution-requirements"></a>Lösningskrav
Om du vill arbeta med funktionen För DevTest Labs-tokenautentisering finns det några konfigurationskrav för gateway-datorer, domännamnstjänster (DNS) och funktioner.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Krav för fjärrskrivbordsgatewaydatorer
- TLS/SSL-certifikat måste vara installerat på gateway-datorn för att hantera HTTPS-trafik. Certifikatet måste matcha det fullständigt kvalificerade domännamnet (FQDN) för belastningsutjämnaren för gateway-servergruppen eller FQDN för själva datorn om det bara finns en dator. Jokertecken TLS/SSL-certifikat fungerar inte.  
- Ett signeringscertifikat installerat på gateway-datorer. Skapa ett signeringscertifikat med hjälp av [create-signingCertificate.ps1-skriptet.](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)
- Installera modulen [Pluggbar autentisering](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) som stöder tokenautentisering för fjärrskrivbordsgatewayen. Ett exempel på en `RDGatewayFedAuth.msi` sådan modul är att levereras med [System Center Virtual Machine Manager (VMM) avbildningar](/system-center/vmm/install-console?view=sc-vmm-1807). Mer information om System Center finns i Dokumentation och [prisinformation för](https://www.microsoft.com/cloud-platform/system-center-pricing) [System Center](https://docs.microsoft.com/system-center/) .  
- Gateway-servern kan hantera `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`begäranden som gjorts till .

    Gateway-hostname är FQDN för belastningsutjämnaren för gateway-servergruppen eller FQDN för själva datorn om det bara finns en dator. Det `{lab-machine-name}` är namnet på labbmaskinen som du försöker `{port-number}` ansluta och den är-port där anslutningen ska göras.  Som standard är den här porten 3389.  Men om den virtuella datorn använder den [delade IP-funktionen](devtest-lab-shared-ip.md) i DevTest Labs, kommer porten att vara annorlunda.
- [Modulen Application Routing Request](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) for Internet Information Server (IIS) kan användas för att omdirigera `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` begäranden till azure-funktionen, som hanterar begäran om att hämta en token för autentisering.


## <a name="requirements-for-azure-function"></a>Krav för Azure-funktionen
Azure-funktionen hanterar begäran `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` med format för och returnerar autentiseringstoken baserat på samma signeringscertifikat som är installerat på gateway-datorerna. Den `{function-app-uri}` är uri som används för att komma åt funktionen. Funktionsnyckeln skickas automatiskt i huvudet på begäran. En exempelfunktion finns [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)i . 


## <a name="requirements-for-network"></a>Krav för nätverk

- DNS för FQDN som är associerat med TLS/SSL-certifikatet som är installerat på gateway-datorerna måste dirigera trafik till gateway-datorn eller belastningsutjämnaren för gateway-datorgruppen.
- Om labbdatorn använder privata IP-adresser måste det finnas en nätverkssökväg från gateway-datorn till labbdatorn, antingen genom att dela samma virtuella nätverk eller använda peer-virtuella virtuella nätverk.

## <a name="configure-the-lab-to-use-token-authentication"></a>Konfigurera labbet så att det använder tokenautentisering 
Det här avsnittet visar hur du konfigurerar ett labb för att använda en fjärrskrivbordsgatewaydator som stöder tokenautentisering. Det här avsnittet täcker inte hur du konfigurerar en fjärrskrivbordsgatewaygrupp själv. Den informationen finns i exemplet för att skapa ett avsnitt om [fjärrskrivbordsgateway](#sample-to-create-a-remote-desktop-gateway) i slutet av den här artikeln. 

Innan du uppdaterar labbinställningarna lagrar du nyckeln som behövs för att köra funktionen för att returnera en autentiseringstoken i labbets nyckelvalv. Du kan hämta funktionsnyckelvärdet på sidan **Hantera** för funktionen i Azure-portalen. Mer information om hur du sparar en hemlighet i ett nyckelvalv finns i [Lägga till en hemlighet i Key Vault](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault). Spara namnet på hemligheten för senare användning.

Om du vill hitta ID:et för labbets nyckelvalv kör du följande Azure CLI-kommando: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Konfigurera labbet så att det använder tokenautentiseringen med hjälp av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
1. Välj ditt **labb**i listan över labb.
1. På labbets sida väljer du **Konfiguration och principer**.
1. Välj **Labbinställningar**i avsnittet **Inställningar** på den vänstra menyn .
1. I avsnittet **Fjärrskrivbord** anger du fullständigt kvalificerat domännamn (FQDN) eller IP-adressen för gateway-datorn för fjärrskrivbordstjänster eller servergruppen för fältet **Gateway-värdnamn.** Det här värdet måste matcha FQDN för TLS/SSL-certifikatet som används på gateway-datorer.

    ![Alternativ för fjärrskrivbord i labbinställningar](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. Ange **Remote desktop** namnet på hemligheten som skapats tidigare för **gateway-token** hemligheten. Det här värdet är inte själva funktionsnyckeln, utan namnet på hemligheten i labbets nyckelvalv som innehåller funktionsnyckeln.

    ![Gateway token hemlighet i labbinställningar](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Spara** Ändringar.

    > [!NOTE] 
    > Genom att klicka på **Spara**godkänner du licensvillkoren för [Fjärrskrivbordsgateway.](https://www.microsoft.com/licensing/product-licensing/products) Mer information om fjärrgateway finns i [Välkommen till Fjärrskrivbordstjänster](https://aka.ms/rds) och [Distribuera din fjärrskrivbordsmiljö](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Om det är att föredra att konfigurera labbet via automatisering läser du [Ange DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) för ett exempel på PowerShell-skript för att ange **hemliga inställningar** för **gateway-värdnamn** och gateway token. [Azure DevTest Labs GitHub-databasen](https://github.com/Azure/azure-devtestlab) tillhandahåller också en Azure Resource Manager-mall som skapar eller uppdaterar ett labb med hemliga inställningar **för gateway-värdnamn** och **gateway-token.**

## <a name="configure-network-security-group"></a>Konfigurera nätverkssäkerhetsgrupp
För att ytterligare skydda labbet kan en nätverkssäkerhetsgrupp (NSG) läggas till i det virtuella nätverket som används av labbets virtuella datorer. Instruktioner om hur du konfigurerar en NSG finns i [Skapa, ändra eller ta bort en nätverkssäkerhetsgrupp](../virtual-network/manage-network-security-group.md).

Här är ett exempel NSG som bara tillåter trafik som först går genom gatewayen för att nå labbmaskiner. Källan i den här regeln är IP-adressen för den enskilda gateway-datorn eller IP-adressen för belastningsutjämnaren framför gateway-datorerna.

![Nätverkssäkerhetsgrupp - regler](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Exempel för att skapa en fjärrskrivbordsgateway

> [!NOTE] 
> Genom att använda exempelmallarna godkänner du licensvillkoren för [Fjärrskrivbordsgateway](https://www.microsoft.com/licensing/product-licensing/products). Mer information om fjärrgateway finns i [Välkommen till Fjärrskrivbordstjänster](https://aka.ms/rds) och [Distribuera din fjärrskrivbordsmiljö](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

[Azure DevTest Labs GitHub-databasen](https://github.com/Azure/azure-devtestlab) innehåller några exempel som hjälper dig att konfigurera de resurser som behövs för att använda tokenautentisering och fjärrskrivbordsgateway med DevTest Labs. Dessa exempel inkluderar Azure Resource Manager-mallar för gateway-datorer, labbinställningar och funktionsapp.

Följ dessa steg för att konfigurera en exempellösning för servergruppen för fjärrskrivbordsgateway.

1. Skapa ett signeringscertifikat.  Kör [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Spara tumavtrycket, lösenordet och Base64-kodningen för det skapade certifikatet.
2. Skaffa ett TLS/SSL-certifikat. FQDN som är associerat med TLS/SSL-certifikatet måste vara för den domän som du styr. Spara tumavtrycket, lösenordet och Base64-kodningen för det här certifikatet. Använd följande kommandon för att få tumavtryck med PowerShell.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Om du vill hämta Base64-kodningen med PowerShell använder du följande kommando.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Ladda ner [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)filer från .

    Mallen kräver åtkomst till några andra Resource Manager-mallar och relaterade resurser på samma bas-URI. Kopiera alla filer [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) från och RDGatewayFedAuth.msi till en blob-behållare i ett lagringskonto.  
4. Distribuera **azuredeploy.json** från [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway). Mallen tar följande parametrar:
    - adminUsername – Obligatoriskt.  Administratörsanvändarnamn för gateway-datorerna.
    - adminPassword - Krävs. Lösenord för administratörskontot för gateway-datorerna.
    - instanceCount – Antal gateway-datorer att skapa.  
    - alwaysOn – Anger om den skapade Azure Functions-appen ska behållas i ett varmt tillstånd eller inte. Om du behåller Azure Functions-appen undviker du fördröjningar när användare först försöker ansluta till sin virtuella labb-dator, men det har kostnadskonsekvenser.  
    - tokenLifetime – Hur länge den skapade token kommer att vara giltig. Formatet är HH:MM:SS.
    - sslCertificate – Base64-kodningen för TLS/SSL-certifikatet för gateway-datorn.
    - sslCertificatePassword – Lösenordet för TLS/SSL-certifikatet för gateway-datorn.
    - sslCertificateThumbprint - Certifikatets tumavtryck för identifiering i det lokala certifikatarkivet för TLS/SSL-certifikatet.
    - signCertificate – Base64-kodningen för signering av certifikat för gateway-datorn.
    - signCertificatePassword – Lösenordet för signering av certifikat för gateway-datorn.
    - signCertificateThumbprint - Certifikatets tumavtryck för identifiering i det lokala certifikatarkivet för signeringscertifikatet.
    - _artifactsLocation – URI-plats där alla stödresurser finns. Det här värdet måste vara en fullt kvalificerad UIR, inte en relativ sökväg.
    - _artifactsLocationSasToken – SAS-token (Shared Access Signature) som används för att komma åt supportresurser, om platsen är ett Azure-lagringskonto.

    Mallen kan distribueras med hjälp av Azure CLI med hjälp av följande kommando:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Här är beskrivningarna av parametrarna:

    - Slutpunkten {storage-account-end} kan erhållas genom att köra `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`.  {storage-acct-name} är namnet på det lagringskonto som innehåller filer som du har laddat upp.  
    - {container-name} är namnet på behållaren i {storage-acct-name} som innehåller filer som du har laddat upp.  
    - {sas-token} kan erhållas `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`genom att köra . 
        - {storage-acct-name} är namnet på det lagringskonto som innehåller filer som du har laddat upp.  
        - {container-name} är namnet på behållaren i {storage-acct-name} som innehåller filer som du har laddat upp.  
        - {utc-utgångsdatum} är det datum, i UTC, då SAS-token upphör att gälla och SAS-token kan inte längre användas för att komma åt lagringskontot.

    Registrera värdena för gatewayFQDN och gatewayIP från malldistributionutdata. Du måste också spara värdet på funktionsnyckeln för den nyskapade funktionen, som finns på fliken [Funktionsappinställningar.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)
5. Konfigurera DNS så att FQDN för TLS/SSL-cert dirigerar till IP-adressen för gatewayIP från föregående steg.

    När servergruppen för fjärrskrivbordsgateway har skapats och lämpliga DNS-uppdateringar har gjorts är den klar att användas av ett labb i DevTest Labs. Hemliga inställningar **för gatewayvärdnamn** och **gateway token** måste konfigureras för att använda gateway-datorer som du har distribuerat. 

    > [!NOTE]
    > Om labbdatorn använder privata IP-adresser måste det finnas en nätverkssökväg från gateway-datorn till labbdatorn, antingen genom att dela samma virtuella nätverk eller med hjälp av ett peer-virtuellt nätverk.

    När både gateway och labb har konfigurerats innehåller anslutningsfilen som skapas när labbanvändaren klickar på **Connect** automatiskt information som krävs för att ansluta med hjälp av tokenautentisering.     

## <a name="next-steps"></a>Nästa steg
Mer information om [dokumentation](/windows-server/remote/remote-desktop-services/Welcome-to-rds) för Fjärrskrivbordstjänster finns i följande artikel


