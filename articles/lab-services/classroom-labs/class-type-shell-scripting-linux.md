---
title: Konfigurera ett skriptlabb för Linux-skal med Azure Lab Services | Microsoft-dokument
description: Lär dig hur du konfigurerar ett labb för att lära ut skalskript på Linux.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 100a485588c77f6977001dae984b30ebcb1de557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443558"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Skapa ett labb för att lära ut skalskript på Linux
Den här artikeln visar hur du ställer in ett labb för att lära skal skript på Linux. Skript är en användbar del av systemadministrationen som gör det möjligt för administratörer att undvika repetitiva uppgifter. I det här exempelscenariot täcker klassen traditionella bash-skript och förbättrade skript. Förbättrade skript är skript som kombinerar bash-kommandon och Ruby. Den här metoden gör det möjligt för Ruby att skicka data runt och bash kommandon för att interagera med skalet. 

Studenter som tar dessa skript klasser få en Linux virtuell maskin för att lära sig grunderna i Linux, och även bekanta sig med bash skal skript. Linux virtuella maskinen levereras med fjärrskrivbordsåtkomst aktiverad och med [gedit](https://help.gnome.org/users/gedit/stable/) och [Visual Studio Code](https://code.visualstudio.com/) textredigerare installerade.

## <a name="lab-configuration"></a>Labbkonfiguration
För att konfigurera det här labbet behöver du en Azure-prenumeration för att komma igång. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. När du har en Azure-prenumeration kan du antingen skapa ett nytt labbkonto i Azure Lab Services eller använda ett befintligt labbkonto. Se följande självstudie för att skapa ett nytt labbkonto: [Handledning för att konfigurera ett labbkonto](tutorial-setup-lab-account.md).

När du har skapat labbkontot aktiverar du följande inställningar i labbkontot: 

| Inställning av labbkonto | Instruktioner |
| ----------- | ------------ |  
| Marketplace-bilder | Aktivera [Ubuntu Server 18.04 LTS-avbildningen](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) för användning i ditt labbkonto. Mer information finns i [Ange Marketplace-avbildningar som är tillgängliga för labbskapare](specify-marketplace-images.md). | 

Följ [den här självstudien](tutorial-setup-classroom-lab.md) för att skapa ett nytt labb och tillämpa följande inställningar:

| Labbinställningar | Värde/instruktioner | 
| ------------ | ------------------ |
| Storlek på virtuell dator (VM) | Liten  |
| Vm-avbildning | [Ubuntu-server 18,04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| Aktivera anslutning till fjärrskrivbord | Aktivera. <p>Om du aktiverar den här inställningen kan lärare och elever ansluta till sina virtuella datorer med fjärrskrivbord (RDP). Mer information finns i [Aktivera fjärrskrivbord för virtuella Linux-datorer i ett labb i Azure Lab Services](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm). </p>|


## <a name="install-desktop-and-xrdp"></a>Installera skrivbord och xrdp
[Ubuntu Server 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) LTS-avbildningen har inte fjärrskrivbordsserver installerad som standard. Följ instruktionerna i [installera och konfigurera Fjärrskrivbord för att ansluta till en Linux-vm i Azure-artikeln](../../virtual-machines/linux/use-remote-desktop.md) för att installera de paket som behövs på malldatorn för att ansluta via fjärrskrivbordsprotokoll.

## <a name="install-ruby"></a>Installera Ruby
Ruby är ett dynamiskt språk med öppen källkod som kan kombineras med bash-skript. Det här avsnittet `apt-get` visar hur du använder för att installera den senaste versionen av [Ruby](https://www.ruby-lang.org/).

1. Installera uppdateringar genom att köra följande kommandon:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Installera [Ruby](https://www.ruby-lang.org/).  Ruby är ett dynamiskt språk med öppen källkod som kan kombineras med bash-skript. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Installera utvecklingsverktyg
I det här avsnittet visas hur du installerar ett par textredigerare. Gedit är standardtextredigeraren för gnome-skrivbordsmiljön. Den är utformad som en allmän textredigerare. Visual Studio Code är en textredigerare som innehåller stöd för felsökning och källkontrollintegrering.

> [!NOTE]
> Det finns flera olika textredigerare tillgängliga. Visual Studio Code och gedit är bara två exempel.

1. Installera [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Installera [Visual Studio Code](https://code.visualstudio.com/).  Visual Studio-kod kan installeras med Snap Store.  Alternativa installationsalternativ finns i [alternativa nedladdningar av Visual Studio-kod](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    Mallen är nu uppdaterad och har både programmeringsspråk och utvecklingsverktyg som behövs för att slutföra labbet. Mallbilden kan nu publiceras i labbet. Välj knappen **Publicera** på mallsidan om du vill publicera mallen i labbet.  

## <a name="cost"></a>Kostnad 
Om du vill uppskatta kostnaden för det här labbet kan du använda följande exempel:
 
För en klass på 25 elever med 20 timmars schemalagd lektionstid och 10 timmars kvot för läxor eller uppgifter, skulle priset för labbet vara: 

25 studenter * (20 + 10) timmar * 20 lab enheter * 0,01 USD per timme = 150 USD

Mer information om prissättningen finns i följande dokument: [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Slutsats
Den här artikeln gick igenom stegen för att skapa ett labb för skriptklasser. Medan den här artikeln fokuserade på att konfigurera Ruby skriptverktyg på Linux-maskin, kan samma installation användas för andra skriptklasser som Python på Linux.

## <a name="next-steps"></a>Nästa steg
Nästa steg är vanliga för att konfigurera ett labb:

- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users). 





