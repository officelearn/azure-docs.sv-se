---
title: "Köra Java-programservern på en klassisk Azure-VM | Microsoft Docs"
description: "Den här kursen använder resurser som har skapats med den klassiska distributionsmodellen och visar hur du skapar en Windows virtuell dator och konfigurera den för att köra Apache Tomcat-programserver."
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d627aa09-f7d6-4239-8110-f8fc5111b939
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 03/16/2017
ms.author: robmcm
ms.openlocfilehash: 6e02f42613808bcb13c0057e9f8fcc1c02273e77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Så kör du ett Java-program på en virtuell dator skapad med den klassiska distributionsmodellen
> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. En Resource Manager-mall att distribuera en webapp med Java 8 och Tomcat finns [här](https://azure.microsoft.com/documentation/templates/201-web-app-java-tomcat/).

Du kan använda en virtuell dator med Azure, för att tillhandahålla serverfunktioner. Exempelvis kan en virtuell dator som körs på Azure konfigureras som värd för Java-programservern, till exempel Apache Tomcat.

När du har slutfört den här guiden kommer du ha en förståelse för hur du skapar en virtuell dator som körs på Azure och konfigurera den för att köra en Java-programservern. Du lär dig och utföra följande uppgifter:

* Så här skapar du en virtuell dator som har en Java Development Kit (JDK) redan har installerats.
* Hur du loggar in via fjärranslutning till den virtuella datorn.
* Så här installerar du en Java-programservern--Apache Tomcat--på den virtuella datorn.
* Så här skapar du en slutpunkt för den virtuella datorn.
* Hur du öppnar en port i brandväggen för programservern.

Slutfört installationen resulterar i Tomcat som körs på en virtuell dator.

![Virtuell dator som kör Apache Tomcat][virtual_machine_tomcat]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Skapa en virtuell dator
1. Logga in på [Azure Portal](https://portal.azure.com).  
2. Klicka på **ny**, klickar du på **Compute**, klicka på **se alla** i den **aktuella appar**.
3. Klicka på **JDK**, klickar du på **JDK 8** i den **JDK** fönstret.  
   Virtuella bilder som stöder **JDK 6** och **JDK 7** är tillgängliga om du har äldre program som inte är redo att köras i JDK 8.
4. Välj i rutan JDK 8 **klassiska**, klicka på **skapa**.
5. I den **grunderna** bladet:
   1. Ange ett namn för den virtuella datorn.
   2. Ange ett namn för administratören i den **användarnamn** fältet. Kom ihåg detta namn och tillhörande lösenord som följer i nästa fält. Du behöver dem när du loggar in via fjärranslutning till den virtuella datorn.
   3. Ange ett lösenord i den **nytt lösenord** fältet och ange den i den **Bekräfta lösenord** fältet. Lösenordet är för administratörskontot.
   4. Välj lämpliga **prenumeration**.
   5. För den **resursgruppen**, klickar du på **Skapa nytt** och ange namnet på en ny resursgrupp. Klicka på **använda befintliga** och välj en av de tillgängliga resursgrupperna.
   6. Välj en plats där den virtuella datorn finns, som **södra centrala USA**.
6. Klicka på **Nästa**.
7. I den **virtuella bildstorleken** bladet väljer **A1 Standard** eller en annan lämplig bild.
8. Klicka på **Välj**.

9. I den **inställningar** bladet, klickar du på **OK**. Du kan använda standardvärdena som tillhandahålls av Azure.  
10. I den **sammanfattning** bladet, klickar du på **OK**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Logga in via en fjärranslutning till den virtuella datorn
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **virtuella datorer (klassisk)**. Om det behövs klickar du på **fler tjänster** i nedre vänstra hörnet under tjänstekategorier. Den **virtuella datorer (klassisk)** posten visas i den **Compute** grupp.
3. Klicka på namnet på den virtuella dator som du vill logga in på.
4. När den virtuella datorn har startats tillåter en meny längst upp i fönstret anslutningar.
5. Klicka på **Anslut**.
6. Svara på frågorna som behövs för att ansluta till den virtuella datorn. Normalt spara eller öppna .rdp-filen som innehåller anslutningsinformationen. Du kan behöva kopiera URL-adress: port som den sista delen av den första raden i RDP-filen och klistra in den i inloggning fjärrprogram.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Installera en Java-programservern på den virtuella datorn
Du kan kopiera en Java-programservern till den virtuella datorn eller du kan installera en Java-programservern via ett installationsprogram.

Den här kursen använder Tomcat som Java-programservern för att installera.

1. När du är inloggad på den virtuella datorn, öppna en webbläsarsession till [Apache Tomcat](http://tomcat.apache.org/download-80.cgi).
2. Dubbelklicka på länken för **32-bitars/64-bitars Windows installationsprogram**. Med den här tekniken kan installeras Tomcat som en Windows-tjänst.
3. När du uppmanas du välja att köra installationsprogrammet.
4. I den **Apache Tomcat installationsprogrammet** guiden, följer du anvisningarna för att installera Tomcat. Vid tillämpningen av den här kursen räcker det bra att acceptera standardinställningarna. När du når den **Slutför installationsguiden för Apache Tomcat** dialogrutan kan du om du vill kontrollera **köra Apache Tomcat** ha Tomcat starta nu. Klicka på **Slutför** att slutföra installationsprocessen för Tomcat.

## <a name="to-start-tomcat"></a>Starta Tomcat

Du kan starta Tomcat manuellt genom att öppna en kommandotolk på den virtuella datorn och kör kommandot **net&nbsp;starta&nbsp;Tomcat8**.

När Tomcat körs kan du komma åt Tomcat genom att ange URL: en <http://localhost: 8080> i webbläsaren för den virtuella datorn.

Om du vill se Tomcat körs från externa datorer, måste du skapa en slutpunkt och öppnar en port.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Så här skapar du en slutpunkt för den virtuella datorn
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **virtuella datorer (klassisk)**.
3. Klicka på namnet på den virtuella datorn som kör Java-programservern.
4. Klicka på **Slutpunkter**.
5. Klicka på **Lägg till**.
6. I den **lägga till slutpunkten** dialogrutan:
   1. Ange ett namn för slutpunkten; till exempel **HttpIn**.
   2. Välj **TCP** för protokollet.
   3. Ange **80** för den offentliga porten.
   4. Ange **8080** för den privata porten.
   5. Välj **inaktiverad** för flytande IP-adress.
   6. Lämna åtkomstkontrollistan är.
   7. Klicka på den **OK** knappen för att stänga dialogrutan och skapa slutpunkten.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Öppna en port i brandväggen för den virtuella datorn
1. Logga in på den virtuella datorn.
2. Klicka på **starta Windows**.
3. Klicka på **Kontrollpanelen**.
4. Klicka på **System och säkerhet**, klickar du på **Windows-brandväggen**, och klicka sedan på **avancerade inställningar**.
5. Klicka på **regler för inkommande trafik**, och klicka sedan på **ny regel**.  
   ![Ny inkommande regel][NewIBRule]
6. För den **regeltyp**väljer **Port**, och klicka sedan på **nästa**.  
   ![Ny inkommande regel port][NewRulePort]
7. På den **protokoll och portar** väljer **TCP**, ange **8080** som den **specifika lokala portar**, och klicka sedan på **nästa**.  
  ![Ny inkommande regel][NewRuleProtocol]
8. På den **åtgärd** väljer **tillåter anslutningen**, och klicka sedan på **nästa**.
   ![Ny inkommande Regelåtgärd][NewRuleAction]
9. På den **profil** kontrollerar du att **domän**, **privata**, och **offentliga** är markerade och klickar sedan på **nästa**.
   ![Ny inkommande regel profil][NewRuleProfile]
10. På den **namn** skärmen, ange ett namn för regeln, exempelvis **HttpIn** (Regelnamnet inte är obligatoriskt att matcha namnet på slutpunkten men), och klicka sedan på **Slutför**.  
    ![Namn på ny inkommande regel][NewRuleName]

Tomcat webbplatsen ska nu visas på en extern webbläsare. I adressfältet i webbläsaren, skriver du en URL i formatet  **http://*din\_DNS\_namn*. cloudapp.net**, där ***din\_DNS\_namn*** är DNS-namnet som du angav när du skapade den virtuella datorn.

## <a name="application-lifecycle-considerations"></a>Programmet livscykel överväganden
* Du kan skapa egna exempelwebbappens Arkiv (WAR) och lägger till den i den **webbappar** mapp. Till exempel skapa ett grundläggande Service (Java JSP sidan) dynamiskt webbprojekt och exportera det som en WAR-fil. Kopiera WAR till Apache Tomcat **webbappar** mapp på den virtuella datorn som kör det i en webbläsare.
* När tjänsten Tomcat är installerad och är det som standard ska startas manuellt. Du kan växla den att starta automatiskt med hjälp av snapin-modulen tjänster. Starta snapin-modulen Tjänster genom att klicka på **Windows starta**, **Administrationsverktyg**, och sedan **Services**. Dubbelklicka på den **Apache Tomcat** och ange **starttyp** till **automatisk**.

    ![Ange en tjänst för att starta automatiskt][service_automatic_startup]

    Fördelen med Tomcat startar automatiskt är att den börjar köras när den virtuella datorn startas (till exempel efter programuppdateringar som kräver en omstart har installerats).

## <a name="next-steps"></a>Nästa steg
Du kan lära dig om andra tjänster (t.ex Azure Storage, service bus och SQL-databas) som du kanske vill ta med Java-program. Visa information i den [Java-Utvecklingscenter](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]:media/java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]:media/java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]:media/java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]:media/java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]:media/java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]:media/java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]:media/java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]:media/java-run-tomcat-app-server/NewRuleProfile.png


<!-- Deleted from the "To create an ednpoint for your virtual mache" 3/17/2017,
     to use the new portal.
6. In the **Add endpoint** dialog box, ensure **Add standalone endpoint** is selected, and then click **Next**.
7. In the **New endpoint details** dialog box:
-->
