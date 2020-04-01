---
title: Självstudiekurs - Skapa en Jenkins-server på Azure
description: I den här självstudien installerar du Jenkins på en virtuell Azure Linux-dator från Jenkins-lösningsmallen och skapar ett exempel på Java-program.
keywords: jenkins, azure, devops, portal, virtuell dator, lösningsmall
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274902"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Självstudiekurs: Skapa en Jenkins-server på en virtuell Azure Linux-dator 

Den här självstudien visar hur du installerar [Jenkins](https://jenkins.io) på en Ubuntu Linux-virtuell dator med de verktyg och plugin-program som konfigurerats för att fungera med Azure. När du är klar körs en Jenkins-server i Azure och skapar en Java-exempelapp från [GitHub](https://github.com).

> [!div class="checklist"]
> * Installera och konfigurera en Jenkins-server på Azure
> * Komma åt Jenkins-konsolen med en SSH-tunnel
> * Skapa ett Freestyle-projekt
> * Kompilera koden och paketera exempelappen

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]