---
title: Snabbstart för Azure – Skapa Azure Batch AI-kluster – portalen | Microsoft Docs
description: Snabbstart – Skapa ett Azure Batch AI-kluster för träning med maskininlärning och AI-modeller – Azure-portalen
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: danlep
ms.openlocfilehash: 8b9daa0fbbf84e0f602498a0847c9e120f709b17
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057940"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-portal"></a>Snabbstart: Skapa ett kluster för Azure Batch AI-träningsjobb med Azure-portalen

Den här snabbstarten visar hur du använder Azure-portalen för att skapa ett Batch AI-kluster som du kan använda för att träna modeller med AI och maskininlärning. Azure Batch AI är en hanterad tjänst som datatekniker och AI-forskare kan använda för att träna modeller med AI och maskininlärning i stor skala på kluster av virtuella Azure-datorer.

Klustret har inledningsvis en enda GPU-nod och en ansluten filserver. När du har slutfört den här snabbstarten har du ett kluster som du kan skala upp och använda för att träna dina djupinlärningsmodeller. Skicka träningsjobb till klustret med Batch AI, [Azure Machine Learning-verktyg](../machine-learning/service/overview-what-is-azure-ml.md) eller [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-ssh-key-pair"></a>Skapa SSH-nyckelpar

Du behöver ett SSH-nyckelpar för att slutföra den här snabbstarten. Om du har ett befintligt SSH-nyckelpar kan du hoppa över det här steget.

Skapa ett SSH-nyckelpar genom att köra följande kommando från ett Bash-gränssnitt och följa instruktionerna på skärmen. Du kan till exempel använda [Azure Cloud Shell](../cloud-shell/overview.md) eller [Windows delsystemet för Linux](/windows/wsl/install-win10) (om du använder Windows). Kommandoutdata innehåller filnamnet för den offentliga nyckeln. Kopiera innehållet i den offentliga nyckelfilen (`cat ~/.ssh/id_rsa.pub`) till Urklipp eller annan plats som du kan komma åt i ett senare steg.

```bash
ssh-keygen -t rsa -b 2048
```

Mer information om hur du skapar ett SSH-nyckelpar finns i [Skapa ett offentligt och ett privat SSH-nyckelpar för virtuella Linux-datorer i Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-batch-ai-workspace"></a>Skapa en Azure Batch AI-arbetsyta

Börja med att skapa en Batch AI-arbetsyta för att organisera dina Batch AI-resurser. En arbetsyta kan innehålla ett eller flera kluster eller andra Batch AI-resurser.

1. Välj **Alla tjänster** och filtrera efter **Batch AI**.

2. Välj **Lägg till arbetsyta**.

3. Ange värden för **Namn på arbetsyta** och **Resursgrupp**. Om du vill kan du välja olika alternativ för **Prenumeration** och **Plats** för arbetsytan. Välj **Skapa arbetsyta**.

  ![Skapa en Batch AI-arbetsyta](./media/quickstart-create-cluster-portal/create-workspace.png)

När meddelandet **Distribueringen lyckades** visas kan du gå till den resurs som du skapade och välja arbetsytan.

## <a name="create-a-file-server"></a>Skapa en filserver

En Batch AI-filserver är ett NFS-filsystem med en enda nod som automatiskt kan monteras på klusternoder. Det här är bara ett av flera olika sätt att tillhandahålla lagring för indata och utdata från dina träningsjobb.

1. Gå till arbetsytan och välj **Filserver** > **Add Batch AI file server** (Lägg till en Batch AI-filserver).

2. Ange värden för **Filservernamn** och **Storlek på virtuell dator**. I den här snabbstarten föreslår vi att du använder VM-storleken *Standard D1_v2* för filservern. Välj en annan storlek om du behöver lagra större mängder indata eller utdata för träningsjobb.

3. Ange **administratörens användarnamn** och kopiera innehållet i din offentliga SSH-nyckelfil till **SSH-nyckeln**. Godkänn standardinställningarna för de återstående värdena och välj **Skapa filserver**.

  ![Skapa en Azure Batch AI-filserver](./media/quickstart-create-cluster-portal/create-file-server.png)

Det tar några minuter att distribuera filservern.

När servern har skapats klickar du på **Egenskaper** och antecknar **monteringsinställningarna**. Du kan använda SSH för att ansluta till den offentliga IP-adressen för servern och ladda upp/ned träningsdata och utdatafiler till/från den angivna katalogen (*/data*).

![Filserveregenskaper](./media/quickstart-create-cluster-portal/file-server-properties.png)

## <a name="create-a-cluster"></a>Skapa ett kluster

Med följande steg kan du skapa ett kluster med en enda GPU-nod. Klusternoden kör en standardversion av en Ubuntu Server-avbildning som utformats för att vara värd för containerbaserade program, som du kan använda för de flesta arbetsbelastningar vid träning. Klusternoden monterar filservern på dess monteringspunkt. 

1. På arbetsytan för Batch AI väljer du **Kluster** > **Add Batch AI cluster** (Lägg till Batch AI-kluster).

2. Ange värden för **Klusternamn** och följande inställningar. Föreslagen storlek på den virtuella datorn är NVIDIA Tesla K80 GPU.
  
   |Inställning  |Värde  |
   |---------|---------|
   |**Storlek på virtuell dator**     |Standard NC6|
   |**Antalet målnoder**     |1|

3. Ange **administratörens användarnamn** och kopiera innehållet i din offentliga SSH-nyckelfil till **SSH-nyckeln**. Godkänn standardinställningarna för resten av värdena på den här sidan och välj **Nästa: Nodkonfiguration**.

   ![Ange grundläggande klusterinformation](./media/quickstart-create-cluster-portal/create-cluster.png)

4. Under **Monteringsvolymer** väljer du **Filserverreferenser** > **Lägg till**. Markera den filserver som du skapade i ett tidigare steg. Ange en **Relativ monteringssökväg** för att ange var servern är monterad på varje klusternod. Välj **Spara och fortsätt**.

   ![Lägg till filserverreferens](./media/quickstart-create-cluster-portal/file-server-reference.png)

Spara nodkonfigurationen och välj **Skapa kluster**.

Det tar några minuter för Azure Batch AI att allokera noden. Under denna tid håller klustrets **Allokeringstillstånd** på med att **ändra storlek**. Efter några minuter är tillståndet för klustret **Konstant** och noderna startar.

![Klustret startas](./media/quickstart-create-cluster-portal/cluster-resizing.png)

Välj namnet på klustret för att kontrollera tillståndet för noden. När en nods tillstånd anges som **Inaktiv** är den redo att köra träningsjobb.

### <a name="list-cluster-nodes"></a>Visa en lista över klusternoder

Om du vill ansluta till klusternoderna (i det här fallet en enskild nod) för att installera program eller utföra underhåll så får du anslutningsinformation via portalen. När klustret har skapats klickar du på **Noder** och antecknar inställningarna för **SSH-anslutningen** (IP-adress och portnummer).

![Klusternoder](./media/quickstart-create-cluster-portal/cluster-nodes.png)

Använd informationen för att upprätta en SSH-anslutning till noden. Använd följande kommando och ersätt värdena med rätt IP-adress och portnummer för din nod:

```bash
ssh myusername@137.135.82.15 -p 50000
``` 

### <a name="resize-the-cluster"></a>Ändra storlek på klustret

När du använder ditt kluster för att träna en modell kanske du behöver fler beräkningsresurser. Om du vill öka storleken till 2 noder för ett distribuerat träningsjobb väljer du exempelvis **Skala** och anger **Target number of node** (Önskat antal noder) till 2. Spara konfigurationen.

![Skala ett kluster](./media/quickstart-create-cluster-portal/scale-cluster.png)

Det tar några minuter att ändra storleken på klustret.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta med Azure Batch AI-självstudier och -exempel ska du använda Batch AI-arbetsytan, filservern och det kluster som skapats i denna snabbstart.

Du debiteras för Batch AI-kluster och filservern så länge som de underliggande virtuella datorerna körs, även om inga jobb är schemalagda. Om du vill behålla klusterkonfigurationen och inte har några jobb att köra bör du ändra storleken på klustret till 0 noder. Senare, när du har jobb att köra, kan du byta till 1 eller flera noder. 

När Batch AI-arbetsytan som innehåller klustret och filservern inte längre behövs kan du ta bort den. Gör detta genom att markera Batch AI-arbetsytan och välja **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Batch AI-kluster med tillhörande filserver via Azure-portalen. Om du vill ha mer information om hur du använder Batch AI-kluster för att träna en modell fortsätter du till snabbstarten för att träna en modell för djupinlärning.

> [!div class="nextstepaction"]
> [Träna en modell för djupinlärning](./quickstart-tensorflow-training-cli.md)
