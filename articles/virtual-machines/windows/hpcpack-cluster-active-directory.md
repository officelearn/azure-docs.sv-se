---
title: HPC Pack kluster med Azure Active Directory | Microsoft Docs
description: "Lär dig hur du integrerar ett HPC Pack 2016-kluster i Azure med Azure Active Directory"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/14/2016
ms.author: danlep
ms.openlocfilehash: c5a06a9c810349b1bcce01c7f73563941a5af0ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Hantera ett HPC Pack kluster i Azure med hjälp av Azure Active Directory
[Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) stöder integration med [Azure Active Directory](../../active-directory/index.md) (Azure AD) för administratörer som distribuerar ett HPC Pack kluster i Azure.



Följ stegen i den här artikeln för följande uppgifter för hög nivå: 
* Integrera manuellt HPC Pack klustret med Azure AD-klient
* Hantera och schemalägga jobb i HPC Pack klustret i Azure 

En klusterlösning HPC Pack integrera med Azure AD följer standard steg om du vill integrera andra program och tjänster. Den här artikeln förutsätter att du är bekant med grundläggande användarhantering i Azure AD. Mer information och bakgrund, finns det [dokumentationen för Azure Active Directory](../../active-directory/index.md) och följande avsnitt.

## <a name="benefits-of-integration"></a>Fördelar med integrering


Azure Active Directory (AD Azure) är en flera innehavare molnbaserade katalog och identity management-tjänst som tillhandahåller enkel inloggning (SSO) åtkomst till molnlösningar.

Integrering av ett HPC Pack kluster med Azure AD kan hjälpa dig att uppnå följande mål:

* Ta bort den traditionella Active Directory-domänkontrollanten från HPC Pack-kluster. Det kan minska kostnaderna för att underhålla klustret om det inte är nödvändiga för ditt företag och påskynda distributionen.
* Använda följande fördelar som ansluts av Azure AD:
    *   Enkel inloggning 
    *   Med hjälp av en lokal AD-identitet för HPC Pack klustret i Azure 

    ![Azure Active Directory-miljö](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>Krav
* **HPC Pack 2016-kluster som distribueras på Azure-datorer** – anvisningar finns i [distribuera ett HPC Pack 2016-kluster i Azure](hpcpack-2016-cluster.md). Du behöver DNS-namnet på huvudnoden och autentiseringsuppgifterna för en Klusteradministratör för att slutföra stegen i den här artikeln.

  > [!NOTE]
  > Azure Active Directory-integrering stöds inte i versioner av HPC Pack före HPC Pack 2016.



* **Klientdatorn** -du behöver en klientdator för Windows eller Windows Server att köra HPC Pack klientverktyg. Om du endast vill använda HPC Pack webbportal eller REST API för att skicka jobb kan du använda alla klientdatorer som du väljer.

* **HPC Pack klientverktyg** -installera klientverktyg HPC Pack på klientdatorn, med tillgängliga lediga installationspaketet från Microsoft Download Center.


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>Steg 1: Registrera server för HPC-kluster med Azure AD-klient
1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Klicka på **Active Directory** i den vänstra menyn och klicka sedan på den önskade katalogen i din prenumeration. Du måste ha behörighet att komma åt resurser i katalogen.
3. Klicka på **användare**, och kontrollera att det finns användarkonton redan skapats eller konfigurerade.
4. Klicka på **program** > **Lägg till**, och klicka sedan på **Lägg till ett program som min organisation utvecklar**. Ange följande information i guiden:
    * **Namnet** -HPCPackClusterServer
    * **Typen** – Välj **webb-program och/eller webb-API**
    * **URL: en inloggning**-bas-URL för det här exemplet som är som standard`https://hpcserver`
    * **App-ID URI** - `https://<Directory_name>/<application_name>`. Ersätt `<Directory_name`> med det fullständiga namnet på din Azure AD-klient, till exempel `hpclocal.onmicrosoft.com`, och Ersätt `<application_name>` med det namn du valde tidigare.

5. När appen har lagts till, klickar du på **konfigurera**. Konfigurera följande egenskaper:
    * Välj **Ja** för **program är flera innehavare**
    * Välj **Ja** för **Användartilldelning som krävs för att komma åt appen**.

6. Klicka på **Spara**. När du sparar är klar klickar du på **hantera Manifest**. Den här åtgärden hämtar programmets JavaScript object notation (JSON) manifestfilen. Redigera hämtade manifestet genom att leta upp den `appRoles` inställning och lägga till programrollen för följande:
    ```json
    "appRoles": [
        {
        "allowedMemberTypes": [
            "User",
            "Application"
        ],
        "displayName": "HpcAdminMirror",
        "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
        "isEnabled": true,
        "description": "HpcAdminMirror",
        "value": "HpcAdminMirror"
        },
        {
        "allowedMemberTypes": [
            "User",
            "Application"
        ],
        "description": "HpcUsers",
        "displayName": "HpcUsers",
        "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
        "isEnabled": true,
        "value": "HpcUsers"
        }
    ],
    ```
7. Spara filen. Klicka sedan på portalen, **hantera Manifest** > **överför Manifest**. Du kan sedan överföra redigerade manifestet.
8. Klicka på **användare**, Välj en användare och klicka sedan på **tilldela**. Tilldela någon av de tillgängliga rollerna (HpcUsers eller HpcAdminMirror) för användaren. Upprepa detta steg med ytterligare användare i katalogen. Bakgrundsinformation om klustret användare finns [hantera klustret användare](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx).

   > [!NOTE] 
   > För att hantera användare, bör du använda Azure Active Directory preview bladet i den [Azure-portalen](https://portal.azure.com).
   >


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>Steg 2: Registrera klienten för HPC-kluster med Azure AD-klient

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Klicka på **Active Directory** i den vänstra menyn och klicka sedan på den önskade katalogen i din prenumeration. Du måste ha behörighet att komma åt resurser i katalogen.
3. Klicka på **program** > **Lägg till**, och klicka sedan på **Lägg till ett program som min organisation utvecklar**. Ange följande information i guiden:

    * **Namnet** -HPCPackClusterClient
    * **Typen** – Välj **Native Client-program**
    * **Omdirigerings-URI** - `http://hpcclient`

4. När appen har lagts till, klickar du på **konfigurera**. Kopiera den **klient-ID** värde och spara den. Du behöver det senare när du konfigurerar ditt program.

5. I **behörigheter för andra program**, klickar du på **Lägg till program**. Söka och lägga till programmet HpcPackClusterServer (som du skapade i steg 1).

6. I den **delegerade behörigheter** listrutan **åtkomst HpcClusterServer**. Klicka sedan på **Spara**.


## <a name="step-3-configure-the-hpc-cluster"></a>Steg 3: Konfigurera HPC-kluster

1. Ansluta till HPC Pack 2016 huvudnod i Azure.

2. Starta HPC PowerShell.

3. Kör följande kommando:

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    där

    * `AADTenant`anger klientnamn Azure AD som`hpclocal.onmicrosoft.com`
    * `AADClientAppId`Anger klient-ID för appen skapade i steg 2.

4. Starta om tjänsten HpcSchedulerStateful.

    Du kan köra följande PowerShell-kommandon på huvudnoden växla den primära repliken för tjänsten HpcSchedulerStateful i ett kluster med flera huvudnoderna:

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName “fabric:/HpcApplication/SchedulerStatefulService”

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>Steg 4: Hantera och skicka jobb från klienten

Hämta HPC Pack 2016-installationsfiler (fullständig installation) från Microsoft Download Center om du vill installera klientverktyg HPC Pack på datorn. När du påbörjar installationen, välja installationsprogrammet för den **HPC Pack klientverktyg**.

För att förbereda klientdatorn, installerar du det certifikat som används under [konfiguration av HPC](hpcpack-2016-cluster.md) på klientdatorn. Använd standard Windows certifikat management procedurer för att installera offentliga certifikatet till den **certifikat – aktuell användare** > **betrodda rotcertifikatutfärdare** lagras. 

Du kan nu köra kommandona HPC Pack eller använda HPC Pack jobbhanteraren GUI för att skicka och hantera kluster-jobb med hjälp av Azure AD-kontot. Skicka alternativ, se [HPC skicka jobb till ett HPC Pack kluster i Azure](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster).

> [!NOTE]
> När du försöker ansluta till HPC Pack-kluster i Azure för första gången visas ett popup-fönster. Ange dina autentiseringsuppgifter för Azure AD att logga in. Token cachelagras sedan. Senare anslutningar till klustret i Azure använda cachelagrade token om autentisering ändringar eller det cachelagrade är avmarkerad.
>
  
Till exempel när du har slutfört föregående steg kan du fråga efter jobb från en lokal klient på följande sätt:

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Användbar cmdlets för jobbet med Azure AD-integrering 

### <a name="manage-the-local-token-cache"></a>Hantera lokala token-cache

HPC Pack 2016 innehåller två nya HPC PowerShell-cmdlets för att hantera den lokala cachen för token. Dessa cmdlet: ar är användbara för att skicka jobb icke-interaktivt. Se följande exempel:

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Ange autentiseringsuppgifter för att skicka jobb med hjälp av Azure AD-kontot 

Ibland kanske du vill köra jobbet under användare för HPC-kluster (för en domänansluten HPC-kluster, kör som en domänanvändare; för ett icke-domänanslutna HPC-kluster körs som en lokal användare på huvudnoden).

1. Använd följande kommandon för att ange autentiseringsuppgifter för:

    ```powershell
    $localUser = “<username>”

    $localUserPassword=”<password>”

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. Sedan skicka jobbet. Projektaktiviteten körs under $localUser på compute-noder.

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   Om `–Credential` inte anges med `Submit-HpcJob`, av jobbet körs under en lokal användare som är mappade som Azure AD-kontot. (HPC-kluster skapas en lokal användare med samma namn som Azure AD-kontot för att köra uppgiften.)
    
3. Ange utökade data för Azure AD-kontot. Detta är användbart när du kör ett MPI-jobb på Linux-noder som använder Azure AD-kontot.

   * Ange utökade data för det Azure AD-kontot

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * Ange utökade data och köra som användaren för HPC-kluster
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```

