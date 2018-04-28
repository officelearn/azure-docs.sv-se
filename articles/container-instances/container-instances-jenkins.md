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
ms.openlocfilehash: dbe418db8fb3d73739a30b60703f15b3dc47b291
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Använd Azure Behållarinstanser som en Jenkins skapa agent

Azure-behållarinstanser tillhandahålla en på begäran, burstable och isolerade miljö för körning av arbetsbelastningen. På grund av dessa attribut göra Azure behållarinstanser en utmärkt plattform för Jenkins build jobb som körs på en stor skala. Det här dokumentet går igenom hur du distribuerar och använder en Jenkins-server som är förkonfigurerad med ACI som ett build-mål.

Läs mer på Azure Behållarinstanser [om Azure Behållarinstanser][about-aci].

## <a name="deploy-jenkins-server"></a>Distribuera Jenkins server

Välj i Azure-portalen **skapar du en resurs** och Sök efter **Jenkins**. Välj Jenkins erbjudande med en utgivare **Microsoft** och välj **skapa**.

Ange följande information i formuläret grunderna och klicka på **OK** när du är klar.

- **Namnet** - namn för distributionen av Jenkins.
- **Användarnamnet** -användarnamnet används som administratör för den virtuella datorn Jenkins.
- **Autentiseringstypen** – SSH offentlig nyckel rekommenderas. Om markerad kopiera i en offentlig SSH-nyckel som ska användas när du loggar in på den virtuella datorn Jenkins.
- **Prenumerationen** -Välj en Azure-prenumeration.
- **Resursgruppen** – skapa en ny eller välj en befintlig resursgrupp.
- **Plats** -Välj en plats för Jenkins-server.

![Grundläggande inställningar för Jenkins distribution](./media/container-instances-jenkins/jenkins-portal-01.png)

Utför följande funktioner i formuläret ytterligare inställningar:

- **Storlek** -Välj lämplig storleksalternativ för den virtuella datorn Jenkins.
- **VM disktyp** -ange Hårddisk (hårddiskenheten) eller SSD (SSD) för servern Jenkins.
- **Virtuellt nätverk** -(valfritt) Välj virtuella nätverk för att ändra standardinställningarna.
- **Undernät** – Välj undernät, kontrollerar du informationen och välj **OK**.
- **Offentliga IP-adressen** -att välja den offentliga IP-adressen kan du ge det ett eget namn, konfigurera SKU och tilldelningsmetod.
- **Domännamnet** -ange ett värde för att skapa en fullständig URL till den virtuella datorn Jenkins.
- **Jenkins versionstyp** -Markera önskade versionen från alternativen: LTS, varje vecka skapandet eller Azure verifieras.

![Jenkins distribution av ytterligare inställningar](./media/container-instances-jenkins/jenkins-portal-02.png)

Tjänstens huvudnamn integration, Välj **Auto(MSI)** har [Azure hanterade tjänstidentiteten] [ managed-service-identity] automatiskt skapa en autentiseringsidentitet för Jenkins-instansen. Välj Manuell till providern dina service principal autentiseringsuppgifter.

Molnet agenter konfigurera en molnbaserad plattform för Jenkins skapa jobb. Välj ACI för det här dokumentet. Med ACI moln agenten körs varje Jenkins build jobbet i en instans för Azure-behållare.

![Inställningar för katalogintegrering Jenkins distribution moln](./media/container-instances-jenkins/jenkins-portal-03.png)

När du har gjort med inställningar för katalogintegrering, klickar du på **OK**, och sedan **OK** igen på verifieringssammanfattningen. Klicka på **skapa** om villkoren för sammanfattning av användning. Jenkins servern tar några minuter att distribuera.

## <a name="configure-jenkins"></a>Konfigurera Jenkins

Bläddra till Jenkins resursgrupp i Azure-portalen, väljer Jenkins virtuell dator och anteckna DNS-namn.

![Jenkins inloggning instruktioner](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

Webbläsare som DNS-namnet för Jenkins VM och kopiera den returnerade SSH-strängen.

![Jenkins inloggning instruktioner](./media/container-instances-jenkins/jenkins-portal-04.png)

Öppna en terminalsession i utvecklingssystemet och klistra in i SSH-strängen från det sista steget. Uppdatera 'användarnamn' till det användarnamn som anges när du distribuerar Jenkins-server.

När du är ansluten, kör du följande kommando för att hämta det inledande administratörslösenordet.

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Lämna SSH-session och tunnel som körs och navigera till http://localhost:8080 i en webbläsare. Klistra in det första adminlösenordet i fältet som visas i följande bild. Välj **Fortsätt** när du är klar.

![Låsa upp Jenkins](./media/container-instances-jenkins/jenkins-portal-05.png)

Välj **installera plugin-program för föreslagna** installera alla rekommenderade Jenkins plugin-program.

![Installera Jenkins plugin-programmet](./media/container-instances-jenkins/jenkins-portal-06.png)

Skapa ett nytt admin-användarkonto. Det här kontot används för att logga in på och arbeta med Jenkins-instans.

![Skapa Jenkins användare](./media/container-instances-jenkins/jenkins-portal-07.png)

Välj **spara och slutför** när du är klar och sedan **börja använda Jenkins** för att slutföra konfigurationen.

Jenkins har nu konfigurerats och är redo att skapa och distribuera kod. I det här exemplet används ett enkelt Java-program att visa en Jenkins version på Azure-Behållarinstanser.

## <a name="create-build-job"></a>Skapa build-jobb

När genom att använda en avbildning av behållare som en Jenkins skapa mål, måste du ange en avbildning som innehåller alla tooling krävs för en lyckad version. Välj för att ange bilden **hantera Jenkins** > **konfigurera systemet** och rulla ned till den **moln** avsnitt. Uppdatera Docker avbildningen värdet till det här exemplet `microsoft/java-on-azure-jenkins-slave`.

När du har gjort, klickar du på **spara** att återgå till instrumentpanelen Jenkins.

![Jenkins molnkonfigurationen](./media/container-instances-jenkins/jenkins-aci-image.png)

Nu ska du skapa ett Jenkins build-jobb. Välj **nytt objekt**, namnge projektet build som `aci-java-demo`väljer **Freestyle projektet**, och klicka på **OK**.

![Skapa Jenkins-jobb](./media/container-instances-jenkins/jenkins-new-job.png)

Under **allmänna**, se till att **begränsa där du kan köra det här projektet** är markerad. Ange `linux` för etikettuttrycket som. Den här konfigurationen garanterar att bygga jobbet körs på ACI molnet.

![Skapa Jenkins-jobb](./media/container-instances-jenkins/jenkins-job-01.png)

Under källan kod management väljer `Git` och ange `https://github.com/spring-projects/spring-petclinic.git` för URL: en för databasen. Den här GitHub-lagringsplatsen innehåller exempelkod för programmet.

![Lägg till källkoden Jenkins jobb](./media/container-instances-jenkins/jenkins-job-02.png)

Markera under version **lägga till ett build-steg** och välj `Invoke top-level Maven targets`. Ange `package` som build steget mål.

![Lägg till Jenkins skapa steg](./media/container-instances-jenkins/jenkins-job-03.png)

Välj **spara** när du är klar.

## <a name="run-the-build-job"></a>Kör jobb för att bygga

TESTJOBBET build och se Azure Behållarinstanser som build-plattform manuellt starta en version.

Välj **skapa nu** ett build-jobb. Det tar några minuter för jobbet att starta när du kör, bör du se statusen som liknar följande bilder.

![Lägg till Jenkins skapa steg](./media/container-instances-jenkins/jenkins-job-status.png)

När jobbet körs, öppna Azure-portalen och titta på Jenkins resursgruppen. Du bör se att en instans för Azure-behållaren har skapats. Det finns i den här instansen Jenkins jobbet körs.

![Jenkins versioner i ACI](./media/container-instances-jenkins/jenkins-aci.png)

Jenkins körs fler jobb än det konfigurerade antalet Jenkins executors (standard 2) har flera instanser av Azure-behållare skapas.

![Jenkins versioner i ACI](./media/container-instances-jenkins/jenkins-aci-multi.png)

När alla build-jobb har utförts, tas Azure-behållarinstanser bort.

![Jenkins versioner i ACI](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Jenkins på Azure Se [Azure och Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md