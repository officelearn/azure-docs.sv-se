---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 40cc1856a5e943ca5596e7d11712febadd30e3ec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67133516"
---
## <a name="prerequisites"></a>Krav

### <a name="get-sample-code"></a>Hämta exempelkod

Självstudien förutsätter att du redan har slutfört stegen i [föregående självstudie](../articles/container-registry/container-registry-tutorial-quick-task.md), samt att du har förgrenat och klonat exempellagringsplatsen. Om du inte har gjort det, slutför du stegen i avsnittet [Krav](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) i föregående självstudie innan du fortsätter.

### <a name="container-registry"></a>Containerregister

Du måste ha ett Azure-containerregister i din Azure-prenumeration för att kunna slutföra den här självstudien. Om du behöver ett register kan du gå till [föregående självstudie](../articles/container-registry/container-registry-tutorial-quick-task.md) eller [Snabbstart: Skapa ett containerregister med hjälp av Azure CLI](../articles/container-registry/container-registry-get-started-azure-cli.md).

## <a name="create-a-github-personal-access-token"></a>Skapa en personlig åtkomsttoken för GitHub

För att utlösa en uppgift för att binda till en Git-databas behöver ACR-uppgifter en personlig åtkomsttoken (PAT) för att komma åt databasen. Om du inte redan har en PAT följer du dessa steg för att generera en i GitHub:

1. Gå till sidan där personliga åtkomsttokens skapas i GitHub på https://github.com/settings/tokens/new
1. Ange en kort **beskrivning** för din token, till exempel ”ACR Tasks Demo”
1. Välj scope för ACR för att komma åt repo. För att komma åt en offentlig repo som i den här självstudien, under **repo,** aktivera **repo:status** och **public_repo**

   ![Skärmbild av sidan Personlig åtkomsttoken i GitHub][build-task-01-new-token]

   > [!NOTE]
   > Om du vill generera en PAT för att komma åt en *privat* repa väljer du omfånget för fullständig **repakontroll.**

1. Välj knappen **Generera token** (du kan behöva bekräfta lösenordet)
1. Kopiera och spara din genererade token på en **säker plats** (du använder denna token när du definierar en uppgift i följande avsnitt)

   ![Skärmbild av genererad personlig åtkomsttoken i GitHub][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
