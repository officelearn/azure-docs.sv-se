---
title: "Kända problem och felsökningsguiden | Microsoft Docs"
description: "Lista över kända problem och en guide för att felsöka"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: d1e3a4fd4415afb995f614ac687096f6fb8ece95
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2018
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning arbetsstationen - kända problem och felsökningsguide 
Den här artikeln hjälper dig att hitta och korrigera fel eller fel uppstod som en del av använder i Azure Machine Learning Workbench. 

## <a name="find-the-workbench-build-number"></a>Hitta arbetsstationen build-nummer
Vid kommunikation med supportgruppen är det viktigt att ha appen arbetsstationen build-nummer. I Windows, som du kan ta reda build-nummer genom att klicka på den **hjälp** menyn och välj **om Azure ML arbetsstationen**. I macOS, kan du klicka på den **Azure ML-arbetsstationen** menyn och välj **om Azure ML arbetsstationen**.

## <a name="machine-learning-msdn-forum"></a>Machine Learning MSDN-Forum
Vi har en MSDN-Forum kan du skriva frågor. Produktteamet övervakar forumet aktivt. Forumet URL: en är [https://aka.ms/azureml-forum](https://aka.ms/azureml-forum). 

## <a name="gather-diagnostics-information"></a>Samla in diagnostikinformation
Ibland kan det vara användbart om du kan ange diagnostikinformation när be om hjälp. Här är där loggfilerna live:

### <a name="installer-log"></a>Installer-loggen
Om du stöter på problem under installationen finns installer loggfilerna här:

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
Du kan zip-innehållet i de här katalogerna och skicka det till oss för diagnostik.

### <a name="workbench-desktop-app-log"></a>Arbetsstationen skrivbordsapp logg
Om du har problem med att logga in, eller om arbetsstationen skrivbordet krascher, hittar du här loggfiler:
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
Du kan zip-innehållet i de här katalogerna och skicka det till oss för diagnostik.

### <a name="experiment-execution-log"></a>Experiment körningsloggen
Om ett visst skript misslyckas under överföring från skrivbordsappen, försök att skicka via med hjälp av CLI `az ml experiment submit` kommando. Detta bör du få fullständigt felmeddelande i JSON-format och viktigast av allt innehåller en **åtgärds-ID** värde. Skicka oss JSON-filen inklusive den **åtgärds-ID** och vi kan hjälpa till att diagnostisera. 

Om ett visst skript lyckas skicka misslyckas körning, ska skrivas på **kör ID** att identifiera den viss körningen. Du kan packa upp de relevanta loggfiler med hjälp av följande kommando:

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

Den `az ml experiment diagnostics` kommandot genererar en `diagnostics.zip` filen i rotmappen för projektet. ZIP-paketet innehåller mappen hela projektet i tillståndet när den kördes plus loggningsinformation. Tänk på att någon känslig information som du inte vill att innan du skickar oss diagnostik-filen.

## <a name="send-us-a-frown-or-a-smile"></a>Skicka en bister (eller en le)

När du arbetar i Azure ML-arbetsstationen, kan du också skicka en bister (eller en le) genom att klicka på ikonen ansikte ansikte i det nedre vänstra hörnet av programmet. Du kan välja att din e-postadress (så att vi kan komma tillbaka till du), och/eller en skärmbild av det aktuella tillståndet. 

## <a name="known-service-limits"></a>Kända tjänstbegränsningarna
- Högsta tillåtna storleken projekt: 25 MB.
    >[!NOTE]
    >Den här gränsen gäller inte för `.git`, `docs` och `outputs` mappar. Dessa mappnamn är skiftlägeskänsliga. Om du arbetar med stora filer kan du läsa [spara ändringar och hantera stora filer](how-to-read-write-files.md).

- Högsta tillåtna körningstiden för experimentet: sju dagar

- Maxstorlek på spårade filen i `outputs` efter en körning: 512 MB
  - Det innebär att om skriptet genererar en fil som är större än 512 MB i mappen utdata, den inte har samlats in det. Om du arbetar med stora filer kan du läsa [spara ändringar och hantera stora filer](how-to-read-write-files.md).

- SSH-nycklar stöds inte när du ansluter till en fjärransluten dator eller Spark-klustret via SSH. Läget för endast användarnamn/lösenord stöds för närvarande.

- När du använder HDInsight-kluster som compute mål, måste den använda Azure blob som primär lagring. Med Azure Data Lake lagring stöds inte.

- Text klustring transformeringar stöds inte för Mac.

- RevoScalePy bibliotek stöds bara på Windows och Linux (i behållare med Docker). Det finns inte stöd för macOS.

- Jupyter-anteckningsböcker har en maxstorlek gräns på 5 MB när du öppnar dem från appen arbetsstationen. Du kan öppna stora anteckningsböcker från CLI kommandot az ml anteckningsboken start och ren cell matar ut för att minska filstorleken.

## <a name="cant-update-workbench"></a>Det går inte att uppdatera arbetsstationen
När en ny uppdatering är tillgänglig, visas arbetsstationen app webbsida ett meddelande som talar om den nya uppdateringen. Du bör se en uppdatering Aktivitetsikon som visas i det nedre vänstra hörnet på appen på på klockikonen. Klicka på skylt och Följ guiden installationsprogrammet för att installera uppdateringen. 

![Uppdatera avbildning](./media/known-issues-and-troubleshooting-guide/update.png)

Om du inte ser meddelandet, försök att starta om appen. Om du fortfarande inte ser uppdateringsmeddelande efter omstart uppstå några orsaker.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>Arbetsstationen lanseras från en fästa genväg i Aktivitetsfältet
Du kanske redan har installerat uppdateringen. Men dina fästa genväg fortfarande pekar på den gamla bits på disken. Du kan kontrollera detta genom att bläddra till den `%localappdata%/AmlWorkbench` mappen och se om du har installerat det senaste version och undersöka egenskapen Fäst genvägen till finns där pekar på. Om verifieras bara ta bort den gamla genvägen, starta arbetsstationen från Start-menyn och du kan också skapa en ny genväg för Fäst i Aktivitetsfältet.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Du har installerat arbetsstationen med hjälp av länken ”installera Azure ML-arbetsstationen” på en Windows-DSVM
Det finns ingen enkel korrigering på denna. Du måste utföra följande steg för att ta bort den installera bits och hämta senaste installationsprogrammet för att installera arbetsstationen färsk: 
   - ta bort mappen`C:\Users\<Username>\AppData\Local\amlworkbench`
   - ta bort skriptet`C:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - ta bort genväg på skrivbordet som startar skriptet ovan
   - Hämta installer https://aka.ms/azureml-wb-msi och installera om.

## <a name="stuck-at-checking-experimentation-account-screen-after-logging-in"></a>Fastnat när ”kontrollerar experiment kontot” visas när du loggar in
När du loggar in kan appen arbetsstationen fastna på ett tomt fönster med ett meddelande som visar ”kontrollerar experiment konto” med en snurrande hjul. Lös problemet, gör du följande:
1. Stäng appen
2. Ta bort följande fil:
  ```
  # on Windows
  %appdata%\AmlWorkbench\AmlWb.settings

  # on macOS
  ~/Library/Application Support/AmlWorkbench/AmlWb.settings
  ```
3. Starta om appen.

## <a name="cant-delete-experimentation-account"></a>Det går inte att ta bort experiment konto
Du kan använda CLI för att ta bort ett experiment konto, men du måste först ta bort underordnade arbetsytorna och underordnade projekt i dessa underordnade arbetsytor. Annars kan se du felet ”inte kan ta bort resursen innan kapslade resurser tas bort”.

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <worksapce name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <worksapce name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

Du kan också ta bort projekt och arbetsytor från i appen arbetsstationen.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>Det går inte att öppna filen om projektet i OneDrive
Om du har Windows 10 faller skapare Update och projektet har skapats i en lokal mapp som mappats till OneDrive, kanske du upptäcker att du inte kan öppna en fil i arbetsstationen. Detta beror på ett programfel som introducerades av faller skapare uppdateringen som orsakar node.js-kod att fungera i en mapp i OneDrive. Programfelet korrigeras snart av Windows update, men fram till dess kan inte skapar du projekt i en mapp i OneDrive.

## <a name="file-name-too-long-on-windows"></a>Filnamnet för långt i Windows
Om du använder arbetsstationen i Windows kan stöta du på maximalt 260 tecken-filen namnet längd Standardgränsen, som kan ansluta till en ”går inte att hitta den angivna sökvägen” fel. Du kan ändra en registernyckel för att tillåta mycket längre filsökvägen. Granska [i den här artikeln](https://msdn.microsoft.com/en-us/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath) för mer information om hur du ställer in den _MAX_PATH_ registernyckeln.

## <a name="interrupt-cli-execution-output"></a>Avbryter CLI utförande-utdatan
Om du startar ett experiment som körs med `az ml experiment submit` eller `az ml notebook start` och du vill avbryta utdata: 
- Använd Ctrl-Break tangentkombinationen från tangentbordet på Windows
- I macOS, använda Ctrl-C.

Observera att detta avbryter utdataströmmen i fönstret CLI. Det faktiskt inte att stoppa ett jobb som körs. Om du vill avbryta en pågående jobbet använder `az ml experiment cancel -r <run_id> -t <target name>` kommando.

På Windows-datorer med tangentbord som inte har Break-nyckel, omfattar möjliga alternativ Fn B, Ctrl-Fn-B eller Fn + Esc. Din maskinvaruleverantören dokumentationen för en specifik tangentkombinationen.

## <a name="docker-error-read-connection-refused"></a>Docker fel ”läsa: anslutningen avslogs”
När körs mot en lokal dockerbehållare kan ibland du se följande fel: 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

Du kan åtgärda det genom att ändra Docker DNS-servern från `automatic` till ett fast värde för `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Ta bort VM körningsfel ”ingen tty finns”
När körs mot en dockerbehållare på en fjärrdator Linux måste stöta du på följande felmeddelande:
```
sudo: no tty present and no askpass program specified.
``` 
Detta kan inträffa om du använder Azure-portalen för att ändra lösenordet för roten för en virtuell Ubuntu Linux-dator. 

Azure Machine Learning arbetsstationen kräver lösenord mindre sudoers åtkomst ska köras på fjärrvärdar. Det enklaste sättet att göra det är att använda _visudo_ att redigera följande fil (du kan skapa filen om den inte finns):

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>Det är viktigt att redigera filen med _visudo_ och inte ett annat kommando. _visudo_ automatiskt syntax kontrollerar alla sudo-konfigurationsfiler och det gick inte att skapa en syntaktiskt korrekt sudoers-fil kan låser sig sudo.

Infoga följande rad i slutet av filen:

```
username ALL=(ALL) NOPASSWD:ALL
```

Där _användarnamn_ är namnet på Azure Machine Learning-arbetsstationen använder för att logga in på ditt fjärrvärden.

Raden måste placeras efter #includedir ”/ etc/sudoers.d”, annars den kan åsidosättas med en annan regel.

Om du har en mer komplicerad sudo-konfiguration kan du vilja dokumentationen sudo för Ubuntu finns här: https://help.ubuntu.com/community/Sudoers

Felet ovan kan också inträffa om du inte använder en baserat på Ubuntu Linux VM i Azure som ett mål för körning. Vi stöder endast baserat på Ubuntu Linux VM för fjärrkörning. 

## <a name="vm-disk-is-full"></a>VM-disken är full
Som standard när du skapar en ny Linux VM i Azure, får du en disk på 30 GB för operativsystemet. Docker-motorn som standard använder samma disk för dra nedåt avbildningar och behållare som körs. Detta kan fylla OS-disken och du ser felet ”VM disken är Full” när det sker.

En snabb lösning är att ta bort alla Docker-avbildningar som du inte längre använder. Kommandot Docker har precis så. (Naturligtvis du behöver SSH till den virtuella datorn för att köra kommandot Docker från ett bash-gränssnitt.)

```
$ docker system prune -a
```

Du kan också lägga till en datadisk och konfigurera Docker-motorn om du vill använda datadisken för lagring av bilder. Här är [hur du lägger till en datadisk](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk). Du kan sedan [ändra där avbildningar lagras i Docker](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Du kan expandera OS-disken och du behöver inte touch Docker-konfigurationen för motorns. Här är [hur du kan expandera OS-disken](https://docs.microsoft.com/azure/virtual-machines/linux/expand-disks).

```azure-cli
#Deallocate VM (stopping will not work)
$ az vm deallocate --resource-group myResourceGroup  --name myVM

# Update Disc Size
$ az disk update --resource-group myResourceGroup --name myVM --size-gb 250
    
# Start VM    
$ az vm start --resource-group myResourceGroup  --name myVM
```

## <a name="sharing-c-drive-on-windows"></a>Dela C-enheten i Windows
Om du kör i en lokal dockerbehållare i Windows ställer `sharedVolumes` till `true` i den `docker.compute` filen `aml_config` kan förbättra prestanda för körning. Detta kräver dock du dela C-enheten i den _Docker för Windows-verktyget för_. Om du inte kunna dela C-enheten, kan du försöka med följande:

* Kontrollera delar på C-enheten med Utforskaren
* Öppna inställningar för nätverkskort och avinstallera/ominstallera ”fil och Printer Sharing for Microsoft Networks” för vEthernet
* Öppna inställningarna för docker och dela C-enheten från docker-inställningar
* Windows-lösenord ändringar påverkar delning. Öppna Utforskaren, dela C-enheten och ange det nya lösenordet.
* Du kan också brandväggen problem uppstå vid försök att dela C-enheten med Docker. Detta [Stack Overflow post](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) kan vara användbart.
* När du delar C-enheten med domänreferenser kan delning sluta fungera i nätverk där domänkontrollanten inte kan nås (till exempel nätverk, offentliga wifi osv.). Mer information finns i [inlägget](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

Du kan undvika att delningsproblem på en mindre prestandakostnad, genom att ange `sharedVolumne` till `false` i den `docker.compute` filen.

## <a name="wipe-clean-workbench-installation"></a>Rensa ren installation av Workbench
Du behöver vanligtvis inte göra detta. Men om du måste rensa ren en installation, följer du stegen:

- I Windows:
  - Först kontrollera att du använder _Lägg till eller ta bort program_ appleten i den _Kontrollpanelen_ att ta bort den _Azure Machine Learning arbetsstationen_ kopplingen.  
  - Sedan kan du hämta och köra någon av följande skript:
    - [Windows-kommandoradsskript](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.cmd).
    - [Windows PowerShell-skript](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.ps1). (Du kan behöva köra `Set-ExecutionPolicy Unrestricted` i privilegiet-upphöjd PowerShell-fönstret innan du kan köra skriptet.)
- I macOS:
  - Bara ladda ned och kör den [macOS bash kommandoskript](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_mac.sh).


## <a name="some-useful-docker-commands"></a>Vissa användbara Docker-kommandon

Här följer några användbara Docker-kommandon:

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```
