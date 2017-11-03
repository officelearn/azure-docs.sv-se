---
title: "Förbereda för att publicera eller distribuera ett Azure-program från Visual Studio | Microsoft Docs"
description: "Läs om procedurerna för att konfigurera moln och storage-Kontotjänster och konfigurera Azure-program."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: cc4fb87e559f554634ae062a59bee31f0831da64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-to-publish-or-deploy-an-azure-application-from-visual-studio"></a>Förbereda för att publicera eller distribuera ett Azure-program från Visual Studio
## <a name="overview"></a>Översikt
Innan du kan publicera ett molntjänstprojekt, måste du ställa in följande tjänster:

* En **Molntjänsten** att köra dina roller i Azure-miljön
* En **lagringskonto** som ger åtkomst till tjänsterna Blob, köer och tabellen.

Använd följande procedurer för att konfigurera dessa tjänster och konfigurera ditt program

## <a name="create-a-cloud-service"></a>Skapa en molntjänst
Om du vill publicera en tjänst i molnet till Azure måste du först skapa en molntjänst som kör dina roller i Azure-miljön. Du kan skapa en molntjänst i den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885), enligt beskrivningen i avsnittet **skapa en tjänst i molnet med hjälp av den klassiska Azure-portalen**senare i det här avsnittet. Du kan också skapa en molntjänst i Visual Studio med hjälp av guiden.

### <a name="to-create-a-cloud-service-by-using-visual-studio"></a>Skapa en tjänst i molnet med hjälp av Visual Studio
1. Öppna snabbmenyn för Azure-projekt och välj **publicera**.

    ![VST_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)
2. Om du inte har loggat in, logga in med ditt användarnamn och lösenord för Microsoft-konto eller organisationskonto som är associerad med din Azure-prenumeration.
3. Välj den **nästa** knappen Fortsätt till den **inställningar** sidan.

    ![Guiden vanliga publiceringsinställningarna](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)
4. I den **molntjänster** Välj **Skapa nytt**. Den **skapa Azure-tjänster** visas.
5. Ange namnet på Molntjänsten. Namnet utgör en del av URL-Adressen för din tjänst och måste därför vara globalt unika. Namnet är inte skiftlägeskänsligt.

### <a name="to-create-a-cloud-service-by-using-the-azure-classic-portal"></a>Skapa en tjänst i molnet med hjälp av den klassiska Azure-portalen
1. Logga in på den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkId=253103) på Microsofts webbplats.
2. (valfritt) Om du vill visa en lista över molntjänster som du redan har skapat, välja molntjänster länken till vänster på sidan.
3. Välj den  **+**  ikonen i det nedre vänstra hörn och välj sedan **Molntjänsten** på menyn som visas. En annan skärm med två alternativ **Snabbregistrering** och **skapa anpassade**, visas. Om du väljer **Snabbregistrering**, du kan skapa en tjänst i molnet genom att bara ange Webbadressen och den region där den ska vara fysiskt värd. Om du väljer **skapa anpassade**, du kan publicera en tjänst i molnet omedelbart genom att ange ett paket (.cspkg-fil), konfigurationsfilen (.cscfg) och ett certifikat. Skapa anpassade krävs inte om du vill publicera din tjänst i molnet med hjälp av den **publicera** i ett Azure-projekt. Den **publicera** kommandot är tillgängligt på snabbmenyn för ett Azure-projekt.
4. Välj **Snabbregistrering** senare publicera din tjänst i molnet med hjälp av Visual Studio.
5. Ange ett namn för din molntjänst. Fullständig URL visas bredvid namnet.
6. Välj den region där de flesta användare finns i listan.
7. Längst ned i fönstret Välj den **skapa molntjänst** länk.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
Ett lagringskonto ger åtkomst till tjänsterna Blob, köer och tabellen. Du kan skapa ett lagringskonto med hjälp av Visual Studio eller [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkId=253103).

### <a name="to-create-a-storage-account-by-using-visual-studio"></a>Skapa ett lagringskonto med hjälp av Visual Studio
1. I **Solution Explorer**, öppna snabbmenyn för den **lagring** nod, och välj sedan **skapa Lagringskonto**.

    ![Skapa ett nytt Azure storage-konto](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)
2. Välj eller ange följande information för det nya lagringskontot i den **skapa Lagringskonto** dialogrutan.

   * Azure-prenumerationen som du vill lägga till lagringskontot.
   * Namnet som du vill använda för det nya lagringskontot.
   * Region eller affinitetsgrupp (till exempel västra USA eller Östasien).
   * Typ av du vill använda för lagringskontot, till exempel Geo-Redundant replikering.
3. När du är klar väljer **skapa**. Det nya lagringskontot som visas i den **lagring** lista i **Server Explorer**.

### <a name="to-create-a-storage-account-by-using-the-azure-classic-portal"></a>Skapa ett lagringskonto med hjälp av den klassiska Azure-portalen
1. Logga in på den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkId=253103) på Microsofts webbplats.
2. (Valfritt) Om du vill visa dina lagringskonton, Välj den **lagring** länk på panelen till vänster på sidan.
3. I det nedre vänstra hörnet på sidan Välj den  **+**  ikon.
4. I menyn som visas väljer du **lagring**, och välj sedan **Snabbregistrering**.
5. Namnge lagringskontot som leder till en unik url.
6. Namnge din tjänst i molnet. Fullständig URL visas bredvid namnet.
7. Välj en region där de flesta användare finns i listan över regioner.
8. Ange om du vill aktivera geo-replikering. Om du aktiverar geo-replikering kan sparas data på flera fysiska platser för att minska risken för dataförlust. Lagring som den här funktionen gör dyrare, men du kan minska kostnaderna genom att aktivera geografiska plats när du skapar storage-konto i stället för att lägga till funktionen senare. Mer information finns i [georeplikering](http://go.microsoft.com/fwlink/?LinkId=253108).
9. Längst ned i fönstret Välj den **skapa Lagringskonto** länk.

När du har skapat ditt lagringskonto, visas de webbadresser som du kan använda för att komma åt resurser i varje Azure-lagringstjänster och primära och sekundära åtkomstnycklarna för ditt konto. Du kan använda dessa nycklar för att autentisera förfrågningar som görs mot storage-tjänster.

> [!NOTE]
> Den sekundära åtkomstnyckeln ger samma åtkomst till ditt lagringskonto som den primära åtkomstnyckeln och genereras som en säkerhetskopia bör din primärnyckeln äventyras. Vi rekommenderar dessutom att du återskapar dina åtkomstnycklar regelbundet. Du kan ändra inställningen för en anslutningssträngen använda den sekundära nyckeln medan du återskapa den primära nyckeln kan du ändra det om du vill använda den återskapade primära nyckeln medan du återskapa den sekundära nyckeln.
>
>

## <a name="configure-your-app-to-use-services-provided-by-the-storage-account"></a>Konfigurera appen för att använda tjänster som tillhandahålls av storage-konto
Du måste konfigurera en roll som har åtkomst till lagringstjänster för att använda Azure storage-tjänster som du har skapat. Du kan använda flera konfigurationer för din Azure-projekt för att göra detta. Som standard skapas två i Azure-projekt. Genom att använda flera konfigurationer kan du använda samma anslutningssträng i koden, men har ett annat värde för en anslutningssträng i varje tjänstkonfigurationen. Du kan till exempel använda en tjänstkonfiguration att köra och felsöka programmet lokalt med hjälp av Azure storage-emulatorn och en annan tjänstkonfiguration att publicera programmet till Azure. Mer information om konfigurationer finns [konfigurera din Azure-projekt med flera tjänstkonfiguration](vs-azure-tools-multiple-services-project-configurations.md).

### <a name="to-configure-your-application-to-use-services-that-the-storage-account-provides"></a>Konfigurera programmet så att tjänster som tillhandahåller storage-konto
1. Öppna din Azure-lösning i Visual Studio. Öppna snabbmenyn för varje roll i din Azure-projekt som har åtkomst till storage-tjänster i Solution Explorer och välj **egenskaper**. En sida med namnet på rollen som visas i Visual Studio-redigeraren. Sidan visas fält för den **Configuration** fliken.
2. Välj i egenskapssidor för rollen, **inställningar**.
3. I den **tjänstkonfiguration** väljer du namnet på tjänstkonfigurationen som du vill redigera. Om du vill göra ändringar i alla tjänstkonfigurationer för den här rollen kan du välja **alla konfigurationer av**.  Mer information om hur du uppdaterar konfigurationer finns i avsnittet **hantera anslutningssträngar för Lagringskonton** i avsnittet [Konfigurera roller för en Azure Cloud Service med Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
4. Om du vill ändra inställningarna för alla-anslutningssträngar, Välj den **...** knappen bredvid inställningen. Den **skapa Lagringsanslutningssträng** dialogrutan visas.
5. Under **ansluta med**, Välj den **prenumerationen** alternativet.
6. I den **prenumeration** väljer din prenumeration. Om listan över prenumerationer inte innehåller det som du vill kan du välja den **hämta Publiceringsinställningar** länk.
7. I den **kontonamn** väljer du namnet på ditt lagringskonto. Azure-verktyg hämtar lagringskontouppgifter automatiskt med hjälp av .publishsettings-fil. Ange autentiseringsuppgifterna för ditt lagringskonto manuellt välja den **autentiseringsuppgifterna anges manuellt** alternativ och sedan fortsätta med den här proceduren. Du kan hämta din lagringskontonamn och primärnyckel från den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=213885). Om du inte vill ange ditt lagringskonto inställningar manuellt, Välj den **OK** för att stänga dialogrutan.
8. Välj den **ange lagringskonto** autentiseringsuppgifter länk.
9. I den **kontonamn** anger du namnet på ditt lagringskonto.

   > [!NOTE]
   > Logga in på den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885), och välj sedan den **lagring** knappen. Portalen visar en lista över storage-konton. Om du väljer ett konto öppnar en sida för den. Du kan kopiera namnet på lagringskontot från den här sidan. Om du använder en tidigare version av den klassiska portalen, namnet på ditt lagringskonto visas i den **Lagringskonton** vyn. Om du vill kopiera det här namnet, markera den i den **egenskaper** fönster för detta visa och välj sedan tangenterna Ctrl + C. Om du vill klistra in namnet i Visual Studio väljer du den **kontonamn** text och välj sedan tangenterna Ctrl + V.
   >
   >
10. I den **kontonyckel** anger primärnyckel, eller kopiera och klistra in den från den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885).
     Kopiera den här nyckeln:

    1. Längst ned på sidan för lämplig storage-konto, Välj den **hantera nycklar** knappen.
    2. På den **hantera nycklar åtkomst** sidan, markera texten på den primära åtkomstnyckeln och välj sedan tangenterna Ctrl + C.
    3. Klistra in nyckeln till i Azure-verktyg, den **kontonyckel** rutan.
    4. Du måste välja ett av följande alternativ för att avgöra hur tjänsten kommer åt storage-konto:

       * **Använd HTTP**. Detta är det standard alternativet. Till exempel `http://<account name>.blob.core.windows.net`.
       * **Använda HTTPS** för en säker anslutning. Till exempel `https://<accountname>.blob.core.windows.net`.
       * **Ange anpassade slutpunkter** för var och en av de tre tjänsterna. Du kan sedan ange dessa slutpunkter i fältet för en specifik tjänst.

         > [!NOTE]
         > Om du skapar anpassade slutpunkter, kan du skapa en mer komplex anslutningssträng. Du kan ange slutpunkter lagring som innehåller ett domännamn som du har registrerat för ditt lagringskonto med Blob-tjänsten när du använder den här strängformat. Du kan också ge endast åtkomst till blob-resurser i en enskild behållare via en signatur för delad åtkomst. Mer information om hur du skapar anpassade slutpunkter finns [konfigurera Azure Storage-anslutningssträngar](storage/common/storage-configure-connection-string.md).
         >
         >
11. Om du vill spara ändringarna anslutning sträng, Välj den **OK** knappen och välj sedan den **spara** i verktygsfältet. När du har sparat ändringarna kan du hämta värdet för den här anslutningssträngen i koden med [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Välj konfigurationen för tjänsten som innehåller Azure storage-konto för anslutningssträngen när du publicerar programmet till Azure. När programmet publiceras, kontrollerar du att programmet fungerar som förväntat mot Azure storage-tjänster

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du publicerar appar till Azure från Visual Studio i [publicering av en tjänst i molnet med hjälp av Azure-verktyg](vs-azure-tools-publishing-a-cloud-service.md).
