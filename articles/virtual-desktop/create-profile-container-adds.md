---
title: Skapa FSLogix-profilbehållare Azure Files Active Directory Domain Services - Azure
description: I den här artikeln beskrivs hur du skapar en FSLogix-profilbehållare med Azure Files och Azure Active Directory Domain Services.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dd01b950435fadb96a961b6bb1c6b28ff436907a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265777"
---
# <a name="create-an-fslogix-profile-container-with-azure-files"></a>Skapa en FSLogix-profilbehållare med Azure Files

Den här artikeln visar hur du skapar en FSLogix-profilbehållare med Azure Files och Azure Active Directory Domain Services (AD DS).

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du redan har konfigurerat en Azure AD DS-instans. Om du inte har en ännu följer du instruktionerna i [Skapa en grundläggande hanterad domän](../active-directory-domain-services/tutorial-create-instance.md) först och returnerar sedan hit.

## <a name="add-azure-ad-ds-admins"></a>Lägga till Azure AD DS-administratörer

Om du vill lägga till ytterligare administratörer skapar du en ny användare och ger dem behörigheter.

Så här lägger du till en administratör:

1. Välj **Azure Active Directory** i sidofältet och välj sedan Alla **användare**och välj sedan Ny **användare**.

2.  Ange användarinformationen i fälten.

3. Välj **Grupper**i fönstret Azure Active Directory till vänster på skärmen .

4. Välj gruppen **AAD DC-administratörer.**

5. Välj **Medlemmar**i den vänstra rutan och välj sedan **Lägg till medlemmar** i huvudfönstret. Detta visar en lista över alla användare som är tillgängliga i Azure AD. Välj namnet på den användarprofil som du just skapade.

## <a name="set-up-an-azure-storage-account"></a>Konfigurera ett Azure Storage-konto

Nu är det dags att aktivera Azure AD DS-autentisering via Server Message Block (SMB). 

Så här aktiverar du autentisering:

1. Om du inte redan har gjort det konfigurerar och distribuerar du ett allmänt v2-Azure Storage-konto genom att följa instruktionerna i [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md).

2. När du är klar med att konfigurera kontot väljer du **Gå till resurs**.

3. Välj **Konfiguration** i fönstret till vänster på skärmen och aktivera sedan **Azure Active Directory-autentisering för Azure-filer** i huvudfönstret. När du är klar väljer du **Spara**.

4. Välj **Översikt** i fönstret till vänster på skärmen och välj sedan **Filer** i huvudfönstret.

5. Välj **Filresurs** och ange **namn** och **kvot** i fälten som visas till höger på skärmen.

## <a name="assign-access-permissions-to-an-identity"></a>Tilldela åtkomstbehörigheter till en identitet

Andra användare behöver åtkomstbehörighet för att komma åt filresursen. För att göra detta måste du tilldela varje användare en roll med rätt åtkomstbehörigheter.

Så här tilldelar du användare åtkomstbehörigheter:

1. Öppna filresursen som du skapade i [Konfigurera ett Azure Storage-konto](#set-up-an-azure-storage-account)från Azure-portalen .

2. Välj **Åtkomstkontroll (IAM)**.

3. Välj **Lägg till en rolltilldelning**.

4. På fliken **Lägg till rolltilldelning** väljer du lämplig inbyggd roll i rolllistan. Du måste åtminstone välja **Storage File Data SMB Share Contributor** för kontot för att få rätt behörighet.

5. För **Tilldela åtkomst till**väljer du Azure Active **Directory-användare, grupp eller tjänsthuvudnamn**.

6. Välj ett namn eller en e-postadress för målet Azure Active Directory-identitet.

7. Välj **Spara**.

## <a name="get-the-storage-account-access-key"></a>Hämta åtkomstnyckeln för lagringskonto

Därefter måste du hämta åtkomstnyckeln för ditt lagringskonto.

Så här hämtar du åtkomstnyckeln för lagringskonto:

1. Välj **Lagringskonton**i sidofältet i Azure-portalen .

2. I listan över lagringskonton väljer du det konto som du har aktiverat Azure AD DS för och skapade de anpassade rollerna i stegen ovan.

3. Under **Inställningar**väljer du **Snabbtangenter** och kopierar nyckeln från **tangent1**.

4. Gå till fliken **Virtuella datorer** och leta reda på alla virtuella datorer som kommer att bli en del av din värdpool.

5. Välj namnet på den virtuella datorn (VM) under **Virtuella datorer (adVM)** och välj **Anslut**

    Detta hämtar en RDP-fil som låter dig logga in på den virtuella datorn med sina egna autentiseringsuppgifter.

    ![En skärmbild av fliken RDP i fönstret Anslut till virtuell dator.](media/rdp-tab.png)

6. När du har loggat in på den virtuella datorn kör du en kommandotolk som administratör.

7. Kör följande kommando:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - Ersätt `<desired-drive-letter>` med en enhetsbeteckning som `y:`du väljer (till exempel ).
    - Ersätt alla instanser med `<storage-account-name>` namnet på det lagringskonto som du angav tidigare.
    - Ersätt `<share-name>` med namnet på den resurs som du skapade tidigare.
    - Ersätt `<storage-account-key>` med lagringskontonyckeln från Azure.

    Ett exempel:  
  
     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Kör följande kommando för att ge användaren fullständig åtkomst till Azure Files-resursen.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(f)
     ```

    - Ersätt `<mounted-drive-letter>` med bokstaven på den enhet som du vill att användaren ska använda.
    - Ersätt `<user-email>` med UPN för den användare som ska använda den här profilen för att komma åt start av virtuella datorer för sessionsvärden.

    Ett exempel:
     
     ```cmd
     icacls y: /grant john.doe@contoso.com:(f)
     ```

## <a name="create-a-profile-container"></a>Skapa en profilcontainer

Nu när dina profiler är klara ska du skapa en FSLogix-profilbehållare.

Så här konfigurerar du en FSLogix-profilbehållare:

1. Logga in på den virtuella datorn för sessionsvärd som du konfigurerade i början av den här artikeln [och hämta och installera FSLogix-agenten](/fslogix/install-ht/).

2. Packa upp FSLogix-agentfilen som du hämtade och gå till **x64** > **Releases**och öppna sedan **FSLogixAppsSetup.exe**.

3. När installationsprogrammet har lanserats väljer **du Jag godkänner licensvillkoren.** Om tillämpligt, ange en ny nyckel.

4. Välj **Installera**.

5. Öppna **Enhet C**och gå sedan till Program **Files** > **FSLogix** > **Apps** för att se till att FSLogix-agenten var korrekt installerad.

     >[!NOTE]
     > Om det finns flera virtuella datorer i värdpoolen måste du upprepa steg 1 till 5 för varje virtuell dator.

6. Kör **Registereditorn** (RegEdit) som administratör.

7. Navigera till **dator** > **HKEY_LOCAL_MACHINE** > **programvara** > **FSLogix**, högerklicka på **FSLogix**, välj **Ny**och välj sedan **Nyckel**.

8. Skapa en ny nyckel med namnet **Profiler**.

9.  Högerklicka på **Profiler,** välj **Nytt**och välj sedan **DWORD (32-bitars) värde.** Namnge värdet **Aktiverat** och ange **datavärdet** till **1**.

    ![En skärmbild av profilnyckeln. Den REG_DWORD filen är markerad och dess datavärde är inställt på 1.](media/dword-value.png)

10. Högerklicka på **Profiler,** välj **Nytt**och välj sedan **Multi-String Value**. Namnge värdet **VHDLocations** och ange URI för `\\fsprofile.file.core.windows.net\share` Azure Files-resursen som datavärde.

    ![En skärmbild av profilnyckeln som visar VHDLocations-filen. Dess datavärde visar URI för Azure Files-resursen.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Tilldela användare till en sessionsvärd

Nu måste du tilldela användare till din sessionsvärd.

Så här tilldelar du användare:

1. Kör Windows PowerShell som administratör och kör sedan följande cmdlet för att logga in på Windows Virtual Desktop med PowerShell:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   När du uppmanas att ange autentiseringsuppgifter anger du samma användare som har beviljats rollen Klientcreator, RDS Owner eller RDS Contributor på windows virtual desktop-klienten.

2. Kör följande cmdlets för att tilldela användaren till fjärrskrivbordsgruppen:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Precis som de tidigare cmdlets, `<wvd-pool>`se `<user-principal>` till att ersätta `<your-wvd-tenant>`, och med relevanta värden.

    Ett exempel:

     ```powershell
     $pool1 = "contoso"
     
     $tenant = "contoso"
     
     $appgroup = "Desktop Application Group"
     
     $user1 = "jane.doe@contoso.com"
     
     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Kontrollera att din profil fungerar

Nu behöver du bara se till att profilen du skapade finns och fungerar som avsett.

Så här verifierar du din profil:

1. Öppna en webbläsare och gå till [webbklienten för Windows Virtual Desktop](https://rdweb.wvd.microsoft.com/webclient/index.html).

2. Logga in med användarkontot som tilldelats gruppen Fjärrskrivbord.

3. När användarsessionen har upprättats öppnar du Azure-portalen och loggar in med ett administrativt konto.

4. Välj **Lagringskonton**i sidofältet .

5. Välj det lagringskonto som du har konfigurerat som filresurs för din sessionsvärdpool och aktiverat med Azure AD DS.

6. Välj ikonen **Filer** och expandera sedan resursen.

    Om allt är korrekt inställt bör du se en **katalog** med ett namn `<user SID>-<username>`som är formaterat så här: .

## <a name="next-steps"></a>Nästa steg

Om du letar efter alternativa sätt att skapa FSLogix profilbehållare kan du läsa följande artiklar:

- [Skapa en profilbehållare för en värdpool med hjälp av en filresurs](create-host-pools-user-profile.md).
- [Skapa en FSLogix-profilbehållare för en värdpool med Azure NetApp-filer](create-fslogix-profile-container.md)

Du hittar mer detaljerad information om begrepp relaterade till FSlogix-behållare för Azure-filer i [FSLogix-profilbehållare och Azure-filer](fslogix-containers-azure-files.md).
