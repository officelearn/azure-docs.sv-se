---
title: 'Distribuera App Services: Azure Stack | Microsoft Docs'
description: Detaljerad vägledning för distribution av App Service i Azure Stack
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
ms.date: 11/13/2018
ms.author: anwestg
ms.openlocfilehash: aa745d827db7633dc9f8601f65fa31dfadbb4076
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614063"
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Lägga till en App Service-resursprovider i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Använd informationen i den här artikeln för att distribuera App Service i Azure Stack.

> [!IMPORTANT]  
> Uppdateringen är 1809 integrerade Azure Stack-system eller distribuera den senaste Azure Stack Development Kit (ASDK) innan du distribuerar Azure App Service 1.4.

Du kan ge användarna möjlighet att skapa webbprogram och API: et. Om du vill låta användarna skapa dessa program, måste du:

 - Lägg till den [App Service-resursprovider](azure-stack-app-service-overview.md) för din Azure Stack-distribution med hjälp av anvisningarna i den här artikeln.
 - Efter installation av App Service-resursprovider kan inkludera du den i dina erbjudanden och planer. Användare kan prenumerera sedan för att hämta tjänsten och börja skapa program.

> [!IMPORTANT]  
> Innan du kör installationsprogrammet för providern resurs, se till att du har följt riktlinjerna i [innan du sätter igång](azure-stack-app-service-before-you-get-started.md).

## <a name="run-the-app-service-resource-provider-installer"></a>Kör installationsprogrammet för providern App Service-resurs

Installera App Service-resursprovider tar minst en timme. Hur lång tid som behövs beror på hur många rollen instanser du distribuerar. Under distributionen så, körs installationsprogrammet följande uppgifter:

 - Skapa en blobbehållare i det angivna Azure Stack-lagringskontot.
 - Skapa en DNS-zon och DNS-poster för App Service.
 - Registrera resursprovidern för App Service.
 - Registrera galleriobjekt App Service.

Distribuera App Service-resursprovider genom att följa dessa steg:

1. Kör appservice.exe som administratör från en dator som har åtkomst till Azure Stack Admin Azure Resource Management-slutpunkten.

2. Välj **distribuera App Service eller uppgradera till den senaste versionen**.

    ![App Service-installationsprogrammet][1]

3. Granska och acceptera licensvillkoren för programvara från Microsoft och välj sedan **nästa**.

4. Granska och acceptera licensvillkoren från tredje part och välj sedan **nästa**.

5. Kontrollera att konfigurationsinformationen för App Service-molnet är korrekt. Om du använde standardinställningarna under distributionen av Azure Stack Development Kit (ASDK) accepterar du standardvärdena. Men om du har anpassat alternativ när du har distribuerat ASDK eller distribuerar på ett integrerat Azure Stack-system, måste du redigera värdena i det här fönstret för att återspegla skillnaderna.

   Till exempel om du använder domänen suffix mycloud.com, måste din Azure Stack klient Azure Resource Manager-slutpunkt ändra till hantering. &lt;region&gt;. mycloud.com. Granska inställningarna och välj sedan **nästa** att spara inställningarna.

   ![App Service-installationsprogrammet][2]

6. Gör följande på sidan nästa Appinstallationsprogram:

    a. Välj **Connect** bredvid den **Azure Stack-prenumerationer**.

     - Om du använder Azure Active Directory (AD Azure), anger du det Azure AD-administratörskonto och lösenord som du angav när du har distribuerat Azure Stack. Välj **logga In**.
     - Om du använder Active Directory Federation Services (AD FS), ange ditt administratörskonto. Till exempel cloudadmin@azurestack.local. Ange ditt lösenord och välj sedan **logga In**.

   b. I **Azure Stack-prenumerationer**väljer den **standard Providerprenumeration**.

     > [!IMPORTANT]  
     > App Service **måste** distribueras till den **standard Providerprenumeration**.

   c. I den **platser för Azure Stack**, Välj den plats som motsvarar den region som du distribuerar till. Välj exempelvis **lokala** om din distribution till Azure Stack Development Kit.

    ![App Service-installationsprogrammet][3]

7. Nu du kan distribuera i ett befintligt virtuellt nätverk som du har konfigurerat [med de här stegen](azure-stack-app-service-before-you-get-started.md#virtual-network), eller låta App Service-installationsprogrammet skapar ett nytt virtuellt nätverk och undernät. Följ dessa steg om du vill skapa ett virtuellt nätverk:

   a. Välj **skapa virtuellt nätverk med standardinställningar**, acceptera standardinställningarna och välj sedan **nästa**.

   b. Du kan också välja **Använd befintligt virtuellt nätverk och undernät**. Utför följande åtgärder:

     - Välj den **resursgrupp** som innehåller det virtuella nätverket.
     - Välj den **virtuellt nätverk** namn som du vill distribuera till.
     - Välj rätt **undernät** värden för var och en av de nödvändiga rolltjänster undernät.
     - Välj **Nästa**.

   ![App Service-installationsprogrammet][4]

8. Ange information för filresursen och välj sedan **nästa**. Adressen till filresursen måste använda det fullständigt kvalificerade domännamn (FQDN) eller IP-adressen för din filserver. Till exempel \\\appservicefileserver.local.cloudapp.azurestack.external\websites, eller \\\10.0.0.1\websites.

   >[!NOTE]
   >Ett försök görs att testa anslutningen till filresursen innan du fortsätter. Men om du distribuerar till ett befintligt virtuellt nätverk, det här Anslutningstestet misslyckas. Du får en varning och en fråga för att fortsätta. Om informationen om filresurs är korrekt kan du fortsätta med distributionen.

   ![App Service-installationsprogrammet][7]

9. Gör följande på sidan nästa Appinstallationsprogram:

   a. I den **Identitetsprogram-ID** Anger GUID för programmet som du använder för identiteten (från Azure AD).

   b. I den **certifikatfil för Identitetsprogram** anger du (eller bläddra till) platsen för certifikatfilen.

   c. I den **identitet lösenord för identitetsprogramcertifikat** anger lösenordet för certifikatet. Det här lösenordet är det som du antecknade när du använder skriptet för att skapa certifikat.

   d. I den **Azure Resource Manager-filen med rotcertifikatet** anger du (eller bläddra till) platsen för certifikatfilen.

   e. Välj **Nästa**.

   ![App Service-installationsprogrammet][9]

10. För var och en av rutorna tre väljer **Bläddra** och navigera till certifikatfil. Du måste ange lösenordet för varje certifikat. Dessa certifikat är de som du skapade i den [skapa nödvändiga certifikat steg](azure-stack-app-service-before-you-get-started.md#get-certificates). Välj **nästa** när du har angett all information.

    | Box | Exempel på certifikatet namn |
    | --- | --- |
    | **App Service standard SSL-certifikatfil** | \_.appservice.local.AzureStack.external.pfx |
    | **App Service API SSL-certifikatfil** | api.appservice.local.AzureStack.external.pfx |
    | **App Service Publisher SSL-certifikatfil** | ftp.appservice.local.AzureStack.external.pfx |

    Om du använde ett annat domänsuffix när du skapade certifikaten kan dina certifikat inte används för filnamn *lokala. AzureStack.external*. I stället använda ditt anpassade domäner.

    ![App Service-installationsprogrammet][10]

11. Ange SQL Server-information för server-instans som används för att vara värd för App Service resource provider-databaser och välj sedan **nästa**. Installationsprogrammet verifierar egenskaper för SQL-anslutning.

    > [!NOTE]
    > Ett försök görs att testa anslutningen till SQL Server innan du fortsätter. Men om du distribuerar till ett befintligt virtuellt nätverk, det här Anslutningstestet misslyckas. Du får en varning och en fråga för att fortsätta. Om SQL Server-information är korrekt kan du fortsätta med distributionen.
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

    ![App Service-installationsprogrammet][11]

12. Granska rollinstansen och SKU-alternativen. Standardvärdena fyller med det minsta antalet förekomster och minsta SKU: N för varje roll i en ASDK-distribution. En sammanfattning av vCPU och minneskraven tillhandahålls för att planera distributionen. När du har gjort ditt val väljer **nästa**.

    >[!NOTE]
    >Vid Produktionsdistribution måste följa riktlinjerna i [kapacitetsplanering för Azure App Service-serverroller i Azure Stack](azure-stack-app-service-capacity-planning.md).

    | Roll | Minsta instanser | Minsta SKU | Anteckningar |
    | --- | --- | --- | --- |
    | Kontrollenhet | 1 | Standard_A2 - (2 virtuella processorer, 3584 MB) | Hanterar och underhåller hälsotillståndet för App Service-molnet. |
    | Hantering | 1 | Standard_A2 - (2 virtuella processorer, 3584 MB) | Hanterar App Service Azure Resource Manager och API-slutpunkter, portal tillägg (admin, klient, Functions-portalen) och data service. Stöd för växling vid fel, ökat rekommenderade instanserna till 2. |
    | Utgivare | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Publicerar innehåll via FTP och webbtjänster distribution. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Dirigerar begäranden till App Service-program. |
    | Delade Arbetarservrar | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Webb-värdar eller API-program och Azure Functions-appar. Du kanske vill lägga till fler instanser. Som operatör kan du definiera ditt erbjudande och välja valfri SKU-nivå. Nivåerna måste ha minst en virtuell processor. |

    ![App Service-installationsprogrammet][13]

    >[!NOTE]
    >**Windows Server 2016 Core är inte en plattformsavbildning som stöds för användning med Azure App Service i Azure Stack.  Använd inte utvärdering avbildningar för Produktionsdistribution.**

13. I den **Välj Plattformsavbildning** väljer du din avbildning av virtuell dator för distribution av Windows Server 2016 från avbildningar i compute-resursprovidern för App Service-molnet. Välj **Nästa**.

14. Gör följande på sidan nästa Appinstallationsprogram:

     a. Ange Arbetsroll VM administratörens användarnamn och lösenord.

     b. Ange andra roller VM administratörens användarnamn och lösenord.

     c. Välj **Nästa**.

    ![App Service-installationsprogrammet][15]

15. Gör följande på sidan Appinstallationsprogram Sammanfattning:

    a. Kontrollera de val du gjort. Använd för att göra ändringar i **föregående** knappar för att gå till föregående sidor.

    b. Markera kryssrutan om konfigurationerna är korrekta.

    c. För att starta distributionen markerar **nästa**.

    ![App Service-installationsprogrammet][16]

16. Gör följande på sidan nästa Appinstallationsprogram:

    a. Spåra under installationen. App Service i Azure Stack tar cirka 60 minuter för att distribuera baserat på standardinställningarna.

    b. När installationsprogrammet har slutförts korrekt väljer **avsluta**.

    ![App Service-installationsprogrammet][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validera App Service på Azure Stack-installation

1. I Azure Stack-administratörsportalen, går du till **Administration - App Service**.

2. I översikten under status kontrollerar du att den **Status** visar **alla roller är redo**.

    ![App Service-hantering](media/azure-stack-app-service-deploy/image12.png)

   >[!IMPORTANT]
   >Om du distribuerar till ett befintligt virtuellt nätverk och använda en intern IP-adress för att ansluta till din filserver, måste du lägga till en utgående säkerhetsregel. Regeln gör det möjligt för SMB-trafik mellan worker-undernät och filservern.  Gör detta genom att gå till WorkersNsg i Admin Portal och Lägg till en utgående säkerhetsregel med följande egenskaper:<br>
    >  - Källa: alla
    >  - Käll-portintervall: *
    >  - Mål: IP-adresser
    >  - Mål-IP-adressintervall: intervall av IP-adresser för din filserver
    >  - Målportintervall: 445
    >  - Protokoll: TCP
    >  - Åtgärd: Tillåt
    >  - Prioritet: 700
    >  - Namn: Outbound_Allow_SMB445

## <a name="test-drive-app-service-on-azure-stack"></a>Testköra App Service på Azure Stack

När du distribuerar och registrera resursprovidern App Service kan du testa den och se till att användare kan distribuera webb- och API-appar.

>[!NOTE]
>Du måste skapa ett erbjudande med namnområdet Microsoft.Web i planen. Du behöver också en klientprenumeration som prenumererar på erbjudandet. Mer information finns i [skapa erbjudandet](azure-stack-create-offer.md) och [Skapa plan](azure-stack-create-plan.md).
>
>Du *måste* har ett klientprenumeration för att skapa program som använder App Service på Azure Stack. De enda åtgärder som en tjänstadministratör kan utföra i administrationsportalen är relaterade till resource provider administrationen av App Service. Detta omfattar att lägga till kapacitet, konfigurera distributionskällor och lägger till arbetarnivåer och SKU: er.
>
>Om du vill skapa webb-, API- och Azure Functions-appar, måste du använda klientportalen och har en klientprenumeration.
>

Följ dessa steg om du vill skapa en webbapp för test:

1. Välj i användarportalen för Azure Stack **+ skapa en resurs** > **webb + mobilt** > **Webbapp**.

2. Under **Webbapp**, ange ett namn i **webbapp**.

3. Under **resursgrupp**väljer **New**. Ange ett namn för den **resursgrupp**.

4. Välj **App Service-plan/plats** > **Skapa ny**.

5. Under **apptjänstplan**, ange ett namn för den **App Service-plan**.

6. Välj **prisnivå** > **kostnadsfri delad** eller **delad delad** > **Välj**  >  **OK** > **skapa**.

7. En panel för den nya webbappen visas på instrumentpanelen. Välj panelen.

8. På **Webbapp**väljer **Bläddra** att visa standardwebbplatsen för den här appen.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Distribuera en WordPress, DNN eller Django-webbplats (valfritt)

1. I Azure Stack-klient-portalen väljer **+**, gå till Azure Marketplace, distribuera en Django-webbplats och sedan vänta tills distributionen är klar. Django web-plattformen använder en fil system-baserad databas. Det kräver inte några ytterligare resource-leverantörer, till exempel SQL eller MySQL.

2. Om du har distribuerat en MySQL-resursprovider också kan du distribuera en WordPress-webbplats från Marketplace. När du uppmanas att ange Databasparametrar anger du användarnamnet som *User1@Server1*med användarnamn och servernamnet på ditt val.

3. Om du har distribuerat en SQL Server-resursleverantör också kan du distribuera en DNN-webbplats från Marketplace. Välj en databas i den dator som kör SQL Server som är ansluten till din resursprovider när du uppmanas att ange Databasparametrar.

## <a name="next-steps"></a>Nästa steg

Du kan också prova att använda andra [plattform som en tjänst (PaaS) tjänster](azure-stack-tools-paas-services.md).

 - [SQL Server-resursleverantör](azure-stack-sql-resource-provider-deploy.md)
 - [MySQL-resursprovider](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png
