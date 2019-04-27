---
title: Hur du använder Azures sekundära plugin-program med kontinuerlig integrering med Hudson | Microsoft Docs
description: Beskriver hur du använder Azures sekundära plugin-program med kontinuerlig integrering med Hudson.
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
ms.openlocfilehash: ef24e356c9ac8424fc519a3b16af5d37a20e706f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444214"
---
# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Hur du använder Azures sekundära plugin-program med kontinuerlig integrering med Hudson
Azures sekundära plugin för Hudson kan du etablera underordnade noder i Azure när du kör distribuerade bygger.

## <a name="install-the-azure-slave-plug-in"></a>Installera Azure underordnad server plugin-programmet
1. I Hudson-instrumentpanelen klickar du på **hantera Hudson**.
2. I den **hantera Hudson** klickar du på **hantera plugin-program**.
3. Klicka på den **tillgänglig** fliken.
4. Klicka på **Search** och skriv **Azure** att begränsa listan till relevanta plugin-program.
   
    Om du väljer för att bläddra igenom listan med tillgängliga plugins, hittar du Azures sekundära plugin-programmet under den **klusterhantering och distribuerade skapa** i avsnittet den **andra** fliken.
5. Markera kryssrutan för **underordnad-Plugin för Azure**.
6. Klicka på **Installera**.
7. Starta om Hudson.

Nu när plugin-programmet har installerats är nästa steg att konfigurera plugin-programmet med din Azure-prenumeration-profil och skapa en mall som ska användas i att skapa den virtuella datorn för den underordnade noden.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Konfigurera Azure-sekundära plugin-program med din prenumeration-profil
En profil för prenumerationen, även kallat Publiceringsinställningar, är en XML-fil som innehåller autentiseringsuppgifter för säker och ytterligare information måste du arbeta med Azure i din utvecklingsmiljö. För att konfigurera Azures sekundära plugin-programmet, behöver du:

* Ditt prenumerations-id
* Ett certifikat för din prenumeration

Dessa återfinns i din [profilen prenumeration]. Nedan visas ett exempel på en prenumeration-profil.

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

När du har din prenumeration-profil, Följ stegen nedan för att konfigurera Azures sekundära plugin-programmet.

1. I Hudson-instrumentpanelen klickar du på **hantera Hudson**.
2. Klicka på **konfigurera System**.
3. Rulla nedåt på sidan för att hitta den **molnet** avsnittet.
4. Klicka på **lägga till nya molntjänster > Microsoft Azure**.
   
    ![lägga till nya molntjänster][add new cloud]
   
    Det här alternativet visas fält där du måste ange information om din prenumeration.
   
    ![Konfigurera profilen][configure profile]
5. Kopiera prenumerations-id och hantering av certifikat från din prenumeration-profil och klistra in dem i lämpliga fält.
   
    När du kopierar prenumerations-id och hantering av certifikat, **inte** ta med citattecknen som omsluter värdena.
6. Klicka på **verifiera konfigurationen**.
7. När konfigurationen är har verifierats klickar du på **spara**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Konfigurera en mall för virtuell dator för underordnad Azure-server plugin-programmet
En virtuell Datormall definierar parametrar som plugin-programmet använder för att skapa en underordnad nod på Azure. I följande steg ska vi skapa mall för en Ubuntu-VM.

1. I Hudson-instrumentpanelen klickar du på **hantera Hudson**.
2. Klicka på **konfigurera System**.
3. Rulla nedåt på sidan för att hitta den **molnet** avsnittet.
4. I den **molnet** avsnittet, hitta **Lägg till mall för virtuella datorer i Azure** och klicka på den **Lägg till** knappen.
   
    ![Lägg till mall för virtuell dator][add vm template]
5. Ange en molntjänstens namn i den **namn** fält. Om det namn du refererar till en befintlig molntjänst, kommer den virtuella datorn att tillhandahållas i tjänsten. I annat fall skapar Azure en ny.
6. I den **beskrivning** fältet, anger du text som beskriver den mall som du skapar. Den här informationen är endast för dokumentärfilmare och används inte etablera en virtuell dator.
7. I den **etiketter** anger **linux**. Den här etiketten används för att identifiera den mallen som du skapar och används sedan för att referera till mallen när du skapar ett Hudson-jobb.
8. Välj en region där den virtuella datorn kommer att skapas.
9. Välj lämplig VM-storlek.
10. Ange ett lagringskonto där den virtuella datorn kommer att skapas. Kontrollera att den är i samma region som den molntjänst som du ska använda. Om du vill att ny lagring som ska skapas, kan du lämna fältet tomt.
11. Kvarhållningstid anger antalet minuter innan Hudson tar bort en inaktiv underordnad server. Lämna denna i du standardvärdet 60.
12. I **användning**, väljer du det lämpliga villkoren när den här underordnad nod ska användas. För tillfället väljer **använda den här noden så mycket som möjligt**.
    
     Nu ser formuläret något ungefär så här:
    
     ![mallkonfiguration][template config]
13. I **bild familj eller Id** måste du ange vilken avbildning som ska installeras på den virtuella datorn. Du kan välja från en lista över bild familjer, eller så kan du ange en anpassad avbildning.
    
     Om du vill välja från en lista över bild familjer, anger du det första tecknet som (skiftlägeskänsligt) i Avbildningsnamnet för serien. Till exempel skriva **U** visas en lista med Ubuntu Server familjer. När du har valt i listan använder Jenkins den senaste versionen av systemavbildningen från den familjen när du etablerar den virtuella datorn.
    
     ![OS-familjen lista][OS family list]
    
     Om du har en anpassad avbildning som du vill använda i stället kan du ange namnet på den anpassa avbildningen. Namn på anpassade bilder visas inte i en lista, så du måste kontrollera att namnet har angetts.    
    
     Den här självstudien anger **U** att ta fram en lista med Ubuntu-avbildningar och välja **Ubuntu Server 14.04 LTS**.
14. För **startmetod**väljer **SSH**.
15. Kopiera skriptet nedan och klistra in den **Init skriptet** fält.
    
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
    
     Den **Init skriptet** körs när den virtuella datorn har skapats. I det här exemplet installerar skriptet Java, git och ant.
16. I den **användarnamn** och **lösenord** fält, ange din önskade värden för administratörskontot som kommer att skapas på den virtuella datorn.
17. Klicka på **Kontrollera mallen** att kontrollera om de parametrar som du angav är giltiga.
18. Klicka på **Spara**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Skapa en Hudson-jobb som körs på en underordnad nod på Azure
I det här avsnittet ska du skapa en Hudson-aktivitet som körs på en underordnad nod i Azure.

1. I Hudson-instrumentpanelen klickar du på **nytt jobb**.
2. Ange ett namn för projektet som du skapar.
3. Jobbtypen, Välj **skapa ett jobb för freestyle-programvara**.
4. Klicka på **OK**.
5. Välj i konfigurationssidan för jobbet **begränsa där du kan köra det här projektet**.
6. Välj **nod och etiketten menyn** och välj **linux** (vi anges den här etiketten när du skapar mallen för virtuella datorer i föregående avsnitt).
7. I den **skapa** klickar du på **Lägg till byggsteg** och välj **köra shell**.
8. Redigera följande skript och ersätter **{github namnet på ditt konto}**, **{ditt projektnamn}**, och **{projektkatalogen}** med lämpliga värden och klistra in den redigerade skript i textområdet som visas.
   
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
10. Hitta jobb som du just har skapat i Hudson-instrumentpanelen och klicka på den **schemalägga en version** ikon.

Hudson sedan skapa en underordnad nod med hjälp av mallen skapades i föregående avsnitt och kör skriptet som du angav i ett byggsteg för den här uppgiften.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder Java i Azure finns i [Azure Java-utvecklingscenter].

<!-- URL List -->

[Azure Java-utvecklingscenter]: https://azure.microsoft.com/develop/java/
[profilen prenumeration]: https://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

