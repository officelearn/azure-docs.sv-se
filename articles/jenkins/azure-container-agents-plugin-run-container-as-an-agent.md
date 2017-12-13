---
title: "Skapa ett projekt i Azure med hjälp av Jenkins och instanser för Azure-behållare"
description: "Lär dig att använda plugin-program för Azure-behållare agenten för Jenkins för att skapa ett projekt i Azure med Azure Container instanser"
services: multiple
documentationcenter: 
author: tomarcher
manager: rloutlaw
editor: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 269e936cb79ba4138285f5dbd326413d70d5924d
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>Skapa ett projekt i Azure med hjälp av Jenkins och instanser för Azure-behållare

Azure Behållarinstanser som gör det enkelt för dig att komma igång och körs utan att behöva etablera virtuella datorer eller anta en högre nivå tjänst. Behållarinstanser som Azure tillhandahåller per sekund fakturering baserat på den kapacitet som du behöver. att det är ett bra alternativ för tillfälliga arbetsbelastningar som till exempel utför en version.

Lär dig att:
> [!div class="checklist"]
> * Installera och konfigurera en Jenkins server på Azure
> * Installera och konfigurera Azure-behållare agenter plugin-programmet för Jenkins
> * Använd Azure Container instanser för att skapa den [Vårversionen PetClinic exempelprogrammet](https://github.com/spring-projects/spring-petclinic)

## <a name="prerequisites"></a>Krav

- **Azure-prenumeration** – du lär dig mer om köpalternativ för Azure finns [köpa Azure](https://azure.microsoft.com/pricing/purchase-options/) eller [kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

- **Azure CLI 2.0 eller Azure Cloud Shell** -installera en av följande produkter där du vill ange Azure-kommandon:

    - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) -gör att du kan köra Azure kommandon från ett kommando eller ett terminalfönster.
    - [Azure Cloud Shell](/azure/cloud-shell/quickstart.md) - webbläsarbaserad upplevelse med skal. Molnet Shell ger åtkomst till ett webbaserat kommandoradsverktyget erfarenheterna med Azure-hanteringsuppgifter i åtanke.

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>Installera en Jenkins server på Azure med hjälp av Jenkins Marketplace-avbildning

Jenkins stöder en modell där Jenkins server delegater arbetar för att en eller flera agenter så att en enda Jenkins installation som värd för ett stort antal projekt eller att tillhandahålla olika miljöer som behövs för bygger eller tester. Stegen i det här avsnittet hjälper dig att installera och konfigurera en Jenkins server på Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>Ansluta till Jenkins-servern körs på Azure

När du har installerat Jenkins på Azure, måste du ansluta till Jenkins. Följande steg beskriver hur du konfigurerar en SSH-anslutning för Jenkins VM som körs på Azure. 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>Uppdatera Jenkins DNS

Jenkins måste känna till sin egen URL när det är att skapa länkar som pekar till sig själv. URL: en måste till exempel som ska användas när Jenkins skickar e-postmeddelanden som innehåller direktlänkar för att skapa resultat. 

Det här avsnittet vägleder dig genom inställningen Jenkins-URL: en.

1. I din webbläsare, navigerar du till Jenkins instrumentpanelen på `http://localhost:8080`.

1. Välj **hantera Jenkins**.

    ![Hantera Jenkins alternativ i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Välj **konfigurera systemet**.

    ![Hantera Jenkins plugin-program för alternativet i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. Under **Jenkins plats**, ange Webbadressen till servern Jenkins.

1. Välj **Spara**.

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>Uppdatera Jenkins för att tillåta Java nätverket starta Protocol (JNLP)

Jenkins agenten ansluter till servern via Java nätverket starta Protocol (JNLP) Jenkins. Det här avsnittet beskrivs hur du anger en port för JNLP agenter som ska användas vid kommunikation med servern Jenkins.

1. I instrumentpanelen för Jenkins Välj **hantera Jenkins**.

    ![Hantera Jenkins alternativ i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Välj **konfigurera globala säkerhet**.

    ![Konfigurera globala säkerhet i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. Under **agenter**väljer **fast**, och ange en port. Skärmbild som visar Portvärdet 12345 som exempel. Du måste ange en port som passar för din miljö.

    ![Uppdatera Jenkins globala inställningar så att JNLP](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. Välj **Spara**.

1. Använder Azure CLI 2.0 eller molnet Shell, ange följande kommando för att skapa en inkommande regel för din Jenkins nätverkssäkerhetsgruppen:

    ```azurecli
    az network nsg rule create  \
    --resource-group JenkinsResourceGroup \
    --nsg-name JenkinsNSG  \
    --name allow-https \
    --description "Allow access to port 12345 for HTTPS" \
    --access Allow \
    --protocol Tcp  \
    --direction Inbound \
    --priority 102 \
    --source-address-prefix "*"  \
    --source-port-range "*"  \
    --destination-address-prefix "*" \
    --destination-port-range "12345"
    ```

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>Skapa och Lägg till en Azure-tjänstens huvudnamn Jenkins autentiseringsuppgifter

Du behöver ett Azure tjänstens huvudnamn för att distribuera till Azure. Följande steg leder dig genom processen att skapa ett huvudnamn för tjänsten (om du inte redan har ett) och uppdatera Jenkins med tjänstens huvudnamn.

1. Om du redan har ett huvudnamn för tjänsten (och känner till dess prenumerations-ID, klient, appId och lösenord), kan du hoppa över det här steget. Om du behöver skapa ett säkerhetsobjekt finns i artikel [skapa en Azure tjänstens huvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). När du skapar objektet anteckna värdena för prenumerations-ID, klient, appId och lösenord.

1. Välj **autentiseringsuppgifter**.

    ![Hantera inloggningsinformation i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. Under **autentiseringsuppgifter**väljer **System**.

    ![Hantera system inloggningsinformation i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. Välj **globala autentiseringsuppgifter (obegränsad)**.

    ![Hantera globala systemet inloggningsinformation i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. Välj **lägger till vissa autentiseringsuppgifter**.

    ![Lägga till autentiseringsuppgifter i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. I den **typ** listrutan, Välj **Microsoft Azure Service Principal** kan orsaka att visa fält att lägga till ett huvudnamn för tjänsten. Ange sedan de begärda värdena på följande sätt:

    - **Scope** -väljer du alternativet för **Global (Jenkins noder, artiklar, alla underordnade objekt, osv.)** .
    - **Prenumerations-ID** -Använd Azure prenumerations-ID du angav när du kör `az account set`.
    - **Klient-ID** – Använd den `appId` värdet som returneras från `az ad sp create-for-rbac`.
    - **Klienthemlighet** – Använd den `password` värdet som returneras från `az ad sp create-for-rbac`.
    - **Klient-ID** – Använd den `tenant` värdet som returneras från `az ad sp create-for-rbac`.
    - **Azure-miljön** – Välj `Azure`.
    - **ID** -ange `myTestSp`. Det här värdet används senare i den här självstudiekursen.
    - **Beskrivning** – (valfritt) Ange ett beskrivningsvärde för detta säkerhetsobjekt.

    ![Ange nya service principal egenskaper i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. När du har angett informationen definiera objektet, du kan också välja **Kontrollera tjänstens huvudnamn** så att allt fungerar korrekt. Om din tjänstens huvudnamn är korrekt definierad, visas ett meddelande om, ”verifierats Microsoft Azure-tjänstens huvudnamn”. under den **beskrivning** fältet.

1. När du är klar väljer du **OK** att lägga till objektet i Jenkins. Jenkins instrumentpanelen visar nyligen tillagda objektet på den **globala autentiseringsuppgifter** sidan.

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>Skapa en Azure-resursgrupp för dina Azure-Behållarinstanser

Azure Container-instanser måste placeras i en Azure-resursgrupp. En Azure-resursgrupp är en behållare som innehåller relaterade resurser för en Azure-lösning.

Med hjälp av Azure CLI 2.0 eller molnet Shell, ange följande kommando för att skapa en resursgrupp med namnet `JenkinsAciResourceGroup` plats `eastus`:

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

När du är klar på `az group create` kommando visar resultat som liknar följande exempel:

```JSON
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/JenkinsAciResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "JenkinsAciResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>Installera Azure-behållare agenter plugin-programmet för Jenkins

Om du redan har installerat Azure Container agenter plugin-programmet kan du hoppa över det här avsnittet.

När du har Azure-resursgrupp som skapats för din Jenkins agent visas nedan hur du installerar Azure Container agenter plugin-programmet:

1. I instrumentpanelen för Jenkins Välj **hantera Jenkins**.

    ![Hantera Jenkins alternativ i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Välj **hantera plugin-program**.

    ![Hantera Jenkins plugin-program för alternativet i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. Välj **tillgängliga**.

    ![Visa tillgängliga Jenkins plugin-program för alternativet i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. Ange `Azure Container Agents` till den **Filter** textruta. (Listan filter som du skriver texten.)

    ![Filtrera de tillgängliga Jenkins plugin-program i instrumentpanelen för Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. Markera kryssrutan bredvid den **Azure Container agenter** plugin-programmet, och ett av alternativen för installation. Jag har valt för den här demon skall den **installera utan att starta om** alternativet.

    ![Installera plugin-program för Azure-behållare agenter från Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  När du har markerat alternativet att installera den önskade plugin(s) visar Jenkins instrumentpanelen en sida med information om status för hur du installerar.

    ![Installationsstatus för att installera plugin-program för Azure-behållare agenter från Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    Om du vill returnera huvudsidan för Jenkins instrumentpanelen, Välj **gå tillbaka till sidan översta**.

## <a name="configure-the-azure-container-agents-plugin"></a>Konfigurera plugin-program för Azure-behållare agenter

När Azure Container agenter plugin-programmet har installerats hjälper i det här avsnittet dig att konfigurera plugin-programmet i instrumentpanelen Jenkins.

1. I instrumentpanelen för Jenkins Välj **hantera Jenkins**.

    ![Hantera Jenkins alternativ i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Välj **konfigurera systemet**.

    ![Hantera Jenkins plugin-program för alternativet i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. Leta upp den **moln** avsnittet längst ned på sidan och från den **lägga till ett nytt moln** listrutan, Välj **Azure Container instans**.

    ![Lägg till en ny molntjänstleverantör från Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. I den **Azure Container instans** och ange följande värden:

    - **Molnnamn** – (valfritt som standardvärdet är ett automatiskt genererat namn.) Ange ett namn för den här instansen. 
    - **Azure autentiseringsuppgifter** – Välj den nedrullningsbara pilen och välj sedan den `myTestSp` post som identifierar Azure tjänstens huvudnamn du skapade tidigare.
    - **Resursgruppen** – Välj den nedrullningsbara pilen och välj sedan den `JenkinsAciResourceGroup` post som identifierar Azure Container instans resursgruppen du skapade tidigare.

    ![Definierar egenskaperna Azure Container-förekomst](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. Välj den **lägga till behållaren mallen** nedrullningsbara pilen och väljer sedan **Aci behållare mallen**.

1. I den **Aci behållare mallen** och ange följande värden:

    - **Namnet** -ange `ACI-container`.
    - **Etiketter** -ange `ACI-container`.
    - **Bild av docker** -ange`cloudbees/jnlp-slave-with-java-build-tools`

    ![Definiera Bildegenskaper Azure Container-förekomst](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. Välj **avancerade**.

1. Välj den **kvarhållning strategi** nedrullningsbara pilen och väljer **behållare inaktiv kvarhållning strategi**. Genom att välja det här alternativet behåller Jenkins agenten tills inga nya jobb körs på agenten och den inaktiva tiden har gått.

    ![Definierar strategin för Azure-behållare instans kvarhållning](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. Välj **Spara**.

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>Skapa det värdet PetClinic jobbet i Jenkins

Följande steg hur du skapar ett jobb för Jenkins - som ett freestyle projekt - att bygga vår PetClinic appen.

1. I instrumentpanelen för Jenkins Välj **nytt objekt**.

    ![Menyalternativet nytt objekt i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. Ange `myPetClinicProject` för namn och välj **Freestyle projekt**.

    ![Nytt freestyle projekt i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. Välj **OK**.

1. Välj den **allmänna** och ange följande värden:

    - **Begränsa där du kan köra project** – Välj det här alternativet.
    - **Etikett uttrycket** -ange `ACI-container`. När du lämnar fältet visar ett meddelande som bekräftar att etiketten hanteras av konfigurationen för molnet skapade i föregående steg.

    ![Allmänna inställningar för ett nytt freestyle projekt i Jenkins instrumentpanelen](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. Välj den **källa kod Management** och ange följande värden:

    - **Datakällan kod Management** – Välj **Git**.
    - **Databasen URL** -ange följande URL för vår PetClinic exempel programmet GitHub-repo: `https://github.com/spring-projects/spring-petclinic.git`.

1. Välj den **skapa** fliken och utföra följande uppgifter:

    - Välj den **Lägg till build steg** nedrullningsbara pilen och väljer **anropa översta Maven mål**.

    - För **mål**, ange `package`.

1. Välj **spara** att spara den nya projekt-definitionen.

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>Skapa källan PetClinic programmet jobbet i Jenkins

Det är dags att bygga projektet! Det här avsnittet beskrivs hur du skapar ett projekt från instrumentpanelen Jenkins.

1. I instrumentpanelen för Jenkins Välj `myPetClinicProject`.

    ![Välj projektet för att skapa från instrumentpanelen Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. Välj **skapa nu**. 

    ![Skapa projektet från instrumentpanelen Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. När du startar en version i Jenkins i bygga kö. Versionen kan inte köras förrän Azure Container agenten har startats och tas online för för en Azure-behållare Agent. Fram till dess kan se du ett meddelande som anger namnet på agenten och det faktum att bygga väntar. (Den här processen tar cirka fem minuter, men behövs bara första gången du använder agenten för en version. Efterföljande versioner är mycket snabbare agenten är online samtidigt.)

    ![Bygga köas förrän agenten har skapats och tas online.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    När bygga startar en barber pol förloppsindikator visar att bygga körs:

    ![Bygga körs när du ser förloppsindikatorn barber pol.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. När förloppsindikatorn barber pol försvinner, väljer du på pilen bredvid build-nummer. På snabbmenyn Välj **konsolen utdata**.

    ![Klicka på menyalternativet konsolen loggen om du vill visa build-information.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. Konsolen logginformation som orsakat från build-processen. Välj om du vill visa alla build-information (inklusive information om build utförs via fjärranslutning på Azure-agenten som du skapade) **fullständig logg**.

    ![Klicka på länken fullständig loggen om du vill visa mer detaljerad build-information.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    Den fullständiga loggen visar mer detaljerad build-information:

    ![Den fullständiga loggen visar mer detaljerad build-information.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. Rulla ned till slutet av loggen om du vill visa disposition för den versionen.

    ![Build-disposition visas längst ned i build-loggen.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>Rensa Azure-resurser

I kursen får skapade du resurser som ingår i två Azure-resursgrupper: 
    - `JenkinsResourceGroup`-Innehåller Azure-resurser för Jenkins-server.
    - `JenkinsAciResourceGroup`-Innehåller Azure-resurser för agenten Jenkins.
    
Om du inte längre behöver använda någon av resurserna i en Azure-resursgrupp kan du ta bort en resurs grupp med den `az group delete` kommandot på följande sätt (ersätter den &lt;resourceGroup > med namnet på resursgruppen som du vill ta bort):

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Besök Jenkins på Azure-hubb för att se de senaste artiklar och exempel](https://docs.microsoft.com/en-us/azure/jenkins/)