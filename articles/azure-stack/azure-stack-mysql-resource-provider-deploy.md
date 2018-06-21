---
title: Med hjälp av MySQL-databaser på Azure-stacken | Microsoft Docs
description: Lär dig hur du kan distribuera MySQL-databaser som en tjänst på Azure-stacken och snabba steg för att distribuera MySQL Server resource provider-adapter.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 061d9431890622dafafa8c180027b8df735c100e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295760"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Använda MySQL-databaser på Microsoft Azure-stacken
Använda resursprovidern Azure Stack MySQL-Server för att visa MySQL-databaser som en tjänst i Azure-stacken. MySQL resource provider-tjänsten körs på MySQL-resursprovidern VM, som är en virtuell dator med Windows Server core.

## <a name="deploy-the-resource-provider"></a>Distribuera resursprovidern 
1. Om du inte redan har gjort det, registrera dina development kit och ladda ned Windows Server 2016 Datacenter Core avbildningen nedladdningsbara via Marketplace-hantering. Du måste använda en avbildning för Windows Server 2016 Core. Du kan också använda ett skript för att skapa en [Windows Server 2016 avbildningen](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Måste du markera alternativet core.) 

2. Logga in på en värd som kan komma åt den privilegierade slutpunkten VM:
    - Logga in på den fysiska värden på Azure SDK-installationer.  
    - Värden måste vara ett system som kan komma åt den privilegierade slutpunkten på integrerade system. 
    
    >[!NOTE] 
    > System där skriptet körs *måste* vara en Windows 10 eller Windows Server 2016 system med den senaste versionen av .NET-körningsmiljön installerad. Annars misslyckas installationen. Azure-stacken ASDK värden uppfyller det här kriteriet. 
    
3. Hämta binära MySQL resursprovidern. Kör sedan Self-Extractor extrahera innehållet till en tillfällig katalog. 
    >[!NOTE]  
    > Resursprovidern har en minsta motsvarande Azure-Stack skapa. Se till att ladda ned rätt binärfil för versionen av Azure-stacken som körs.

    | Azure Stack-versionen | MySQL RP-version| 
    | --- | --- | 
    | Version 1804 (1.0.180513.1)|[MySQL RP version 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) | 
    | Version 1802 (1.0.180302.1) | [MySQL RP version 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) | 
    | Version 1712 (1.0.180102.3 eller 1.0.180106.1 (integrerad system)) | [MySQL RP version 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) | 

4.  För ASDK skapas ett självsignerat certifikat som en del av den här processen. Du måste ange ett lämpligt certifikat för integrerade system. Om du måste ange ditt eget certifikat, placera en .pfx-fil i den **DependencyFilesLocalPath** som uppfyller följande kriterier: 
    - Antingen ett jokerteckencertifikat för \*.dbadapter.\< region\>.\< externa fqdn\> eller en enda platscertifikat med ett eget namn för mysqladapter.dbadapter.\< region\>.\< externa fqdn\>. 
    - Det här certifikatet måste vara betrodd. Det vill säga måste förtroendekedja för finnas utan mellanliggande certifikat. 
    - En enda certifikatfil finns i DependencyFilesLocalPath. 
    - Filnamnet får inte innehålla några specialtecken eller blanksteg. 

5. Öppna en **nya** utökade (administratör) PowerShell-konsolen. Ändra till katalogen där du extraherade filerna. Använd ett nytt fönster för att undvika problem som kan uppstå från felaktig PowerShell-moduler som har redan lästs in i systemet. 

6. Kör den **DeployMySqlProvider.ps1** skript. Skriptet utför de här stegen: 
    - Hämtar MySQL connector binärfilen (Detta kan ges offline). 
    - Överför certifikat och andra artefakter till ett lagringskonto på Azure-stacken. 
    - Publicerar gallery-paket så att du kan distribuera SQL-databaser via galleriet. 
    - Publicerar ett gallery-paket för distribution av värdservrar. 
    - Distribuerar en virtuell dator med hjälp av en stacken för Windows Server 2016 Azure marketplace-avbildning och installerar resursprovidern. 
    - Registrerar en lokal DNS-post som mappar till dina VM-resursprovidern. 
    - Registrerar din resursprovidern med lokala Azure Resource Manager (klient och admin). 

    Du kan ange de obligatoriska parametrarna på kommandoraden eller köra skriptet utan några parametrar och ange dem när du uppmanas. 

    Här är ett exempel som du kan köra från PowerShell-Kommandotolken. Glöm inte att ändra kontoinformation och lösenord som behövs: 

    ```powershell 
    # Install the AzureRM.Bootstrapper module and set the profile. 
    Install-Module -Name AzureRm.BootStrapper -Force 
    Use-AzureRmProfile -Profile 2017-03-09-profile 

    # Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
    $domain = "AzureStack"  

    # For integrated systems, use the IP address of one of the ERCS virtual machines 
    $privilegedEndpoint = "AzS-ERCS01" 

    # Point to the directory where the resource provider installation files were extracted. 
    $tempDir = 'C:\TEMP\MYSQLRP' 

    # The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
    $serviceAdmin = "admin@mydomain.onmicrosoft.com" 
    $AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 

    # Set the credentials for the new resource provider VM local administrator account 
    $vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass) 

    # And the cloudadmin credential required for privileged endpoint access. 
    $CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

    # Change the following as appropriate. 
    $PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 

    # Run the installation script from the folder where you extracted the installation files. 
    # Find the ERCS01 IP address first, and make sure the certificate 
    # file is in the specified directory. 
    $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds ` 
        -VMLocalCredential $vmLocalAdminCreds ` 
        -CloudAdminCredential $cloudAdminCreds ` 
        -PrivilegedEndpoint $privilegedEndpoint ` 
        -DefaultSSLCertificatePassword $PfxPass ` 
        -DependencyFilesLocalPath $tempDir\cert ` 
        -AcceptLicense 
    ``` 

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 parametrar 
Du kan ange dessa parametrar på kommandoraden. Om du inte, eller om någon parameter valideringen misslyckas, uppmanas du att ange de obligatoriska parametrarna. 

| Parameternamn | Beskrivning | Kommentar eller standardvärde | 
| --- | --- | --- | 
| **CloudAdminCredential** | Autentiseringsuppgifter för molnadministratören behövs för att komma åt den privilegierade slutpunkten. | _Krävs_ | 
| **AzCredential** | Autentiseringsuppgifterna för kontot Azure Stack-administratör. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure-stacken. | _Krävs_ | 
| **VMLocalCredential** | Autentiseringsuppgifterna för det lokala administratörskontot för resursprovidern MySQL VM. | _Krävs_ | 
| **PrivilegedEndpoint** | IP-adressen eller DNS-namnet på den privilegierade slutpunkten. |  _Krävs_ | 
| **DependencyFilesLocalPath** | Sökvägen till en lokal resurs som innehåller [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Om du anger ett av dessa sökvägar måste certifikatfilen placeras i den här katalogen samt. | _Valfria_ (_obligatoriska_ för flera noder) | 
| **DefaultSSLCertificatePassword** | Lösenordet för PFX-certifikatet. | _Krävs_ | 
| **MaxRetryCount** | Antal gånger som du vill försöka utföra varje åtgärd om det uppstår ett fel.| 2 | 
| **RetryDuration** | Timeoutintervall mellan försök i sekunder. | 120 | 
| **Avinstallera** | Tar bort resursprovidern och alla associerade resurser (se nedan). | Nej | 
| **DebugMode** | Förhindrar automatisk rensning vid fel. | Nej | 
| **AcceptLicense** | Hoppar över frågan om du vill acceptera licensvillkoren GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

> [!NOTE] 
> SKU: er kan ta upp till en timme att vara synliga i portalen. Du kan inte skapa en databas förrän SKU: N har skapats. 

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Kontrollera distributionen med hjälp av Azure Stack-portalen 
> [!NOTE] 
>  När installationen är klar måste du uppdatera portalen om du vill se admin-bladet. 

1. Logga in på administrationsportal som tjänstadministratör. 
2. Kontrollera att distributionen har slutförts. Gå till **resursgrupper**, och välj sedan den **system.\< plats\>.mysqladapter** resursgruppen. Kontrollera att alla fyra distributioner lyckades:

      ![Kontrollera distributionen av MySQL RP](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png) 


## <a name="next-steps"></a>Nästa steg
[Lägg till värdservrar](azure-stack-mysql-resource-provider-hosting-servers.md)
