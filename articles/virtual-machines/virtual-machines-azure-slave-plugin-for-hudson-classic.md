---
title: Hur du använder den Azure underordnade plugin-program med Hudson kontinuerlig Integration | Microsoft Docs
description: Beskriver hur du använder den Azure underordnade plugin-program med Hudson kontinuerlig Integration.
services: virtual-machines-linux
documentationcenter: ''
author: rmcmurray
manager: wpickett
editor: ''
ms.assetid: b2083d1c-4de8-4a19-a615-ccc9d9b6e1d9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: c11b59f8ea432075b147a391de4b7bd3331e639e
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Hur du använder den Azure underordnade plugin-program med Hudson kontinuerlig Integration
Den Azure underordnade plugin-program för Hudson kan du etablera underordnade noder i Azure när distribuerade versioner.

## <a name="install-the-azure-slave-plug-in"></a>Installera Azure underordnad server plugin-programmet
1. I instrumentpanelen Hudson klickar du på **hantera Hudson**.
2. I den **hantera Hudson** klickar du på **hantera plugin-program**.
3. Klicka på den **tillgänglig** fliken.
4. Klicka på **Sök** och skriv **Azure** att begränsa listan till relevanta plugin-program.
   
    Om du väljer för att gå igenom listan med tillgängliga plugin-program, hittar du den Azure underordnade plugin-program under den **klusterhantering och distribuerade bygga** under den **andra** fliken.
5. Markera kryssrutan för **Azure slavserver Plugin**.
6. Klicka på **Installera**.
7. Starta om Hudson.

Plugin-programmet är installerat, är nästa steg att konfigurera plugin-programmet med din Azure-prenumeration och skapa en mall som ska användas för att skapa den virtuella datorn för den underordnade noden.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Konfigurera Azure-slavserver plugin-program med din prenumeration profil
En profil för prenumerationen, även kallat Publiceringsinställningar, är en XML-fil som innehåller säkra referenser och ytterligare information som du behöver arbeta med Azure i din utvecklingsmiljö. Om du vill konfigurera den Azure underordnade plugin-program behöver du:

* Prenumerations-id
* Ett certifikat för din prenumeration

Dessa återfinns i din [prenumeration profil]. Nedan visas ett exempel på en profil för prenumerationen.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

          <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

              ServiceManagementUrl="https://management.core.windows.net"

              Id="<Subscription ID>"

              Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

          </PublishProfile>

    </PublishData>

När du har din prenumeration profil, Följ dessa steg om du vill konfigurera den Azure underordnade plugin-programmet.

1. I instrumentpanelen Hudson klickar du på **hantera Hudson**.
2. Klicka på **konfigurera systemet**.
3. Rulla ned på sidan för att hitta den **moln** avsnitt.
4. Klicka på **lägga till nya molntjänster > Microsoft Azure**.
   
    ![lägga till nya molntjänster][add new cloud]
   
    Det här alternativet visas fält där du måste ange information om din prenumeration.
   
    ![Konfigurera profilen][configure profile]
5. Kopiera prenumerations-id och hantering av certifikat från profilen prenumeration och klistra in dem i relevanta fält.
   
    När du kopierar prenumerations-id och hantering av certifikat, **inte** citattecknen som omsluter värdena.
6. Klicka på **verifiera konfigurationen**.
7. När konfigurationen är har verifierats klickar du på **spara**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Ställ in en mall för virtuell dator för Azure-slavserver plugin-program
En mall för virtuell dator definierar de parametrar som plugin-programmet använder för att skapa en slav nod på Azure. I följande steg ska vi skapa mall för en Ubuntu VM.

1. I instrumentpanelen Hudson klickar du på **hantera Hudson**.
2. Klicka på **konfigurera systemet**.
3. Rulla ned på sidan för att hitta den **moln** avsnitt.
4. I den **moln** avsnittet, hitta **Lägg till mall för virtuell dator i Azure** och klicka på den **Lägg till** knappen.
   
    ![Lägg till mall för virtuell dator][add vm template]
5. Ange ett tjänstnamn för molnet i den **namn** fältet. Om det namn du refererar till en befintlig molntjänst, kommer den virtuella datorn att tillhandahållas i tjänsten. Annars skapar Azure en ny.
6. I den **beskrivning** , ange text som beskriver den mall som du skapar. Den här informationen är endast för dokumentation och används inte för att etablera en virtuell dator.
7. I den **etiketter** anger **linux**. Den här etiketten används för att identifiera den mall som du skapar och används sedan för att referera till mallen när du skapar ett jobb för Hudson.
8. Välj en region där den virtuella datorn kommer att skapas.
9. Välj lämplig VM-storlek.
10. Ange ett lagringskonto där den virtuella datorn kommer att skapas. Kontrollera att den är i samma region som den molntjänst som du ska använda. Om du vill ny lagring som ska skapas, kan du lämna fältet tomt.
11. Tiden för datakvarhållning anger antalet minuter innan Hudson tar bort en inaktiv slavserver. Lämna det här standardvärdet 60.
12. I **användning**, väljer du den lämpliga när den här sekundära noden kommer att användas. Nu väljer **använda den här noden så mycket som möjligt**.
    
     Nu ser formuläret något ut ungefär så här:
    
     ![Mallkonfigurationen][template config]
13. I **Id eller bild-familjen** måste du ange vilka systemavbildning kommer att installeras på den virtuella datorn. Du kan välja från en lista över avbildningen familjer, eller så kan du ange en anpassad avbildning.
    
     Om du vill välja från en lista över avbildningen familjer ange det första tecknet (skiftlägeskänsligt) familj avbildningsnamn. Till exempel skriva **U** visas en lista över Ubuntu Server familjer. När du har valt i listan använder Jenkins den senaste versionen av systemavbildningen från den familjen vid etablering av den virtuella datorn.
    
     ![OS-familjen lista][OS family list]
    
     Om du har en anpassad avbildning som du vill använda i stället anger du namnet på den egna avbildningen. Anpassad avbildningsnamn visas inte i en lista, så du måste se till att namnet är rätt angivet.    
    
     Den här kursen skriver **U** att få fram en lista över Ubuntu avbildningar och väljer **Ubuntu Server 14.04 LTS**.
14. För **starta metoden**väljer **SSH**.
15. Kopiera skriptet nedan och klistra in i den **Init skriptet** fältet.
    
         # Install Java
    
         sudo apt-get -y update
    
         sudo apt-get install -y openjdk-7-jdk
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y openjdk-7-jdk
    
         # Install git
    
         sudo apt-get install -y git
    
         #Install ant
    
         sudo apt-get install -y ant
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y ant
    
     Den **Init skriptet** ska utföras efter att den virtuella datorn skapas. I det här exemplet installerar skriptet Java, git och ant.
16. I den **användarnamn** och **lösenord** fält, ange din önskade värden för administratörskontot som kommer att skapas på den virtuella datorn.
17. Klicka på **Kontrollera mallen** att kontrollera om de parametrar som du angav är giltiga.
18. Klicka på **Spara**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Skapa ett Hudson jobb som körs på en slav nod på Azure
I det här avsnittet ska du skapa en Hudson aktivitet som körs på en slav nod på Azure.

1. I instrumentpanelen Hudson klickar du på **nytt jobb**.
2. Ange ett namn för det jobb som du skapar.
3. Jobbtypen, Välj **bygga ett ledigt format programvara jobb**.
4. Klicka på **OK**.
5. Välj i konfigurationssidan **begränsa där du kan köra det här projektet**.
6. Välj **nod och etikett menyn** och välj **linux** (vi angett den här etiketten när du skapar mallen för virtuella datorer i föregående avsnitt).
7. I den **skapa** klickar du på **Lägg till build steg** och välj **köra shell**.
8. Redigera följande skript, ersätter **{github kontonamn}**, **{ditt projektnamn}**, och **{projektkatalogen}** med lämpliga värden och klistra in den redigerade skript i textområdet som visas.
   
        # Clone from git repo
   
        currentDir="$PWD"
   
        if [ -e {your project directory} ]; then
   
              cd {your project directory}
   
              git pull origin master
   
        else
   
              git clone https://github.com/{your github account name}/{your project name}.git
   
        fi
   
        # change directory to project
   
        cd $currentDir/{your project directory}
   
        #Execute build task
   
        ant
9. Klicka på **Spara**.
10. I instrumentpanelen för Hudson att hitta jobbet som du just har skapat och klicka på den **schemalägga en build** ikon.

Hudson sedan skapa en slav nod med hjälp av mallen skapas i föregående avsnitt och kör skriptet som du angav i steg build för den här uppgiften.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder Java i Azure finns i [Java-utvecklingscenter].

<!-- URL List -->

[Java-utvecklingscenter]: https://azure.microsoft.com/develop/java/
[prenumeration profil]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

