---
title: Kör kommandon i behållare som körs i Azure Container instanser
description: Lär dig hur köra ett kommando i en behållare som för närvarande körs i Azure Container instanser
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.openlocfilehash: 43211f620efb16cbcd722d3d386b1bb862fc6280
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Köra ett kommando i en instans av körs Azure-behållaren

Azure Container-instanser har stöd för ett kommando körs i en behållare som körs. Ett kommando körs i en behållare som du redan har börjat är särskilt användbart vid utveckling av program och felsökning. Den vanligaste användningen av den här funktionen är att starta en interaktiv shell så att du kan felsöka problem i en behållare som körs.

## <a name="run-a-command-with-azure-cli"></a>Köra ett kommando med Azure CLI

Köra ett kommando i en behållare som körs med [az behållaren exec] [ az-container-exec] i den [Azure CLI][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Till exempel att starta ett Bash-gränssnitt i en Nginx-behållare:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

I de exempel på utdata nedan Bash-gränssnitt har startats i en behållare för körs Linux, vilket ger en terminal där `ls` körs:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

I det här exemplet startas Kommandotolken i en pågående Nanoserver-behållare:

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

Om din [behållargruppen](container-instances-container-groups.md) har flera behållare, till exempel en behållare för program och loggning sidvagn, ange namnet på behållaren där du kör kommandot med `--container-name`.

Till exempel i behållargruppen *mynginx* är två behållare *nginx-app* och *loggaren*. Starta ett skal i den *nginx-app* behållare:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Begränsningar

Azure Behållarinstanser stöder för närvarande starta en process med [az behållaren exec][az-container-exec], och du kan inte skicka kommandoargumenten. Du kan till exempel kedja kommandon som i `sh -c "echo FOO && echo BAR"`, eller kör `echo FOO`.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra verktyg för felsökning och vanliga distributionsproblem med i [felsökning av behållaren och distribution problem i Azure Behållarinstanser](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure