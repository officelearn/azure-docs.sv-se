---
title: Så här hanterar du tjänstkonfiguration och profiler | Microsoft Docs
description: Lär dig hur du arbetar med tjänsten konfigurationer och profiler konfigurationsfiler | som lagrar inställningar för distributionsmiljöer och publiceringsinställningar för molntjänster.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 7da8c551-fb06-4057-b5c7-c77f4b39d803
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 8/11/2017
ms.author: ghogen
ms.openlocfilehash: 4f2dce7cdbf81718f9ccf0c1b7d52c6678cc847f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799064"
---
# <a name="how-to-manage-service-configurations-and-profiles"></a>Så här hanterar du tjänstkonfiguration och profiler
## <a name="overview"></a>Översikt
När du publicerar en tjänst i molnet, Visual Studio lagrar konfigurationsinformation i två typer av konfigurationsfiler: tjänsten konfigurationer och profiler. Tjänstkonfiguration (.cscfg-filer) sparar inställningar för distributionsmiljöer för en Azure-molntjänst. Azure använder konfigurationsfilerna vid hantering av dina molntjänster. Å andra sidan profiler (.azurePubxml-filer) store publiceringsinställningar för molntjänster. Dessa inställningar är en post på vad du väljer när du använder guiden Publicera och används lokalt av Visual Studio. Det här avsnittet beskriver hur du arbetar med båda typerna av konfigurationsfiler.

## <a name="service-configurations"></a>Tjänstkonfiguration
Du kan skapa flera konfigurationer för varje distributionsmiljöer. Du kan till exempel skapa en tjänstkonfiguration för den lokala miljön som används för att köra och testa ett Azure-program och en annan tjänstkonfigurationen för din produktionsmiljö.

Du kan lägga till, ta bort, byta namn på och ändra dessa konfigurationer baserat på dina krav. Du kan hantera dessa konfigurationer från Visual Studio enligt följande bild.

![Hantera konfigurationer](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Du kan också öppna den **hantera konfigurationer** dialogruta från rollens egenskapssidor. Öppna egenskaperna för en roll på Azure-projekt, öppna snabbmenyn för rollen och välj sedan **egenskaper**. På den **inställningar** fliken, expandera den **tjänstkonfiguration** listan och välj sedan **hantera** att öppna den **hantera konfigurationer** i dialogrutan.

### <a name="to-add-a-service-configuration"></a>Att lägga till en tjänstkonfiguration
1. Öppna snabbmenyn för Azure-projekt i Solution Explorer och markera **hantera konfigurationer**.
   
    Den **hantera tjänstkonfiguration** dialogrutan visas.
2. Om du vill lägga till en tjänstkonfiguration, måste du skapa en kopia av en befintlig konfiguration. Om du vill göra det, väljer du den konfiguration som du vill kopiera från listan och välj sedan **Skapa kopia**.
3. (Valfritt) Om du vill ge ett annat namn för konfigurationen av tjänsten, Välj ny tjänstkonfiguration från listan och sedan **Byt namn på**. I den **namn** text Skriv det namn som du vill använda för den här tjänstkonfigurationen och välj sedan **OK**.
   
    En ny service konfigurationsfil som heter ServiceConfiguration. [Nytt namn] .cscfg har lagts till i Azure-projekt i Solution Explorer.

### <a name="to-delete-a-service-configuration"></a>Ta bort en tjänstkonfiguration
1. Öppna snabbmenyn för Azure-projekt i Solution Explorer och markera **hantera konfigurationer**.
   
    Den **hantera tjänstkonfiguration** dialogrutan visas.
2. Om du vill ta bort en tjänstkonfiguration, väljer du den konfiguration som du vill ta bort från den **namn** och väljer sedan **ta bort**. En dialogruta visas för att verifiera att du vill ta bort den här konfigurationen.
3. Välj **Ta bort**.
   
     Tjänstkonfigurationsfilen tas bort från Azure-projekt i Solution Explorer.

### <a name="to-rename-a-service-configuration"></a>Att byta namn på en tjänstkonfiguration
1. Öppna snabbmenyn för Azure-projekt i Solution Explorer och markera sedan **hantera konfigurationer**.
   
    Den **hantera tjänstkonfiguration** dialogrutan visas.
2. Om du vill byta namn på en tjänstkonfiguration, Välj ny tjänstkonfiguration från den **namn** listan och välj sedan **Byt namn på**. I den **namn** text Skriv det namn som du vill använda för den här tjänstkonfigurationen och välj sedan **OK**.
   
    Namnet på tjänstkonfigurationsfilen ändras i Azure-projekt i Solution Explorer.

### <a name="to-change-a-service-configuration"></a>Så här ändrar du en tjänstkonfiguration
* Om du vill ändra en tjänstkonfiguration öppna snabbmenyn för viss roll som du vill ändra i Azure-projekt och väljer sedan **egenskaper**. Se [så här: Konfigurera roller för en Azure Cloud Service med Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) för mer information.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Se annan inställning kombinationer med hjälp av profiler
Med hjälp av en profil kan du automatiskt fylla i den **Publiceringsguiden** med olika kombinationer av inställningar för olika ändamål. Till exempel kan du ha en profil för felsökning och en annan för versionen bygger. I så fall din **felsöka** profil skulle ha **IntelliTrace** aktiverad och **felsöka** konfiguration som har valts och dina **versionen** profil skulle ha **IntelliTrace** inaktiverad och **versionen** konfiguration som valdes. Du kan också använda olika profiler för att distribuera en tjänst med ett annat lagringskonto.

När du kör guiden för första gången, skapas en standardprofil. Visual Studio lagrar profilen i en fil med filnamnstillägget .azurePubXml, som har lagts till i din Azure-projekt under den **profiler** mapp. Om du manuellt ange olika alternativ när du kör guiden senare uppdaterar filen automatiskt. Innan du kör följande procedur kan bör du redan har publicerat din molntjänst minst en gång.

### <a name="to-add-a-profile"></a>Att lägga till en profil
1. Öppna snabbmenyn för din Azure-projekt och välj sedan **publicera**.
2. Bredvid den **mål profil** väljer den **spara profil** knappen som visas i följande bild. Detta skapar en profil för dig.
   
    ![Skapa en ny profil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)
3. När profilen har skapats, Välj **< hantera... >** i den **mål profil** lista.
   
    Den **hantera profiler** dialogruta, som i följande bild.
   
    ![Hantera profiler dialogrutan](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)
4. I den **namn** listan, väljer du en profil och väljer sedan **Skapa kopia**.
5. Välj den **Stäng** knappen.
   
    Den nya profilen visas i mållistan för profilen.
6. I den **mål profil** väljer du den profil som du nyss skapade. Inställningar för guiden Publicera fylls i med alternativ från den profil du valt.
7. Välj den **föregående** och **nästa** knappar för att visa varje sida i guiden Publicera och anpassa inställningarna för den här profilen. Se [Publiceringsguiden för Azure-program](http://go.microsoft.com/fwlink/p/?LinkID=623085) information.
8. När du är klar med att anpassa inställningarna väljer du **nästa** att gå tillbaka till sidan Inställningar. Profilen sparas när du publicerar tjänsten med hjälp av dessa inställningar eller om du väljer **spara** bredvid listan över profiler.

### <a name="to-rename-or-delete-a-profile"></a>Byta namn på eller ta bort en profil
1. Öppna snabbmenyn för din Azure-projekt och välj sedan **publicera**.
2. I den **mål profil** väljer **hantera**.
3. I den **hantera profiler** dialogrutan, Välj den profil som du vill ta bort och välj sedan **ta bort**.
4. Välj i bekräftelsedialogrutan **OK**.
5. Välj **Stäng**.

### <a name="to-change-a-profile"></a>Ändra en profil
1. Öppna snabbmenyn för din Azure-projekt och välj sedan **publicera**.
2. I den **mål profil** väljer du den profil som du vill ändra.
3. Välj den **föregående** och **nästa** knappar för att visa varje sida i guiden Publicera och ändrar sedan inställningarna som du vill använda. Se [Publiceringsguiden för Azure-program](http://go.microsoft.com/fwlink/p/?LinkID=623085) information.
4. När du är klar med ändringarna markerar **nästa** att gå tillbaka till den **inställningar** sidan.
5. (Valfritt) Markera **publicera** att publicera Molntjänsten med de nya inställningarna. Om du inte vill publicera Molntjänsten just nu och stänger guiden Publicera, tillfrågas Visual Studio du om du vill spara ändringarna i profilen.

## <a name="next-steps"></a>Nästa steg
Läs om hur du konfigurerar andra delar av din Azure-projekt från Visual Studio i [konfigurera ett Azure-projekt](http://go.microsoft.com/fwlink/p/?LinkID=623075)

