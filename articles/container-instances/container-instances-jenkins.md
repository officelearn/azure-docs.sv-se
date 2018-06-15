---
title: Använd Azure Behållarinstanser som en Jenkins skapa agent
description: Lär dig hur du använder Azure Behållarinstanser som en Jenkins skapa agenten.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/20/2018
ms.author: nepeters
ms.openlocfilehash: 4df230c8306a3876e94a5e9ada5e7408f134ba26
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359557"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Använd Azure Behållarinstanser som en Jenkins skapa agent

Azure Container instanser (ACI) tillhandahåller en på begäran, burstable och isolerade miljö för körning av arbetsbelastningar. På grund av dessa attribut gör ACI en utmärkt plattform för Jenkins build jobb som körs på en stor skala. Den här artikeln beskriver hur distribuerar och använder en Jenkins-server som är förkonfigurerad med ACI som ett build-mål.

Läs mer på Azure Behållarinstanser [om Azure Behållarinstanser][about-aci].

## <a name="deploy-a-jenkins-server"></a>Distribuera en Jenkins-server

1. Välj i Azure-portalen **skapar du en resurs** och Sök efter **Jenkins**. Välj Jenkins erbjudande med en utgivare **Microsoft**, och välj sedan **skapa**.

2. Ange följande information den **grunderna** formuläret och välj sedan **OK**.

   - **Namnet**: Ange ett namn för distributionen av Jenkins.
   - **Användarnamnet**: Ange ett namn för administratören för den virtuella datorn Jenkins.
   - **Autentiseringstypen**: Vi rekommenderar en offentlig SSH-nyckel för autentisering. Om du väljer det här alternativet kan du klistra in i en offentlig SSH-nyckel som ska användas för att logga in på den virtuella datorn Jenkins.
   - **Prenumeration**: Välj en Azure-prenumeration.
   - **Resursgrupp**: Skapa en resursgrupp eller välj en befintlig.
   - **Plats**: Välj en plats för Jenkins-server.

   ![Grundläggande inställningar för distribution av Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

3. På den **ytterligare inställningar** formuläret genom att utföra följande:

   - **Storlek**: Välj lämpliga storleksalternativ för den virtuella datorn Jenkins.
   - **VM disktyp**: ange antingen **Hårddisk** (hårddiskenheten) eller **SSD** (SSD) för Jenkins-servern.
   - **Virtuellt nätverk**: Markera pilen om du vill ändra standardinställningarna.
   - **Undernät**: Markera pilen, kontrollerar du informationen och välj **OK**.
   - **Offentliga IP-adressen**: Välj på pilen för att ge ett eget namn för den offentliga IP-adressen, konfigurera SKU: N och tilldelning av-metoden.
   - **Domännamnet**: Ange ett värde för att skapa en fullständig URL till den virtuella datorn Jenkins.
   - **Jenkins versionstyp**: Markera önskade versionen från alternativen: **LTS**, **varje vecka skapa**, eller **Azure verifiera**.

   ![Ytterligare inställningar för distribution av Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Tjänstens huvudnamn integration, Välj **Auto(MSI)** har [Azure hanterade tjänstidentiteten] [ managed-service-identity] automatiskt skapa en autentiseringsidentitet för Jenkins instans. Välj **manuell** dina egna service principal autentiseringsuppgifter.

5. Molnet agenter konfigurera en molnbaserad plattform för Jenkins skapa jobb. För den här artikeln, Välj **ACI**. Med ACI moln agenten körs varje Jenkins build-jobbet i en behållare-instans.

   ![Inställningar för katalogintegrering molnet för distribution av Jenkins](./media/container-instances-jenkins/jenkins-portal-03.png)

6. När du är klar med inställningar för katalogintegrering, Välj **OK**, och välj sedan **OK** igen på verifieringssammanfattningen. Välj **skapa** på den **användningsvillkoren** sammanfattning. Jenkins servern tar några minuter att distribuera.

## <a name="configure-jenkins"></a>Konfigurera Jenkins

1. Bläddra till Jenkins resursgrupp i Azure-portalen, väljer Jenkins virtuell dator och anteckna DNS-namn.

   ![DNS-namnet i information om den virtuella datorn Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Bläddra till DNS-namnet på Jenkins VM och kopiera den returnerade SSH-strängen.

   ![Jenkins inloggning instruktioner med SSH-sträng](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Öppna en terminalsession i utvecklingssystemet och klistra in i SSH-strängen från det sista steget. Uppdatera `username` för användarnamnet som du angav när du distribuerade Jenkins-server.

4. När sessionen är ansluten, kör följande kommando för att hämta inledande administratörslösenordet:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Lämna SSH-session och tunnel som körs och gå till http://localhost:8080 i en webbläsare. Klistra in det första administratörslösenordet i rutan och välj sedan **Fortsätt**.

   ![”Låsa upp Jenkins” skärmen med rutan för lösenord](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Välj **installera plugin-program för föreslagna** installera alla rekommenderade Jenkins plugin-program.

   ![”Anpassa Jenkins” skärmen med valt ”installera föreslagna plugin-program”](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Skapa ett administratörskonto för användaren. Det här kontot används för att logga in på och arbeta med din Jenkins-instans.

   ![”Skapa första administratörsanvändare” skärmen med autentiseringsuppgifter som har fyllt i](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Välj **spara och slutför**, och välj sedan **börja använda Jenkins** för att slutföra konfigurationen.

Jenkins har nu konfigurerats och är redo att skapa och distribuera kod. I det här exemplet används ett enkelt Java-program att visa en Jenkins version på Azure-Behållarinstanser.

## <a name="create-a-build-job"></a>Skapa ett build-jobb

När du använder en behållare avbildning som en Jenkins skapa mål, måste du ange en avbildning som innehåller alla tooling krävs för en lyckad version. Ange bilden:

1. Välj **hantera Jenkins** > **konfigurera systemet** och rulla ned till den **moln** avsnitt. Uppdatera Docker avbildningen värdet till det här exemplet **microsoft/java-på-azure-jenkins-slavserver**.

   När du är klar väljer du **spara** att återgå till instrumentpanelen Jenkins.

   ![Jenkins molnkonfigurationen](./media/container-instances-jenkins/jenkins-aci-image.png)

2. Nu ska du skapa ett Jenkins build-jobb. Välj **nytt objekt**, namnge projektet build som **aci-java-demo**väljer **Freestyle projektet**, och välj **OK**.

   ![Kryssrutan för namnet på build-jobbet och listan över projekttyper](./media/container-instances-jenkins/jenkins-new-job.png)

3. Under **allmänna**, se till att **begränsa där du kan köra det här projektet** är markerad. Ange **linux** för etikettuttrycket som. Den här konfigurationen garanterar att bygga jobbet körs på ACI molnet.

   ![”” Allmänt med konfigurationsinformation](./media/container-instances-jenkins/jenkins-job-01.png)

4. Under **källa kod Management**väljer **Git** och ange **https://github.com/spring-projects/spring-petclinic.git** för URL: en för databasen. Den här GitHub-lagringsplatsen innehåller exempelkod för programmet.

   ![”Datakällan kod Management” fliken med information om datakällan](./media/container-instances-jenkins/jenkins-job-02.png)

5. Under **skapa**väljer **Lägg till build steg** och välj **anropa översta Maven mål**. Ange **paketet** som build steget mål.

   ![”Skapa” flik med alternativ för steget Skapa](./media/container-instances-jenkins/jenkins-job-03.png)

6. Välj **Spara**.

## <a name="run-the-build-job"></a>Kör jobb för att bygga

TESTJOBBET build och se Azure Behållarinstanser som build-plattform manuellt starta en version.

1. Välj **skapa nu** ett build-jobb. Det tar några minuter för att jobbet ska starta. Du bör se statusen som liknar följande bild:

   ![”Skapa historik” information med jobbstatus](./media/container-instances-jenkins/jenkins-job-status.png)

2. När jobbet körs, öppna Azure-portalen och titta på Jenkins resursgruppen. Du bör se att en instans för behållaren har skapats. Jenkins jobbet körs i den här instansen.

   ![Behållaren instans i resursgruppen.](./media/container-instances-jenkins/jenkins-aci.png)

3. Jenkins körs fler jobb än det konfigurerade antalet Jenkins executors (standard 2) har flera behållarinstanser skapas.

   ![Nyligen skapade behållarinstanser](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. När alla build-jobb är klar tas behållarinstanser bort.

   ![Resursgrupp med behållarinstanser som tas bort](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om Jenkins på Azure i [Azure och Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md