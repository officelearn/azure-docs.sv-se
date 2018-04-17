---
title: Skapa en Azure Service Fabric-behållarapp på Linux | Microsoft Docs
description: I den här snabbstarten skapar du ditt första Linux-behållarprogram i Azure Service Fabric.  Skapa en Docker-avbildning med din app, överför avbildningen till ett behållarregister och skapa och distribuera en Service Fabric-behållarapp.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 1b65f54547821654f35571c55f61074a0106a292
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Snabbstart: Distribuera ett Azure Service Fabric Linux-behållarprogram i Azure
Azure Service Fabric är en plattform för distribuerade system för distribution och hantering av skalbara och tillförlitliga mikrotjänster och behållare. 

Den här snabbstarten visar hur du distribuerar Linux-behållare till ett Service Fabric-kluster. När du är klar har du ett röstningsprogram som består av en frontwebbtjänst i Python och en Redis-serverdel som körs i ett Service Fabric-kluster. Du lär dig också att redundansväxla ett program och skala program i klustret.

![Webbsida för röstningsappen][quickstartpic]

I den här snabbstarten använder du Bash-miljön i Azure Cloud Shell för att köra Service Fabric CLI-kommandona. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om det är första gången du kör Cloud Shell uppmanas du att konfigurera din `clouddrive`-filresurs. Du kan godkänna standardvärdena eller bifoga en befintlig filresurs. Läs mer i [Konfigurera en `clouddrive`-filresurs](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share).

## <a name="get-the-application-package"></a>Hämta programpaketet
För att kunna distribuera behållare till Service Fabric behöver du en uppsättning manifestfiler (programdefinitionen), som beskriver de enskilda behållarna samt programmet.

I Cloud Shell använder du Git för att klona en kopia av programmets definition. Ändra sedan katalog till `Voting`-katalogen i din klon. 

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Skapa ett Service Fabric-kluster
Om du vill distribuera programmet till Azure behöver du ett Service Fabric-kluster som kör programmet. Partkluster är ett enkelt sätt att snabbt skapa ett Service Fabric-kluster. Partkluster är kostnadsfria och tidsbegränsade Service Fabric-kluster som finns i Azure och som körs av Service Fabric-teamet. Du kan använda partkluster till att distribuera program och lära dig mer om plattformen. Klustret använder ett enda självsignerat certifikat för nod-till-nod- och klient-till-nod-säkerhet.

Logga in och ansluta till ett [Linux-kluster](http://aka.ms/tryservicefabric). Hämta PFX-certifikatet till datorn genom att klicka på **PFX**-länken. Klicka på **Viktigt**-länken för att hitta certifikatlösenordet och anvisningar om hur du konfigurerar olika miljöer till att använda certifikatet. Behåll både sidan **Välkommen** och sidan **Viktigt** öppna så att du kan använda några av instruktionerna i följande steg. 

> [!Note]
> Det finns ett begränsat antal tillgängliga partkluster per timme. Om du får ett felmeddelande när du försöker registrera dig för ett partkluster kan du vänta en stund och försöka igen, eller följa stegen i [Skapa ett Service Fabric-kluster i Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) för att skapa ett kluster i din prenumeration. 
> 
>Om du skapar ett eget kluster bör du tänka på att webbtjänsten i klientdelen är konfigurerad till att lyssna på port 80 för inkommande trafik. Se till att den porten är öppen i ditt kluster. (Om du använder ett partkluster är den här porten öppen.)
>

## <a name="configure-your-environment"></a>Konfigurera din miljö
Service Fabric innehåller flera verktyg som du kan använda för att hantera ett kluster och dess program:

- Service Fabric Explorer, ett webbläsarbaserat verktyg.
- Service Fabric CLI (kommandoradsgränssnitt) som körs ovanpå Azure CLI 2.0.
- PowerShell-kommandon. 

I den här snabbstarten använder du Service Fabric-CLI:n i Cloud Shell och Service Fabric Explorer. Följande avsnitt visar hur du installerar certifikatet som krävs för att ansluta till ditt säkra kluster med dessa verktyg.

### <a name="configure-certificate-for-the-service-fabric-cli"></a>Konfigurera certifikat för Service Fabric CLI
Om du vill använda CLI i Cloud Shell måste du ladda upp certifikatets PFX-fil till Cloud Shell och sedan använda den för att skapa en PEM-fil.

1. Om du vill ladda upp certifikatet till din aktuella arbetskatalog i Cloud Shell drar du certifikatets PFX-fil från den mapp där den hämtades på datorn och släpper den i Cloud Shell-fönstret.  

2. Om du vill konvertera PFX-filen till en PEM-fil använder du följande kommando. (För partkluster kan du kopiera ett specifikt kommando till PFX-filen och lösenordet från instruktionerna på sidan **Viktigt**.)

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

### <a name="configure-certificate-for-service-fabric-explorer"></a>Konfigurera certifikat för Service Fabric Explorer
Om du vill använda Service Fabric Explorer måste du importera certifikatets PFX-fil som du hämtade från partklustrets webbplats till certifikatarkivet (Windows eller Mac) eller till själva webbläsaren (Ubuntu). Du behöver lösenordet för den privata nyckeln i PFX, som du kan hämta på sidan **Viktigt**.

Använd den metod som du är mest bekväm med till att importera certifikatet på datorn. Till exempel:

- I Windows: Dubbelklicka på PFX-filen och följ anvisningarna för att installera certifikatet i ditt personliga arkiv `Certificates - Current User\Personal\Certificates`. Du kan också använda PowerShell-kommandot i **Viktigt**-instruktionerna.
- I Mac: Dubbelklicka på PFX-filen och följ anvisningarna för att installera certifikatet i din nyckelring.
- I Ubuntu: Mozilla Firefox är standardwebbläsaren i Ubuntu 16.04. Klicka på menyknappen i det övre högra hörnet i webbläsaren om du vill importera certifikatet till Firefox. Klicka sedan på **Alternativ**. På sidan **Inställningar** använder du sökrutan för att söka efter ”certifikat”. Klicka på **Visa certifikat**, välj fliken **Dina certifikat** klicka på **Importera** och följ anvisningarna för att importera certifikatet.
 
   ![Installera certifikat i Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png) 

## <a name="deploy-the-service-fabric-application"></a>Distribuera Service Fabric-programmet 
1. Anslut till Service Fabric-klustret i Azure med hjälp av CLI:n i Cloud Shell. Slutpunkten är hanteringsslutpunkten för klustret. Du skapade PEM-filen i föregående avsnitt. (För partkluster kan du kopiera ett kommando som är specifikt för din PEM-fil och hanteringsslutpunkt från instruktionerna på **Viktigt**-sidan.)

    ```bash
    sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
    ```

2. Använd installationsskriptet till att kopiera röstningsprogrammets definition till klustret, registrera programtypen och skapa en instans av programmet.

    ```bash
    ./install.sh
    ```

2. Öppna en webbläsare och gå till Service Fabric Explorer-slutpunkten för klustret. Slutpunkten har följande format: https://\<my-azure-service-fabric-cluster-url >: 19080/Explorer, till exempel `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. </br>(För partkluster hittar du Service Fabric Explorer-slutpunkten för klustret på sidan **Välkommen**.) 

3. Expandera noden **Program** för att se att det nu finns en post för röstningsprogramtypen och instansen som du skapade.

    ![Service Fabric Explorer][sfx]

3. Om du vill ansluta till behållaren som körs öppnar du en webbläsare och går till webbadressen för ditt kluster, till exempel `http://linh1x87d1d.westus.cloudapp.azure.com:80`. Nu ska röstningsprogrammet visas i webbläsaren.

    ![Webbsida för röstningsappen][quickstartpic]


> [!NOTE]
> Du kan också distribuera Service Fabric-program med Docker Compose. Till exempel kan följande kommando användas för att distribuera och installera programmet på klustret med Docker Compose.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Redundansväxla en behållare i ett kluster
Service Fabric säkerställer att dina behållarinstanser flyttas automatiskt till andra noder i klustret om ett fel inträffar. Du kan också tomma en nod på behållare och sedan flytta dem till andra noder i klustret. I Service Fabric finns olika sätt att skala dina tjänster på. I följande steg ska du använda Service Fabric Explorer.

Så här redundansväxlar du behållaren på klientsidan:

1. Öppna Service Fabric Explorer i klustret, till exempel `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Klicka på noden **fabric:/Voting/azurevotefront** i trädvyn och expandera partitionsnoden (visas med en GUID). Lägg märke till nodnamnet i trädvyn som visar de noder som behållaren körs på, till exempel `_nodetype_4`.
3. Visa noden **Noder** i trädvyn. Klicka på ellipsen (...) bredvid den nod som kör behållaren.
4. Välj **Starta om** för att starta om noden och bekräfta omstartsåtgärden. Omstarten gör att behållaren växlar över till en annan nod i klustret.

    ![Nodvy i Service Fabric Explorer][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Skala program och tjänster i ett kluster
Service Fabric-tjänster kan enkelt skalas över ett kluster beroende på belastningen på tjänsterna. Du kan skala en tjänst genom att ändra antalet instanser som körs i klustret.

Gör så här om du vill skala frontwebbtjänsten:

1. Öppna Service Fabric Explorer i klustret, till exempel`https://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Klicka på ellipsen (tre punkter) bredvid noden **fabric:/Voting/azurevotefront** i trädvyn och välj alternativet för att **skala tjänsten**.

    ![Skalningstjänsten i Service Fabric Explorer startas][containersquickstartscale]

  Du kan nu välja att skala antalet instanser av frontwebbtjänsten.

3. Ändra antalet till **2** och klicka på **Scale Service** (Skala tjänst).
4. Klicka på noden **fabric:/Voting/azurevotefront** i trädvyn och expandera partitionsnoden (visas med en GUID).

    ![Skalningstjänsten i Service Fabric Explorer har slutförts][containersquickstartscaledone]

    Du kan nu se att tjänsten har två instanser. I trädvyn kan du se vilka noder instanserna körs på.

Med den här enkla hanteringsåtgärden har du dubblerat tillgängliga resurser för bearbetning av användarbelastningen i frontwebbtjänsten. Det är viktigt att förstå att du inte behöver flera instanser av en tjänst för att den ska kunna köras på ett tillförlitligt sätt. Om en tjänst misslyckas ser Service Fabric till att en ny tjänstinstans körs i klustret.

## <a name="clean-up-resources"></a>Rensa resurser
1. Använd avinstallationsskriptet (uninstall.sh) som medföljer mallen för att ta bort programinstansen från klustret och avregistrera programtypen. Det tar lite tid för skriptet att rensa instansen, så installationsskriptet bör inte köras omedelbart efter det här skriptet. Du kan använda Service Fabric Explorer till att avgöra när instansen har tagits bort och programtypen har avregistrerats. 

    ```bash
    ./uninstall.sh
    ```

2. Om du är färdig med ditt kluster kan du ta bort certifikatet från certifikatarkivet. Till exempel:
   - I Windows: Använd [MMC-snapin-modulen Certifikat](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Välj **Mitt användarkonto** när du lägger till snapin-modulen. Gå till `Certificates - Current User\Personal\Certificates` och ta bort certifikatet.
   - I Mac: Använd nyckelringsappen.
   - I Ubuntu: Följ stegen som du använde för att visa certifikat och ta bort certifikatet.

3. Om du inte vill fortsätta att använda Cloud Shell, kan du ta bort lagringskontot som är associerat med det för att undvika kostnader. Stäng Cloud Shell-sessionen. Klicka på det lagringskonto som är kopplat till Cloud Shell i Azure Portal. Klicka sedan på **Ta bort** längst upp på sidan och svara på frågorna.

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du distribuerat ett program för Linux-behållare till ett Service Fabric-kluster i Azure, utfört redundansväxling på programmet och skalat programmet i klustret. Om du vill veta mer om hur man arbetar med Linux-behållare i Service Fabric kan du fortsätta till självstudien för appar i Linux-behållaren.

> [!div class="nextstepaction"]
> [Skapa en app för Linux-behållare](./service-fabric-tutorial-create-container-images.md)


[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
