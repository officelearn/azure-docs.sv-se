---
title: Kör kommandon i behållare som körs i Azure Container Instances
description: Lär dig hur köra ett kommando i en behållare som körs i Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: danlep
ms.openlocfilehash: 577e2386c352798bc21a2c78b22726128ac7cf0a
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854096"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Köra ett kommando i en aktiva Azure container-instans

Azure Container Instances stöder ett kommando körs i en behållare som körs. Köra ett kommando i en behållare som du redan har börjat är särskilt användbart under utveckling och felsökning. Den vanligaste tillämpningen av den här funktionen är att starta ett interaktivt gränssnitt så att du kan felsöka problem i en behållare som körs.

## <a name="run-a-command-with-azure-cli"></a>Köra ett kommando med Azure CLI

Köra ett kommando i en behållare som körs med [az container exec] [ az-container-exec] i den [Azure CLI][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Till exempel vill starta ett Bash-gränssnitt i en Nginx-behållare:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

I de exempel på utdata nedan Bash-gränssnittet har startats i en fungerande Linux-behållare, att tillhandahålla en terminal där `ls` körs:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

I det här exemplet öppnas kommandotolk i en fungerande Nanoserver-behållare:

```console
$ az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
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

## <a name="multi-container-groups"></a>Grupper med flera behållare

Om din [behållargruppen](container-instances-container-groups.md) har flera behållare, till exempel en programbehållare och loggning sidvagn, ange namnet på behållaren där du kan köra kommandot med `--container-name`.

Till exempel i behållargruppen *mynginx* är två behållare *nginx-app* och *loggaren*. Starta ett skal i den *nginx-app* behållare:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Begränsningar

Azure Container Instances stöder för närvarande starta en enda process med [az container exec][az-container-exec], och du kan inte skicka kommandoargumenten. Exempel: du kan inte länka kommandon som i `sh -c "echo FOO && echo BAR"`, eller kör `echo FOO`.

## <a name="next-steps"></a>Nästa steg

Läs mer om andra verktyg för felsökning och vanliga distributionsproblem i [felsökning av problem med behållare och distribution i Azure Container Instances](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure