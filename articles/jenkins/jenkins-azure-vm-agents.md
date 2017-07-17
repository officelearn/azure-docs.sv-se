---
title: "Använda virtuella Azure-agenter för kontinuerlig integrering med Jenkins."
description: Azure VM-agenter som Jenkins slaves.
services: multiple
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 5f2df414b4d0e8798b7ed6d90d0ea0fb79d42fc2
ms.contentlocale: sv-se
ms.lasthandoff: 06/21/2017

---
# Använda virtuella Azure-agenter för kontinuerlig integrering med Jenkins.
<a id="use-azure-vm-agents-for-continuous-integration-with-jenkins" class="xliff"></a>

Den här snabbstarten visar hur du använder plugin-programmet Jenkins Azure VM-agenter för att skapa en agent för Linux (Ubuntu) på begäran i Azure.

## Krav
<a id="prerequisites" class="xliff"></a>

För att slutföra den här snabbstarten behöver du:

* Om du inte redan har en Jenkins-master, kan du starta börja med [Lösningsmallen](install-jenkins-solution-template.md) 
* Se [Skapa huvudsaklig Azure-tjänst med Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) om du inte redan har en huvudsaklig Azure-tjänst.

## Installera Plugin-program för Azure VM-agenter
<a id="install-azure-vm-agents-plugin" class="xliff"></a>

Om du börjar från [Lösningsmallen](install-jenkins-solution-template.md) är plugin-programmet för Azure VM-agenten installerad i Jenkins-master.

I annat fall installerar du plugin-programmet **Azure VM agenter** från Jenkins instrumentpanelen.

## Konfigurera plugin-programmet
<a id="configure-the-plugin" class="xliff"></a>

* I Jenkins-instrumentpanelen klickar du på **Hantera Jenkins -> Konfigurera System ->**. Bläddra till längst ned på sidan och leta reda på avsnittet i listrutan **Lägga till nya molntjänster**. Välj **Microsoft Azure VM agenter** på menyn
* Välj ett befintligt konto i listrutan Azure-autentiseringsuppgifter.  För att lägga till en ny **Microsoft Azure Service Principal** anger du följande värden: prenumerations-ID, klient-ID, Klienthemligheten och OAuth 2.0-Token för slutpunkt.

![Azure-autentiseringsuppgifter](./media/jenkins-azure-vm-agents/service-principal.png)

* Klicka på **verifiera konfigurationen** och kontrollera att profilkonfigurationen är korrekt.
* Spara konfigurationen och fortsätt till nästa steg.

## Mallkonfiguration
<a id="template-configuration" class="xliff"></a>

### Allmän konfiguration
<a id="general-configuration" class="xliff"></a>
Konfigurera en mall som ska användas för att definiera en Azure VM-agent. 

* Klicka på **Lägg till** att lägga till en mall. 
* Ge din nya mall ett nytt namn. 
* För etiketten, anger du ”ubuntu”. Den här etiketten används under jobbkonfigurationen.
* Välj önskad region från kombinationsrutan.
* Välj önskad storlek för den virtuella datorn.
* Ange namnet på Azure Storage-kontot eller lämna det tomt om du vill använda standardnamnet ”jenkinsarmst”.
* Ange tiden för datakvarhållning i minuter. Den här inställningen anger hur många minuter Jenkins kan vänta innan du tar bort en inaktiv agent automatiskt. Ange 0 om du inte vill att inaktiva agenter ska tas bort automatiskt.

![Allmän konfiguration](./media/jenkins-azure-vm-agents/general-config.png)

### Bildkonfiguration
<a id="image-configuration" class="xliff"></a>

Välj **Bildreferens** och använd följande konfiguration som exempel om du vill skapa en agent för Linux (Ubuntu). Se [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) för den senaste bilderna som stöds i Azure.

* Bildutgivare: Canonical
* Bild-erbjudande: UbuntuServer
* Bild-Sku: 14.04.5-LTS
* Bildversion: senaste
* OS-typ: Linux
* Startmetod: SSH
* Uppge administratörsautentiseringsuppgifter
* Ange följande för initieringsskriptet för virtuell dator:
```
# Install Java
sudo apt-get -y update
sudo apt-get install -y openjdk-7-jdk
sudo apt-get -y update --fix-missing
sudo apt-get install -y openjdk-7-jdk
```
![Bildkonfiguration](./media/jenkins-azure-vm-agents/image-config.png)

* Klicka på **Kontrollera mallen** att kontrollera konfigurationen.
* Klicka på **Spara**.

## Skapa ett jobb i Jenkins
<a id="create-a-job-in-jenkins" class="xliff"></a>

* I instrumentpanelen för Jenkins klickar du på **Nytt objekt**. 
* Ange ett namn och välj **Freestyle-projekt** och sedan på **OK**.
* På fliken **Allmänt** markerar du ”Begränsa var projekt kan köras” och typen ”ubuntu” i etikettuttrycket. Nu kan du se ”ubuntu” i listrutan.
* Klicka på **Spara**.

![Ställ in jobb](./media/jenkins-azure-vm-agents/job-config.png)

## Skapa det nya projektet
<a id="build-your-new-project" class="xliff"></a>

* Gå tillbaka till Jenkins-instrumentpanelen.
* Högerklicka på det nya jobbet du skapade och klicka sedan på **Skapa nu**. En version har startats. 
* När versionen har slutförts, går du till **Konsolutdata**. Du ser att versionen skapades via en fjärranslutning på Azure.

![Konsolutdata](./media/jenkins-azure-vm-agents/console-output.png)

## Referens
<a id="reference" class="xliff"></a>

* Azure Friday-video: [kontinuerlig integration med Jenkins med virtuella Azure-agenter](https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents)
* Supportalternativ och konfigurationsinformation: [Azure VM-agent Jenkins Plugin Wiki](https://wiki.jenkins-ci.org/display/JENKINS/Azure+VM+Agents+Plugin) 


