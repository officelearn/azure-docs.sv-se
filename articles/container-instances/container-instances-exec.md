---
title: Köra kommandon i behållarinstans som körs
description: Lär dig hur du kör ett kommando i en behållare som körs i Azure Container Instances
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: de48e6ac246e2b0751561b4c60bb63d88b599bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247207"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Köra ett kommando i en azure-behållarinstans som körs

Azure Container Instances har stöd för att köra kommandon i aktiva containers. Under apputveckling och felsökning är det särskilt användbart att kunna köra kommandon i containers som redan har startats. Den här funktionen används oftast till att starta ett interaktivt gränssnitt så att du kan felsöka problem i en container som körs.

## <a name="run-a-command-with-azure-cli"></a>Kör ett kommando med Azure CLI

Kör ett kommando i en behållare som körs med [az-behållar exec][az-container-exec] i [Azure CLI:][azure-cli]

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Om du till exempel vill starta ett Bash-skal i en Nginx-behållare:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

I exemplet nedan lanseras Bash-skalet i en Linux-behållare som `ls` körs och tillhandahåller en terminal som körs:

```output
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

I det här exemplet startas Kommandotolken i en nanoserverbehållare som körs:

```azurecli
az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
```

```output
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Grupper med flera container

Om [behållargruppen](container-instances-container-groups.md) har flera behållare, till exempel en programbehållare och en loggningsvagn, anger `--container-name`du namnet på den behållare där kommandot ska köras med .

I behållargruppen finns till exempel *mynginx* två behållare, *nginx-app* och *logger*. Så här startar du ett skal på *nginx-app-behållaren:*

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Begränsningar

Azure Container Instances stöder för närvarande att starta en enda process med [az container exec][az-container-exec]och du kan inte skicka kommandoargument. Du kan till exempel inte `sh -c "echo FOO && echo BAR"`kedja kommandon `echo FOO`som i eller köra .

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra felsökningsverktyg och vanliga distributionsproblem i [Felsöka behållare och distributionsproblem i Azure Container Instances](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure