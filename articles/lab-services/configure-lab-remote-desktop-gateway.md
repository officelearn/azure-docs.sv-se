---
title: Konfigurera ett labb för att använda Remote Desktop Gateway i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb i Azure DevTest Labs med en gateway för fjärrskrivbord att se till att säkra åtkomst till labb virtuella datorer utan att exponera RDP-porten.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 430734878c01d10a4e7dd385dc75d8d502a2d82c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079007"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Konfigurera ditt labb i Azure DevTest Labs för att använda en gateway för fjärrskrivbord
Du kan konfigurera en gateway för fjärrskrivbord för labbet att säkerställa säker åtkomst till virtuella datorer (VM) för testlabbet utan att exponera RDP-porten i Azure DevTest Labs. Labbet ger en central plats för användarna att visa och ansluta till alla virtuella datorer som de har åtkomst till labb. Den **Connect** knappen på den **VM** sida skapas en datorspecifik RDP-fil som du kan öppna för att ansluta till datorn. Du kan ytterligare anpassa och skydda RDP-anslutningen genom att ansluta ditt labb till en gateway för fjärrskrivbord. 

Den här metoden är säkrare eftersom lab-användare autentiserar direkt till gateway-datorn eller använda företagets autentiseringsuppgifter på en domänansluten gateway-dator för att ansluta till sina datorer. Labbet har också stöd för autentisering med enhetstoken till gateway-datorn där användarna kan ansluta till sina virtuella datorer för testlabbet utan RDP-porten som är exponerade mot internet. Den här artikeln går igenom ett exempel på hur du ställer in ett labb som använder autentisering med enhetstoken för att ansluta till labbdatorer.

## <a name="architecture-of-the-solution"></a>Arkitekturen för lösningen

![Arkitekturen för lösningen](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. Den [hämta RDP-filens innehåll](/rest/api/dtl/virtualmachines/getrdpfilecontents) åtgärden anropas när du väljer den **Connect** button.1. 
1. Åtgärden hämta RDP-filen innehållet anropar `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` att begära en autentiseringstoken.
    1. `{gateway-hostname}` är gateway-värdnamn som anges på den **Lab inställningar** för ditt labb i Azure-portalen. 
    1. `{lab-machine-name}` är namnet på den dator som du försöker ansluta.
    1. `{port-number}` är den port som anslutningen ska upprättas. Den här porten är vanligtvis 3389. Om labbet Virtuella datorn använder den [delade IP](devtest-lab-shared-ip.md) funktion i DevTest Labs, porten som påverkas.
1. Fjärrskrivbordsgateway skjuter upp anrop från `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` till en Azure-funktion att generera autentiseringstoken. Tjänsten DevTest Labs inkluderar automatiskt funktionsnyckel i rubriken. Funktionen nyckeln är sparas i den testmiljön key vault. Namnet på den hemlighet som ska visas som **Gateway-token secret** på den **Lab inställningar** för labbet.
1. Azure-funktion förväntas returnera en token för certifikatbaserad autentisering med enhetstoken mot gateway-datorn.  
1. Hämta RDP-filen innehåll åtgärd och sedan returnerar den fullständiga RDP-filen, inklusive autentiseringsinformationen.
1. Du kan öppna RDP-filen med din önskade programmet för RDP-anslutning. Kom ihåg att inte alla program för RDP-anslutning stöder autentisering med enhetstoken. Autentiseringstoken har ett förfallodatum genom att ange av funktionsappen. Kontrollera anslutningen till Virtuella labbdatorer innan token upphör att gälla.
1. När remote desktop gateway-datorn autentiserar token i RDP-filen, vidarebefordras anslutningen till din dator i labbet.

### <a name="solution-requirements"></a>Lösningskrav
Om du vill arbeta med funktionen DevTest Labs tokenautentisering, finns det några konfigurationskrav för gateway-datorer, domain name services (DNS) och funktioner.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Krav för gateway för fjärrskrivbord-datorer
- SSL-certifikat måste installeras på gateway-datorn för att hantera HTTPS-trafik. Certifikatet måste matcha det fullständigt kvalificerade domännamnet (FQDN) för belastningsutjämnaren för gateway-grupp eller det fullständiga Domännamnet för datorn, om det finns bara en dator. Jokertecken SSL-certifikat fungerar inte.  
- Ett signeringscertifikat som installerats på gateway-datorer. Skapa ett signeringscertifikat med hjälp av [skapa SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) skript.
- Installera den [Pluggable Authentication](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) module som stöder autentisering med enhetstoken för fjärrskrivbordsgateway. Ett exempel på sådana en modul är `RDGatewayFedAuth.msi` som medföljer [System Center Virtual Machine Manager (VMM)-avbildningar](/system-center/vmm/install-console?view=sc-vmm-1807). Mer information om System Center finns i [dokumentation för System Center](https://docs.microsoft.com/system-center/) och [prisinformation](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Gateway-servern kan hantera begäranden som görs till `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`.

    Gateway-värdnamnet är det fullständiga Domännamnet för belastningsutjämnaren av gateway-grupp eller det fullständiga Domännamnet för datorn om det finns bara en dator. Den `{lab-machine-name}` är namnet på den labb-dator som du försöker ansluta, och `{port-number}` är port som anslutningen kommer att göras.  Som standard är den här porten 3389.  Men om den virtuella datorn använder den [delade IP](devtest-lab-shared-ip.md) funktion i DevTest Labs, porten som påverkas.
- Den [routning programbegäran](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) modul för Internet Information Server (IIS) kan användas för att omdirigera `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` förfrågningar till azure-funktion som hanterar begäran om att hämta en token för autentisering.


## <a name="requirements-for-azure-function"></a>Krav för Azure-funktion
Azure-funktionen hanterar begäran med formatet för `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` och returnerar autentiseringstoken baserat på samma signeringscertifikat som installerats på gateway-datorerna. Den `{function-app-uri}` är den uri som används för åtkomst till funktionen. Funktionsnyckel skickas automatiskt i huvudet i begäran. En exempelfunktion Se [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs). 


## <a name="requirements-for-network"></a>Krav för nätverk

- DNS FQDN-namn som är associerade med det SSL-certifikat som installerats på gateway-datorerna måste dirigera trafik till gateway-dator eller belastningsutjämnare av servergruppen för gateway-datorn.
- Om labb-dator använder privata IP-adresser, måste det finnas en nätverkssökväg från gateway-datorn till labb-dator, antingen via delning av samma virtuella nätverk eller använda peer-kopplade virtuella nätverk.

## <a name="configure-the-lab-to-use-token-authentication"></a>Konfigurera labb för att använda autentisering med enhetstoken 
Det här avsnittet visar hur du konfigurerar ett labb för att använda en gateway för fjärrskrivbord-dator som har stöd för autentisering med enhetstoken. Det här avsnittet täcker inte hur du konfigurerar en gateway för fjärrskrivbord-grupp. Den informationen finns i den [exempel för att skapa en gateway för fjärrskrivbord](#sample-to-create-a-remote-desktop-gateway) i slutet av den här artikeln. 

Lagra den nyckel som behövs för att köra funktionen för att returnera en autentiseringstoken i den testmiljön nyckelvalv har innan du uppdaterar labb-inställningar. Du kan hämta funktionen nyckelvärdet i den **hantera** för funktionen i Azure-portalen. Mer information om hur du sparar en hemlighet i key vault finns i [lägga till en hemlighet i Key Vault](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault). Spara namnet på hemligheten för senare användning.

Kör följande Azure CLI-kommando för att hitta ID: T för den testmiljön nyckelvalv: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Konfigurera labb för att använda token autentisering med hjälp av de här stegen:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
1. I listan över labbar, väljer din **lab**.
1. På sidan för den testmiljön väljer **konfiguration och principer**.
1. På den vänstra menyn i den **inställningar** väljer **Lab inställningar**.
1. I den **fjärrskrivbord** avsnittet anger den fullständigt kvalificerade domännamnet (FQDN) eller IP-adressen för gateway-datorn Fjärrskrivbordstjänster eller servergrupp för den **Gateway värdnamn** fält. Det här värdet måste matcha det fullständiga Domännamnet för SSL-certifikatet som används på datorer för gateway.

    ![Remote desktop alternativ i inställningarna för labb](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. I den **fjärrskrivbord** avsnittet för **gatewaytoken** hemlighet, anger du namnet på den hemlighet som skapades tidigare. Det här värdet är inte själva nyckeln funktionen, men namnet på hemlighet i den testmiljön nyckelvalv som innehåller funktionsnyckel.

    ![Gateway-token secret i inställningarna för labb](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Spara** ändringar.

    > [!NOTE] 
    > Genom att klicka på **spara**, samtycker du till [licensvillkoren för Remote Desktop Gateway](https://www.microsoft.com/licensing/product-licensing/products). Mer information om fjärr-gateway finns i [Välkommen till Fjärrskrivbordstjänster](https://aka.ms/rds) och [Distribuera fjärråtkomst skrivbordsmiljön](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Om du konfigurerar labbet via automation är att föredra, se [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) ett PowerShell-exempelskript att ställa in **gateway värdnamn** och **gateway-token secret**inställningar. Den [Azure DevTest Labs GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab) innehåller också en Azure Resource Manager-mall som skapar eller uppdaterar ett testlabb med den **gateway värdnamn** och **gateway-token secret**inställningar.

## <a name="configure-network-security-group"></a>Konfigurera någon nätverkssäkerhetsgrupp
För att ytterligare skydda labbet, kan en nätverkssäkerhetsgrupp (NSG) läggas till det virtuella nätverket som används av virtuella datorer för testlabbet. Mer information hur du ställer in en NSG finns [skapa, ändra eller ta bort en nätverkssäkerhetsgrupp](../virtual-network/manage-network-security-group.md).

Här är ett exempel NSG som endast tillåter trafik som passerar först gatewayen att nå labbdatorer. Källa i den här regeln är IP-adressen för den enda gateway-datorn, eller IP-adressen för belastningsutjämnaren framför gateway-datorerna.

![Nätverkssäkerhetsgrupp - regler](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Exempel för att skapa en gateway för fjärrskrivbord

> [!NOTE] 
> Med hjälp av exempelmallarna, godkänner du [licensvillkoren för Remote Desktop Gateway](https://www.microsoft.com/licensing/product-licensing/products). Mer information om fjärr-gateway finns i [Välkommen till Fjärrskrivbordstjänster](https://aka.ms/rds) och [Distribuera fjärråtkomst skrivbordsmiljön](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

Den [Azure DevTest Labs GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab) innehåller några exempel för att installationsprogrammet de resurser som behövs för att använda autentisering med enhetstoken och fjärrskrivbordsgateway med DevTest Labs. De här exemplen omfattar Azure Resource Manager-mallar för gateway-datorer, lab inställningar och funktionsapp.

Följ dessa steg för att ställa in en exempellösningen för fjärrskrivbordsgateway-servergruppen.

1. Skapa ett signeringscertifikat.  Kör [skapa SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Spara tumavtryck, lösenord och Base64-kodning av det skapade certifikatet.
2. Få ett SSL-certifikat. FQDN som är associerade med SSL-certifikatet måste vara för domänen som du har kontroll över. Spara tumavtryck, lösenord och Base64-kodning för det här certifikatet. Använd följande kommandon för att hämta tumavtrycket med hjälp av PowerShell.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Använd följande kommando för att hämta det Base64-kodning med hjälp av PowerShell.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Ladda ned filer från [ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway).

    Mallen kräver åtkomst till några andra Resource Manager-mallar och relaterade resurser på samma bas-URI. Kopiera alla filer från [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) och RDGatewayFedAuth.msi till en blobbehållare i ett lagringskonto.  
4. Distribuera **azuredeploy.json** från [ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway). Mallen använder följande parametrar:
    - adminUsername – krävs.  Administratörsanvändarnamn för gateway-datorerna.
    - adminPassword – krävs. Lösenordet för administratörskontot för gateway-datorerna.
    - instanceCount – antal gateway-datorer för att skapa.  
    - alwaysOn – anger om du vill behålla den skapade Azure Functions-app med tillståndet varma eller inte. Att hålla Azure Functions-app undviker fördröjningar när användare försöker först ansluta till sina lab VM, men den har kostnaden effekter.  
    - tokenLifetime – hur lång tid som skapade token kommer att gälla. Formatet är: mm: ss.
    - sslCertificate – The Base64-kodning för SSL-certifikat för gateway-datorn.
    - sslCertificatePassword – lösenordet för SSL-certifikat för gateway-datorn.
    - sslCertificateThumbprint - certifikatets tumavtryck för identifiering i det lokala certifikatarkivet för SSL-certifikat.
    - signCertificate – The Base64-kodning för att signera certifikat för gateway-datorn.
    - signCertificatePassword – lösenord för att signera certifikat för gateway-datorn.
    - signCertificateThumbprint - certifikatets tumavtryck för identifiering i det lokala certifikatarkivet för signeringscertifikatet.
    - _artifactsLocation – URI platsen där alla resurser kan hittas. Det här värdet måste vara ett fullständigt kvalificerat UIR, inte en relativ sökväg.
    - _artifactsLocationSasToken – the signatur för delad åtkomst (SAS)-token som används för åtkomst till resurser, om platsen är en Azure storage-konto.

    Mallen kan distribueras med hjälp av Azure CLI med hjälp av följande kommando:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation=”{storage-account-endpoint}/{container-name}” -–parameters _artifactsLocationSasToken = “?{sas-token}”
    ```

    Här följer beskrivningar av parametrar:

    - {Storage-konto-slutpunkten} kan hämtas genom att köra `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`.  {Namnet på storage-acct-} är namnet på lagringskontot som innehåller filer som du laddat upp.  
    - De {container-name} är namnet på behållaren i den {storage-acct-name} som innehåller filer som du laddat upp.  
    - {Sas-token} kan hämtas genom att köra `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`. 
        - {Namnet på storage-acct-} är namnet på lagringskontot som innehåller filer som du laddat upp.  
        - De {container-name} är namnet på behållaren i den {storage-acct-name} som innehåller filer som du laddat upp.  
        - {Utc--utgångsdatumet} avser datumet i UTC, då SAS-token upphör att gälla och SAS-token kan inte längre användas för att komma åt lagringskontot.

    Anteckna värdena för gatewayFQDN och gatewayIP från mallutdata för distributionen. Du måste också spara värdet för funktionsnyckel för den nyligen skapade-funktion som finns i den [fungera appinställningar](../azure-functions/functions-how-to-use-azure-function-app-settings.md) fliken.
5. Konfigurera DNS så att det FQDN för SSL-certifikatet som leder till IP-adressen för gatewayIP från föregående steg.

    När Remote Desktop Gateway-servergruppen har skapats och lämpliga DNS-uppdateringar har gjorts, är den redo att användas av ett labb i DevTest Labs. Den **gateway värdnamn** och **gateway-token secret** måste inställningarna konfigureras för användning av gateway-datorerna som du har distribuerat. 

    > [!NOTE]
    > Om labb-dator använder privata IP-adresser, måste det finnas en nätverkssökväg från gateway-datorn till labb-dator, antingen via delning av samma virtuella nätverk eller med hjälp av en peer-kopplade virtuella nätverket.

    När både gatewayen och labb har konfigurerats, filen skapas när lab användaren klickar på den **Connect** automatiskt innehåller information som behövs för att ansluta med hjälp av autentisering med enhetstoken.     

## <a name="next-steps"></a>Nästa steg
Se följande artikel för mer information om Remote Desktop Services: [Remote Desktop Services-dokumentation](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


