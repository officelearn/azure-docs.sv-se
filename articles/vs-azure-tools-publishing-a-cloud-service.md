---
title: Publicera en tjänst i molnet med hjälp av Azure-verktyg | Microsoft Docs
description: Läs mer om hur du publicerar Azure cloud service-projekt med hjälp av Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: a60f00e1df994988bfeae112db10bffa43f81642
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969498"
---
# <a name="publishing-a-cloud-service-using-visual-studio"></a>Publicera en molntjänst med Visual Studio

Visual Studio kan publicera ett program direkt till Azure, med stöd för både mellanlagring och produktion miljöer i en molntjänst. När du publicerar kan välja du distributionsmiljö och ett lagringskonto som används tillfälligt för distributionspaketet.

När du utvecklar och testar ett Azure-program, kan du använda webbdistribution för att publicera ändringar stegvis för web-roller. När du har publicerat ditt program till en distributionsmiljö låter Web Deploy dig distribuera ändringar direkt till den virtuella datorn som kör webbrollen. Du behöver inte paketera och publicera hela Azure programmet varje gång som du vill uppdatera dina web-roll att testa ändringarna. Du kan ha dina web-roll ändringar tillgängliga i molnet för testning utan att vänta på att programmet publiceras till en distributionsmiljö med den här metoden.

Använd följande procedurer för att publicera dina Azure-program och uppdatera en webbroll med hjälp av Web Deploy:

- Publicera eller paketera ett Azure-program från Visual Studio
- Uppdatera en webbroll som en del av utvecklings- och testcykel

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publicera eller paketera ett Azure-program från Visual Studio

När du publicerar dina Azure-program kan göra du något av följande uppgifter:

- Skapa ett tjänstepaket för: du kan använda det här paketet och konfigurationsfilen för tjänsten för att publicera programmet till en distributionsmiljö av den [Azure-portalen](https://portal.azure.com).

- Publicera din Azure-projekt från Visual Studio: för att publicera programmet direkt till Azure måste du använda guiden Publicera. Mer information finns i [Publiceringsguiden för Azure Application](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Att skapa ett servicepaket från Visual Studio

1. När du är redo att publicera ditt program, öppna Solution Explorer, öppna snabbmenyn för Azure-projekt som innehåller dina roller och välj Publicera.

1. Följ dessa steg om du vill skapa ett servicepaket:

   a. På snabbmenyn för Azure-projekt och väljer **paketet**.

   b. I den **paketera Azure programmet** dialogrutan Välj tjänstens konfiguration som du vill skapa ett paket och välj sedan versionskonfiguration.

   c. (Valfritt) Om du vill aktivera Fjärrskrivbord för Molntjänsten när du har publicerat, Välj **aktivera Fjärrskrivbord för alla roller**, och välj sedan **inställningar** konfigurera Fjärrskrivbordets autentiseringsuppgifter. Mer information finns i [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med hjälp av Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

      Om du vill felsöka cloud Services när du har publicerat aktivera fjärrfelsökning genom att välja **aktivera Fjärrfelsökningsprogrammet för alla roller**.

   d. För att skapa paketet, Välj den **paketet** länk.

      Utforskaren visar sökvägen till det nya paketet. Du kan kopiera den här platsen så att du kan använda den från Azure-portalen.

   e. Om du vill publicera det här paketet till en distributionsmiljö, måste du använda den här platsen som paketplatsen när du skapar en tjänst i molnet och distribuera det här paketet till en miljö med Azure-portalen.

1. (Valfritt) Om du vill avbryta distributionsprocessen på snabbmenyn för objektet på raden i aktivitetsloggen, väljer **avbryta och ta bort**. Detta kommando stoppar distributionsprocessen och tar bort distributionsmiljö från Azure. Ta bort miljön efter distributionen med Azure-portalen.

1. (Valfritt) När dina rollinstanser har startat, visar Visual Studio automatiskt distributionsmiljö i den **molntjänster** noden i Server Explorer. Härifrån kan se du status för enskilda rollinstanser. Se [hantera Azure-resurser med Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md). Följande bild visar rollinstanserna när de är fortfarande i initierar tillstånd:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Uppdatera en webbroll som en del av utvecklings- och testcykel

Om din Apps serverdel infrastruktur är stabilt, men web-roller ska mer uppdateras regelbundet, kan du använda webbdistribution för att uppdatera en webbroll i projektet. Webbdistribution är praktiskt när du inte vill återskapa och distribuera om arbetsroller backend-server, eller om du har flera web-roller och du vill uppdatera endast en av de web-rollerna.

### <a name="requirements-for-using-web-deploy"></a>Krav för att använda Web Deploy

- **För utveckling och testning i utvärderingssyfte**: ändringarna görs direkt till den virtuella datorn där webbrollen körs. Om den här virtuella datorn har återvinns, förloras ändringarna eftersom det ursprungliga paketet som du har publicerat används för att återskapa den virtuella datorn för rollen. Publicera programmet att hämta de senaste ändringarna för webbrollen.

- **Endast webbroller kan uppdateras**: Worker-roller kan inte uppdateras. Dessutom kan du kan inte uppdatera den `RoleEntryPoint` i `web role.cs`.

- **Stöder bara en enda instans av en webbroll**: du kan inte ha flera instanser av en webbroll i din distributionsmiljö. Flera webbroller varje med endast en instans stöds dock.

- **Aktivera anslutningar till fjärrskrivbord**: det här kravet kan webbdistribution för att använda användare och lösenord för att ansluta till den virtuella datorn för att distribuera ändringarna till den server som kör Internet Information Services (IIS). Du kan dessutom behöva ansluta till den virtuella datorn för att lägga till ett betrott certifikat i IIS på den virtuella datorn. (Det här certifikatet säkerställer att fjärranslutning för IIS som används av webbdistribution är säker.)

Följande procedur förutsätter att du använder den **publicera Azure-program** guiden.

### <a name="enable-web-deploy-when-you-publish-your-application"></a>Aktivera Web Deploy när du publicerar ditt program

1. Att aktivera den **aktivera webbdistribution för alla webbroller** alternativet måste du först konfigurera anslutningar till fjärrskrivbord. Välj **aktivera Fjärrskrivbord** för alla roller och sedan ange de autentiseringsuppgifter som används för att ansluta via en fjärranslutning i den **konfiguration av fjärrskrivbord** som visas. Se [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med hjälp av Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

1. Om du vill aktivera webbdistribution för alla web-roller i ditt program, Välj **aktivera webbdistribution för alla webbroller**.

    En gul varningstriangel visas. Webbdistribution använder ett självsignerat certifikat som inte är betrodd som standard, vilket inte rekommenderas för att ladda upp känsliga data. Om du vill skydda den här processen för känsliga data kan du lägga till ett SSL-certifikat som ska användas för Web Deploy-anslutningar. Det här certifikatet måste vara ett betrott certifikat. Mer information finns i [skydda web distribuera](#make-web-deploy-secure).

1. Välj **nästa** att visa den **sammanfattning** skärmen och välj sedan **publicera** att distribuera Molntjänsten.

    Molntjänsten har publicerats. Den virtuella datorn som skapas har aktiverats för IIS så att Web Deploy kan användas för att uppdatera dina web-roller utan att publicera dem för fjärranslutningar.

   > [!NOTE]
   > Om du har fler än en instans som konfigurerats för en webbroll, visas ett varningsmeddelande om att varje webbroll är begränsad till en instans endast i paketet som skapas för att publicera ditt program. Klicka på **OK** för att fortsätta. Enligt informationen i avsnittet krav, kan du ha mer än en webbroll men endast en instans av varje roll.

### <a name="update-your-web-role-by-using-web-deploy"></a>Uppdatera din webbroll med hjälp av Web Deploy

1. Om du vill använda webbdistribution måste göra ändringar i koden i projektet för någon av dina web-roller i Visual Studio som du vill publicera, högerklicka på den här projektnoden i din lösning och peka på **publicera**. Den **Publicera webbplats** dialogrutan visas.

1. (Valfritt) Om du har lagt till ett betrott SSL-certifikat ska användas för fjärranslutningar för IIS, kan du rensa den **Tillåt ej betrodda certifikat** markerar du kryssrutan. Information om hur du lägger till ett certifikat för att skydda Web Deploy finns i avsnittet **att göra Web distribuera säkra** senare i den här artikeln.

1. Om du vill använda webbdistribution måste mekanismen för publicera det användarnamn och lösenord som du har konfigurerat för din anslutning till fjärrskrivbord när du först publicerade paketet.

   a. I **användarnamn**, anger du användarnamnet.

   b. I **lösenord**, ange lösenordet.

   c. (Valfritt) Om du vill spara lösenordet i den här profilen, väljer **spara lösenordet**.

1. För att publicera ändringarna i din webbroll, Välj **publicera**.

    Statusfältet visar **publicera igång**. När publiceringen har slutförts **publicera lyckades** visas. Ändringarna har nu distribuerats till web-roll på den virtuella datorn. Nu kan du börja dina Azure-program i Azure-miljön att testa ändringarna.

### <a name="make-web-deploy-secure"></a>Skydda web distribuera

1. Webbdistribution använder ett självsignerat certifikat som inte är betrodd som standard, vilket inte rekommenderas för att ladda upp känsliga data. Om du vill skydda den här processen för känsliga data kan du lägga till ett SSL-certifikat som ska användas för Web Deploy-anslutningar. Det här certifikatet måste vara ett betrott certifikat som du kan hämta från en certifikatutfärdare (CA).

    Om du vill skydda Web Deploy för varje virtuell dator för var och en av dina web-roller, måste du ladda upp det betrodda certifikatet som du vill använda för webben distribuera till Azure-portalen. Det här certifikatet ser till att certifikatet har lagts till den virtuella dator som har skapats för webbrollen när du publicerar ditt program.

1. Följ dessa steg om du vill lägga till ett betrott SSL-certifikat i IIS ska använda för fjärranslutningar:

   a. För att ansluta till den virtuella datorn som kör webbrollen, väljer du instansen av web-roll i **Cloud Explorer** eller **Server Explorer**, och välj sedan den **ansluta med hjälp av fjärrskrivbord**  kommando. Detaljerade anvisningar om hur du ansluter till den virtuella datorn i [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med hjälp av Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md). Din webbläsare uppmanar dig att ladda ned en `.rdp` fil.

   b. Om du vill lägga till ett SSL-certifikat, öppna management-tjänsten i IIS-hanteraren. I IIS-hanteraren aktivera SSL genom att öppna den **bindningar** länken i den **åtgärd** fönstret. Den **Lägg till bindning för webbplats** dialogrutan visas. Välj **Lägg till**, och välj sedan HTTPS i den **typ** listrutan. I den **SSL-certifikat** väljer SSL-certifikatet att du har signerats av en Certifikatutfärdare och att du har överförts till Azure-portalen. Mer information finns i [Konfigurera anslutningsinställningar för Management-tjänsten](http://go.microsoft.com/fwlink/?LinkId=215824).

      > [!NOTE]
      > Om du lägger till ett betrott SSL-certifikat, gul varningstriangel inte längre visas i den **Publiceringsguiden**.

## <a name="include-files-in-the-service-package"></a>Inkludera filer i tjänstpaketet

Du kan behöva innehålla specifika filer i ditt abonnemang så att de är tillgängliga på den virtuella dator som har skapats för en roll. Du kanske exempelvis vill lägga till en `.exe` eller en `.msi` -fil som används av ett startskript till ditt abonnemang. Eller du kan behöva lägga till en sammansättning som kräver att en roll eller worker webbrollsprojektet. För att inkludera filer, måste de läggas till lösningen för dina Azure-program.

1. Använd följande steg för att lägga till en sammansättning i ett tjänstepaket:

   a. I **Solution Explorer**, öppna projektnoden för projektet som saknar den refererade sammansättningen.
   b. Lägg till sammansättningen i projektet genom att öppna snabbmenyn för den **referenser** mapp och välj sedan **Lägg till referens**. Dialogrutan Lägg till referens visas.
   c. Välj referens som du vill lägga till och välj sedan **OK**. Referensen har lagts till i listan under den **referenser** mapp.
   d. Öppna snabbmenyn för sammansättningen som du har lagt till och välj **egenskaper**. Den **egenskaper** fönster visas.

      Att inkludera den här sammansättningen i tjänstpaketet, i den **kopiera lokala lista** väljer **SANT**.
1. I **Solution Explorer** öppna projektnoden för projektet som saknar den refererade sammansättningen.

1. Lägg till sammansättningen i projektet genom att öppna snabbmenyn för den **referenser** mapp och välj sedan **Lägg till referens**. Den **Lägg till referens** dialogruta.

1. Välj referens som du vill lägga till och välj sedan den **OK** knappen.

    Referensen har lagts till i listan under den **referenser** mapp.

1. Öppna snabbmenyn för sammansättningen som du har lagt till och välj **egenskaper**. Fönstret Egenskaper visas.

1. Att inkludera den här sammansättningen i tjänstpaketet, i den **kopiera lokala** väljer **SANT**.

1. För att inkludera filer i tjänstpaketet som har lagts till i din webbrollsprojektet, öppna snabbmenyn för filen och välj sedan **egenskaper**. Från den **egenskaper** fönstret Välj **innehåll** från den **byggåtgärd** listruta.

1. För att inkludera filer i tjänstpaketet som har lagts till arbetsrollsprojektet, öppna snabbmenyn för filen och välj sedan **egenskaper**. Från den **egenskaper** fönstret Välj **kopiera om nyare** från den **kopiera till utdatakatalog** listruta.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du publicerar till Azure från Visual Studio, finns i [Publiceringsguiden för Azure Application](vs-azure-tools-publish-azure-application-wizard.md).
