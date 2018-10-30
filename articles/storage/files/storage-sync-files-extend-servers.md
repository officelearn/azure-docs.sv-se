---
title: 'Självstudie: Utöka Windows-filservrar med Azure File Sync | Microsoft Docs'
description: Lär dig hur du utökar Windows-filservrar med Azure File Sync, från början till slut.
services: storage
author: wmgries
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: cc34411cc27870dbd9c707a34ebf34b96c7253dc
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986125"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Självstudie: Utöka Windows-filservrar med Azure File Sync
I den här självstudien visar vi dig de grundläggande stegen för att utöka lagringskapaciteten för Windows Server med hjälp av Azure File Sync. Vi använder en virtuell Azure-dator på Windows Server i den här kursen men du skulle normalt utföra den här processen för dina lokala servrar. Om du är redo att distribuera Azure File Sync i din egen miljö använder du istället artikeln [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md).

> [!div class="checklist"]
> * Distribuera tjänsten för synkronisering av lagring
> * Förbereda Windows Server för användning med Azure File Sync
> * Installera Azure File Sync-agenten
> * Registrera Windows Server i tjänsten för synkronisering av lagring
> * Skapa en synkroniseringsgrupp och en molnslutpunkt
> * Skapa en serverslutpunkt

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på https://portal.azure.com.

## <a name="prepare-your-environment"></a>Förbered din miljö
Det finns några saker du behöver konfigurera för den här självstudiekursen innan du distribuerar Azure File Sync. Tillsammans med att skapa ett Azure Storage-konto skapar du en virtuell dator för Windows Server 2016 Datacenter och förbereder den servern för Azure File Sync.

### <a name="create-a-folder-and-txt-file"></a>Skapa en mapp och en .txt-fil

På en lokal dator skapar du en mapp med namnet *FilesToSync* och lägger till en textfil med namnet *mytestdoc.txt*. Du laddar upp den filen på filresursen senare i den här kursen.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Skapa en filresurs
Därefter skapar du en filresurs.

1. När distributionen av Azure-lagringskontot är klar klickar du på **Gå till resurs**.
1. Klicka på **Filer** i lagringskontots fönster.

    ![Klicka på Filer](./media/storage-sync-files-extend-servers/click-files.png)

1. Klicka på **+ Filresurs**.

    ![Klicka på knappen för att lägga till filresurs](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Namnge den nya filresursen *afsfileshare* och ange ”1” i fältet **Kvot** och klicka sedan på **Skapa**. Kvoten kan vara högst 5 TiB men du behöver bara 1 GB för den här kursen.

    ![Ange ett namn och en kvot för den nya filresursen](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Välj den nya filresursen och klicka sedan på **Ladda upp** på filresursens plats.

    ![Överför en fil](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Bläddra till mappen *FilesToSync*, där du har skapat .txt-filen, välj *mytestdoc.txt* och klicka på **Ladda upp**.

    ![Bläddra i filresursen](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Nu har du skapat ett Azure Storage-konto och en filresurs med en fil i Azure. Nu ska du skapa den virtuella Azure-datorn i Windows Server 2016 Datacenter som representerar den lokala servern i den här kursen.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Distribuera en virtuell dator och ansluta en datadisk

1. Expandera sedan menyn till vänster om portalen och välj **Skapa en resurs** i det övre vänstra hörnet i Azure-portalen.
1. I sökrutan ovanför listan över resurser i **Azure Marketplace**, söker di efter och väljer **Windows Server 2016 Datacenter** och därefter **Skapa**.
1. På fliken **Grundinställningar**, under **Projektinformation**, väljer du den resursgrupp du har skapat för den här kursen.

   ![Ange grundläggande information om de virtuella datorerna på portalens blad](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. Under **Instansinformation** anger du ett namn på virtuell dator, till exempel *myVM*.
1. Lämna standardinställningar för **Region**, **Tillgänglighetsalternativ**, **Avbildning** och **Storlek**.
1. Under **Administratörskonto**, ange ett **användarnamn** och **lösenord** för den virtuella datorn.
1. Under **Regler för inkommande portar** väljer du **Tillåt valda portar** och sedan **RDP (3389)** och **HTTP** från listrutan.

   Innan du skapar en virtuell dator måste du skapa en datadisk.

1. Klicka på **Nästa:Diskar**

   ![Lägga till datadiskar](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

1. Lämna standardinställningarna på fliken **Diskar**, under **Diskalternativ**.
1. Under **DATADISKAR** klickar du på **Skapa och anslut en ny disk**.

1. Lämna standardinställningarna, förutom **Storlek (GiB)**, som du ändrar till **1 GB** för den här självstudiekursen.

   ![Datadiskinformation](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

1. Klicka på **OK**.
1. Klicka på **Granska + skapa**.
1. Klicka på **Skapa**.

   Du kan klicka på ikonen **Meddelanden** om du vill ser **distributionsförloppet**. Det tar några minuter att skapa en ny virtuell dator.

1. När distributionen av den virtuella datorn är klar klickar du på **Gå till resurs**.

   ![Gå till resurs](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

   Nu har du skapat en ny virtuell dator och anslutit en datadisk. Nu måste du ansluta till den virtuella datorn.

### <a name="connect-to-your-vm"></a>Ansluta till din virtuella dator

1. I Azure-portalen klickar du på **Anslut** på sidan för den virtuella datorns egenskaper.

   ![Ansluta till en virtuell Azure-dator från portalen](./media/storage-sync-files-extend-servers/connect-vm.png)

1. På sidan **Anslut till den virtuella datorn** behåller du standardalternativen för att ansluta med **IP-adress** via port 3389 och klickar på **Hämta RDP-filen**.

   ![Hämta RDP-filen](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Öppna den hämtade RDP-filen och klicka på **Anslut** när du tillfrågas.
1. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange användarnamnet som *localhost\användarnamn* och ange det lösenord som du skapade för den virtuella datorn. Klicka sedan på **OK**.

   ![Fler alternativ](./media/storage-sync-files-extend-servers/local-host2.png)

1. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att skapa anslutningen.

### <a name="prepare-the-windows-server"></a>Förbereda Windows Server
För **Windows Server 2016 Datacenter**-servern inaktiverar du **Förbättrad säkerhetskonfiguration i Internet Explorer**. Det här steget krävs bara för inledande serverregistrering. Du kan aktivera det igen när servern har registrerats.

På den virtuella **Windows Server 2016 Datacenter**-datorn öppnas **Serverhanteraren** automatiskt.  Om **Serverhanteraren** öppnas inte som standard söker du efter den i Utforskaren.

1. I **Serverhanteraren** klickar du på **Lokal server**.

   ![”Lokal server” till vänster i användargränssnittet för Serverhanteraren](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. I underfönstret **Egenskaper** väljer du länken för **Förbättrad säkerhetskonfiguration i Internet Explorer**.  

    ![Fönstret ”Förbättrad säkerhetskonfiguration i Internet Explorer” i användargränssnittet i Serverhanteraren](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. I dialogrutan **Förbättrad säkerhetskonfiguration i Internet Explorer** väljer du **Av** för **Administratörer** och **Användare**.

    ![Popup-fönstret Förbättrad säkerhetskonfiguration i Internet Explorer med ”Av” valt](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

   Nu kan du lägga till datadisken på den virtuella datorn.

### <a name="add-the-data-disk"></a>Lägga till datadisken

1. När du är på den virtuella **Windows Server 2016 Datacenter**-datorn klickar du på **Filer och lagringstjänster** > **Volymer** > **Diskar**.

    ![Datadisk](media/storage-sync-files-extend-servers/your-disk.png)

1. Högerklickar på 1 GB-disken med namnet **Msft Virtual Disk** och klicka på **Ny volym**.
1. Slutför guiden och lämna standardinställningarna, notera den tilldelade enhetsbeteckningen och klicka på **Skapa**.
1. Klicka på **Stäng**.

   Nu har du tagit disken online och skapat en volym. Du kan bekräfta att datadisken har lagts till genom att öppna Utforskaren på den virtuella datorn och bekräfta att den nya enheten finns.

1. I Utforskaren på den virtuella datorn expanderar du **Den här datorn** och dubbelklickar på den nya enheten. I det här exemplet är det F:-enheten.
1. Högerklicka och välj **Nytt** > **Mapp**. Namnge mappen *FilesToSync*.
1. Dubbelklicka på mappen **FilesToSync**.
1. Högerklicka och välj **Nytt** > **Textdokument**. Namnge textfilen *MyTestFile*.

    ![Lägga till en ny textfil](media/storage-sync-files-extend-servers/new-file.png)

1. Stäng **Utforskaren** och **Serverhanteraren**.

### <a name="download-the-azurerm-powershell-module"></a>Ladda ned AzureRM PowerShell-modulen
På den virtuella **Windows Server 2016 Datacenter**-datorn installerar du sedan **AzureRM PowerShell-modulen** på servern.

1. På den virtuella datorn öppnar du ett PowerShell-fönster med förhöjd behörighet
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

Modulen `AzureRM` är en sammanslagen modul för Azure PowerShell-cmdletar. När du installerar den laddar den ned alla tillgängliga Azure Resource Manager-moduler och gör dess cmdletar tillgängliga för användning.

Nu har du slutfört konfigurationen av din miljö för självstudien och är redo att börja distribuera **tjänsten för synkronisering av lagring**.

## <a name="deploy-the-service"></a>Distribuera tjänsten 
Distributionen av Azure File Sync börjar med att placera en resurs för **tjänsten för synkronisering av lagring** i en resursgrupp för din valda prenumeration. Tjänsten för synkronisering av lagring ärver åtkomstbehörigheter från den prenumeration och resursgrupp du distribuerar den till.

1. I Azure-portalen klickar du på **Skapa en resurs** och sök sedan efter **Azure File Sync**.
1. Välj **Azure File Sync** i sökresultatet.
1. Välj **Skapa** för att öppna fliken **Distribuera synkronisering av lagring**.

   ![Distribuera synkronisering av lagring](media/storage-sync-files-extend-servers/afs-info.png)

   I fönstret som öppnas anger du följande information:

   | Värde | Beskrivning |
   | ----- | ----- |
   | **Namn** | Ett unikt namn (per prenumeration) för tjänsten för synkronisering av lagring.<br><br>I den här kursen använder vi *afssyncservice02*. |
   | **Prenumeration** | Den prenumeration som du använder för den här kursen. |
   | **Resursgrupp** | Den resursgrupp du använder för den här kursen.<br><br>I den här kursen använder vi *afsresgroup101918*. |
   | **Plats** | Östra USA |

1. När du är klar väljer du **Skapa** för att distribuera **tjänsten för synkronisering av lagring**.
1. Klicka på fliken **Meddelanden** > **Gå till resurs**.

## <a name="install-the-agent"></a>Installera agenten
Azure File Sync-agenten är ett nedladdningsbart paket som möjliggör att Windows Server kan synkroniseras med en Azure-filresurs.

1. Gå tillbaka till den virtuella **Windows Server 2016 Datacenter**-datorn och öppna **Internet Explorer**.
1. Gå till [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Rulla ned till avsnittet **Azure File Sync-agent** och klicka på **Ladda ned**.

   ![Nedladdning av synkroniseringsagent](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Markera rutan **StorageSyncAgent_V3_WS2016.EXE** och klicka på **Nästa**.

   ![Välja agent](media/storage-sync-files-extend-servers/select-agent.png)

1. **Tillåt en gång** > **Kör** > **Öppna** filen.
1. Stäng PowerShell-fönstret, om du inte redan har gjort det.
1. Acceptera standardinställningarna i **konfigurationsguiden för lagringssynkroniseringsagenten**.
1. Klicka på **Installera**.
1. Klicka på **Slutför**.

Du har distribuerat Azure-synkroniseringstjänsten och installerat agenten på den virtuella **Windows Server 2016 Datacenter**-datorn. Nu måste du registrera den virtuella datorn på **tjänsten för synkronisering av lagring**.

## <a name="register-windows-server"></a>Registrera Windows Server
När du registrerar Windows Server med en tjänst för synkronisering av lagring etableras en förtroenderelation mellan din server (eller ditt kluster) och tjänsten för synkronisering av lagring. En server kan bara registreras på en tjänst för synkronisering av lagring och kan synkronisera med andra servrar och Azure-filresurser som associeras med samma tjänst för synkronisering av lagring.

Användargränssnittet för serverregistrering bör öppnas automatiskt när du har installerat **Azure File Sync-agenten**. Om det inte gör det kan du öppna det från dess filplats: C:\Program\Azure\StorageSyncAgent\ServerRegistration.exe.

1. När användargränssnittet för serverregistrering öppnas i den virtuella datorn klickar du på **OK**.
1. Klicka på **Logga in** för att börja.
1. Logga in med autentiseringsuppgifterna för Azure-kontot och klicka på **Logga in**.
1. Ange följande information:

   ![Skärmbild av användargränssnittet för serverregistrering](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Värde | Beskrivning |
   | **Azure-prenumeration** | Den prenumeration som innehåller tjänsten för synkronisering av lagring för den här kursen. |
   | **Resursgrupp** | Den resursgrupp som innehåller tjänsten för synkronisering av lagring för den här kursen. Vi har använt *afsresgroup101918* genom hela kursen. |
   | **Tjänst för synkronisering av lagring** | Namnet på den tjänst för synkronisering av lagring du har använt för den här kursen. Vi har använt *afssyncservice02* genom hela kursen. |

1. Klicka på **Registrera** för att slutföra serverregistreringen.
1. Som en del av registreringsprocessen uppmanas du att logga in en ytterligare inloggning. Logga in och klicka på **Nästa**.
1. Klicka på **OK**.

## <a name="create-a-sync-group"></a>Skapa en synkroniseringsgrupp
En synkroniseringsgrupp definierar synkroniseringstopologin för en uppsättning filer. En synkroniseringsgrupp måste innehålla en molnslutpunkt, som representerar en Azure-filresurs och en eller flera serverslutpunkter. En serverslutpunkt representerar en sökväg på en registrerad servern.

1. Om du vill skapa en synkroniseringsgrupp i [Azure-portalen](https://portal.azure.com/) väljer du **+ Synkroniseringsgrupp** från tjänsten för synkronisering av lagring du har skapat för den här kursen. Vi har använt *afssyncservice02* som exempel i den här kursen.

   ![Skapa en ny synkroniseringsgrupp i Azure-portalen](media/storage-sync-files-extend-servers/add-sync-group.png)

1. I fönstret som öppnas anger du följande information för att skapa en synkroniseringsgrupp med en molnslutpunkt:

   | Värde | Beskrivning |
   | ----- | ----- |
   | **Namn på synkroniseringsgrupp** | Det här namnet måste vara unikt i tjänsten för synkronisering av lagring men kan vara vilket namn som helst som är logiskt för dig. I den här kursen använder vi *afssyncgroup*.|
   | **Prenumeration** | Prenumerationen där du har distribuerat tjänsten för synkronisering av lagring för den här kursen. |
   | **Lagringskonto** |Klicka på **Välj lagringskonto**. I fönstret som visas väljer du det lagringskonto som har den Azure-filresurs du har skapat för den här kursen. Vi har använt *afsstoracct101918*. |
   | **Azure-filresurs** | Namnet på den Azure-filresurs du har skapat för den här kursen. Vi har använt *afsfileshare*. |

1. Klicka på **Skapa**.

Om du väljer din synkroniseringsgrupp kan du se att du nu har en **molnslutpunkt**.

## <a name="add-a-server-endpoint"></a>Lägga till en serverslutpunkt
En serverslutpunkt representerar en viss plats på en registrerad server, till exempel en mapp på en servervolym.

1. Lägg till en serverslutpunkt genom att välja den nya synkroniseringsgruppen och välj sedan **Lägg till serverslutpunkt**.

   ![Lägga till en serverslutpunkt i fönstret för synkroniseringsgruppen](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. I fönstret **Lägg till serverslutpunkt** anger du följande information för att skapa en serverslutpunkt:

   | | |
   | ----- | ----- |
   | Värde | Beskrivning |
   | **Registrerad server** | Namnet på den server du har skapat för den här kursen. Vi har använt *afsvm101918* i den här kursen |
   | **Sökväg** | Windows Server-sökvägen till den enhet du har skapat för den här kursen. I vårt exempel är det *f:\filestosync*. |
   | **Lagringsnivåer för moln** | Lämna det inaktiverat för den här kursen. |
   | **Ledigt utrymme på volym** | Lämna det tomt för den här kursen. |

1. Klicka på **Skapa**.

Dina filer är nu synkroniserade i Azure-filresursen och Windows Server.

![Azure Storage har synkroniserats](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen har du lärt dig de grundläggande stegen för att utöka lagringskapaciteten för Windows Server med hjälp av Azure File Sync. Följ den här länken om du vill ha en mer grundlig titt på planering av en Azure File Sync-distribution.

> [!div class="nextstepaction"]
> [Planera för Azure File Sync-distribution](./storage-sync-files-planning.md)