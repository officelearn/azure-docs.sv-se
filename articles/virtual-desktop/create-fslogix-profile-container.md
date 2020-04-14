---
title: FSLogix profil behållare NetApp Windows Virtual Desktop - Azure
description: Så här skapar du en FSLogix-profilbehållare med Azure NetApp-filer i Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 149fb5818ee360c7333997655ea9eb8d7ded346c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270901"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>Skapa en FSLogix-profilbehållare för en värdpool med Azure NetApp-filer

Vi rekommenderar att du använder FSLogix profilbehållare som en användarprofillösning för [Windows Virtual Desktop-tjänsten](overview.md). FSLogix profilbehållare lagrar en komplett användarprofil i en enda behållare och är utformade för att flytta profiler i icke-beständiga fjärrdatormiljöer som Windows Virtual Desktop. När du loggar in kopplas behållaren dynamiskt till datormiljön med hjälp av en lokalt stödd virtuell hårddisk (VHD) och Hyper-V virtuell hårddisk (VHDX). Dessa avancerade filterdrivrutinstekniker gör att användarprofilen omedelbart är tillgänglig och visas i systemet precis som en lokal användarprofil. Mer information om FSLogix-profilbehållare finns i [FSLogix-profilbehållare och Azure-filer](fslogix-containers-azure-files.md).

Du kan skapa FSLogix-profilbehållare med [Azure NetApp](https://azure.microsoft.com/services/netapp/)Files , en lättanvänd Azure-inbyggd plattformstjänst som hjälper kunder att snabbt och tillförlitligt etablera SMB-volymer i företagsklass för sina Windows Virtual Desktop-miljöer. Mer information om Azure NetApp-filer finns i [Vad är Azure NetApp-filer?](../azure-netapp-files/azure-netapp-files-introduction.md)

Den här guiden visar hur du konfigurerar ett Azure NetApp Files-konto och skapar FSLogix-profilbehållare i Windows Virtual Desktop.

Den här artikeln förutsätter att du redan har [värdpooler](create-host-pools-azure-marketplace.md) konfigurerade och grupperade i en eller flera klienter i windows virtual desktop-miljö. Mer information om hur du konfigurerar klienter finns [i Skapa en klient i Windows Virtual Desktop](tenant-setup-azure-active-directory.md) och [blogginlägget för Tech Community.](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)

Instruktionerna i den här guiden är specifikt för Windows Virtual Desktop-användare. Om du letar efter mer allmän vägledning för hur du konfigurerar Azure NetApp-filer och skapar FSLogix-profilbehållare utanför Windows Virtual Desktop läser du [Azure NetApp-filerna konfigurera Azure NetApp-filer och skapar en snabbstart för NFS-volymen](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).

>[!NOTE]
>Den här artikeln innehåller inte metodtips för att skydda åtkomsten till Azure NetApp-filresursen.

>[!NOTE]
>Om du letar efter jämförelsematerial om de olika lagringsalternativen för FSLogix-profilbehållare på Azure läser du [Lagringsalternativ för FSLogix-profilbehållare](store-fslogix-profile.md).

## <a name="prerequisites"></a>Krav

Innan du kan skapa en FSLogix-profilbehållare för en värdpool måste du:

- Konfigurera och konfigurera Windows Virtual Desktop
- Etablera en värdpool för Virtuellt skrivbord i Windows
- [Aktivera din Azure NetApp Files-prenumeration](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Konfigurera ditt Azure NetApp-filer-konto

För att komma igång måste du konfigurera ett Azure NetApp Files-konto.

1. Logga in på [Azure-portalen](https://portal.azure.com). Kontrollera att ditt konto har deltagar- eller administratörsbehörighet.

2. Välj **Azure Cloud Shell-ikonen** till höger om sökfältet för att öppna Azure Cloud Shell.

3. När Azure Cloud Shell är öppet väljer du **PowerShell**.

4. Om det här är första gången du använder Azure Cloud Shell skapar du ett lagringskonto i samma prenumeration som du behåller dina Azure NetApp-filer och Windows Virtual Desktop.

   ![Fönstret lagringskonto med knappen Skapa lagring längst ned i fönstret markerat i rött.](media/create-storage-button.png)

5. Kör följande två cmdlets när Azure Cloud Shell läses in.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. Välj **Alla tjänster**till vänster i fönstret . Ange **Azure NetApp-filer** i sökrutan som visas högst upp på menyn.

   ![En skärmbild av en användare som anger "Azure NetApp Files" i sökrutan Alla tjänster. Sökresultaten visar Azure NetApp Files-resursen.](media/azure-netapp-files-search-box.png)


7. Välj **Azure NetApp-filer** i sökresultaten och välj sedan **Skapa**.

8. Välj knappen **Lägg till**.
9. När fliken **Nytt NetApp-konto** öppnas anger du följande värden:

    - För **Namn**anger du ditt NetApp-kontonamn.
    - För **Prenumeration**väljer du prenumerationen för det lagringskonto som du konfigurerade i steg 4 på den nedrullningsbara menyn.
    - För **resursgrupp**väljer du antingen en befintlig resursgrupp på den nedrullningsbara menyn eller skapar en ny genom att välja **Skapa ny**.
    - För **Plats**väljer du regionen för ditt NetApp-konto på rullgardinsmenyn. Den här regionen måste vara samma region som dina virtuella datorer för sessionen.

   >[!NOTE]
   >Azure NetApp-filer stöder för närvarande inte montering av en volym mellan regioner.

10. När du är klar väljer du **Skapa** för att skapa ditt NetApp-konto.

## <a name="create-a-capacity-pool"></a>Skapa en kapacitetspool

Skapa sedan en ny kapacitetspool: 

1. Gå till Azure NetApp Files-menyn och välj ditt nya konto.
2. På kontomenyn väljer du **Kapacitetspooler** under Lagringstjänst.
3. Välj **Lägg till pool**.
4. När fliken **Ny kapacitetspool** öppnas anger du följande värden:

    - För **Namn**anger du ett namn för den nya kapacitetspoolen.
    - För **servicenivå**väljer du önskat värde på den nedrullningsbara menyn. Vi rekommenderar **Premium** för de flesta miljöer.
       >[!NOTE]
       >Premium-inställningen ger det minsta dataflödet som är tillgängligt för en Premium Service-nivå, vilket är 256 MBps. Du kan behöva justera det här dataflödet för en produktionsmiljö. Det slutliga dataflödet baseras på relationen som beskrivs i [dataflödesgränser](../azure-netapp-files/azure-netapp-files-service-levels.md).
    - För **Storlek (TiB)** anger du den kapacitetspoolstorlek som bäst passar dina behov. Den minsta storleken är 4 TiB.

5. Välj **OK** när du är klar.

## <a name="join-an-active-directory-connection"></a>Ansluta till en Active Directory-anslutning

Därefter måste du ansluta till en Active Directory-anslutning.

1. Välj **Active Directory-anslutningar** på menyn till vänster på sidan och välj sedan knappen **Anslut** för att öppna sidan Anslut till **Active Directory.**

   ![En skärmbild av menyn Anslut active directory-anslutningar.](media/active-directory-connections-menu.png)

2. Ange följande värden på sidan **Anslut active directory** för att ansluta till en anslutning:

    - För **Primär DNS**anger du IP-adressen för DNS-servern i din miljö som kan matcha domännamnet.
    - För **Domän**anger du ditt fullständiga domännamn (FQDN).
    - För **SMB Server-prefix (Datorkonto)** anger du den sträng som du vill lägga till i datorkontonamnet.
    - För **Användarnamn**anger du namnet på kontot med behörighet att utföra domänanslutning.
    - För **Lösenord**anger du kontots lösenord.

## <a name="create-a-new-volume"></a>Skapa en ny volym

Därefter måste du skapa en ny volym.

1. Välj **Volymer**och välj sedan **Lägg till volym**.

2. När fliken **Skapa en volym** öppnas anger du följande värden:

    - För **Volymnamn**anger du ett namn på den nya volymen.
    - För **kapacitetspool**väljer du den kapacitetspool som du just skapade på den nedrullningsbara menyn.
    - För **Kvot (GiB)** anger du den volymstorlek som är lämplig för din miljö.
    - För **virtuellt nätverk**väljer du ett befintligt virtuellt nätverk som har anslutning till domänkontrollanten på den nedrullningsbara menyn.
    - Under **Undernät**väljer du **Skapa nytt**. Tänk på att det här undernätet delegeras till Azure NetApp-filer.

3.  Välj **Nästa: \> Protokoll** för att öppna fliken Protokoll och konfigurera parametrarna för volymåtkomst.

## <a name="configure-volume-access-parameters"></a>Konfigurera parametrar för volymåtkomst

När du har skapat volymen konfigurerar du parametrarna för volymåtkomst.

1.  Välj **SMB** som protokolltyp.
2.  Under Konfiguration på den nedrullningsbara menyn **Active Directory** väljer du samma katalog som du ursprungligen anslöt i Anslut till en [Active Directory-anslutning](create-fslogix-profile-container.md#join-an-active-directory-connection). Tänk på att det finns en gräns för en Active Directory per prenumeration.
3.  I textrutan **Dela namn** anger du namnet på den resurs som används av sessionsvärdpoolen och dess användare.

4.  Välj **Granska + skapa** längst ned på sidan. Då öppnas valideringssidan. När volymen har validerats väljer du **Skapa**.

5.  Nu börjar den nya volymen distribueras. När distributionen är klar kan du använda Azure NetApp-filresursen.

6.  Om du vill visa monteringsbanan väljer du **Gå till resurs** och letar efter den på fliken Översikt.

    ![En skärmbild av översiktsskärmen med en röd pil som pekar mot monteringsbanan.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Konfigurera FSLogix på virtuella datorer för sessionsvärdar (VIRTUELLA datorer)

Det här avsnittet baseras på [Skapa en profilbehållare för en värdpool med hjälp av en filresurs](create-host-pools-user-profile.md).

1. [Hämta FSLogix agent .zip-filen](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) medan du fortfarande är fjärrstyrd i den virtuella datorn för sessionsvärd.

2. Packa upp den nedladdade filen.

3. Gå till **x64-versioner** > **Releases** i filen och kör **FSLogixAppsSetup.exe**. Installationsmenyn öppnas.

4.  Om du har en produktnyckel anger du den i textrutan Produktnyckel.

5. Markera kryssrutan **bredvid Jag godkänner licensvillkoren**.

6. Välj **Installera**.

7. Navigera till **\\C:\\Programfiler\\FSLogix Apps** för att bekräfta agenten installerad.

8. På Start-menyn kör du **RegEdit** som administratör.

9. Navigera till **\\dator HKEY_LOCAL_MACHINE\\\\programvara FSLogix**.

10. Skapa en nyckel med namnet **Profiler**.

11.  Skapa ett värde med namnet **Aktiverad** med en **REG_DWORD** typ inställd på ett datavärde **på 1**.

12. Skapa ett värde med namnet **VHDLocations** med en **multisträngstyp** och ange dess datavärde till URI för Azure NetApp-filresursen.

13. Skapa ett värde med namnet **DeleteLocalProfileWhenVHDShouldApply** med ett DWORD-värde på 1 för att undvika problem med befintliga lokala profiler innan du loggar in.

     >[!WARNING]
     >Var försiktig när du skapar värdet DeleteLocalProfileWhenVHDShouldApply. När FSLogix Profiles-systemet bestämmer att en användare ska ha en FSLogix-profil, men det redan finns en lokal profil, tas profilbehållaren bort den lokala profilen permanent. Användaren kommer då att loggas in med den nya FSLogix-profilen.

## <a name="assign-users-to-session-host"></a>Tilldela användare till sessionsvärd

1. Öppna **PowerShell ISE** som administratör och logga in på Windows Virtual Desktop.

2. Kör följande cmdlets:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. När du uppmanas att ange autentiseringsuppgifterna för användaren med rollerna Klientskapare eller RDS Owner/RDS Contributor på Windows Virtual Desktop-klienten.

4. Kör följande cmdlets för att tilldela en användare till en fjärrskrivbordsgrupp:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Se till att användarna kan komma åt Azure NetApp-filresursen

1. Öppna din webbläsare och <https://rdweb.wvd.microsoft.com/webclient/index.html>gå till .

2. Logga in med autentiseringsuppgifterna för en användare som tilldelats gruppen Fjärrskrivbord.

3. När du har etablerat användarsessionen loggar du in på Azure-portalen med ett administrativt konto.

4. Öppna **Azure NetApp-filer,** välj ditt Azure NetApp-filer konto och välj sedan **Volymer**. När menyn Volymer öppnas väljer du motsvarande volym.

   ![En skärmbild av det NetApp-konto som du har konfigurerat tidigare i Azure-portalen med knappen Volymer markerad.](media/netapp-account.png)

5. Gå till fliken **Översikt** och bekräfta att FSLogix-profilbehållaren använder utrymme.

6. Anslut direkt till valfri VM-del av värdpoolen med fjärrskrivbord och öppna **Utforskaren.** Navigera sedan till **monteringsbanan** (i följande exempel \\ \\är monteringsbanan anf-SMB-3863.gt1107.onmicrosoft.com\\anf-VOL).

   I den här mappen bör det finnas en virtuell profil (eller VHDX) som den i följande exempel.

   ![En skärmbild av innehållet i mappen i monteringssökvägen. Inuti finns en enda VHD-fil med namnet "Profile_ssbb".](media/mount-path-folder.png)

## <a name="next-steps"></a>Nästa steg

Du kan använda FSLogix profilbehållare för att ställa in en användarprofilresurs. Mer information om hur du skapar användarprofilresurser med dina nya behållare finns i [Skapa en profilbehållare för en värdpool med hjälp av en filresurs](create-host-pools-user-profile.md).
