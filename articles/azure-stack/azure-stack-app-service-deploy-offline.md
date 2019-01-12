---
title: Distribuera App Service i en miljö som är offline i Azure Stack | Microsoft Docs
description: Detaljerad information om hur du distribuerar App Service i en frånkopplad Azure Stack-miljö som skyddas av AD FS.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: anwestg
ms.openlocfilehash: db4c0f2d1197a190b33bd297bb597fd19057d875
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54230347"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Lägga till en App Service-resursprovider i en frånkopplad Azure Stack-miljö som skyddas av AD FS

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

> [!IMPORTANT]
> Uppdateringen är 1809 integrerade Azure Stack-system eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service 1.4.

Genom att följa anvisningarna i den här artikeln kan du installera den [App Service-resursprovider](azure-stack-app-service-overview.md) till en Azure Stack-miljö som är:

- inte ansluten till Internet
- skyddas av Active Directory Federation Services (AD FS).

 > [!IMPORTANT]
 > Innan du distribuerar resursprovidern, granska viktig information att lära dig om nya funktioner och korrigeringar kända problem som kan påverka din distribution.
 
Om du vill lägga till App Service-resursprovider i distributionen Azure Stack, måste du slutföra de här toppnivåaktiviteter:

1. Slutför den [nödvändiga steg](azure-stack-app-service-before-you-get-started.md) (t.ex. köpa certifikat, vilket kan ta ett par dagar att ta emot).
2. [Ladda ned och extrahera filer för installation och helper](azure-stack-app-service-before-you-get-started.md) till en dator som är ansluten till Internet.
3. Skapa ett offlineinstallationspaket.
4. Kör installationsfilen appservice.exe.

## <a name="create-an-offline-installation-package"></a>Skapa ett offlineinstallationspaket

Om du vill distribuera App Service i en frånkopplad miljö måste du först skapa ett offlineinstallationspaket på en dator som är ansluten till Internet.

1. Kör installationsprogrammet för AppService.exe på en dator som är ansluten till Internet.

2. Klicka på **Avancerat** > **skapa offlineinstallationspaket**.

    ![App Service-installationsprogrammet][1]

3. App Service-installationsprogrammet skapar ett offlineinstallationspaket och visar sökvägen till den. Du kan klicka på **Öppna mapp** öppna mappen i Utforskaren.

    ![App Service-installationsprogrammet](media/azure-stack-app-service-deploy-offline/image02.png)

4. Kopiera installationsprogrammet (AppService.exe) och offlineinstallationspaketet till din Azure Stack-värddatorn.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Slutför offlineinstallation av App Service i Azure Stack

1. Kör appservice.exe som administratör från en dator som kan nå Azure Stack-administratören Azure Resource Manager-slutpunkten.

2. Klicka på **Avancerat** > **Slutför offlineinstallationen**.

    ![App Service-installationsprogrammet][2]

3. Bläddra till platsen för det offlineinstallationspaket som du skapade tidigare och klicka sedan på **nästa**.

    ![App Service-installationsprogrammet](media/azure-stack-app-service-deploy-offline/image04.png)

4. Granska och acceptera licensvillkoren för programvara från Microsoft och klickar sedan på **nästa**.

5. Granska och acceptera licensvillkoren från tredje part och klicka sedan på **nästa**.

6. Kontrollera att konfigurationsinformationen för App Service-molnet är korrekt. Om du använde standardinställningarna under distributionen av Azure Stack Development Kit kan godkänna du standardvärdena här. Om du har anpassat alternativ när du har distribuerat Azure Stack eller distribuerar på ett integrerat system, måste du redigera värdena i det här fönstret för att avspegla. Till exempel om du använder domänen suffix mycloud.com, måste din Azure Stack klient Azure Resource Manager-slutpunkt ändra till hantering. <region>. mycloud.com. När du har bekräftat din information, klickar du på **nästa**.

    ![App Service-installationsprogrammet][3]

7. På nästa sida:
    1. Klicka på den **Connect** knappen bredvid den **Azure Stack-prenumerationer** box.
        - Ange ditt administratörskonto. Till exempel cloudadmin@azurestack.local. Ange ditt lösenord och klicka på **logga In**.
    2. I den **Azure Stack-prenumerationer** väljer den **standard Providerprenumeration**.
    
    > [!NOTE]
    > App Service kan bara distribueras till den **standard Providerprenumeration**.
    >
    
    3. I den **platser för Azure Stack** väljer du den plats som motsvarar den region som du distribuerar till. Välj exempelvis **lokala** om din distribution till Azure Stack Development Kit.
    4. Klicka på **Nästa**.

    ![App Service-installationsprogrammet][4]

8. Nu har du möjlighet att distribuera till ett befintligt virtuellt nätverk som konfigurerats genom stegen [här](azure-stack-app-service-before-you-get-started.md#virtual-network), eller låta App Service-installationsprogrammet för att skapa ett virtuellt nätverk och associerade undernät.
    1. Välj **skapa virtuellt nätverk med standardinställningar**, acceptera standardinställningarna och klicka sedan på **nästa**, eller;
    2. Välj **Använd befintligt virtuellt nätverk och undernät**.
        1. Välj den **resursgrupp** som innehåller det virtuella nätverket;
        2. Välj rätt **virtuellt nätverk** namn som du vill distribuera till;
        3. Välj rätt **undernät** värden för var och en av de nödvändiga rolltjänster undernäten;
        4. Klicka på **Nästa**

    ![App Service-installationsprogrammet][5]

9. Ange information för filresursen och klickar sedan på **nästa**. Adressen till filresursen måste använda fullständigt kvalificerat domännamn eller IP-adressen för din filserver. Till exempel \\\appservicefileserver.local.cloudapp.azurestack.external\websites, eller \\\10.0.0.1\websites

    > [!NOTE]
    > Installationsprogrammet försöker att testa anslutningen till filresursen innan du fortsätter.  Om du väljer att distribuera i ett befintligt virtuellt nätverk, kan installationsprogrammet kanske inte kan ansluta till filresursen och visas en varning som frågar om du vill fortsätta  Kontrollera informationen om filresursen och fortsätta om de är korrekta.
    >
    >

   ![App Service-installationsprogrammet][8]

10. På nästa sida:
    1. I den **Identitetsprogram-ID** Anger GUID för programmet som du använder för identiteten (från Azure AD).
    2. I den **certifikatfil för Identitetsprogram** anger du (eller bläddra till) platsen för certifikatfilen.
    3. I den **identitet lösenord för identitetsprogramcertifikat** anger lösenordet för certifikatet. Det här lösenordet är det som du antecknade när du använder skriptet för att skapa certifikat.
    4. I den **Azure Resource Manager-filen med rotcertifikatet** anger du (eller bläddra till) platsen för certifikatfilen.
    5. Klicka på **Nästa**.

    ![App Service-installationsprogrammet][10]

11. För var och en av de tre filen rutorna och klicka på **Bläddra** och gå sedan till certifikatfil. Du måste ange lösenordet för varje certifikat. Dessa certifikat är de som du skapade i den [skapa nödvändiga certifikat steg](azure-stack-app-service-before-you-get-started.md#get-certificates). Klicka på **nästa** när du har angett all information.

    | Box | Exempel på certifikatet namn |
    | --- | --- |
    | **App Service standard SSL-certifikatfil** | \_.appservice.local.AzureStack.external.pfx |
    | **App Service API SSL-certifikatfil** | api.appservice.local.AzureStack.external.pfx |
    | **App Service Publisher SSL-certifikatfil** | ftp.appservice.local.AzureStack.external.pfx |

    Om du använde ett annat domänsuffix när du skapade certifikaten kan dina certifikat inte används för filnamn *lokala. AzureStack.external*. I stället använda ditt anpassade domäner.

    ![App Service-installationsprogrammet][11]

12. Ange SQL Server-information för server-instans som används för att vara värd för App Service resource provider-databaser och klicka sedan på **nästa**. Installationsprogrammet verifierar egenskaper för SQL-anslutning. Du **måste** Ange intern IP-adress eller fullständigt domännamn för SQL Server-namn.

    > [!NOTE]
    > Installationsprogrammet försöker att testa anslutningen till SQl Server innan du fortsätter.  Om du väljer att distribuera i ett befintligt virtuellt nätverk, kan installationsprogrammet kanske inte kan ansluta till SQL Server och visar en varning som frågar om du vill fortsätta  Kontrollera SQL Server-information och fortsätta om de är korrekta.
    >
    > Installationsprogrammet kommer från Azure App Service i Azure Stack-1.3 och senare kontrollera att SQL Server har databas inneslutning aktiverad på SQL Server-nivå.  Om det inte är det, uppmanas du att följande undantag:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;  
    >    GO  
    >    RECONFIGURE;  
    >    GO
    >    ***********************************************************
    > ```
    > Referera till den [viktig information för Azure App Service i Azure Stack 1.3](azure-stack-app-service-release-notes-update-three.md) för mer information.
   
   ![App Service-installationsprogrammet][12]

13. Granska rollinstansen och SKU-alternativen. Standardvärdena fylls i med det minsta antalet förekomster och minsta SKU: N för varje roll i en ASDK-distribution. En sammanfattning av vCPU och minneskraven tillhandahålls för att planera distributionen. När du har gjort dina val klickar du på **nästa**.

     > [!NOTE]
     > Vid Produktionsdistribution måste följa riktlinjerna i [kapacitetsplanering för Azure App Service-serverroller i Azure Stack](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Roll | Minsta instanser | Minsta SKU | Anteckningar |
    | --- | --- | --- | --- |
    | Kontrollenhet | 1 | Standard_A2 - (2 virtuella processorer, 3584 MB) | Hanterar och underhåller hälsotillståndet för App Service-molnet. |
    | Hantering | 1 | Standard_A2 - (2 virtuella processorer, 3584 MB) | Hanterar App Service Azure Resource Manager och API-slutpunkter, portal tillägg (admin, klient, Functions-portalen) och data service. Stöd för växling vid fel, ökat rekommenderade instanserna till 2. |
    | Utgivare | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Publicerar innehåll via FTP och webbtjänster distribution. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Dirigerar begäranden till App Service-program. |
    | Delade Arbetarservrar | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Webb-värdar eller API-program och Azure Functions-appar. Du kanske vill lägga till fler instanser. Som operatör kan du definiera ditt erbjudande och välja valfri SKU-nivå. Nivåerna måste ha minst en virtuell processor. |

    ![App Service-installationsprogrammet][14]

    > [!NOTE]
    > **Windows Server 2016 Core är inte en plattformsavbildning som stöds för användning med Azure App Service i Azure Stack.  Använd inte utvärdering avbildningar för Produktionsdistribution.  Azure App Service i Azure Stack kräver att Microsoft.Net 3.5.1 SP1 har aktiverats på den avbildning som används för distribution.   Marketplace syndikeras Windows Server 2016 avbildningar inte har den här funktionen är aktiverad, du måste därför skapa och använda en avbildning av Windows Server 2016 med redan aktiverat.**

14. I den **Välj Plattformsavbildning** väljer du din avbildning av virtuell dator för distribution av Windows Server 2016 från de som finns i compute-resursprovidern för App Service-molnet. Klicka på **Nästa**.

15. På nästa sida:
     1. Ange Arbetsroll VM administratörens användarnamn och lösenord.
     2. Ange andra roller VM administratörens användarnamn och lösenord.
     3. Klicka på **Nästa**.

    ![App Service-installationsprogrammet][16]

16. På sidan Sammanfattning:
    1. Kontrollera de val du gjort. Använd för att göra ändringar i **föregående** knappar för att gå till föregående sidor.
    2. Markera kryssrutan om konfigurationerna är korrekta.
    3. Starta distributionen, klicka på **nästa**.

    ![App Service-installationsprogrammet][17]

17. På nästa sida:
    1. Spåra under installationen. App Service i Azure Stack tar cirka 60 minuter för att distribuera baserat på standardinställningarna.
    2. När installationsprogrammet har slutförts korrekt klickar du på **avsluta**.

    ![App Service-installationsprogrammet][18]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validera App Service på Azure Stack-installation

1. I Azure Stack-administratörsportalen, går du till **Administration - App Service**.

2. I översikten under status kontrollerar du att den **Status** visar **alla roller är redo**.

    ![App Service-hantering](media/azure-stack-app-service-deploy/image12.png)

> [!NOTE]
> Om du väljer att distribuera till ett befintligt virtuellt nätverk och en intern IP-adress för att ansluta till din filserver, du måste lägga till en utgående säkerhetsregel att aktivera SMB-trafik mellan worker-undernät och filservern.  Gör detta genom att gå till WorkersNsg i Admin Portal och Lägg till en utgående säkerhetsregel med följande egenskaper:
> * Källa: Alla
> * Käll-portintervall: *
> * Mål: IP-adresser
> * Mål-IP-adressintervall: Intervall av IP-adresser för din filserver
> * Målportintervall: 445
> * Protokoll: TCP
> * Åtgärd: Tillåt
> * Prioritet: 700
> * Namn: Outbound_Allow_SMB445
>

## <a name="test-drive-app-service-on-azure-stack"></a>Testköra App Service på Azure Stack

När du distribuerar och registrera resursprovidern App Service kan du testa den och se till att användare kan distribuera webb- och API-appar.

> [!NOTE]
> Du måste skapa ett erbjudande som har Microsoft.Web namnrymd i planen. Måste ha en klientprenumeration som prenumererar på erbjudandet. Mer information finns i [skapa erbjudandet](azure-stack-create-offer.md) och [Skapa plan](azure-stack-create-plan.md).
>
> Du *måste* har ett klientprenumeration för att skapa program som använder App Service på Azure Stack. Endast funktioner som en tjänstadministratör kan utföra i administrationsportalen är relaterade till resource provider administrationen av App Service. Funktionerna omfattar att lägga till kapacitet, konfigurera distributionskällor och lägger till arbetarnivåer och SKU: er.
>
> Från och med den tredje tekniska förhandsversionen för att skapa webb-, API- och Azure Functions-appar, måste du använda klientportalen och har en klientprenumeration.

1. I Azure Stack-klient-portalen klickar du på **+ skapa en resurs** > **webb + mobilt** > **Webbapp**.

2. På den **Webbapp** bladet anger du ett namn i den **webbapp** box.

3. Under **resursgrupp**, klickar du på **New**. Ange ett namn i den **resursgrupp** box.

4. Klicka på **App Service plan/plats** > **Skapa ny**.

5. På den **apptjänstplan** bladet anger du ett namn i den **App Service-plan** box.

6. Klicka på **prisnivå** > **kostnadsfri delad** eller **delad delad** > **Välj**  >   **OK** > **skapa**.

7. I mindre än en minut visas en ikon för den nya webbappen på instrumentpanelen. Klicka på panelen.

8. På den **Webbapp** bladet klickar du på **Bläddra** att visa standardwebbplatsen för den här appen.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Distribuera en WordPress, DNN eller Django-webbplats (valfritt)

1. I Azure Stack-klient-portalen klickar du på **+**, gå till Azure Marketplace, distribuera en Django-webbplats och vänta på slutförande. Django web-plattformen använder en fil system-baserad databas. Det kräver inte några ytterligare resource-leverantörer, till exempel SQL eller MySQL.

2. Om du har distribuerat en MySQL-resursprovider också kan du distribuera en WordPress-webbplats från Marketplace. När du uppmanas att ange Databasparametrar anger du användarnamnet som *User1@Server1*med användarnamn och servernamnet på ditt val.

3. Om du har distribuerat en SQL Server-resursleverantör också kan du distribuera en DNN-webbplats från Marketplace. Välj en databas i den dator som kör SQL Server som är ansluten till din resursprovider när du uppmanas att ange Databasparametrar.

## <a name="next-steps"></a>Nästa steg

Du kan också prova att använda andra [plattform som en tjänst (PaaS) tjänster](azure-stack-tools-paas-services.md).

- [SQL Server-resursleverantör](azure-stack-sql-resource-provider-deploy.md)
- [MySQL-resursprovider](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: https://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: https://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: https://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
