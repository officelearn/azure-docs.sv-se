---
title: "Distribuera Apptjänster: Azure Stack | Microsoft Docs"
description: "Detaljerad vägledning för distribution av Apptjänst i Azure-stacken"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: d4394463be02d067b8228099acd30a0421ce4be9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Lägga till en Apptjänst-resursprovidern i Azure stapel
*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Som operatör Azure Stack molnet kan ge du användarna möjlighet att skapa webb- och API-program. Om du vill göra detta måste du först lägga till den [Apptjänst resursprovidern](azure-stack-app-service-overview.md) för din Azure Stack-distribution som beskrivs i den här artikeln. När du har installerat resursprovidern Apptjänst kan inkludera du den i dina erbjudanden och planer. Användare kan sedan prenumerera för att få tjänsten och börja skapa program.

> [!IMPORTANT]
> Innan du kör installationsprogrammet måste du kontrollera att du har följt anvisningarna i [innan du börjar](azure-stack-app-service-before-you-get-started.md).
>
>



## <a name="run-the-app-service-resource-provider-installer"></a>Kör installationsprogrammet för App Service resource provider

Installera App-tjänstresursprovider i din Azure Stack-miljö kan ta upp till en timme. Under den här processen kommer installationsprogrammet

* Skapa en blob-behållare i det angivna Azure-stacken storage-kontot.
* Skapa en DNS-zon och poster för Apptjänst.
* Registerresursleverantören Apptjänst.
* Registrera galleriobjekt App Service.

Följ dessa steg om du vill distribuera appen tjänstresursprovider:

1. Kör appservice.exe som administratör (azurestack\CloudAdmin).

2. Klicka på **distribuerar Apptjänst i ditt Azure-stacken moln**.

    ![App Service Installer](media/azure-stack-app-service-deploy/image01.png)

3. Granska och acceptera licensvillkoren för programvara från Microsoft och klicka sedan på **nästa**.

4. Granska och acceptera licensvillkoren för tredjeparts- och klicka sedan på **nästa**.

5. Kontrollera att konfigurationsinformationen Apptjänst molnet är korrekt. Du kan acceptera standardvärdena här om du använder standardinställningarna under distributionen av Azure-stacken Development Kit. Om du har anpassat alternativen när du har distribuerat Azure-stacken, måste du redigera värdena i det här fönstret för att återspegla som. Om du använder domänen suffix mycloud.com måste slutpunkten ändra exempelvis till management.mycloud.com. När du bekräftar din information klickar du på **nästa**.

    ![App Service Installer](media/azure-stack-app-service-deploy/image02.png)

6. På nästa sida:
    1. Klicka på den **Anslut** knappen bredvid den **Azure Stack-prenumerationer** rutan.
        - Om du använder Azure Active Directory (Azure AD), anger du Azure AD-administratörskonto och lösenord som du angav när du har distribuerat Azure Stack. Klicka på **logga In**.
        - Om du använder Active Directory Federation Services (AD FS), ange ditt administratörskonto. Till exempel cloudadmin@azurestack.local. Ange ditt lösenord och klicka på **logga In**.
    2. I den **Azure Stack-prenumerationer** väljer din prenumeration.
    3. I den **Azure Stack platser** väljer du den plats som motsvarar den region som du distribuerar till. Välj exempelvis **lokala** om din distribution till Azure-stacken Development Kit.
    4. Ange en **resursgruppens namn** för Apptjänst-distributionen. Som standard anges till **APPTJÄNST\<REGION\>**.
    5. Ange den **Lagringskontonamnet** som du vill att Apptjänst ska skapa som en del av installationen. Som standard anges till **appsvclocalstor**.
    6. Klicka på **Nästa**.

    ![App Service Installer](media/azure-stack-app-service-deploy/image03.png)

7. Ange information för filresursen och klicka sedan på **nästa**. Adressen till filresursen måste använda fullständigt kvalificerade domännamn på filservern, till exempel \\\appservicefileserver.local.cloudapp.azurestack.external\websites eller IP-adress, till exempel \\\10.0.0.1\websites.

    ![App Service Installer](media/azure-stack-app-service-deploy/image04.png)

8. På nästa sida:
    1. I den **identitet program-ID** Ange GUID för programmet som du använder för identiteten (från Azure AD).
    2. I den **identitet programmet certifikatfilen** rutan, ange (eller bläddra till) platsen för certifikatfilen.
    3. I den **identitet programmet certifikatlösenord** ange lösenordet för certifikatet. Lösenordet är det som du antecknade när du använde skriptet för att skapa certifikat.
    4. I den **Azure Resource Manager rotcertifikatfilen** rutan, ange (eller bläddra till) platsen för certifikatfilen.
    5. Klicka på **Nästa**.

    ![App Service Installer](media/azure-stack-app-service-deploy/image05.png)

9. För var och en av de tre filen rutor och klicka på **Bläddra** och navigera till certifikatfil. Du måste ange lösenordet för varje certifikat. Dessa certifikat är de som du skapade i den [skapa nödvändiga certifikat steg](azure-stack-app-service-deploy.md#create-the-required-certificates). Klicka på **nästa** när du har angett all information.

    | Box | Exempel på certifikatet namn |
    | --- | --- |
    | **Apptjänst standardfil SSL-certifikat** | \_.appservice.local.AzureStack.external.pfx |
    | **Filen för Apptjänst API SSL-certifikat** | api.appservice.local.AzureStack.external.pfx |
    | **App Service Publisher SSL certifikatfilen** | ftp.appservice.local.AzureStack.external.pfx |

    Om du har använt ett annat domänsuffix när du skapade certifikaten filnamn certifikatet inte använder *lokala. AzureStack.external*. I stället använda ditt anpassade domäner.

    ![App Service Installer](media/azure-stack-app-service-deploy/image06.png)    

10. Ange SQL Server-information för server-instansen som används för att Apptjänst resource provider-databaser och klicka sedan på **nästa**. Installationsprogrammet verifierar egenskaper för SQL-anslutning.

    ![App Service Installer](media/azure-stack-app-service-deploy/image07.png)    

11. Granska rollinstansen och SKU-alternativ. Standardvärdena är fyllda med det minsta antalet instansen och minsta SKU: N för varje roll i en ASDK-distribution. En sammanfattning av vCPU och minneskrav tillhandahålls för att planera distributionen. När du har gjort dina val klickar du på **nästa**.

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

12. I den **Välj Plattformsavbildning** väljer din avbildning av virtuell dator för distribution av Windows Server 2016 från de som finns i compute-resursprovidern för Apptjänst-molnet. Klicka på **Nästa**.

13. På nästa sida:
     1. Ange användarnamn för Worker-rollen virtuell dator administratör och lösenord.
     2. Ange andra roller virtuella administratörsanvändarnamn och lösenord.
     3. Klicka på **Nästa**.

    ![App Service Installer](media/azure-stack-app-service-deploy/image09.png)    

14. På sidan Sammanfattning:
    1. Kontrollera de val du gjort. Använd för att göra ändringar i **föregående** knappar för att besöka föregående sidor.
    2. Markera kryssrutan om konfigurationerna är korrekta.
    3. Starta distributionen, klicka på **nästa**.

    ![App Service Installer](media/azure-stack-app-service-deploy/image10.png)    

15. På nästa sida:
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
Om du vill skapa webb-, API- och Azure funktioner appar, du måste använda klientportalen och har en klientprenumeration.

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
