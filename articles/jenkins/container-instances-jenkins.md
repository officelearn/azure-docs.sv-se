---
title: Jenkins bygger på behållarinstans
description: Lär dig hur du konfigurerar en Jenkins-server för att köra byggjobb på begäran i Azure Container Instances
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: 8bb84895fb581053248fbad326ea7b2c8d1873a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617975"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Använda Azure Container Instances som Jenkins build-agent

Azure Container Instances (ACI) tillhandahåller en on-demand, burstable och isolerad miljö för att köra containeriserade arbetsbelastningar. På grund av dessa attribut gör ACI en bra plattform för att köra Jenkins bygga jobb i stor skala. Den här artikeln går igenom distribution och användning av en Jenkins-server som är förkonfigurerad med ACI som ett byggmål.

Mer information om Azure Container Instances finns i [Om Azure Container Instances](../container-instances/container-instances-overview.md).

## <a name="deploy-a-jenkins-server"></a>Distribuera en Jenkins-server

1. I Azure-portalen väljer du **Skapa en resurs** och söker efter **Jenkins**. Välj Jenkins-erbjudandet med en utgivare av **Microsoft**och välj sedan **Skapa**.

2. Ange följande information i formuläret **Grunderna** och välj sedan **OK**.

   - **Namn**: Ange ett namn för Jenkins-distributionen.
   - **Användarnamn**: Ange ett namn för administratörsanvändaren för den virtuella jenkins-datorn.
   - **Autentiseringstyp**: Vi rekommenderar en allmän SSH-nyckel för autentisering. Om du väljer det här alternativet klistrar du in en SSH-offentlig nyckel som ska användas för att logga in på den virtuella Jenkins-datorn.
   - **Prenumeration**: Välj en Azure-prenumeration.
   - **Resursgrupp**: Skapa en resursgrupp eller välj en befintlig.
   - **Plats**: Välj en plats för Jenkins-servern.

   ![Grundläggande inställningar för Jenkins portaldistribution](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Fyll i följande objekt i formuläret **Ytterligare inställningar:**

   - **Storlek:** Välj lämpligt storleksalternativ för din virtuella Jenkins-dator.
   - **VM-disktyp:** Ange antingen **hårddisk** (hårddisk) eller **SSD** (solid state-enhet) för Jenkins-servern.
   - **Virtuellt nätverk:** Markera pilen om du vill ändra standardinställningarna.
   - **Undernät:** Markera pilen, verifiera informationen och välj **OK**.
   - **Offentlig IP-adress:** Välj pilen för att ge den offentliga IP-adressen ett anpassat namn, konfigurera SKU och ange tilldelningsmetoden.
   - **Domännamnsetikett:** Ange ett värde för att skapa en fullständigt kvalificerad URL till den virtuella jenkins-datorn.
   - **Jenkins versionstyp:** Välj önskad versionstyp från alternativen: **LTS**, **Weekly build**eller **Azure Verified**.

   ![Ytterligare inställningar för Jenkins portaldistribution](./media/container-instances-jenkins/jenkins-portal-02.png)

4. För integrering av tjänstens huvudnamn väljer du **Auto(MSI)** om du vill att [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) automatiskt ska skapa en autentiseringsidentitet för Jenkins-instansen. Välj **Handbok** om du vill ange dina egna autentiseringsuppgifter för tjänstens huvudnamn.

5. Molnagenter konfigurerar en molnbaserad plattform för Jenkins-byggjobb. Av hänsyn till den här artikeln väljer du **ACI**. Med ACI-molnagenten körs varje Jenkins-byggjobb i en behållarinstans.

   ![Molnintegreringsinställningar för Jenkins portaldistribution](./media/container-instances-jenkins/jenkins-portal-03.png)

6. När du är klar med integrationsinställningarna väljer du **OK**och väljer sedan **OK** igen i valideringssammanfattningen. Välj **Skapa** i sammanfattningen **av användningsvillkoren.** Jenkins-servern tar några minuter att distribuera.

## <a name="configure-jenkins"></a>Konfigurera Jenkins

1. I Azure-portalen bläddrar du till jenkins-resursgruppen, väljer den virtuella Jenkins-datorn och noterar DNS-namnet.

   ![DNS-namn i information om jenkins virtuella dator](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Bläddra till DNS-namnet på Jenkins VM och kopiera den returnerade SSH-strängen.

   ![Jenkins inloggningsinstruktioner med SSH-sträng](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Öppna en terminalsession på utvecklingssystemet och klistra in SSH-strängen från det sista steget. Uppdatera `username` till det användarnamn som du angav när du distribuerade Jenkins-servern.

4. När sessionen är ansluten kör du följande kommando för att hämta det ursprungliga administratörslösenordet:

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Lämna SSH-sessionen och tunneln igång `http://localhost:8080` och gå till i en webbläsare. Klistra in det ursprungliga administratörslösenordet i rutan och välj sedan **Fortsätt**.

   !["Lås upp Jenkins"-skärmen med rutan för administratörslösenordet](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Välj **Installera föreslagna plugins** för att installera alla rekommenderade Jenkins plugins.

   !["Anpassa Jenkins"-skärmen med "Installera föreslagna plugins" valt](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Skapa ett administratörsanvändarkonto. Det här kontot används för att logga in på och arbeta med din Jenkins-instans.

   ![Skärmen "Skapa första administratörsanvändare" med autentiseringsuppgifter ifyllda](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Välj **Spara och slutför**och välj sedan Börja använda **Jenkins** för att slutföra konfigurationen.

Jenkins är nu konfigurerad och redo att skapa och distribuera kod. I det här exemplet används ett enkelt Java-program för att demonstrera en Jenkins-version av Azure Container Instances.

## <a name="create-a-build-job"></a>Skapa ett byggjobb

Nu skapas ett Jenkins-byggjobb för att demonstrera Jenkins bygger på en Azure-behållarinstans.

1. Välj **Nytt objekt,** ge byggprojektet ett namn som **aci-demo**, välj **Freestyle-projekt**och välj **OK**.

   ![Ruta för namnet på byggjobbet och en lista över projekttyper](./media/container-instances-jenkins/jenkins-new-job.png)

2. Under **Allmänt**kontrollerar du att **Begränsa var det här projektet kan köras** är markerat. Ange **linux** för etikettuttrycket. Den här konfigurationen säkerställer att det här byggjobbet körs på ACI-molnet.

   ![Fliken "Allmänt" med konfigurationsinformation](./media/container-instances-jenkins/jenkins-job-01.png)

3. Under **Skapa**väljer du **Lägg till byggsteg** och väljer **Kör Skal**. Ange `echo "aci-demo"` som kommando.

   ![Fliken "Bygg" med val för byggsteget](./media/container-instances-jenkins/jenkins-job-02.png)

5. Välj **Spara**.

## <a name="run-the-build-job"></a>Kör byggjobbet

Om du vill testa byggjobbet och observera Azure Container Instances som byggplattform startar du en version manuellt.

1. Välj **Bygg nu** om du vill starta ett byggjobb. Det tar några minuter för jobbet att starta. Du bör se en status som liknar följande bild:

   !["Skapa historik"-information med jobbstatus](./media/container-instances-jenkins/jenkins-job-status.png)

2. Medan jobbet körs öppnar du Azure-portalen och tittar på Jenkins-resursgruppen. Du bör se att en behållarinstans har skapats. Jenkins-jobbet körs i den här instansen.

   ![Behållarinstans i resursgruppen](./media/container-instances-jenkins/jenkins-aci.png)

3. När Jenkins kör fler jobb än det konfigurerade antalet Jenkins-körsändare (standard 2) skapas flera behållarinstanser.

   ![Nyligen skapade behållarinstanser](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. När alla byggjobb har slutförts tas behållarinstanserna bort.

   ![Resursgrupp med behållarinstanser borttagna](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av Jenkins-plugin-programmet

Om du stöter på några buggar med Jenkins plugins, lämna in ett problem i [Jenkins JIRA](https://issues.jenkins-ci.org/) för den specifika komponenten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [CI/CD till Azure App Service](java-deploy-webapp-tutorial.md)