---
title: Använd Azure Container Instances som en Jenkins skapa agenten
description: Lär dig hur du använder Azure Container Instances som en Jenkins build agent.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 08/31/2018
ms.author: danlep
ms.openlocfilehash: 85b67fb4fa474bb00b5b7ca66580273671081cdf
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478204"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Använd Azure Container Instances som en Jenkins skapa agenten

Azure Container Instances (ACI) är en på begäran, anpassningsbara och isolerad miljö för att köra arbetsbelastningar i behållare. På grund av dessa attribut gör ACI en utmärkt plattform för att köra Jenkins-skapade jobb i stor skala. Den här artikeln visar hur distribuerar och använder en Jenkins-server som redan har konfigurerats med ACI som ett build-mål.

Mer information om Azure Container Instances finns [om Azure Container Instances][about-aci].

## <a name="deploy-a-jenkins-server"></a>Distribuera en Jenkins-server

1. I Azure-portalen väljer du **skapa en resurs** och Sök efter **Jenkins**. Välj Jenkins-erbjudande med utgivare av **Microsoft**, och välj sedan **skapa**.

2. Ange följande information den **grunderna** formuläret och välj sedan **OK**.

   - **Namn**: Ange ett namn för Jenkins-distribution.
   - **Användarnamnet**: Ange ett namn för administratören för den virtuella Jenkins-datorn.
   - **Autentiseringstyp**: Vi rekommenderar en offentlig SSH-nyckel för autentisering. Om du väljer det här alternativet kan du klistra in en offentlig SSH-nyckel som ska användas för att logga in på Jenkins-dator.
   - **Prenumeration**: Välj en Azure-prenumeration.
   - **Resursgrupp**: Skapa en resursgrupp eller välj en befintlig.
   - **Plats**: Välj en plats för Jenkins-servern.

   ![Grundläggande inställningar för distributioner av Jenkins-portalen](./media/container-instances-jenkins/jenkins-portal-01.png)

3. På den **ytterligare inställningar** formuläret, Slutför följande objekt:

   - **Storlek**: Välj alternativet lämplig storlek för den virtuella Jenkins-datorn.
   - **Typ av virtuell datordisk**: Ange antingen **HDD** (hårddiskenheten) eller **SSD** (SSD) för Jenkins-servern.
   - **Virtuellt nätverk**: Markera pilen om du vill ändra standardinställningarna.
   - **Undernät**: Välj pilen, kontrollerar du informationen och väljer **OK**.
   - **Offentlig IP-adress**: Välj pilen för att ge ett anpassat namn för offentliga IP-adress, konfigurera SKU: N och ange tilldelningsmetoden.
   - **Domännamnsetiketten**: Ange ett värde för att skapa en fullständig URL till den virtuella Jenkins-datorn.
   - **Jenkins versionstyp**: Markera önskade versionen i alternativen: **LTS**, **varje vecka skapa**, eller **Azure verifierat**.

   ![Ytterligare inställningar för distributioner av Jenkins-portalen](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Tjänstens huvudnamn integration, Välj **Auto(MSI)** ha [hanterade identiteter för Azure-resurser] [ managed-identities-azure-resources] automatiskt skapa en autentiseringsidentitet för den Jenkins-instansen. Välj **manuell** att ge dina egna autentiseringsuppgifter för tjänstens huvudnamn.

5. Molnet agenter konfigurera en molnbaserad plattform för Jenkins build jobb. För den här artikeln väljer **ACI**. Varje Jenkins-byggjobb körs i en behållarinstans med ACI-molnagent.

   ![Inställningar för integrering av molnet för distributioner av Jenkins-portalen](./media/container-instances-jenkins/jenkins-portal-03.png)

6. När du är klar med inställningar för katalogintegrering, Välj **OK**, och välj sedan **OK** igen på Sammanfattning verifieringen. Välj **skapa** på den **användningsvillkoren** sammanfattning. Jenkins-servern tar några minuter att distribuera.

## <a name="configure-jenkins"></a>Konfigurera Jenkins

1. Bläddra till Jenkins-resursgrupp i Azure-portalen, Välj den virtuella Jenkins-datorn och anteckna DNS-namn.

   ![DNS-namnet i information om den virtuella datorn för Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Bläddra till DNS-namnet på Jenkins VM och kopiera den returnerade strängen för SSH.

   ![Instruktioner för Jenkins-inloggning med SSH-sträng](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Öppna en terminalsession i utvecklingssystemet och klistra in den SSH-strängen från det sista steget. Uppdatera `username` för användarnamnet som du angav när du distribuerade Jenkins-servern.

4. När sessionen är ansluten, kör följande kommando för att hämta det ursprungliga administratörslösenordet:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Lämna SSH-session och kör-tunnel och gå till http://localhost:8080 i en webbläsare. Klistra in det ursprungliga administratörslösenordet i rutan och välj sedan **Fortsätt**.

   ![”Låsa upp Jenkins” skärmen med rutan administratörslösenordet](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Välj **installera föreslagna plugin-programmen** installera alla rekommenderade Jenkins plugin-program.

   ![”Anpassa Jenkins” fönster där du har valt ”installera föreslagna plugin-program”](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Skapa ett administratörskonto för användaren. Det här kontot används för att logga in och arbeta med din Jenkins-instans.

   ![”Skapa första administratörsanvändaren” skärmen med autentiseringsuppgifter som har fyllt i](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Välj **spara och slutför**, och välj sedan **börja använda Jenkins** att slutföra konfigurationen.

Jenkins har nu konfigurerats och är redo att skapa och distribuera kod. I det här exemplet används ett enkelt Java-program för att demonstrera en Jenkins build på Azure Container Instances.

## <a name="create-a-build-job"></a>Skapa ett build-jobb

Nu skapas en Jenkins-byggjobb för att demonstrera Jenkins bygger på ett Azure container-instans.

1. Välj **nytt objekt**, ge build-projektet ett namn som **aci-demo**väljer **Freestyle-projekt**, och välj **OK**.

   ![Fält för namnet på den skapandejobb och listan över projekttyper](./media/container-instances-jenkins/jenkins-new-job.png)

2. Under **Allmänt**, se till att **begränsa där du kan köra det här projektet** har valts. Ange **linux** för etikettuttrycket. Den här konfigurationen garanterar att den här build-jobb körs på ACI-molnet.

   ![På fliken ”Allmänt” med information om konfiguration](./media/container-instances-jenkins/jenkins-job-01.png)

3. Under **skapa**väljer **Lägg till byggsteg** och välj **köra Shell**. Ange `echo "aci-demo"` som kommandot.

   ![”Skapa” fliken med val för ett byggsteg](./media/container-instances-jenkins/jenkins-job-02.png)

5. Välj **Spara**.

## <a name="run-the-build-job"></a>Kör jobbet för att skapa

Om du vill testa build-jobb och noterar Azure Container Instances som build-plattform, manuellt starta en version.

1. Välj **skapa nu** ett build-jobb. Det tar några minuter för att jobbet ska starta. Du bör se en status som liknar följande bild:

   ![”Versionshistorik” information med jobbstatus](./media/container-instances-jenkins/jenkins-job-status.png)

2. När jobbet körs, öppna Azure-portalen och titta på Jenkins-resursgruppen. Du bör se att en instans i behållaren har skapats. Jenkins-jobbet körs i den här instansen.

   ![Container-instans i resursgruppen](./media/container-instances-jenkins/jenkins-aci.png)

3. När Jenkins körs fler jobb än det konfigurerade antalet Jenkins executors (standard 2) skapas flera behållarinstanser.

   ![Nyligen skapade behållarinstanser](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. När alla build-jobb är klar kan tas bort behållarinstanserna.

   ![Resursgrupp med behållarinstanser som har tagits bort](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av Jenkins-plugin-programmet

Om du stöter på buggar med Jenkins-plugin-programmet kan du rapportera problemet i [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg

Läs mer om Jenkins på Azure i [Azure och Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-identities-azure-resources]: ../active-directory/managed-identities-azure-resources/overview.md