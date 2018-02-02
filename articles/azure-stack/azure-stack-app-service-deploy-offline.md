---
title: "Distribuera App Service i en frånkopplad miljö: Azure Stack | Microsoft Docs"
description: "Detaljerad information om hur du distribuerar Apptjänst i en frånkopplad miljö för Azure-stacken skyddas av AD FS."
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: anwestg
ms.openlocfilehash: 2e527620825a3b419c0191244ba0baff4b74f0fa
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Lägg till en Apptjänst-resursprovidern i en frånkopplad miljö för Azure-stacken skyddas av AD FS
*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Genom att följa anvisningarna i den här artikeln kan du installera den [Apptjänst resursprovidern](azure-stack-app-service-overview.md) till en Azure-Stack-miljö som är:
- inte ansluten till internet
- skyddas av Active Directory Federation Services (AD FS).

Lägg till App-tjänstresursprovider i distributionen Azure-stacken, måste du utföra aktiviteterna på den högsta nivån:

1. Slutför den [förkraven](azure-stack-app-service-before-you-get-started.md) (som att köpa certifikat, vilket kan ta ett par dagar att ta emot).
2. [Ladda ned och extrahera filer för installation och helper](azure-stack-app-service-before-you-get-started.md) till en dator som är ansluten till internet.
3. Skapa ett offline installationspaket.
4. Kör installationsfilen appservice.exe.

## <a name="create-an-offline-installation-package"></a>Skapa ett installationspaket för offline

Om du vill distribuera Apptjänst i en frånkopplad miljö måste du först skapa ett installationspaket för offline på en dator som är ansluten till Internet.

1. Kör installationsprogrammet för AppService.exe på en dator som är ansluten till Internet.

2. Klicka på **Avancerat** > **Skapa offline installationspaketet**.

    ![App Service Installer](media/azure-stack-app-service-deploy-offline/image01.png)   

3. Installationsprogrammet för App Service skapar ett installationspaket för offline och visar sökvägen till den. Du kan klicka på **öppna mappen** att öppna mappen i din Utforskaren.

    ![App Service Installer](media/azure-stack-app-service-deploy-offline/image02.png)   

4. Kopiera installationsprogrammet (AppService.exe) och offline installationspaketet till din Azure Stack-värddatorn.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Installationen av App Service på Azure-stacken offline

1. Kör appservice.exe som azurestack\clouadmin på Azure-stacken frånkopplade värddatorn.

2. Klicka på **Avancerat** > **offline installationen**.

    ![App Service Installer](media/azure-stack-app-service-deploy-offline/image03.png)   

3. Bläddra till platsen för det offline installationspaket som du skapade tidigare och klicka sedan på **nästa**.

    ![App Service Installer](media/azure-stack-app-service-deploy-offline/image04.png)   

4. Granska och acceptera licensvillkoren för programvara från Microsoft och klicka sedan på **nästa**.

5. Granska och acceptera licensvillkoren för tredjeparts- och klicka sedan på **nästa**.

6. Kontrollera att konfigurationsinformationen Apptjänst molnet är korrekt. Du kan acceptera standardvärdena här om du använder standardinställningarna under distributionen av Azure-stacken Development Kit. Om du har anpassat alternativen när du har distribuerat Azure-stacken, måste du redigera värdena i det här fönstret för att återspegla som. Om du använder domänen suffix mycloud.com måste slutpunkten ändra exempelvis till management.mycloud.com. När du bekräftar din information klickar du på **nästa**.

    ![App Service Installer](media/azure-stack-app-service-deploy/image02.png)

7. På nästa sida:
    1. Klicka på den **Anslut** knappen bredvid den **Azure Stack-prenumerationer** rutan.
        - Om du använder Azure Active Directory (Azure AD), ange din Azure AD-administratörskonto och lösenord som du angav när du har distribuerat Azure stacken. Klicka på **logga In**.
        - Om du använder Active Directory Federation Services (AD FS), ange ditt administratörskonto. Till exempel cloudadmin@azurestack.local. Ange ditt lösenord och klicka på **logga In**.
    2. I den **Azure Stack-prenumerationer** väljer din prenumeration.
    3. I den **Azure Stack platser** väljer du den plats som motsvarar den region som du distribuerar till. Välj exempelvis **lokala** om din distribution till Azure-stacken Development Kit.
    4. Ange en **resursgruppens namn** för Apptjänst-distributionen. Som standard anges till **APPTJÄNST-lokala**.
    5. Ange den **Lagringskontonamnet** som du vill att Apptjänst ska skapa som en del av installationen. Som standard anges till **appsvclocalstor**.
    6. Klicka på **Nästa**.

    ![App Service Installer](media/azure-stack-app-service-deploy/image03.png)

8. Ange information för filresursen och klicka sedan på **nästa**. Adressen till filresursen måste använda fullständigt kvalificerade domännamn på filservern, till exempel \\\appservicefileserver.local.cloudapp.azurestack.external\websites eller IP-adress, till exempel \\\10.0.0.1\websites.

    ![App Service Installer](media/azure-stack-app-service-deploy/image04.png)

9. På nästa sida:
    1. I den **identitet program-ID** Ange GUID för programmet som du använder för identiteten.
    2. I den **identitet programmet certifikatfilen** rutan, ange (eller bläddra till) platsen för certifikatfilen.
    3. I den **identitet programmet certifikatlösenord** ange lösenordet för certifikatet. Lösenordet är det som du antecknade när du använde skriptet för att skapa certifikat.
    4. I den **Azure Resource Manager rotcertifikatfilen** rutan, ange (eller bläddra till) platsen för certifikatfilen.
    5. Klicka på **Nästa**.

    ![App Service Installer](media/azure-stack-app-service-deploy/image05.png)

10. För var och en av de tre filen rutor och klicka på **Bläddra** och navigera till certifikatfil och ange ett lösenord. Dessa certifikat är de som du skapade i den [skapa nödvändiga certifikat steg](azure-stack-app-service-deploy.md). Klicka på **nästa** när du har angett all information.

    | Box | Exempel på certifikatet namn |
    | --- | --- |
    | **Apptjänst standardfil SSL-certifikat** | \_.appservice.local.AzureStack.external.pfx |
    | **Filen för Apptjänst API SSL-certifikat** | api.appservice.local.AzureStack.external.pfx |
    | **App Service Publisher SSL certifikatfilen** | ftp.appservice.local.AzureStack.external.pfx |

    Om du har använt ett annat domänsuffix när du skapade certifikaten filnamn certifikatet inte använder *lokala. AzureStack.external*. I stället använda ditt anpassade domäner.

    ![App Service Installer](media/azure-stack-app-service-deploy/image06.png)    

11. Ange SQL Server-information för server-instansen som används för att Apptjänst resource provider-databaser och klicka sedan på **nästa**. Installationsprogrammet verifierar egenskaper för SQL-anslutning.

    ![App Service Installer](media/azure-stack-app-service-deploy/image07.png)    

12. Granska rollinstansen och SKU-alternativ. Standardvärdena är fyllda med det minsta antalet instansen och minsta SKU: N för varje roll i en ASDK-distribution. En sammanfattning av vCPU och minneskrav tillhandahålls för att planera distributionen. När du har gjort dina val klickar du på **nästa**.

     > [!NOTE]
     > För Produktionsdistribution följa riktlinjerna i [kapacitetsplanering för Azure App Service-serverroller i Azure-stacken](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Roll | Minsta instanser | Minsta SKU | Anteckningar |
    | --- | --- | --- | --- |
    | Kontrollenhet | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Hanterar och underhåller hälsotillståndet för App Service-molnet. |
    | Hantering | 1 | Standard_A2 - (2 vCPUs, 3584 MB) | Hanterar App Service Azure Resource Manager och API-slutpunkter, portal tillägg (admin, klient, Functions-portalen) och datatjänsten. Som stöd för växling vid fel, ökade rekommenderade instanser till 2. |
    | Utgivare | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Publicerar innehåll via FTP- och webb-distribution. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Skickar begäranden till App-tjänstprogram. |
    | Delade Worker | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Värdar web eller API-program och Azure Functions-appar. Du kanske vill lägga till flera instanser. Som operatör kan du definiera dina erbjudanden och väljer en SKU-nivå. Nivåerna måste ha minst en vCPU. |

    ![App Service Installer](media/azure-stack-app-service-deploy/image08.png)    

    > [!NOTE]
    > **Windows Server 2016 Core är inte en bild för plattform som stöds för användning med Azure App Service på Azure-stacken**.

13. I den **Välj Plattformsavbildning** väljer din avbildning av virtuell dator för distribution av Windows Server 2016 från de som finns i compute-resursprovidern för Apptjänst-molnet. Klicka på **Nästa**.

14. På nästa sida:
     1. Ange användarnamn för Worker-rollen virtuell dator administratör och lösenord.
     2. Ange andra roller virtuella administratörsanvändarnamn och lösenord.
     3. Klicka på **Nästa**.

    ![App Service Installer](media/azure-stack-app-service-deploy/image09.png)    

15. På sidan Sammanfattning:
    1. Kontrollera de val du gjort. Använd för att göra ändringar i **föregående** knappar för att besöka föregående sidor.
    2. Markera kryssrutan om konfigurationerna är korrekta.
    3. Starta distributionen, klicka på **nästa**.

    ![App Service Installer](media/azure-stack-app-service-deploy/image10.png)    

16. På nästa sida:
    1. Spåra installationsförloppet. Apptjänst Azure stacken tar ungefär 60 minuter för att distribuera baserat på standardvalen.
    2. När installationsprogrammet slutförs, klickar du på **avsluta**.

    ![App Service Installer](media/azure-stack-app-service-deploy/image11.png)    


## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validera App Service på Azure Stack-installation

1. Gå till i administrationsportalen för Azure-stacken **Administration - Apptjänst**.

2. I översikten under status, kontrollerar du att den **Status** visar **alla roller som är klara**.

    ![App Service-hantering](media/azure-stack-app-service-deploy/image12.png)    


## <a name="test-drive-app-service-on-azure-stack"></a>Test Apptjänst Azure-stacken

När du distribuerar och registerresursleverantören Apptjänst, testa och se till att användarna kan distribuera webb- och API apps.

> [!NOTE]
> Du måste skapa ett erbjudande som har Microsoft.Web namnområdet i planen. Du måste ha en prenumeration för innehavaren som prenumererar på det här erbjudandet. Mer information finns i [skapa erbjudande](azure-stack-create-offer.md) och [Skapa plan](azure-stack-create-plan.md).
>
Du *måste* har en klientprenumeration att skapa program som använder App Service på Azure-stacken. Endast funktioner som en tjänstadministratör kan fylla i administrationsportal är relaterade till resource provider administration av App Service. Dessa funktioner omfattar att lägga till kapacitet, konfigurerar distributionen källor och lägger till Worker nivåer och SKU: er.
>
Från och med technical preview tredje funktioner appar för att skapa webb-, API- och Azure, du måste använda klientportalen och har en klientprenumeration.

1. Klicka i klientportalen Azure Stack **ny** > **webb + mobilt** > **Web App**.

2. På den **Web App** bladet, ange ett namn i den **webbprogrammet** rutan.

3. Under **resursgruppen**, klickar du på **ny**. Ange ett namn i den **resursgruppen** rutan.

4. Klicka på **App Service plan/plats** > **Skapa ny**.

5. På den **programtjänstplanen** bladet, ange ett namn i den **programtjänstplanen** rutan.

6. Klicka på **prisnivå** > **lediga delade** eller **delade delade** > **Välj**  >   **OK** > **skapa**.

7. Under en minut visas en panel för den nya webbappen på instrumentpanelen. Klicka på panelen.

8. På den **Webbapp** bladet klickar du på **Bläddra** att visa standardwebbplatsen för den här appen.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Distribuera en WordPress, DNN eller Django-webbplats (valfritt)

1. Klicka i klientportalen Azure Stack  **+** , gå till Azure Marketplace, distribuera en Django-webbplats och vänta tills åtgärden lyckades. Django webbplatform använder en databas med fil-system. Det kräver inte några ytterligare resurs-leverantörer, till exempel SQL eller MySQL.

2. Om du har distribuerat en MySQL-resursprovidern också kan du distribuera en WordPress-webbplats från Marketplace. När du uppmanas att ange parametrar anger du användarnamnet som  *User1@Server1* , med användarnamn och servernamnet du väljer.

3. Om du också distribueras till en provider för SQL Server-resurs, kan du distribuera en DNN webbplats från Marketplace. När du uppmanas att ange parametrar kan du välja en databas i den dator som kör SQL Server som är ansluten till din resursprovidern.

## <a name="next-steps"></a>Nästa steg

Du kan också prova att använda andra [plattform som en tjänst (PaaS) services](azure-stack-tools-paas-services.md).

- [SQL Server-resursprovidern](azure-stack-sql-resource-provider-deploy.md)
- [MySQL-resursprovidern](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
