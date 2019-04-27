---
title: Skapa en Jenkins-server i Azure
description: Installera Jenkins på en virtuell Azure Linux-dator från Jenkins-lösningsmappen och skapa ett Java-exempelprogram.
ms.service: jenkins
keywords: jenkins, azure, devops, portal, virtuell dator, lösningsmall
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 6/7/2017
ms.openlocfilehash: 6bc0d8a1e938f2b8a97cab486d4679bfc445f6fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60642442"
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Skapa en Jenkins-server på en virtuell Azure Linux-dator från Azure Portal

Den här snabbstarten visar hur du installerar [Jenkins](https://jenkins.io) på en virtuell Ubuntu Linux-dator med verktygen och plugin-programmen som är konfigurerade för att fungera med Azure. När du är klar körs en Jenkins-server i Azure och skapar en Java-exempelapp från [GitHub](https://github.com).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration
* Åtkomst till SSH på datorns kommandorad (som Bash-gränssnitt eller [PuTTY](https://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Skapa den virtuella Jenkins-datorn från lösningsmallen
Jenkins har stöd för en modell där Jenkins-servern delegerar arbete till en eller flera agenter så att en enskild Jenkins-installation kan vara värd för ett stort antal projekt eller tillhandahålla olika miljöer som behövs för byggen eller tester. Stegen i det här avsnittet hjälper dig att installera och konfigurera en Jenkins-server på Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>Ansluta till Jenkins

Navigera till din virtuella dator (till exempel http://jenkins2517454.eastus.cloudapp.azure.com/)) i webbläsaren. Jenkins-konsolen går inte att komma åt via osäker HTTP så det medföljer anvisningar på sidan om hur du får åtkomst till Jenkins-konsolen säkert från datorn med hjälp av en SSH-tunnel.

![Låsa upp Jenkins](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Konfigurera tunneln med kommandot `ssh` på sidan från kommandoraden. Detta ersätter `username` med namnet på den virtuella datorns administratörsanvändare som valdes tidigare när den virtuella datorn skapades från lösningsmallen.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

När du har startat tunneln navigerar du till `http://localhost:8080/` på den lokala datorn. 

Skaffa det initiala lösenordet genom att köra följande kommando på kommandoraden när du är ansluten via SSH till den virtuella Jenkins-datorn.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Första gången låser du upp Jenkins-instrumentpanelen med det här initiala lösenordet.

![Låsa upp Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Välj **Install suggested plugins** (Installera föreslagna plugin-program) på nästa sida och skapa en Jenkins-administratörsanvändare för att få åtkomst till Jenkins-instrumentpanelen.

![Jenkins är redo!](./media/install-jenkins-solution-template/jenkins-welcome.png)

Nu kan Jenkins-servern skapa kod.

## <a name="create-your-first-job"></a>Skapa ditt första virtuella jobb

Välj **Skapa nya jobb** från Jenkins-konsolen, använd namnet **mySampleApp** och välj **Freestyle-projekt**. Välj sedan **OK**.

![Skapa ett nytt jobb](./media/install-jenkins-solution-template/jenkins-new-job.png) 

Markera fliken **Source Code Management** (Källkodshantering), aktivera **Git** och ange följande URL i fältet **Repository URL** (Centrallagrets URL): `https://github.com/spring-guides/gs-spring-boot.git`

![Definiera Git-lagringsplatsen](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

Välj fliken **Build** (Bygg) och sedan **Add build step** (Lägg till byggsteg), **Invoke Gradle script** (Anropa Gradle-skript). Välj **Use Gradle Wrapper** (Använd Gradle-omslutning) och ange `complete` i **Wrapper location** (Omslutningsplats) och `build` för **Tasks** (Uppgifter).

![Använda Gradle-omslutning för att bygga](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

Välj **Avancerat** och ange sedan `complete` i fältet **Root Build script** (Root Build-skript). Välj **Spara**.

![Ange avancerade inställningar i steget för att bygga Gradle-omslutning](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Skapa koden

Select **Build Now** (Skapa nu) för att kompilera koden och paketera exempelappen. När din version är klar markerar du länken **Arbetsyta** för projektet.

![Gå till arbetsytan för att hämta JAR-filen från versionen](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

Gå till `complete/build/libs` och kontrollera att `gs-spring-boot-0.1.0.jar` finns där och att din version har skapats. Jenkins-servern är nu klar att skapa egna projekt i Azure.

## <a name="troubleshooting-the-jenkins-solution-template"></a>Felsöka Jenkins-lösningsmallen

Om du stöter på buggar med Jenkins-lösningsmallen kan du rapportera problemet på [Jenkins-GitHub-lagringsplatsen](https://github.com/azure/jenkins/issues).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till virtuella Azure-datorer som Jenkins-agenter](jenkins-azure-vm-agents.md)
