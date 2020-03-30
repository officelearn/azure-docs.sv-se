---
title: 'Interaktiv felsökning: VS-kod & ML-beräkningsinstanser'
titleSuffix: Azure Machine Learning
description: Konfigurera VS-kodfjärrkontroll för att interaktivt felsöka koden med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169745"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Felsök interaktivt på en Azure Machine Learning Compute-instans med VS-kodfjärrkontroll

I den här artikeln får du lära dig hur du konfigurerar Visual Studio Code Remote på en Azure Machine Learning Compute-instans så att du interaktivt kan **felsöka koden** från VS-kod. 

+ En [Azure Machine Learning Compute Instance](concept-compute-instance.md) är en fullständigt hanterad molnbaserad arbetsstation för dataexperter och tillhandahåller funktioner för hantering och företagsberedskap för IT-administratörer. 


+ [Fjärrkontrollen Visual Studio-kod](https://code.visualstudio.com/docs/remote/remote-overview) Med utveckling kan du använda en behållare, en fjärrdator eller Windows Subsystem för Linux (WSL) som en komplett utvecklingsmiljö. 

## <a name="prerequisite"></a>Krav  

På Windows-plattformar måste du [installera en OpenSSH-kompatibel SSH-klient](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) om en sådan inte redan finns. 

> [!Note]
> PuTTY stöds inte i Windows eftersom kommandot ssh måste finnas i sökvägen. 

## <a name="get-ip-and-ssh-port"></a>Hämta IP- och SSH-port 

1. Gå till Azure Machine https://ml.azure.com/Learning studio på .

2. Välj [arbetsyta](concept-workspace.md).
1. Klicka på fliken **Beräkningsinstanser.**
1. I kolumnen **Program-URI** klickar du på **SSH-länken** för den beräkningsinstans som du vill använda som en fjärrberäkning. 
1. I dialogrutan bör du ta del av IP-adressen och SSH-porten. 
1. Spara din privata nyckel till katalogen ~/.ssh/ på den lokala datorn. Till exempel öppna en redigerare för en ny fil och klistra in nyckeln i: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   Den privata nyckeln kommer att se ut ungefär så här:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Ändra behörigheter för filen för att se till att bara du kan läsa filen.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Lägga till instans som värd 

Öppna filen `~/.ssh/config` (Linux) `C:\Users<username>.ssh\config` eller (Windows) i en redigerare och lägg till en ny post som liknar detta:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Här några detaljer om fälten: 

|Field|Beskrivning|
|----|---------|
|Värd|Använd vilken stenografi du vill för beräkningsinstansen |
|Värdnamn|Detta är IP-adressen för beräkningsinstansen |
|Port|Det här är porten som visas i SSH-dialogrutan ovan |
|Användare|Detta måste `azureuser` |
|IdentityFile|Bör peka på filen där du sparade den privata nyckeln |

Nu bör du kunna ssh till din beräkningsinstans med `ssh azmlci1`hjälp av stenografi du använde ovan, . 

## <a name="connect-vs-code-to-the-instance"></a>Anslut VS-kod till instansen 

1. [Installera Visual Studio-kod](https://code.visualstudio.com/).

1. [Installera fjärr-SSH-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Klicka på ikonen Remote-SSH till vänster för att visa dina SSH-konfigurationer.

1. Högerklicka på den SSH-värdkonfiguration som du just skapade.

1. Välj **Anslut till värd i aktuellt fönster**. 

Från och med nu arbetar du helt på beräkningsinstansen och du kan nu redigera, felsöka, använda git, använda tillägg etc. - precis som du kan med din lokala Visual Studio-kod. 

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat Visual Studio Code Remote kan du använda en beräkningsinstans som fjärrberäkning från Visual Studio-kod för att interaktivt felsöka koden. 

[Självstudiekurs: Träna din första ML-modell](tutorial-1st-experiment-sdk-train.md) visar hur du använder en beräkningsinstans med en integrerad anteckningsbok.