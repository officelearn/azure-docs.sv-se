---
title: Jenkins-version på behållar instans
description: Lär dig hur du konfigurerar en Jenkins-Server för att köra Bygg jobb på begäran i Azure Container Instances
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: 8bb84895fb581053248fbad326ea7b2c8d1873a2
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617975"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Använd Azure Container Instances som Jenkins build-agent

Azure Container Instances (ACI) tillhandahåller en miljö på begäran, burst-anslutning och isolerad miljö för att köra arbets belastningar i behållare. På grund av dessa attribut gör ACI en bra plattform för att köra Jenkins-build-jobb i stor skala. Den här artikeln beskriver hur du distribuerar och använder en Jenkins-server som är förkonfigurerad med ACI som bygg mål.

Mer information om Azure Container Instances finns i [om Azure Container instances](../container-instances/container-instances-overview.md).

## <a name="deploy-a-jenkins-server"></a>Distribuera en Jenkins-Server

1. I Azure Portal väljer du **skapa en resurs** och söker efter **Jenkins**. Välj Jenkins-erbjudandet med en utgivare av **Microsoft**och välj sedan **skapa**.

2. Ange följande information i formuläret **grundläggande** och välj sedan **OK**.

   - **Namn**: Ange ett namn för Jenkins-distributionen.
   - **Användar namn**: Ange ett namn på administratörs användaren för den virtuella Jenkins-datorn.
   - **Autentiseringstyp**: Vi rekommenderar en offentlig SSH-nyckel för autentisering. Om du väljer det här alternativet klistrar du in en offentlig SSH-nyckel som ska användas för att logga in på den virtuella Jenkins-datorn.
   - **Prenumeration**: Välj en Azure-prenumeration.
   - **Resursgrupp**: Skapa en resursgrupp eller välj en befintlig.
   - **Plats**: Välj en plats för Jenkins-servern.

   ![Grundläggande inställningar för distribution av Jenkins-portalen](./media/container-instances-jenkins/jenkins-portal-01.png)

3. I formuläret **ytterligare inställningar** slutför du följande objekt:

   - **Storlek**: Välj lämpligt storleks alternativ för din virtuella Jenkins-dator.
   - **Disk typ för virtuell dator**: Ange antingen **HDD** (hård disk enhet) eller **SSD** (Solid-State Drive) för Jenkins-servern.
   - **Virtuellt nätverk**: Välj pilen om du vill ändra standardinställningarna.
   - **Undernät**: Välj pilen, verifiera informationen och välj **OK**.
   - **Offentlig IP-adress**: Välj pilen för att ge den offentliga IP-adressen ett anpassat namn, konfigurera SKU: n och ange tilldelnings metoden.
   - **Domän namns etikett**: Ange ett värde för att skapa en fullständigt kvalificerad URL till den virtuella Jenkins-datorn.
   - **Jenkins-versions typ**: Välj önskad versions typ från alternativen: **LTS**, **veckovis build**eller **Azure verifierad**.

   ![Ytterligare inställningar för distribution av Jenkins-portalen](./media/container-instances-jenkins/jenkins-portal-02.png)

4. För tjänstens huvud namns integrering väljer du **Auto (MSI)** om du vill att [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) automatiskt ska skapa en Autentiseringsidentitet för Jenkins-instansen. Välj **manuell** om du vill ange dina egna autentiseringsuppgifter för tjänstens huvud namn.

5. Moln agenter konfigurerar en molnbaserad plattform för Jenkins build-jobb. I den här artikeln väljer du **ACI**. Med ACI Cloud Agent körs varje Jenkins-build-jobb i en behållar instans.

   ![Moln integrerings inställningar för distribution av Jenkins-portalen](./media/container-instances-jenkins/jenkins-portal-03.png)

6. När du är klar med integrations inställningarna väljer du **OK**och väljer sedan **OK** igen på validerings sammanfattningen. Välj **skapa** på **användningsvillkor** Sammanfattning. Det tar några minuter att distribuera Jenkins-servern.

## <a name="configure-jenkins"></a>Konfigurera Jenkins

1. I Azure Portal bläddrar du till resurs gruppen Jenkins, väljer den virtuella Jenkins-datorn och noterar DNS-namnet.

   ![DNS-namn i information om den virtuella Jenkins-datorn](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Bläddra till DNS-namnet för den virtuella Jenkins-datorn och kopiera den returnerade SSH-strängen.

   ![Jenkins inloggnings instruktioner med SSH-sträng](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Öppna en terminalsession i utvecklings systemet och klistra in SSH-strängen från det sista steget. Uppdatera `username` till det användar namn som du angav när du distribuerade Jenkins-servern.

4. När sessionen är ansluten kör du följande kommando för att hämta det ursprungliga administratörs lösen ordet:

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Lämna SSH-sessionen och-tunneln igång och gå till `http://localhost:8080` i en webbläsare. Klistra in det ursprungliga administratörs lösen ordet i rutan och välj sedan **Fortsätt**.

   ![Skärmen "Lås upp Jenkins" med rutan för administratörs lösen ordet](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Välj **Installera föreslagna plugin** -program för att installera alla rekommenderade Jenkins-plugin-program.

   ![Skärmen "anpassa Jenkins" med "installera föreslagna plugin-program" valt](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Skapa ett administratörs användar konto. Det här kontot används för att logga in på och arbeta med din Jenkins-instans.

   ![Skärmen "skapa första administratörs användare" med autentiseringsuppgifter ifyllt](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Välj **Spara och slutför**och välj sedan **börja använda Jenkins** för att slutföra konfigurationen.

Jenkins har nu kon figurer ATS och är redo att bygga och distribuera kod. I det här exemplet används ett enkelt Java-program för att demonstrera en Jenkins-version på Azure Container Instances.

## <a name="create-a-build-job"></a>Skapa ett Bygg jobb

Nu skapas ett Jenkins build-jobb för att demonstrera Jenkins-versioner på en Azure Container instance.

1. Välj **nytt objekt**, ge build-projektet ett namn som **ACI-demo**, Välj **Freestyle projekt**och välj **OK**.

   ![Box för namnet på Build-jobbet och en lista över projekt typer](./media/container-instances-jenkins/jenkins-new-job.png)

2. Under **Allmänt**kontrollerar du att **begränsa var det här projektet kan köras** är markerat. Ange **Linux** som etikett uttryck. Den här konfigurationen säkerställer att det här bygg jobbet körs i ACI-molnet.

   ![Fliken Allmänt med konfigurations information](./media/container-instances-jenkins/jenkins-job-01.png)

3. Under **build**väljer du **Lägg till build-steg** och väljer **Kör gränssnitt**. Ange `echo "aci-demo"` som kommando.

   ![Fliken "Build" med val för build-steget](./media/container-instances-jenkins/jenkins-job-02.png)

5. Välj **Spara**.

## <a name="run-the-build-job"></a>Kör build-jobbet

Om du vill testa build-jobbet och titta Azure Container Instances som build-plattform startar du en version manuellt.

1. Välj **Skapa nu** för att starta ett build-jobb. Det tar några minuter för jobbet att starta. Du bör se en status som liknar följande bild:

   ![Information om "versions historik" med jobb status](./media/container-instances-jenkins/jenkins-job-status.png)

2. När jobbet körs öppnar du Azure Portal och tittar på resurs gruppen Jenkins. Du bör se att en behållar instans har skapats. Jenkins-jobbet körs i den här instansen.

   ![Container instans i resurs gruppen](./media/container-instances-jenkins/jenkins-aci.png)

3. När Jenkins kör fler jobb än det konfigurerade antalet Jenkins-körningar (standard 2) skapas flera behållar instanser.

   ![Nyligen skapade behållar instanser](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. När alla Bygg jobb har avslut ATS tas behållar instanserna bort.

   ![Resurs grupp med behållar instanser borttagna](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av Jenkins-plugin-programmet

Om du stöter på buggar med Jenkins-plugin-programmet kan du rapportera problemet i [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [CI/CD till Azure App Service](java-deploy-webapp-tutorial.md)