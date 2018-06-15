---
description: ta med fil
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: include
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: 552e93e9bd1b17c73fb1638fbae2ac30b051c261
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
ms.locfileid: "29957557"
---
1. I webbläsaren och öppna den [Azure Marketplace-avbildning för Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Välj **Hämta nu IT**.

    ![Välj GIT IT nu för att starta installationsprocessen för Jenkins Marketplace-avbildning.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. När du har granskat prisinformation information och villkoren, Välj **Fortsätt**.

    ![Jenkins Marketplace priser och villkor avbildningsinformationen.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Välj **skapa** att konfigurera servern Jenkins i Azure-portalen. 

    ![Installera Jenkins Marketplace-avbildningen.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. I den **grunderna** anger du följande värden:

    - **Namnet** -ange `Jenkins`.
    - **Användarnamnet** -ange ett användarnamn som ska användas när du loggar in på den virtuella datorn som kör Jenkins. Användarnamnet måste uppfylla [specifika krav](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
    - **Autentiseringstypen** – Välj **offentliga SSH-nyckeln**.
    - **Offentlig SSH-nyckel** -kopiera och klistra in en offentlig RSA-nyckel i en rad format (från och med `ssh-rsa`) eller flerradiga PEM-format. Du kan skapa SSH-nycklar med hjälp av ssh-keygen i Linux och macOS eller PuTTYGen i Windows. Mer information om SSH-nycklar och Azure finns i artikeln [så Använd SSH-nycklar med Windows på Azure](/azure/virtual-machines/linux/ssh-from-windows).
    - **Prenumerationen** – Välj Azure-prenumeration som du vill installera Jenkins.
    - **Resursgruppen** – Välj **Skapa nytt**, och ange ett namn för resursgruppen som fungerar som en logisk behållare för insamling av resurser som utgör Jenkins installationen.
    - **Plats** – Välj **östra USA**.

    ![Ange autentiserings- och gruppinformation för Jenkins på fliken Grundläggande.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Välj **OK** för att komma till den **ytterligare inställningar** fliken. 

1. I den **ytterligare inställningar** anger du följande värden:

    - **Storlek** -Välj lämplig storleksalternativ för den virtuella datorn Jenkins.
    - **VM disktyp** – ange antingen Hårddisk (hårddiskenheten) eller SSD (SSD) som anger vilken lagringstyp som disk är tillåten för den virtuella datorn Jenkins.
    - **Virtuellt nätverk** -(valfritt) Välj **för virtuella nätverk** att ändra standardinställningarna.
    - **Undernät** – Välj **undernät**, kontrollerar du informationen och välj **OK**.
    - **Offentliga IP-adressen** -IP-adressnamn som standard Jenkins namnet du angav i föregående sida med suffixet - IP. Du kan välja alternativet för att ändra som standard.
    - **Domännamnet** -ange värdet för den fullständiga URL-Adressen till den virtuella datorn Jenkins.
    - **Jenkins versionstyp** -Markera önskade versionen från alternativen: `LTS`, `Weekly build`, eller `Azure Verified`. Den `LTS` och `Weekly build` alternativ beskrivs i artikel [Jenkins LTS versionen rad](https://jenkins.io/download/lts/). Den `Azure Verified` alternativet refererar till en [Jenkins LTS version](https://jenkins.io/download/lts/) som har verifierats ska köras på Azure. 

    ![Ange inställningar för virtuell dator för Jenkins på fliken Inställningar.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Välj **OK** för att komma till den **inställningar för katalogintegrering** fliken.

1. I den **inställningar för katalogintegrering** anger du följande värden:

    - **Tjänstens huvudnamn** -tjänstens huvudnamn läggs till i Jenkins som autentiseringsuppgifter för autentisering med Azure. `Auto` innebär att objektet kommer att skapas av MSI (hanterade tjänstidentiteten). `Manual` innebär att objektet ska skapas av dig. 
        - **Program-ID** och **hemlighet** - om du väljer den `Manual` för den **tjänstens huvudnamn** alternativet måste du ange den `Application ID` och `Secret` för din tjänstens huvudnamn. När [skapar ett huvudnamn för tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli), Observera att standardroll **deltagare**, vilket är tillräckligt för att arbeta med Azure-resurser.
    - **Aktivera molnet agenter** -ange molnet standardmallen för agenter där `ACI` refererar till Azure-behållare instans och `VM` refererar till virtuella datorer. Du kan också ange `No` om du inte vill aktivera en moln-agenten.

1. Välj **OK** för att komma till den **sammanfattning** fliken.

1. När den **sammanfattning** visar, informationen har verifierats. När du ser den **validering godkänd** meddelande (överst på fliken), Välj **OK**. 

    ![Fliken Sammanfattning visar och verifierar dina valda alternativ.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. När den **skapa** visar, Välj **skapa** att skapa den virtuella datorn Jenkins. När servern är klar, visas ett meddelande i Azure-portalen.

    ![Jenkins är klar meddelande.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)