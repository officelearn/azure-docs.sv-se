---
title: "Publicera en tjänst i molnet med hjälp av Azure-verktyg | Microsoft Docs"
description: "Läs mer om hur du publicerar Azure molntjänstprojekt med hjälp av Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/14/2017
ms.author: kraigb
ms.openlocfilehash: e617d600dbc8287eea737fc4969833e873365288
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>Publicera en tjänst i molnet med hjälp av Azure-verktyg
Genom att använda Azure Tools för Microsoft Visual Studio kan du publicera dina Azure-program direkt från Visual Studio. Visual Studio stöder integrerad publicering till antingen mellanlagrings- eller produktionsmiljön i en molntjänst.

Innan du kan publicera ett Azure-program, måste du ha en Azure-prenumeration. Du måste också ange ett moln tjänsten och storage-konto som ska användas av ditt program. Du kan konfigurera dessa på den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!IMPORTANT]
> När du publicerar väljer du distributionsmiljön för Molntjänsten. Du måste också välja ett lagringskonto som används för att lagra programpaket för distribution. Efter distributionen tas programpaketet bort från lagringskontot.
> 
> 

När du utvecklar och testar ett Azure-program kan använda du webbdistribution för att publicera ändringar inkrementellt för web-roller. När du publicerar ditt program till en distributionsmiljö, kan Web Deploy du distribuera ändringarna direkt till den virtuella datorn som kör webbrollen. Du behöver inte paketet och publicera hela Azure programmet varje gång som du vill uppdatera din webbroll att testa ändringarna. Du kan ha web rollen ändringarna tillgängliga i molnet för att testa utan att ha programmet publiceras till en distribution med den här metoden.

Använd följande procedurer för att publicera ditt Azure-program och för att uppdatera en webbroll med hjälp av Web Deploy:

* Publicera eller ett Azure-program från Visual Studio-paketet
* Uppdatera en webbroll som en del av utveckling och testning cykel

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publicera eller ett Azure-program från Visual Studio-paketet
När du publicerar ditt Azure-program kan göra du något av följande uppgifter:

* Skapa ett tjänstepaket: du kan använda det här paketet och tjänstkonfigurationsfilen för att publicera programmet till en distribution från den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885).
* Publicera din Azure-projekt från Visual Studio: för att publicera programmet direkt till Azure måste du använda guiden Publicera. Mer information finns i [Publiceringsguiden för Azure-program](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Att skapa ett servicepaket från Visual Studio
1. När du är redo att publicera programmet öppna Solution Explorer, öppna snabbmenyn för Azure-projekt som innehåller dina roller och välja publicera.
2. Följ dessa steg om du vill skapa ett servicepaket:  
   
   1. Välj på snabbmenyn för Azure-projekt, **paketet**.
   2. I den **paketet Azure-programmet** dialogrutan Välj konfigurationen för tjänsten som du vill skapa ett paket och välj sedan versionskonfiguration.
   3. (valfritt) Om du vill aktivera Fjärrskrivbord för Molntjänsten när du har publicerat, Välj den **aktivera Fjärrskrivbord för alla roller** kryssrutan och välj sedan **inställningar** Konfigurera fjärrskrivbord. Om du vill felsöka din molntjänst när du har publicerat aktivera fjärrfelsökning genom att välja **aktivera fjärråtkomst felsökare för alla roller**.
      
      Mer information finns i [med hjälp av fjärrskrivbord med Azure-roller](vs-azure-tools-remote-desktop-roles.md).
   4. För att skapa paketet, Välj den **paketet** länk.
      
      Utforskaren visar sökvägen till det nya paketet. Du kan kopiera den här platsen så att du kan använda den från den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885).
   5. Om du vill publicera det här paketet till en distributionsmiljö, måste du använda den här platsen som paketplatsen när du skapar en tjänst i molnet och distribuera det här paketet till en miljö med den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885).
3. (Valfritt) Om du vill avbryta distributionsprocessen på snabbmenyn för artikeln i aktivitetsloggen, Välj **Avbryt och ta bort**. Detta stoppar distributionsprocessen och tar bort distributionsmiljö från Azure.
   
   > [!NOTE]
   > Om du vill ta bort den här distributionsmiljön när det har distribuerats, måste du använda den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885).
   > 
   > 
4. (Valfritt) När dina rollinstanser har startat, visar Visual Studio automatiskt distributionsmiljön i den **molntjänster** nod i Server Explorer. Här kan se du status för enskilda instanser. Se [hantera Azure-resurser med Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md). Följande bild visar rollinstanserna medan de är fortfarande i tillståndet initierar:
   
    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Uppdatera en Webbroll som en del av utveckling och testning cykel
Om appens backend-infrastruktur är stabilt, men web-roller behöver mer uppdateras regelbundet, kan du använda webbdistribution för att uppdatera en webbroll i projektet. Detta är praktiskt när du inte vill återskapa och distribuera om backend-arbetsroller, eller om du har flera webbroller och du vill uppdatera endast en av rollerna webb.

### <a name="requirements"></a>Krav
Här är kraven för att använda webbdistribution för att uppdatera din webbroll:

* **Endast avsett för utveckling och testning:** ändringarna görs direkt till den virtuella datorn där webbrollen körs. Om den här virtuella datorn har återvinnas, försvinner ändringarna eftersom det ursprungliga paketet som du har publicerat används för att skapa den virtuella datorn för rollen. Du måste publicera om tillämpningsprogrammet att hämta de senaste ändringarna för webbrollen.
* **Endast webbroller kan uppdateras:** arbetsroller kan inte uppdateras. Dessutom kan du uppdatera RoleEntryPoint i web role.cs.
* **Stöder bara en instans av en webbroll:** du kan inte ha flera instanser av en webbserver-rollen i din distributionsmiljö. Dock som flera webbroller varje med endast en instans stöds.
* **Du måste aktivera anslutning till fjärrskrivbord:** detta krävs så att webbdistribution kan använda användare och lösenord för att ansluta till den virtuella datorn för att distribuera ändringarna till den server som kör Internet Information Services (IIS). Dessutom kan behöva du ansluta till den virtuella datorn för att lägga till ett betrott certifikat i IIS på den virtuella datorn. (Det säkerställer att fjärranslutningen för IIS som används av webbdistribution är säker.)

Följande procedur förutsätter att du använder den **publicera Azure-programmet** guiden.

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>Om du vill aktivera webbdistribution när du publicerar ditt program
1. Så här aktiverar du den **aktivera Web Deploy** för alla webbprogram roller måste du först konfigurera anslutning till fjärrskrivbord. Välj **aktivera Fjärrskrivbord** för alla roller och ange de autentiseringsuppgifter som används för att ansluta via fjärranslutning i den **konfiguration av fjärrskrivbord** som visas. Se [med hjälp av fjärrskrivbord med Azure-roller](vs-azure-tools-remote-desktop-roles.md) för mer information.
2. Välj för att aktivera webbdistribution för webbroller i ditt program **aktivera webbdistribution för alla webbroller för**.
   
    En gul varningstriangel visas. Webbdistribution använder en icke betrodd och självsignerat certifikat som standard, vilket inte rekommenderas för uppladdning av känsliga data. Om du behöver att skydda den här processen för känsliga data kan du lägga till ett SSL-certifikat som ska användas för Web Deploy-anslutningar. Det här certifikatet måste vara ett betrott certifikat. Information om hur du gör detta finns i avsnittet **att göra Web distribuera säkra** senare i det här avsnittet.
3. Välj **nästa** att visa den **sammanfattning** skärmen och väljer sedan **publicera** att distribuera Molntjänsten.
   
    Molntjänsten har publicerats. Den virtuella datorn som skapas har fjärranslutningar aktiverade för IIS så att webbdistribution kan användas för att uppdatera web-roller utan att publicera dem.
   
   > [!NOTE]
   > Om du har mer än en instans som konfigurerats för en webbroll, visas ett varningsmeddelande om att varje webbroll begränsas till en instans endast i paketet som skapas för att publicera programmet. Välj **OK** att fortsätta. Du kan ha fler än en webbroll men bara en instans av varje roll som anges i avsnittet krav.
   > 
   > 

### <a name="to-update-your-web-role-by-using-web-deploy"></a>Om du vill uppdatera din Webbroll med webbdistribution
1. Om du vill använda webbdistribution måste göra kodändringar i projektet för någon av dina webbroller i Visual Studio som du vill publicera, högerklicka på den här projektnoden i din lösning och peka på **publicera**. Den **Publicera webbplats** dialogrutan visas.
2. (Valfritt) Om du har lagt till ett betrott SSL-certifikat ska användas för fjärranslutningar i IIS kan du rensa den **Tillåt ej betrodda certifikat** kryssrutan. Information om hur du lägger till ett certifikat för att göra webbdistribution säker, finns i avsnittet **att göra Web distribuera säkra** senare i det här avsnittet.
3. Om du vill använda webbdistribution måste publicera mekanism användarnamn och lösenord som du ställer in för din anslutning till fjärrskrivbord när du först publicerade paketet.
   
   1. I **användarnamn**, ange namnet på användaren.
   2. I **lösenord**, ange lösenordet.
   3. (Valfritt) Välj om du vill spara lösenordet i den här profilen **spara lösenordet**.
4. Om du vill publicera ändringarna till din webbroll, Välj **publicera**.
   
    Statusfältet visar **publicera igång**. När publiceringen är klar **publicera lyckades** visas. Ändringarna har nu distribuerats till rollen webbserver på den virtuella datorn. Nu kan du börja ditt Azure-program i Azure-miljön att testa dina ändringar.

### <a name="to-make-web-deploy-secure"></a>För att distribuera webbprogram säkerheten
1. Webbdistribution använder en icke betrodd och självsignerat certifikat som standard, vilket inte rekommenderas för uppladdning av känsliga data. Om du behöver att skydda den här processen för känsliga data kan du lägga till ett SSL-certifikat som ska användas för Web Deploy-anslutningar. Det här certifikatet måste vara ett betrott certifikat som du kan hämta från en certifikatutfärdare (CA).
   
    Om du vill göra webbdistribution säker för varje virtuell dator för varje web-roller, måste du överföra det betrodda certifikatet som du vill använda för web distribuera till den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885). Detta säkerställer att certifikatet har lagts till den virtuella datorn som skapas för webbrollen när du publicerar ditt program.
2. Följ dessa steg om du vill lägga till ett betrott SSL-certifikat i IIS ska använda för fjärranslutningar:
   
   1. För att ansluta till den virtuella datorn som kör webbrollen, väljer du instansen av rollen webbserver i **Cloud Explorer** eller **Server Explorer**, och välj sedan den **ansluta med hjälp av fjärrskrivbord**  kommando. Detaljerade anvisningar om hur du ansluter till den virtuella datorn finns [med hjälp av fjärrskrivbord med Azure-roller](vs-azure-tools-remote-desktop-roles.md).
      
      Din webbläsare uppmanas du att hämta en. RDP-filen.
   2. Om du vill lägga till ett SSL-certifikat, öppna management-tjänsten i IIS-hanteraren. I IIS-hanteraren aktivera SSL genom att öppna den **bindningar** länken i den **åtgärd** fönstret. Den **Lägg till webbplatsbindning** dialogrutan visas. Välj **Lägg till**, och välj sedan HTTPS i den **typen** listrutan. I den **SSL-certifikat** Välj SSL-certifikatet som du har signerats av en Certifikatutfärdare och att du har överförts till den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885). Mer information finns i [Konfigurera anslutningsinställningar för hanteringstjänsten](http://go.microsoft.com/fwlink/?LinkId=215824).
      
      > [!NOTE]
      > Om du lägger till ett betrott certifikat för SSL på gul varningstriangel inte längre visas i den **Publiceringsguiden**.
      > 
      > 

## <a name="include-files-in-the-service-package"></a>Ta med filer i tjänstepaketet
Du kan behöva inkludera specifika filer i tjänstepaketet så att de är tillgängliga på den virtuella datorn som skapas för en roll. Du kanske vill lägga till en .exe eller en MSI-fil som används av ett startskript till ditt abonnemang. Eller kanske du måste lägga till en sammansättning som kräver en roll eller worker webbrollsprojektet. Inkludera filer som de måste läggas till lösningen för ditt Azure-program.

### <a name="to-include-files-in-the-service-package"></a>Inkludera filer i tjänstepaketet
1. Använd följande steg för att lägga till en sammansättning i ett tjänstepaket:
   
   1. I **Solution Explorer** öppna projektnoden för projektet som saknar den refererade sammansättningen.
   2. Lägg till sammansättningen i projektet genom att öppna snabbmenyn för den **referenser** mapp och välj sedan **Lägg till referens**. Dialogrutan Lägg till referens visas.
   3. Välj den referens som du vill lägga till och välj sedan den **OK** knappen.
      
      Referensen har lagts till i listan under den **referenser** mapp.
   4. Öppna snabbmenyn för sammansättningen som du har lagt till och välj **egenskaper**. Den **egenskaper** visas.
      
      Med den här sammansättningen i service-paketet i den **kopiera lokala listan** Välj **True**.
2. I **Solution Explorer** öppna projektnoden för projektet som saknar den refererade sammansättningen.
3. Lägg till sammansättningen i projektet genom att öppna snabbmenyn för den **referenser** mapp och välj sedan **Lägg till referens**. Den **Lägg till referens** visas.
4. Välj den referens som du vill lägga till och välj sedan den **OK** knappen.
   
    Referensen har lagts till i listan under den **referenser** mapp.
5. Öppna snabbmenyn för sammansättningen som du har lagt till och välj **egenskaper**. Fönstret Egenskaper visas.
6. Att inkludera den här sammansättningen i service-paketet i den **kopiera lokala** Välj **SANT**.
7. Om du vill ta med filer i tjänstepaketet som har lagts till din webbrollsprojektet, öppna snabbmenyn för filen och välj sedan **egenskaper**. Från den **egenskaper** fönstret Välj **innehåll** från den **Skapa åtgärd** listrutan.
8. Om du vill ta med filer i tjänstepaketet som har lagts till arbetsrollsprojektet, öppna snabbmenyn för filen och välj sedan **egenskaper**. Från den **egenskaper** fönstret Välj **kopiera om nyare** från den **kopiera till utdatakatalog** listrutan.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du publicerar till Azure från Visual Studio i [Publiceringsguiden för Azure-program](vs-azure-tools-publish-azure-application-wizard.md).

