---
title: Så här hanterar du tjänstkonfiguration och -profiler | Microsoft Docs
description: Lär dig hur du arbetar med tjänsten konfigurationer och profiler konfigurationsfiler | lagra inställningarna för distributionen vilket publiceringsinställningar för molntjänster.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 7da8c551-fb06-4057-b5c7-c77f4b39d803
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/11/2017
ms.author: ghogen
ms.openlocfilehash: 06ec029d1978488649d9b1e6f8a68f8ab5179f04
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969358"
---
# <a name="how-to-manage-service-configurations-and-profiles"></a>Så här hanterar du tjänstkonfiguration och profiler
## <a name="overview"></a>Översikt
När du publicerar en tjänst i molnet, Visual Studio lagrar konfigurationsinformation i två typer av konfigurationsfiler: konfigurationer och profiler. Tjänstkonfigurationer (.cscfg-filer) sparar inställningar för distribution för en Azure-molntjänst. Azure använder dessa konfigurationsfiler vid hantering av dina molntjänster. Å andra sidan profiler (.azurePubxml-filer) store publiceringsinställningar för molntjänster. Dessa inställningar gäller koll på vad du väljer när du använder Publiceringsguiden och används lokalt av Visual Studio. Det här avsnittet förklarar hur du arbetar med båda typerna av konfigurationsfiler.

## <a name="service-configurations"></a>Tjänstkonfiguration
Du kan skapa flera konfigurationer för var och en av dina distributionsmiljöer. Du kan till exempel skapa en tjänstkonfiguration för den lokala miljön som används för att köra och testa ett Azure-program och en annan tjänstkonfigurationen för din produktionsmiljö.

Du kan lägga till, ta bort, byta namn på och ändra konfigurationerna tjänst efter dina behov. Du kan hantera konfigurationerna service från Visual Studio, enligt följande bild.

![Hantera Tjänstkonfigurationer av](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Du kan också öppna den **hantera konfigurationer** dialogrutan från rollens egenskapssidor. Öppna egenskaperna för en roll i Azure-projektet genom att öppna snabbmenyn för rollen och välj sedan **egenskaper**. På den **inställningar** fliken, expandera den **tjänstkonfiguration** och sedan välja **hantera** att öppna den **hantera konfigurationer** dialogrutan.

### <a name="to-add-a-service-configuration"></a>Att lägga till en tjänstkonfiguration
1. Öppna snabbmenyn för Azure-projekt i Solution Explorer och välj sedan **hantera konfigurationer**.
   
    Den **hantera tjänstkonfiguration** dialogrutan visas.
2. Om du vill lägga till en tjänstkonfiguration, måste du skapa en kopia av en befintlig konfiguration. Gör detta genom att välja den konfiguration som du vill kopiera från listan och välj sedan **Skapa kopia**.
3. (Valfritt) Om du vill ge konfigurationen av ett annat namn, Välj ny tjänstkonfiguration från listan och välj sedan **Byt namn på**. I den **namn** text skriver du det namn som du vill använda för den här tjänstkonfigurationen och välj sedan **OK**.
   
    En ny tjänstkonfigurationsfil som heter ServiceConfiguration. [Ny Name] .cscfg läggs till i Azure-projekt i Solution Explorer.

### <a name="to-delete-a-service-configuration"></a>Att ta bort en tjänstkonfiguration
1. Öppna snabbmenyn för Azure-projekt i Solution Explorer och välj sedan **hantera konfigurationer**.
   
    Den **hantera tjänstkonfiguration** dialogrutan visas.
2. Om du vill ta bort en tjänstkonfiguration, väljer du den konfiguration som du vill ta bort från den **namn** listan och välj sedan **ta bort**. En dialogruta att verifiera att du vill ta bort den här konfigurationen.
3. Välj **Ta bort**.
   
     Tjänstkonfigurationsfilen tas bort från Azure-projekt i Solution Explorer.

### <a name="to-rename-a-service-configuration"></a>Att byta namn på en tjänstkonfiguration
1. Öppna snabbmenyn för Azure-projekt i Solution Explorer och välj sedan **hantera konfigurationer**.
   
    Den **hantera tjänstkonfiguration** dialogrutan visas.
2. Byt namn på en tjänstkonfiguration genom att välja den nya tjänstkonfigurationen från den **namn** och sedan välja **Byt namn på**. I den **namn** text skriver du det namn som du vill använda för den här tjänstkonfigurationen och välj sedan **OK**.
   
    Namnet på tjänstkonfigurationsfilen ändras i Azure-projektet i Solution Explorer.

### <a name="to-change-a-service-configuration"></a>Ändra en tjänstkonfiguration
* Om du vill ändra en tjänstkonfiguration öppna snabbmenyn för den specifika rollen som du vill ändra i Azure-projektet och väljer sedan **egenskaper**. Se [så här: Konfigurera rollerna för en Azure-molntjänst med Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) för mer information.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Se annan inställning kombinationer med hjälp av profiler
Med hjälp av en profil kan du automatiskt fylla i den **Publiceringsguiden** med olika kombinationer av inställningar för olika syften. Exempel: du kan ha en profil för felsökning och en annan version byggen. I så fall kan din **felsöka** profil skulle ha **IntelliTrace** aktiverad och **felsöka** vald, konfiguration och dina **versionen** profilen skulle ha **IntelliTrace** inaktiverad och **versionen** valda konfigurationen. Du kan också använda olika profiler för att distribuera en tjänst med ett annat lagringskonto.

När du kör guiden för första gången, skapas en standardprofil. Visual Studio lagrar profilen i en fil med filnamnstillägget .azurePubXml, som har lagts till i projektet Azure under den **profiler** mapp. Om du manuellt ange olika alternativ när du kör guiden senare uppdaterar filen automatiskt. Innan du kör följande procedur kan bör du redan har publicerat din molntjänst minst en gång.

### <a name="to-add-a-profile"></a>Att lägga till en profil
1. Öppna snabbmenyn för projektet Azure och välj sedan **publicera**.
2. Bredvid den **målprofilen** väljer den **spara profil** knapp, så som visas i följande bild. Detta skapar en profil.
   
    ![Skapa en ny profil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)
3. När profilen har skapats kan du välja **< hantera... >** i den **målprofilen** lista.
   
    Den **Spravovat Profily** dialogruta, som i följande bild.
   
    ![Hantera profiler dialogrutan](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)
4. I den **namn** listan, Välj en profil och välj sedan **Skapa kopia**.
5. Välj den **Stäng** knappen.
   
    Den nya profilen visas i profillistan mål.
6. I den **målprofilen** väljer du den profil som du nyss skapade. Guiden Publicera inställningarna är ifyllda med alternativ från den profil som du har valt.
7. Välj den **föregående** och **nästa** knappar för att visa varje sida i guiden Publicera och sedan anpassa inställningarna för den här profilen. Se [Publiceringsguiden för Azure Application](http://go.microsoft.com/fwlink/p/?LinkID=623085) information.
8. När du är klar med att anpassa inställningarna väljer **nästa** att gå tillbaka till sidan Inställningar. Profilen har sparats när du publicerar tjänsten med hjälp av dessa inställningar eller om du väljer **spara** bredvid listan över profiler.

### <a name="to-rename-or-delete-a-profile"></a>Byt namn på eller ta bort en profil
1. Öppna snabbmenyn för projektet Azure och välj sedan **publicera**.
2. I den **målprofilen** väljer **hantera**.
3. I den **Spravovat Profily** dialogrutan, Välj den profil som du vill ta bort och välj sedan **ta bort**.
4. I den bekräftande dialogrutan som visas, väljer **OK**.
5. Välj **Stäng**.

### <a name="to-change-a-profile"></a>Ändra en profil
1. Öppna snabbmenyn för projektet Azure och välj sedan **publicera**.
2. I den **målprofilen** väljer du den profil som du vill ändra.
3. Välj den **föregående** och **nästa** knappar för att visa varje sida i guiden Publicera och ändra sedan önskade inställningar. Se [Publiceringsguiden för Azure Application](http://go.microsoft.com/fwlink/p/?LinkID=623085) information.
4. När du är klar med ändringarna väljer **nästa** att gå tillbaka till den **inställningar** sidan.
5. (Valfritt) Välj **publicera** att publicera molntjänst med de nya inställningarna. Om du inte vill publicera din molntjänst just nu och stänger guiden Publicera, frågar Visual Studio om du vill spara ändringarna i profilen.

## <a name="next-steps"></a>Nästa steg
Läs om hur du konfigurerar andra delar av din Azure-projekt från Visual Studio i [konfigurerar ett Azure-projekt](http://go.microsoft.com/fwlink/p/?LinkID=623075)

