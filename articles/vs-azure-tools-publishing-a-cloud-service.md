---
title: Publicera en tjänst i molnet med hjälp av Azure-verktyg | Microsoft Docs
description: Läs mer om hur du publicerar Azure molntjänstprojekt med hjälp av Visual Studio.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 59a34f92937136079d8c693a1747b2d54f37e6d8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="publishing-a-cloud-service-using-visual-studio"></a>Publicera en tjänst i molnet med hjälp av Visual Studio

Visual Studio kan publicera ett program direkt till Azure, med stöd för både mellanlagring och produktion miljöer för en tjänst i molnet. När publicering, väljer du den distributionsmiljön och ett lagringskonto som används för tillfället för distributionspaketet.

När du utvecklar och testar ett Azure-program, kan du använda webbdistribution för att publicera ändringar inkrementellt för web-roller. När du publicerar ditt program till en distributionsmiljö, kan Web Deploy du distribuera ändringarna direkt till den virtuella datorn som kör webbrollen. Du behöver inte paketet och publicera hela Azure programmet varje gång som du vill uppdatera din webbroll att testa ändringarna. Du kan ha web rollen ändringarna tillgängliga i molnet för att testa utan att ha programmet publiceras till en distribution med den här metoden.

Använd följande procedurer för att publicera ditt Azure-program och för att uppdatera en webbroll med hjälp av Web Deploy:

- Publicera eller ett Azure-program från Visual Studio-paketet
- Uppdatera en webbroll som en del av utveckling och testning cykel

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publicera eller ett Azure-program från Visual Studio-paketet

När du publicerar ditt Azure-program kan göra du något av följande uppgifter:

- Skapa ett tjänstepaket: du kan använda det här paketet och tjänstkonfigurationsfilen för att publicera programmet till en distribution från den [Azure-portalen](https://portal.azure.com).

- Publicera din Azure-projekt från Visual Studio: för att publicera programmet direkt till Azure måste du använda guiden Publicera. Mer information finns i [Publiceringsguiden för Azure-program](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Att skapa ett servicepaket från Visual Studio

1. När du är redo att publicera programmet öppna Solution Explorer, öppna snabbmenyn för Azure-projekt som innehåller dina roller och välja publicera.

1. Följ dessa steg om du vill skapa ett servicepaket:

   a. Välj på snabbmenyn för Azure-projekt, **paketet**.

   b. I den **paketet Azure-programmet** dialogrutan Välj konfigurationen för tjänsten som du vill skapa ett paket och välj sedan versionskonfiguration.

   c. (Valfritt) Om du vill aktivera Fjärrskrivbord för Molntjänsten när du har publicerat, Välj **aktivera Fjärrskrivbord för alla roller**, och välj sedan **inställningar** att konfigurera behörigheter för fjärrskrivbord. Mer information finns i [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

      Om du vill felsöka din molntjänst när du har publicerat aktivera fjärrfelsökning genom att välja **aktivera fjärråtkomst felsökare för alla roller**.

   d. För att skapa paketet, Välj den **paketet** länk.

      Utforskaren visar sökvägen till det nya paketet. Du kan kopiera den här platsen så att du kan använda den från Azure-portalen.

   e. Om du vill publicera det här paketet till en distributionsmiljö, måste du använda den här platsen som paketet plats när du skapar en tjänst i molnet och distribuera det här paketet till en miljö med Azure-portalen.

1. (Valfritt) Om du vill avbryta distributionsprocessen på snabbmenyn för artikeln i aktivitetsloggen, Välj **Avbryt och ta bort**. Det här kommandot stoppar distributionsprocessen och tar bort distributionsmiljö från Azure. Använd Azure-portalen för att ta bort miljön efter distributionen.

1. (Valfritt) När dina rollinstanser har startat, visar Visual Studio automatiskt distributionsmiljön i den **molntjänster** nod i Server Explorer. Här kan se du status för enskilda instanser. Se [hantera Azure-resurser med Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md). Följande bild visar rollinstanserna medan de är fortfarande i tillståndet initierar:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Uppdatera en webbroll som en del av utveckling och testning cykel

Om appens backend-infrastruktur är stabilt, men web-roller behöver mer uppdateras regelbundet, kan du använda webbdistribution för att uppdatera en webbroll i projektet. Webbdistribution är praktisk när du inte vill återskapa och distribuera om backend-arbetsroller, eller om du har flera webbroller och du vill uppdatera endast en av rollerna webb.

### <a name="requirements-for-using-web-deploy"></a>Krav för att använda webbdistribution

- **Endast avsett för utveckling och testning**: ändringarna görs direkt till den virtuella datorn där webbrollen körs. Om den här virtuella datorn har återvinnas, försvinner ändringarna eftersom det ursprungliga paketet som du har publicerat används för att skapa den virtuella datorn för rollen. Publicera programmet att få de senaste ändringarna för webbrollen.

- **Endast webbroller kan uppdateras**: arbetsroller kan inte uppdateras. Dessutom kan du kan inte uppdatera den `RoleEntryPoint` i `web role.cs`.

- **Stöder bara en instans av en webbroll**: du kan inte ha flera instanser av en webbserver-rollen i din distributionsmiljö. Dock som flera webbroller varje med endast en instans stöds.

- **Aktivera fjärrskrivbordsanslutningar**: Detta krav kan Web Deploy du använder användar- och lösenord för att ansluta till den virtuella datorn för att distribuera ändringarna till den server som kör Internet Information Services (IIS). Dessutom kan behöva du ansluta till den virtuella datorn för att lägga till ett betrott certifikat i IIS på den virtuella datorn. (Det här certifikatet garanterar att fjärranslutningen för IIS som används av webbdistribution är säker.)

Följande procedur förutsätter att du använder den **publicera Azure-programmet** guiden.

### <a name="enable-web-deploy-when-you-publish-your-application"></a>Aktivera Web Deploy när du publicerar ditt program

1. Så här aktiverar du den **aktivera webbdistribution för alla webbroller för** alternativet, måste du först konfigurera anslutning till fjärrskrivbord. Välj **aktivera Fjärrskrivbord** för alla roller och ange de autentiseringsuppgifter som används för att ansluta via fjärranslutning i den **konfiguration av fjärrskrivbord** som visas. Se [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

1. Välj för att aktivera webbdistribution för webbroller i ditt program **aktivera webbdistribution för alla webbroller för**.

    En gul varningstriangel visas. Webbdistribution använder en icke betrodd och självsignerat certifikat som standard, vilket inte rekommenderas för uppladdning av känsliga data. Om du behöver att skydda den här processen för känsliga data kan du lägga till ett SSL-certifikat som ska användas för Web Deploy-anslutningar. Det här certifikatet måste vara ett betrott certifikat. Mer information finns i [att distribuera webbprogram säkrare](#make-web-deploy-secure).

1. Välj **nästa** att visa den **sammanfattning** skärmen och väljer sedan **publicera** att distribuera Molntjänsten.

    Molntjänsten har publicerats. Den virtuella datorn som skapas har fjärranslutningar aktiverade för IIS så att webbdistribution kan användas för att uppdatera web-roller utan att publicera dem.

   > [!NOTE]
   > Om du har mer än en instans som konfigurerats för en webbroll, visas ett varningsmeddelande om att varje webbserver-rollen är begränsad till en instans endast i paketet som skapas för att publicera programmet. Klicka på **OK** för att fortsätta. Du kan ha fler än en webbroll men bara en instans av varje roll som anges i avsnittet krav.

### <a name="update-your-web-role-by-using-web-deploy"></a>Uppdatera din webbroll med webbdistribution

1. Om du vill använda webbdistribution måste göra kodändringar i projektet för någon av dina webbroller i Visual Studio som du vill publicera, högerklicka på den här projektnoden i din lösning och peka på **publicera**. Den **Publicera webbplats** dialogrutan visas.

1. (Valfritt) Om du har lagt till ett betrott SSL-certifikat ska användas för fjärranslutningar i IIS kan du rensa den **Tillåt ej betrodda certifikat** kryssrutan. Information om hur du lägger till ett certifikat för att göra webbdistribution säker, finns i avsnittet **att göra Web distribuera säkra** senare i den här artikeln.

1. Om du vill använda webbdistribution måste publicera mekanism användarnamn och lösenord som du ställer in för din fjärrskrivbordsanslutning när du först publicerade paketet.

   a. I **användarnamn**, ange namnet på användaren.

   b. I **lösenord**, ange lösenordet.

   c. (Valfritt) Välj om du vill spara lösenordet i den här profilen **spara lösenordet**.

1. Om du vill publicera ändringarna till din webbroll, Välj **publicera**.

    Statusfältet visar **publicera igång**. När publiceringen är klar **publicera lyckades** visas. Ändringarna har nu distribuerats till rollen webbserver på den virtuella datorn. Nu kan du börja ditt Azure-program i Azure-miljön att testa dina ändringar.

### <a name="make-web-deploy-secure"></a>Att distribuera webbprogram säkrare

1. Webbdistribution använder en icke betrodd och självsignerat certifikat som standard, vilket inte rekommenderas för uppladdning av känsliga data. Om du behöver att skydda den här processen för känsliga data kan du lägga till ett SSL-certifikat som ska användas för Web Deploy-anslutningar. Det här certifikatet måste vara ett betrott certifikat som du kan hämta från en certifikatutfärdare (CA).

    Om du vill göra webbdistribution säker för varje virtuell dator för varje web-roller, måste du överföra det betrodda certifikatet som du vill använda för web distribuera till Azure-portalen. Det här certifikatet ser till att certifikatet har lagts till den virtuella datorn som skapas för webbrollen när du publicerar ditt program.

1. Följ dessa steg om du vill lägga till ett betrott SSL-certifikat i IIS ska använda för fjärranslutningar:

   a. För att ansluta till den virtuella datorn som kör webbrollen, väljer du instansen av rollen webbserver i **Cloud Explorer** eller **Server Explorer**, och välj sedan den **ansluta med hjälp av fjärrskrivbord**  kommando. Detaljerade anvisningar om hur du ansluter till den virtuella datorn finns [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md). Din webbläsare du uppmanas att hämta en `.rdp` fil.

   b. Om du vill lägga till ett SSL-certifikat, öppna management-tjänsten i IIS-hanteraren. I IIS-hanteraren aktivera SSL genom att öppna den **bindningar** länken i den **åtgärd** fönstret. Den **Lägg till webbplatsbindning** dialogrutan visas. Välj **Lägg till**, och välj sedan HTTPS i den **typen** listrutan. I den **SSL-certifikat** Välj SSL-certifikatet att du har signerats av en Certifikatutfärdare och att du har överförts till den Azure-portalen. Mer information finns i [Konfigurera anslutningsinställningar för hanteringstjänsten](http://go.microsoft.com/fwlink/?LinkId=215824).

      > [!NOTE]
      > Om du lägger till ett betrott certifikat för SSL på gul varningstriangel inte längre visas i den **Publiceringsguiden**.

## <a name="include-files-in-the-service-package"></a>Ta med filer i tjänstepaketet

Du kan behöva inkludera specifika filer i tjänstepaketet så att de är tillgängliga på den virtuella datorn som skapas för en roll. Du kanske exempelvis vill lägga till en `.exe` eller en `.msi` -fil som används av ett startskript till ditt abonnemang. Eller kanske du måste lägga till en sammansättning som kräver en roll eller worker webbrollsprojektet. För att inkludera filer, måste de läggas till lösningen för ditt Azure-program.

1. Använd följande steg för att lägga till en sammansättning i ett tjänstepaket:

   a. I **Solution Explorer**, öppna projektnoden för projektet som saknar den refererade sammansättningen.
   b. Lägg till sammansättningen i projektet genom att öppna snabbmenyn för den **referenser** mapp och välj sedan **Lägg till referens**. Dialogrutan Lägg till referens visas.
   c. Välj den referens som du vill lägga till och välj sedan **OK**. Referensen har lagts till i listan under den **referenser** mapp.
   d. Öppna snabbmenyn för sammansättningen som du har lagt till och välj **egenskaper**. Den **egenskaper** visas.

      Med den här sammansättningen i service-paketet i den **kopiera lokala listan** Välj **True**.
1. I **Solution Explorer** öppna projektnoden för projektet som saknar den refererade sammansättningen.

1. Lägg till sammansättningen i projektet genom att öppna snabbmenyn för den **referenser** mapp och välj sedan **Lägg till referens**. Den **Lägg till referens** visas.

1. Välj den referens som du vill lägga till och välj sedan den **OK** knappen.

    Referensen har lagts till i listan under den **referenser** mapp.

1. Öppna snabbmenyn för sammansättningen som du har lagt till och välj **egenskaper**. Fönstret Egenskaper visas.

1. Att inkludera den här sammansättningen i service-paketet i den **kopiera lokala** Välj **SANT**.

1. Om du vill ta med filer i tjänstepaketet som har lagts till din webbrollsprojektet, öppna snabbmenyn för filen och välj sedan **egenskaper**. Från den **egenskaper** fönstret Välj **innehåll** från den **Skapa åtgärd** listrutan.

1. Om du vill ta med filer i tjänstepaketet som har lagts till arbetsrollsprojektet, öppna snabbmenyn för filen och välj sedan **egenskaper**. Från den **egenskaper** fönstret Välj **kopiera om nyare** från den **kopiera till utdatakatalog** listrutan.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du publicerar till Azure från Visual Studio i [Publiceringsguiden för Azure-program](vs-azure-tools-publish-azure-application-wizard.md).
