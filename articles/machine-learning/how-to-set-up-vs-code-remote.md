---
title: 'Interaktiv fel sökning: VS Code & ML Compute instances'
titleSuffix: Azure Machine Learning
description: Konfigurera VS Code Remote för att interaktivt felsöka din kod med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 19d4b6e7994450eacacab51e9a01cbf58eab803a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84433132"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Felsöka interaktivt på en Azure Machine Learning beräknings instans med VS Code Remote

I den här artikeln får du lära dig hur du konfigurerar Visual Studio Code-fjärrhantering på en Azure Machine Learning beräknings instans så att du **interaktivt kan felsöka koden** från vs Code. 

+ En [Azure Machine Learning beräknings instans](concept-compute-instance.md) är en fullständigt hanterad molnbaserad arbets station för data forskare och innehåller funktioner för hantering och företags beredskap för IT-administratörer. 


+ [Visual Studio Code-fjärrkontroll](https://code.visualstudio.com/docs/remote/remote-overview) Med utveckling kan du använda en behållare, fjärrdator eller Windows-undersystemet för Linux (WSL) som en komplett utvecklings miljö. 

## <a name="prerequisite"></a>Förutsättning  

På Windows-plattformar måste du [installera en OpenSSH-kompatibel SSH-klient](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) om en sådan inte redan finns. 

> [!Note]
> SparaTillFil stöds inte i Windows eftersom SSH-kommandot måste finnas i sökvägen. 

## <a name="get-ip-and-ssh-port"></a>Hämta IP-och SSH-port 

1. Gå till Azure Machine Learning Studio på https://ml.azure.com/ .

2. Välj din [arbets yta](concept-workspace.md).
1. Klicka på fliken **beräknings instanser** .
1. I kolumnen **program-URI** klickar du på **SSH** -länken för den beräknings instans som du vill använda som fjärrberäkning. 
1. Anteckna IP-adressen och SSH-porten i dialog rutan. 
1. Spara din privata nyckel i katalogen ~/.ssh/på den lokala datorn. öppna exempelvis en redigerare för en ny fil och klistra in nyckeln i: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   Den privata nyckeln ser ut ungefär så här:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Ändra fil behörigheter för att se till att endast du kan läsa filen.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Lägg till instans som värd 

Öppna filen `~/.ssh/config` (Linux) eller `C:\Users<username>.ssh\config` (Windows) i en redigerare och Lägg till en ny post liknande detta:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Här finns information om fälten: 

|Fält|Beskrivning|
|----|---------|
|Värd|Använd den stenografiska du gillar för beräknings instansen |
|Värdnamn|Detta är beräknings instansens IP-adress |
|Port|Detta är porten som visas i dialog rutan SSH ovan |
|Användare|Detta måste vara `azureuser` |
|IdentityFile|Ska peka på filen där du sparade den privata nyckeln |

Nu bör du kunna använda SSH till din beräknings instans med hjälp av den kort skrift som du använde ovan `ssh azmlci1` . 

## <a name="connect-vs-code-to-the-instance"></a>Ansluta VS-kod till instansen 

1. [Installera Visual Studio Code](https://code.visualstudio.com/).

1. [Installera fjärr-SSH-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Klicka på fjärr-SSH-ikonen till vänster för att visa dina SSH-konfigurationer.

1. Högerklicka på den SSH-värd konfiguration som du nyss skapade.

1. Välj **Anslut till värd i aktuellt fönster**. 

Härifrån är du helt igång med beräknings instansen och nu kan du redigera, felsöka, använda Git, använda tillägg osv. – precis som du kan med din lokala Visual Studio-kod. 

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat Visual Studio Code-fjärrhantering kan du använda en beräknings instans som fjärrberäkning från Visual Studio Code för att interaktivt felsöka din kod. 

[Självstudie: träna din första ml-modell](tutorial-1st-experiment-sdk-train.md) visar hur du använder en beräknings instans med en integrerad antecknings bok.