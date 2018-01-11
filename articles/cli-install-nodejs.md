---
title: Installera Azure CLI 1.0 | Microsoft Docs
description: "Installera Azure CLI 1.0 för Mac, Linux och Windows att börja använda Azure-tjänster"
editor: 
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: bdb776c8-7a76-4f3a-887c-236b4fffee10
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: command-line-interface
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: rasquill
ms.openlocfilehash: 67aa5bb122b277e998119506bb2a574d6b4fde5e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="install-the-azure-cli-10"></a>Installera Azure CLI 1.0
> [!div class="op_single_selector"]
> * [PowerShell](/powershell/azure/overview)
> * [Azure CLI 1.0](cli-install-nodejs.md)
> * [Azure CLI 2.0](/cli/azure/install-azure-cli)

> [!IMPORTANT]
> Det här avsnittet beskriver hur du installerar Azure CLI 1.0. Den här CLI är inaktuell och bör endast användas för support med Azure Service Management (ASM) modellen med ”klassiska” resurser.
> Azure Resource Manager (ARM) distributioner använder [Azure CLI 2.0](/cli/azure/overview).

Snabbt installera Azure-kommandoradsgränssnittet (Azure CLI 1.0) om du vill använda en uppsättning med öppen källkod shell-baserade kommandon för att skapa och hantera resurser i Microsoft Azure. Du har flera alternativ för att installera verktygen plattformsoberoende på datorn:

* **npm paketet** -kör npm (package manager för JavaScript) för att installera det senaste Azure CLI 1.0-paketet på din distribution av Linux eller OS. Kräver node.js och npm på datorn.
* **Installer** -hämta ett installationsprogram för enkel installation på Mac- eller Windows.
* **Dockerbehållare** - Start med hjälp av senaste CLI i en klar och kör dockerbehållare. Kräver Docker värden på datorn.

Fler alternativ och bakgrunden finns i databasen projektet på [GitHub](https://github.com/azure/azure-xplat-cli).

När du har installerat Azure CLI 1.0 [ansluta till din Azure-prenumeration](/cli/azure/authenticate-azure-cli) och kör den **azure** kommandon från din kommandoradsgränssnittet (Bash, Terminal, Kommandotolken och så vidare) att arbeta med din Azure-resurser.

## <a name="option-1-install-an-npm-package"></a>Alternativ 1: Installera npm-paket
Om du vill installera CLI från ett npm-paket, kontrollera att du har hämtat och installerat den [senaste Node.js och npm](https://nodejs.org/en/download/package-manager/). Kör sedan **installera npm** att installera azure cli-paketet:

```bash
npm install -g azure-cli
```

På Linux-distributioner, kan du behöva använda **sudo** för att köra den **npm** kommandot på följande sätt:

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Om du behöver installera eller uppdatera Node.js och npm på Linux-distribution eller OS, rekommenderar vi att du installerar den senaste versionen för Node.js LTS (4.x). Om du använder en äldre version, kan du hämta installationsfel.

Om du vill hämta den senaste Linux [tar-filen] [ linux-installer] för npm paketet lokalt. Installera sedan hämtade npm-paketet enligt följande (på Linux-distributioner som du kan behöva använda **sudo**):

```bash
npm install -g <path to downloaded tar file>
```

## <a name="option-2-use-an-installer"></a>Alternativ 2: Använda ett installationsprogram
Om du använder en Mac- eller Windows-dator är följande CLI installationsprogram hämtas:

* [Mac OS X-installationsprogrammet][mac-installer]
* [Windows MSI][windows-installer]

> [!TIP]
> I Windows, kan du också hämta de [installationsprogram för webbplattform](https://go.microsoft.com/?linkid=9828653) installera CLI. Det här installationsprogrammet kan du välja att installera ytterligare Azure SDK och kommandoradsverktygen när du har installerat CLI.

## <a name="option-3-use-a-docker-container"></a>Alternativ 3: Använda en dockerbehållare
Om du har konfigurerat datorn som en [Docker](https://docs.docker.com/engine/understanding-docker/) värd, kan du köra den senaste Azure CLI 1.0 i en dockerbehållare. Kör följande kommando (på Linux-distributioner som du kan behöva använda **sudo**):

```bash
docker run -it microsoft/azure-cli
```

## <a name="run-azure-cli-10-commands"></a>Kör kommandon för Azure CLI 1.0
När Azure CLI 1.0 installeras, kör du den **azure** från kommandoraden användargränssnittet (Bash, Terminal, Kommandotolken och så vidare). Till exempel för att köra kommandot help, skriver du följande:

```azurecli
azure help
```

> [!NOTE]
> På vissa Linux-distributioner kan du få ett felmeddelande liknande `/usr/bin/env: ‘node’: No such file or directory`. Det här felet kommer från nya installationer av Node.js som installeras på /usr/bin/nodejs. Åtgärda det genom att skapa en symbolisk länk till /usr/bin/node genom att köra det här kommandot:

```bash
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Om du vill se versionen av Azure CLI 1.0 som du har installerat, skriver du följande:

```azurecli
azure --version
```

Nu är du klar! Åtkomst till alla CLI-kommandona ska fungera med egna resurser, [ansluta till din Azure-prenumeration från Azure CLI](/cli/azure/authenticate-azure-cli).

> [!NOTE]
> När du börjar använda Azure CLI, visas ett meddelande som frågar om du vill att Microsoft ska kunna samla in information om användning. Det är frivilligt att delta. Om du väljer att delta, du kan stoppa när som helst genom att köra `azure telemetry --disable`. Om du vill aktivera delta när som helst köra `azure telemetry --enable`.

## <a name="update-the-cli"></a>Uppdatera CLI
Microsoft släpper ofta uppdaterade versioner av Azure CLI. Installera om CLI med hjälp av installationsprogrammet för operativsystemet eller kör den senaste dockerbehållare. Om du har de senaste Node.js och npm installerad, uppdatera genom att skriva följande (på Linux-distributioner som du kan behöva använda **sudo**).

```bash
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Aktivera flikavslutande
Flikslutförande av CLI-kommandon stöds för Mac- och Linux.

Om du vill aktivera den i zsh, kör du:

```bash
echo '. <(azure --completion)' >> .zshrc
```

Om du vill aktivera den i bash, kör du:

```bash
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Nästa steg
* [Ansluta från CLI på Azure-prenumerationen](/cli/azure/authenticate-azure-cli) att skapa och hantera Azure-resurser.
* Om du vill veta mer om Azure CLI, ladda ned källkoden rapportera problem eller bidra till projektet, finns det [GitHub-lagringsplatsen för Azure CLI](https://github.com/azure/azure-xplat-cli).
* Om du har frågor om hur du använder Azure CLI eller Azure finns i [Azure forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).


[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: /cli/azure/get-started-with-az-cli2
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
