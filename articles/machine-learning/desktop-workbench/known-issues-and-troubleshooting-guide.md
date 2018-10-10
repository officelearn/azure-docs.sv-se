---
title: Kända problem och felsökningsguide för | Microsoft Docs
description: Lista över kända problem och en guide för att felsöka
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 8177808fd4d666ea04b1bc097f261c7643931704
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885058"
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - kända problem och felsökningsguide 
Den här artikeln hjälper dig att hitta och korrigera fel eller fel som uppstod som en del av med hjälp av Azure Machine Learning Workbench-programmet. 

## <a name="find-the-workbench-build-number"></a>Hitta Workbench build-nummer
När kommunikation med supportteamet, är det viktigt att inkludera build-nummer i Workbench. På Windows, kan du se build-nummer genom att klicka på den **hjälpa** menyn och välj **om Azure ML Workbench**. I macOS, kan du klicka på den **Azure ML Workbench** menyn och välj **om Azure ML Workbench**.

## <a name="machine-learning-msdn-forum"></a>Machine Learning-forumet på MSDN
Vi har en MSDN-Forum som att du kan ställa frågor. Forumet övervakas aktivt produktteamet. Forumet URL: en är [ https://aka.ms/aml-forum-service ](https://aka.ms/aml-forum-service). 

## <a name="gather-diagnostics-information"></a>Samla in diagnostikinformation
Ibland kan det vara bra om du kan ange diagnostisk information när du frågar om du behöver hjälp. Här är där loggfilerna live:

### <a name="installer-log"></a>Installer-loggen
Om du får problem under installationen, är installationsprogram loggfilerna här:

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
Du kan zippa upp innehållet i dessa kataloger och skicka det till oss för diagnostik.

### <a name="workbench-desktop-app-log"></a>Workbench skrivbordsapp log
Om du har problem med att logga in, eller om Workbench skrivbordet kraschar, hittar du här loggfiler:
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
Du kan zippa upp innehållet i dessa kataloger och skicka det till oss för diagnostik.

### <a name="experiment-execution-log"></a>Körningsloggen för experiment
Om ett visst skript inte ärendet från skrivbordsappen, försöker skicka via CLI med `az ml experiment submit` kommando. Detta bör du få fullständig felmeddelande i JSON-format och viktigast av allt innehåller en **åtgärds-ID** värde. Skicka oss JSON-fil för inklusive den **åtgärds-ID** och vi kan hjälpa att diagnostisera. 

Om ett visst skript lyckas bidrag men inte köras, det bör skriva ut den **kör ID** att identifiera körningen. Du kan packa upp de relevanta loggfiler med hjälp av följande kommando:

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

Den `az ml experiment diagnostics` kommandot genererar en `diagnostics.zip` fil i rotmappen för projektet. ZIP-paketet innehåller hela projektmappen tillståndet när den har utförts, plus loggningsinformation. Glöm inte att ta bort känslig information som du inte vill inkludera innan du skickar oss diagnostik-filen.

## <a name="send-us-a-frown-or-a-smile"></a>Skicka en bister min (eller ett Leende)

När du arbetar i Azure ML Workbench, kan du också skicka en bister min (eller ett Leende) genom att klicka på ikonen ansikte ansikte i det nedre vänstra hörnet av programmet. Du kan välja att din e-postadress (så att vi kan gå tillbaka till du), och/eller en skärmbild av det aktuella tillståndet. 

## <a name="known-service-limits"></a>Kända tjänstbegränsningar
- Högsta tillåtna projekt mappstorleken: 25 MB.
    >[!NOTE]
    >Den här gränsen gäller inte för `.git`, `docs` och `outputs` mappar. Dessa mappnamn är skiftlägeskänsliga. Om du arbetar med stora filer, se [spara ändringar och behandlar stora filer](how-to-read-write-files.md).

- Högsta tillåtna körningstiden för experimentet: sju dagar

- Maxstorlek på spårade filen i `outputs` mapp när du har en körning: 512 MB
  - Det innebär att om skriptet genererar en fil som är större än 512 MB i mappen utdata, den inte har samlats in det. Om du arbetar med stora filer, se [spara ändringar och behandlar stora filer](how-to-read-write-files.md).

- SSH-nycklar stöds inte när du ansluter till en fjärransluten dator eller Spark-klustret via SSH. Användarnamn/lösenord läge stöds för närvarande.

- När du använder HDInsight-klustret efter beräkningsmålet, måste den använda Azure blob som primär lagring. Med hjälp av Azure Data Lake Storage stöds inte.

- Klustring texttransformeringar stöds inte på Mac.

- RevoScalePy bibliotek stöds endast på Windows och Linux (i Docker-behållare). Det finns inte stöd i macOS.

- Jupyter Notebooks har en maximal storleksgräns på 5 MB när du öppnar dem från Workbench-appen. Du kan öppna stora anteckningsböcker från CLI-kommandot ”az ml notebook start” och rensa cell matar ut för att minska filstorleken.

## <a name="cant-update-workbench"></a>Det går inte att uppdatera Workbench
När en ny uppdatering är tillgänglig, visas ett meddelande som informerar dig om den nya uppdateringen i Workbench appens startsida. Du bör se en update-märket visas på det nedre vänstra hörnet av appen på klockikonen. Klicka på aktivitetsikonen och Följ guiden installationsprogrammet för att installera uppdateringen. 

![Uppdatera avbildning](../service/media/known-issues-and-troubleshooting-guide/update.png)

Om du inte ser meddelandet, kan du försöka starta om appen. Om du inte kan se meddelanden om uppdateringar efter omstart, kan det finnas några orsaker.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>Du startar Workbench från en fästa genväg i Aktivitetsfältet
Du kanske redan har installerat uppdateringen. Men dina fästa genväg fortfarande pekar på de gamla bits på disken. Du kan kontrollera detta genom att bläddra till den `%localappdata%/AmlWorkbench` mappen och se om du har installerat det senaste version och undersöka egenskapen för den fästa genväg till Se där den pekar till. Om verifieras, helt enkelt ta bort den gamla genvägen, starta Workbench från Start-menyn och du kan också skapa en ny fästa genväg i Aktivitetsfältet.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Du har installerat Workbench med hjälp av länken ”installera Azure ML Workbench” på en Windows DSVM
Det finns tyvärr inga enkelt åtgärdas med det här. Du måste utföra följande steg för att ta bort de installera bitarna och ladda ned det senaste installationsprogrammet för att nya-installation Workbench: 
   - ta bort mappen `C:\Users\<Username>\AppData\Local\amlworkbench`
   - ta bort skriptet `C:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - ta bort genvägen på skrivbordet som startar skriptet ovan
   - ladda ned installationsprogrammet https://aka.ms/azureml-wb-msi och installera om.

## <a name="stuck-at-checking-experimentation-account-screen-after-logging-in"></a>Fastnat på ”kontroll experimenteringskontot” skärmen när du loggar in
Efter inloggningen kan Workbench-appen fastnar på ett tomt fönster med ett meddelande som visar ”kontrollerar experimenteringskontot” med att hjul. För att lösa problemet, gör du följande:
1. Stäng av appen
2. Ta bort följande fil:
  ```
  # on Windows
  %appdata%\AmlWorkbench\AmlWb.settings

  # on macOS
  ~/Library/Application Support/AmlWorkbench/AmlWb.settings
  ```
3. Starta om appen.

## <a name="cant-delete-experimentation-account"></a>Det går inte att ta bort Experimenteringskontot
Du kan använda CLI för att ta bort ett konto för experimentering, men du måste först ta bort underordnade arbetsytor och underordnade-projekt i dessa underordnade arbetsytorna. Annars ser du felet ”inte kan ta bort resursen innan kapslade resurser tas bort”.

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <workspace name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <workspace name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

Du kan också ta bort projekt och arbetsytor från i Workbench-appen.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>Det går inte att öppna filen om projektet är i OneDrive
Om du har Windows 10 Fall Creators Update och projektet skapas i en lokal mapp som mappats till OneDrive, kanske du upptäcker att du inte kan öppna en fil i Workbench. Detta beror på en bugg som introducerades av Fall Creators Update som orsakar node.js-kod att misslyckas i en OneDrive-mapp. Felet åtgärdas snart av Windows update, men fram till dess, skapa inte projekt i en OneDrive-mapp.

## <a name="file-name-too-long-on-windows"></a>Filnamn för länge på Windows
Om du använder Workbench på Windows kan stöta du på maximal 260 tecken namn längd Standardgränsen, vilket kan yta som felet ”Det går inte att hitta den angivna sökvägen”. Du kan ändra en registernyckel för att tillåta mycket längre sökvägsnamn. Granska [i den här artikeln](https://msdn.microsoft.com/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath) för mer information om hur du ställer in den _MAX_PATH_ registernyckeln.

## <a name="interrupt-cli-execution-output"></a>Avbryt körningen CLI-utdata
Om du startar ett experiment att köras med `az ml experiment submit` eller `az ml notebook start` och du vill avbryta utdata: 
- Använd Ctrl-Break tangentkombinationen från tangentbordet på Windows
- På macOS, använder du Ctrl-C.

Observera att detta avbryter utdataströmmen i CLI-fönstret. Det faktiskt inte att stoppa ett jobb som körs. Om du vill avbryta ett pågående jobb använder `az ml experiment cancel -r <run_id> -t <target name>` kommando.

Möjliga alternativ ta Fn-B, Ctrl-Fn-B eller Fn + Esc på Windows-datorer med tangentbord som inte har Break-tangenten. Läs dokumentationen från maskinvaruleverantören för en specifik tangentkombination.

## <a name="docker-error-read-connection-refused"></a>Docker-fel ”Läs: går inte att ansluta”
När du kör mot en lokal Docker-behållare, kan ibland du se följande fel: 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

Du kan åtgärda det genom att ändra DNS-Server för Docker från `automatic` till ett fast värde för `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Ta bort VM-körningsfel ”inga tty finns”
När du kör mot en Docker-behållare på en fjärrdator med Linux, du kan stöta på följande felmeddelande visas:
```
sudo: no tty present and no askpass program specified.
``` 
Detta kan inträffa om du använder Azure-portalen för att ändra rotlösenordet på en Ubuntu Linux-dator. 

Azure Machine Learning Workbench kräver lösenord utan sudoers åtkomst att köra på fjärrvärdar. Det enklaste sättet att göra det är att använda _visudo_ att redigera följande fil (du kan skapa filen om den inte finns):

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>Det är viktigt att redigera filen med _visudo_ och inte ett annat kommando. _visudo_ syntax kontrollerar automatiskt alla sudo-konfigurationsfiler och det gick inte att skapa en syntaktiskt korrekta sudoers-fil kan låsa dig ute från sudo.

Infoga följande rad i slutet av filen:

```
username ALL=(ALL) NOPASSWD:ALL
```

Där _användarnamn_ är namnet på Azure Machine Learning Workbench använder för att logga in på ditt fjärr-värden.

Raden måste placeras efter #includedir ”/ etc/sudoers.d”, annars den kan åsidosättas med en annan regel.

Om du har en mer komplicerad sudo-konfiguration kan behöva du Läs sudo-dokumentationen om Ubuntu tillgänglig här: https://help.ubuntu.com/community/Sudoers

Ovanstående felet kan också inträffa om du inte använder en Ubuntu-baserad Linux VM i Azure som en körningsmål. Vi stöder endast Ubuntu-baserad Linux VM för fjärrkörning. 

## <a name="vm-disk-is-full"></a>VM-disken är full
Som standard när du skapar en ny Linux-VM i Azure kan få du en 30 GB-disk för operativsystemet. Docker-motorn som standard använder samma disk för dra nedåt avbildningar och behållare som körs. Detta kan du fylla in OS-disken och du ser felet ”VM disken är Full” när det händer.

En snabbkorrigering är att ta bort alla Docker-avbildningar som du inte längre använda. Kommandot Docker gör just detta. (Naturligtvis du måste SSH till den virtuella datorn för att köra kommandot Docker från ett bash-gränssnitt.)

```
$ docker system prune -a
```

Du kan också lägga till en datadisk och konfigurerar Docker-motorn om du vill använda datadisken för lagring av bilder. Här är [hur du lägger till en datadisk](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk). Du kan sedan [ändra där Docker lagrar avbildningar](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Eller, du kan expandera Operativsystemets disk och du behöver inte touch konfigurationen av Docker-motorn. Här är [hur du kan expandera Operativsystemets disk](https://docs.microsoft.com/azure/virtual-machines/linux/expand-disks).

```azure-cli
# Deallocate VM (stopping will not work)
$ az vm deallocate --resource-group myResourceGroup  --name myVM

# Get VM's Disc Name
az disk list --resource-group myResourceGroup --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table

# Update Disc Size using above name
$ az disk update --resource-group myResourceGroup --name myVMdisc --size-gb 250
    
# Start VM    
$ az vm start --resource-group myResourceGroup  --name myVM
```

## <a name="sharing-c-drive-on-windows"></a>Dela C-enheten på Windows
Om du kör i en lokal dockerbehållare på Windows, ange `sharedVolumes` till `true` i den `docker.compute` filen under `aml_config` kan förbättra prestanda för körning. Men detta kräver att du delar C-enheten i den _Docker för Windows-verktyget_. Om du inte har tillgång till en enhet C, kan du prova följande tips:

* Kontrollera delning på C-enheten med hjälp av Utforskaren
* Öppna inställningarna för nätverkskortet och avinstallera/ominstallera ”fil och Printer Sharing for Microsoft Networks” för vEthernet
* Öppna inställningarna för docker och dela C-enheten från docker-inställningar
* Windows-lösenord ändringar påverkar delning. Öppna Utforskaren, dela C-enheten och ange det nya lösenordet.
* Du kan även uppstå brandväggen problem vid försök att dela dina C-enheten med Docker. Detta [Stack Overflow post](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) kan vara till hjälp.
* När du delar C-enheten med hjälp av autentiseringsuppgifter för domänen, kan delning sluta fungera i nätverk där domänkontrollanten inte kan nås (till exempel nätverk, offentlig wifi osv.). Mer information finns i [det här inlägget](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

Du kan också förhindra delning problemet på en mindre prestandakostnad, genom att ange `sharedVolumne` till `false` i den `docker.compute` filen.

## <a name="wipe-clean-workbench-installation"></a>Rensa ren installation av Workbench
Du behöver vanligtvis inte göra detta. Men om du måste rensa ren en installation, följer du stegen:

- I Windows:
  - Kontrollera först att du använder _Lägg till eller ta bort program_ appleten i den _Kontrollpanelen_ att ta bort den _Azure Machine Learning Workbench_ ser ut.  
  - Du kan sedan hämta och köra någon av följande skript:
    - [Skript för Windows-kommandoraden](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.cmd).
    - [Windows PowerShell-skript](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.ps1). (Du kan behöva köra `Set-ExecutionPolicy Unrestricted` i ett privilege-upphöjd PowerShell-fönster innan du kan köra skriptet.)
- I macOS:
  - Bara ladda ned och kör den [macOS bash-kommandoskript](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_mac.sh).

## <a name="azure-ml-using-a-different-python-location-than-the-azure-ml-installed-python-environment"></a>Azure ML med hjälp av en annan python-plats än den Azure ML installerat python-miljö
På grund av en ändring i Azure Machine Learning Workbench kan användare få lokala körs inte kanske pekar på python-miljö som installerats av Azure ML Workbench längre. Detta kan inträffa om användaren har en annan python-miljö som är installerat på datorn och sökvägen ”Python” är inställd så att den pekar till den miljön. För att kunna använda Azure ML Workbench installerat Python-miljö, Följ dessa steg:
- Gå till local.compute under aml_config mapp under projektroten.
- Ändra variabeln ”pythonLocation” så att den pekar till den fysiska sökvägen för Azure ML workbench installerat python-miljön. Du kan hämta den här sökvägen på två sätt:
    - Azure ML python-plats finns på %localappdata%\AmlWorkbench\python\python.exe
    - Du kan öppna cmd från Azure ML Workbench, Skriv python på Kommandotolken, importera sys.exe, kör sys.executable och hämta sökvägen därifrån. 



## <a name="some-useful-docker-commands"></a>Några användbara Docker-kommandon

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
