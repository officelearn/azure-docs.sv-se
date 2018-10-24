---
title: Självstudiekursen utökar Windows-filservrar med Azure File Sync | Microsoft Docs
description: Lär dig hur du utökar Windows-filservrar med Azure File Sync, från början till slut.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 4c3ceead792f60ceac7c5eddb64dc4644d662f83
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49960674"
---
<!---Customer intent: As a IT Administrator, I want see how to extend Windows file servers with Azure File Sync, so I can evaluate the process for extending storage capacity of my Windows Servers.
--->

# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Självstudie: Utöka Windows-filservrar med Azure File Sync
Du kan använda Azure File Sync för att centralisera din organisations filresurser och att utöka lagringskapaciteten för en lokal filserver. Azure File Sync omvandlar Windows Server till ett snabbt cacheminne för din Azure-filresurs.

I de här självstudierna visar vi de grundläggande stegen för att utöka lagringskapaciteten för en Windows-Server med hjälp av Azure File Sync. Vi använder en Windows Server Azure VM för den här självstudien, men du skulle gör processen för dina lokala servrar. Om du är redo att distribuera Azure File Sync i din egen miljö kan du använda den [distribuera Azure File Sync](storage-sync-files-deployment-guide.md) artikel i stället.

> [!div class="checklist"]
> * Distribuera Storage Sync-tjänsten
> * Förbereda Windows Server som ska användas med Azure File Sync
> * Installera Azure File Sync-agenten
> * Registrera Windows-servern med Storage Sync-tjänsten
> * Skapa en synkroniseringsgrupp och en slutpunkt i molnet
> * Skapa en serverslutpunkt

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på https://portal.azure.com.

## <a name="prepare-your-environment-for-the-tutorial"></a>Förbereda din miljö för självstudien
Det finns några saker som du måste skriva på plats för den här självstudiekursen innan du distribuerar Azure File Sync. Tillsammans med skapar ett Azure Storage-konto och filen dela, skapar en Windows Server 2016 Datacenter-VM och förbereda servern för Azure File Sync.

### <a name="create-a-folder-and-txt-file"></a>Skapa en mapp och .txt-fil

Skapa en ny mapp med namnet på din lokala dator *FilesToSync* och Lägg till en textfil med namnet *mytestdoc.txt*. du överföra filen till filresursen senare i den här självstudien.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Skapa en filresurs
Därefter skapar du en filresurs.

1. När Azure storage-konto-distributionen är klar klickar du på **gå till resurs**.
1. Klicka på **filer** från fönstret storage-konto.

    ![Klicka på filer](./media/storage-sync-files-extend-servers/click-files.png)

1. Klicka på **+ filresurs**.

    ![Klicka på knappen Lägg till filresurs](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Namnge den nya filresursen *afsfileshare* och ange ”1” för den **kvot**, klicka sedan på **skapa**. Kvoten kan vara högst 5 TiB, men du behöver bara 1 GB för den här självstudiekursen.

    ![Ange ett namn och kvot för den nya filresursen](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Välj den nya filresursen och sedan klicka på filresursplatsen **överför**.

    ![Överför en fil](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Bläddra till den *FilesToSync* mapp där du skapade din txt-fil väljer *mytestdoc.txt* och klicka på **överför**.

    ![Bläddra-filresurs](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Nu har du skapat ett Azure Storage-konto och en filresurs med en fil i den i Azure. Nu ska du skapa den virtuella Azure-datorer med Windows Server 2016 Datacenter som representerar den lokala servern i den här självstudien.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Distribuera en virtuell dator och Anslut en datadisk

1. Därefter expanderar du menyn på vänster sida av portalen och välj **skapa en resurs** i det övre vänstra hörnet i Azure Portal.
1. I sökrutan ovanför listan med **Azure Marketplace** resurser, Sök efter och välj **Windows Server 2016 Datacenter**, välj sedan **skapa**.
1. I den **grunderna** fliken, under **projektet information**, Välj den resursgrupp som du skapade för den här kursen.

   ![Ange grundläggande information om de virtuella datorerna på portalens blad](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. Under **instans information**, ange ett namn, till exempel *myVM*.
1. Lämna standardinställningarna för **Region**, **tillgänglighetsalternativ**, **bild**, och **storlek**.
1. Under **administratörskontot**, ange en **användarnamn** och **lösenord** för den virtuella datorn.
1. Under **Regler för inkommande portar** väljer du **Tillåt valda portar** och sedan **RDP (3389)** och **HTTP** från listrutan.

   Innan du skapar den virtuella datorn, måste du skapa en datadisk.

1. Klicka på **nästa: diskar**

   ![Lägga till datadiskar](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

1. I den **diskar** fliken, under **Disk alternativ**, lämna standardvärdena. 
1. Under **DATADISKAR**, klickar du på **skapa och koppla en ny disk**.

1. Lämna standardvärdena, men ändrar den **storlek (GiB)** till **1 GB** för den här självstudiekursen.

   ![Diskinformation för data](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

1. Klicka på **OK**.
1. Klicka på **Granska + skapa**.
1. Klicka på **Skapa**.

   Du kan klicka på den **meddelanden** ikon för att titta på den **förloppsindikator**. Skapa en ny virtuell dator tar några minuter att slutföra.

1. När VM-distributionen är klar klickar du på **gå till resurs**.

   ![Gå till resurs](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

   Du har nu skapat en ny virtuell dator och kopplat en datadisk. Nu måste du ansluta till den virtuella datorn.

### <a name="connect-to-your-vm"></a>Ansluta till den virtuella datorn

1. I Azure-portalen klickar du på **Connect** på egenskapssidan för virtuella datorn.

   ![Ansluta till en virtuell Azure-dator från portalen](./media/storage-sync-files-extend-servers/connect-vm.png)

1. I den **Anslut till den virtuella datorn** behåller standardalternativen för att ansluta med **IP-adress** över port 3389 och klicka på **ladda ned RDP-filen**.

   ![Hämta RDP-filen](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Öppna den hämtade RDP-filen och klicka på **Anslut** när du tillfrågas.
1. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Skriv användarnamnet som *localhost\username*, ange lösenordet som du skapade för den virtuella datorn och klicka sedan på **OK**.

   ![Fler alternativ](./media/storage-sync-files-extend-servers/local-host2.png)

1. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att skapa anslutningen.

### <a name="prepare-the-windows-server"></a>Förbereda Windows-Server
För den **Windows Server 2016 Datacenter** server, inaktivera **Förbättrad säkerhetskonfiguration i Internet Explorer**. Det här steget krävs endast för inledande serverregistrering. Du kan aktivera det igen när servern har registrerats.

I den **Windows Server 2016 Datacenter** VM, **Serverhanteraren** öppnas automatiskt.  Om **Serverhanteraren** inte öppnas som standard söker efter det i Utforskaren.

1. I **Serverhanteraren** klickar du på **lokal Server**.

   ![”Lokal Server” på vänster sida av Server Manager-UI](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. På den **egenskaper** subpane, klicka på länken för **Förbättrad säkerhetskonfiguration**.  

    ![Fönstret ”Förbättrad säkerhetskonfiguration” i Server Manager-UI](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. I den **Förbättrad säkerhetskonfiguration i Internet Explorer** dialogrutan **av** för **administratörer** och **användare**:  

    ![Förbättrad säkerhetskonfiguration i Internet Explorer pop-fönstret med ”av” markerad](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

   Nu kan du lägga till datadisken till den virtuella datorn.

### <a name="add-the-data-disk"></a>Lägga till datadisken

1. När den är fortfarande i den **Windows Server 2016 Datacenter** virtuell dator, klicka på **filer och lagringstjänster** > **volymer** > **diskar** .

    ![Datadisk](media/storage-sync-files-extend-servers/your-disk.png)

1. Högerklicka på 1 GB-disk med namnet **Msft virtuell Disk** och klicka på **ny volym**.
1. Slutför guiden lämna standardvärdena på plats, loggas en tilldelad enhetsbeteckning och klicka på **skapa**.
1. Klicka på **Stäng**.

   Nu har du gav oss disken online och har skapat en volym. Du kan bekräfta att lägga till datadisken lyckades genom att öppna Utforskaren på den virtuella datorn och bekräfta att den nya enheten finns.

1. I Utforskaren på den virtuella datorn expanderar **den här datorn** och dubbelklicka på den nya enheten. Det är F: enheten i det här exemplet.
1. Högerklicka och välj **New** > **mappen**. Mappen namnet *FilesToSync*.
1. Dubbelklicka på den **FilesToSync** mapp.
1. Högerklicka och välj **New** > **textdokument**. Namnge textfilen *MyTestFile*.

    ![Lägg till en ny textfil](media/storage-sync-files-extend-servers/new-file.png)

1. Stäng **Explorer** och **Serverhanteraren**.

### <a name="download-the-azurerm-powershell-module"></a>Ladda ned AzureRM PowerShell-modulen
I den **Windows Server 2016 Datacenter** virtuell dator, installera den **AzureRM PowerShell-modulen** på servern.

1. Öppna en upphöjd PowerShell-kommandotolk på den virtuella datorn
1. Kör följande kommando:

   ```powershell
   Install-Module -Name AzureRM -AllowClobber
   ```

   > [!NOTE]
   > Om du har en version av NuGet som är äldre än 2.8.5.201, uppmanas du att ladda ner och installera den senaste versionen av NuGet.

   Som standard konfigureras inte PowerShell-galleriet som en betrodd lagringsplats för PowerShellGet. Första gången du använder PSGallery visas följande meddelande:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Svara `Yes` eller `Yes to All` för att fortsätta med installationen.

Modulen `AzureRM` är en sammanslagen modul för Azure PowerShell-cmdletar. Installerar den hämtar alla tillgängliga Azure Resource Manager-moduler och gör deras cmdlets som är tillgängliga för användning.

Nu kan du har konfigurerat din miljö för den här självstudien och du är redo att börja distribuera den **Lagringssynkroniseringstjänst**.

## <a name="deploy-the-storage-sync-service"></a>Distribuera Storage Sync-tjänsten 
Distribuera Azure File Sync börjar med att placera en **Lagringssynkroniseringstjänst** resurs i en resursgrupp för din valda prenumeration. Storage Sync-tjänsten ärver behörigheter från prenumerationen och resursgruppen som du distribuerar den till.

1. I Azure-portalen klickar du på **skapa en resurs** och söker sedan efter **Azure File Sync**.
1. I sökresultaten väljer **Azure File Sync**.
1. Välj **skapa** att öppna den **distribuera synkronisering av lagring** fliken.

   ![Distribuera synkronisering av lagring](media/storage-sync-files-extend-servers/afs-info.png)

   I fönstret som öppnas anger du följande information:

   | Värde | Beskrivning |
   | ----- | ----- |
   | **Namn** | Ett unikt namn (per prenumeration) för Storage Sync-tjänsten.<br><br>I den här självstudien använder vi *afssyncservice02*. |
   | **Prenumeration** | Den prenumeration som du använder för den här självstudien. |
   | **Resursgrupp** | Den resursgrupp som du använder för den här självstudien.<br><br>I den här självstudien använder vi *afsresgroup101918*. |
   | **Plats** | Östra USA |

1. När du är klar väljer du **skapa** att distribuera den **Lagringssynkroniseringstjänst**.
1. Klicka på den **meddelanden** fliken > **gå till resurs**.

## <a name="install-the-azure-file-sync-agent"></a>Installera Azure File Sync-agenten
Azure File Sync-agenten är hämtningsbara paket som gör det möjligt för Windows Server till att synkroniseras med en Azure-filresurs.

1. Gå tillbaka till den **Windows Server 2016 Datacenter** virtuell dator och öppna **Internet Explorer**
1. Gå till den [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Rulla ned till den **Azure File Sync-agenten** och klicka **hämta**.

   ![Synkronisera agent-hämtning](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Markera kryssrutan för **StorageSyncAgent_V3_WS2016. EXE** och klicka på **nästa**.

   ![Välj agent](media/storage-sync-files-extend-servers/select-agent.png)

1. **Tillåt en gång** > **kör** > **öppna** filen.
1. Om du inte redan gjort det, stänger du PowerShell-fönstret.
1. Acceptera standardinställningarna i den **Storage Sync-Agentinstallationsguiden**.
1. Klicka på **Installera**.
1. Klicka på **Slutför**.

Du har distribuerat Azure-synkroniseringstjänsten och har installerat agenten på den **Windows Server 2016 Datacenter** VM. Nu måste du registrera den virtuella datorn med den **Lagringssynkroniseringstjänst**.

## <a name="register-windows-server-with-storage-sync-service"></a>Registrera Windows-servern med Storage Sync-tjänsten
Registrera din Windows-Server med en tjänst för Lagringssynkronisering upprättar en förtroenderelation mellan din server (eller kluster) och Storage Sync-tjänsten. En server kan bara registreras för en tjänst för Lagringssynkronisering och kan synkroniseras med andra servrar och Azure-filresurser som är associerade med samma Storage Sync-tjänsten.

Användargränssnittet för servern registrering ska öppnas automatiskt när du har installerat den **Azure File Sync-agenten**. Om inte, du kan öppna den manuellt från dess filplats: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.

1. När Användargränssnittet för servern registrering öppnas i den virtuella datorn, klickar du på **OK**.
1. Klicka på **inloggning** att börja.
1. Logga in med dina Azure-konto och klicka på **inloggning**.
1. Ange följande information:

   ![En skärmbild av Användargränssnittet för servern registrering](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Värde | Beskrivning |
   | **Azure-prenumeration** | Den prenumeration som innehåller Storage Sync-tjänsten för den här självstudiekursen. |
   | **Resursgrupp** | Den resursgrupp som innehåller Storage Sync-tjänsten för den här självstudiekursen. Vi har använt *afsresgroup101918* i den här självstudien. |
   | **Storage Sync-tjänsten** | Namnet på den Lagringssynkroniseringstjänst som du använde för den här självstudien. Vi har använt *afssyncservice02* i den här självstudien. |

1. Klicka på **registrera** att slutföra registreringen för servern.
1. Som en del av registreringen uppmanas du för en ytterligare inloggning. Logga in och klicka på **nästa**.
1. Klicka på **OK**

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Skapa en synkroniseringsgrupp och en slutpunkt i molnet
En synkroniseringsgrupp definierar sync-topologi för en uppsättning filer. En synkroniseringsgrupp måste innehålla en molnslutpunkt som representerar en Azure-filresurs och en eller flera serverslutpunkter. En serverslutpunkt representerar en sökväg på en registrerad server.

1. Skapa en synkroniseringsgrupp i den [Azure-portalen](https://portal.azure.com/)väljer **+ synkroniseringsgruppen** från Storage Sync-tjänsten som du skapade för den här kursen. Vi använde *afssyncservice02* som exempel i den här självstudien.

   ![Skapa en ny synkroniseringsgrupp i Azure portal](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Ange följande information för att skapa en synkroniseringsgrupp med en slutpunkt i fönstret som öppnas:

   | Värde | Beskrivning |
   | ----- | ----- |
   | **Namn på synkroniseringsgrupp** | Det här namnet måste vara unikt inom Storage Sync-tjänsten, men kan vara vilket namn som är logisk för dig. I den här självstudien använder vi *afssyncgroup*.|
   | **Prenumeration** | Den prenumeration där du har distribuerat Storage Sync-tjänsten för den här självstudiekursen. |
   | **Lagringskonto** |Klicka på **Välj lagringskonto**. I fönstret som visas väljer du det lagringskonto som har Azure-filresursen som du skapade för den här kursen. Vi använde *afsstoracct101918*. |
   | **Azure-filresurs** | Namnet på Azure-filresursen som du skapade för den här kursen. Vi använde *afsfileshare*. |

1. Klicka på **Skapa**.

Om du väljer din synkroniseringsgruppen, kan du se att du får nu en **Molnets slutpunkt**.

## <a name="add-a-server-endpoint"></a>Lägg till en serverslutpunkt
En serverslutpunkt representerar en specifik plats på en registrerad server, till exempel en mapp på en server-volym.

1. Om du vill lägga till en serverslutpunkt, Välj den nyligen skapade synkroniseringsgruppen och välj sedan **Lägg till serverslutpunkt**.

   ![Lägg till en ny serverslutpunkt i fönstret sync grupp](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. I den **Lägg till serverslutpunkt** fönstret anger du följande information för att skapa en serverslutpunkt:

   | | |
   | ----- | ----- |
   | Värde | Beskrivning |
   | **Registrerad server** | Namnet på den server som du skapade för den här självstudiekursen. Vi använde *afsvm101918* i den här självstudien ||
   | **Sökväg** | Windows Server-sökvägen till den enhet som du skapade för den här kursen. I vårt exempel är det *f:\filestosync*. |
   | **Lagringsnivåer för moln** | Lämna inaktiverat för den här självstudien. |
   | **Ledigt utrymme på volym** | Lämna tomt för den här självstudiekursen. |

1. Klicka på **Skapa**.

Dina filer nu synkroniseras i Azure-filresurs- och Windows Server.

![Azure Storage har synkroniserats](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Nästa steg
I den här självstudien beskrivs de grundläggande stegen för att utöka lagringskapaciteten för en Windows-Server med hjälp av Azure File Sync. Följ den här länken för en mer omfattande titt på planera för distribution av Azure File Sync.

> [!div class="nextstepaction"]
> [Planera för distribution av Azure File Sync](./storage-sync-files-planning.md)