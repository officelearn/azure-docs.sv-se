---
title: 'Distribuera Apptjänster: Azure Stack | Microsoft Docs'
description: Detaljerad vägledning för distribution av Apptjänst i Azure-stacken
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
ms.date: 05/22/2018
ms.author: anwestg
ms.openlocfilehash: f44e6e917058306e37b9eb99819afda76a742389
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604275"
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Lägga till en Apptjänst-resursprovidern i Azure stapel

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

> [!IMPORTANT]
> Uppdateringen är 1804 Azure Stack integrerade systemet eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service 1.2.
>
>

Som operatör Azure Stack molnet kan ge du användarna möjlighet att skapa webb- och API-program. Om du vill göra detta måste du först lägga till den [Apptjänst resursprovidern](azure-stack-app-service-overview.md) för din Azure Stack-distribution som beskrivs i den här artikeln. När du har installerat resursprovidern Apptjänst kan inkludera du den i dina erbjudanden och planer. Användare kan sedan prenumerera för att få tjänsten och börja skapa program.

> [!IMPORTANT]
> Innan du kör installationsprogrammet måste du kontrollera att du har följt anvisningarna i [innan du börjar](azure-stack-app-service-before-you-get-started.md).
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Kör installationsprogrammet för App Service resource provider

Installera App-tjänstresursprovider i din Azure Stack-miljö kan ta minst en timme beroende på hur många rollinstanser som du vill distribuera. Under den här processen kommer installationsprogrammet

* Skapa en blob-behållare i det angivna Azure-stacken storage-kontot.
* Skapa en DNS-zon och poster för Apptjänst.
* Registerresursleverantören Apptjänst.
* Registrera galleriobjekt App Service.

Följ dessa steg om du vill distribuera appen tjänstresursprovider:

1. Kör appservice.exe som administratör från en dator som kan nå Azure Stack Admin Azure Resource Management-slutpunkten.

2. Klicka på **distribuerar Apptjänst eller uppgradera till den senaste versionen**.

    ![Installationsprogrammet för App Service][1]

3. Granska och acceptera licensvillkoren för programvara från Microsoft och klicka sedan på **nästa**.

4. Granska och acceptera licensvillkoren för tredjeparts- och klicka sedan på **nästa**.

5. Kontrollera att konfigurationsinformationen Apptjänst molnet är korrekt. Du kan acceptera standardvärdena här om du använder standardinställningarna under distributionen av Azure-stacken Development Kit. Om du har anpassat alternativen när du har distribuerat Azure Stack eller distribuerar på ett integrerat system, måste du redigera värdena i det här fönstret för att återspegla som. Om du använder domänen suffix mycloud.com måste Azure Stack klient Azure Resource Manager-slutpunkten ändra exempelvis till management. &lt;region&gt;. mycloud.com. När du bekräftar din information klickar du på **nästa**.

    ![Installationsprogrammet för App Service][2]

6. På nästa sida:
    1. Klicka på den **Anslut** knappen bredvid den **Azure Stack-prenumerationer** rutan.
        * Om du använder Azure Active Directory (Azure AD), anger du Azure AD-administratörskonto och lösenord som du angav när du har distribuerat Azure Stack. Klicka på **logga In**.
        * Om du använder Active Directory Federation Services (AD FS), ange ditt administratörskonto. Till exempel cloudadmin@azurestack.local. Ange ditt lösenord och klicka på **logga In**.
    2. I den **Azure Stack-prenumerationer** markerar den **standard prenumeration på leverantör**.
    
    > [!NOTE]
    > Apptjänst kan endast distribueras till den **standard prenumeration på leverantör** just nu.  I en kommande uppdatering Apptjänst ska distribuera till den nya avläsning prenumerationen som introducerades i Azure-stacken 1804 och alla befintliga distributioner kommer att migreras till den här nya prenumerationen också.
    >
    >
    
    3. I den **Azure Stack platser** väljer du den plats som motsvarar den region som du distribuerar till. Välj exempelvis **lokala** om din distribution till Azure-stacken Development Kit.

    ![Installationsprogrammet för App Service][3]

7. Nu har du möjlighet att distribuera till ett befintligt virtuellt nätverk som konfigurerats genom stegen [här](azure-stack-app-service-before-you-get-started.md#virtual-network), eller tillåta App Service-installationsprogrammet att skapa ett virtuellt nätverk och associerade undernät.
    1. Välj **skapa VNet med standardinställningar**, acceptera standardinställningarna och klickar på **nästa**, eller;
    2. Välj **använda befintliga VNet och undernät**.
        1. Välj den **resursgruppen** som innehåller det virtuella nätverket;
        2. Välj rätt **virtuellt nätverk** namn som du vill distribuera till;
        3. Välj rätt **undernät** värden för var och en av de nödvändiga rolltjänster undernät;
        4. Klicka på **Nästa**

    ![Installationsprogrammet för App Service][4]

8. Ange information för filresursen och klicka sedan på **nästa**. Adressen till filresursen måste använda fullständigt domännamn eller IP-adressen för din filserver. Till exempel \\\appservicefileserver.local.cloudapp.azurestack.external\websites, eller \\\10.0.0.1\websites.

   > [!NOTE]
   > Installationsprogrammet försöker att testa anslutningen till filresursen innan du fortsätter.  Men om du har valt att distribuera i ett befintligt virtuellt nätverk kan installationsprogrammet kanske inte kan ansluta till filresursen och en varning visas frågar om du vill fortsätta.  Kontrollera informationen om filresursen och fortsätta om de är korrekta.
   >
   >

   ![Installationsprogrammet för App Service][7]

9. På nästa sida:
    1. I den **identitet program-ID** Ange GUID för programmet som du använder för identiteten (från Azure AD).
    2. I den **identitet programmet certifikatfilen** rutan, ange (eller bläddra till) platsen för certifikatfilen.
    3. I den **identitet programmet certifikatlösenord** ange lösenordet för certifikatet. Lösenordet är det som du antecknade när du använde skriptet för att skapa certifikat.
    4. I den **Azure Resource Manager rotcertifikatfilen** rutan, ange (eller bläddra till) platsen för certifikatfilen.
    5. Klicka på **Nästa**.

    ![Installationsprogrammet för App Service][9]

10. För var och en av de tre filen rutor och klicka på **Bläddra** och navigera till certifikatfil. Du måste ange lösenordet för varje certifikat. Dessa certifikat är de som du skapade i den [skapa nödvändiga certifikat steg](azure-stack-app-service-before-you-get-started.md#get-certificates). Klicka på **nästa** när du har angett all information.

    | Box | Exempel på certifikatet namn |
    | --- | --- |
    | **Apptjänst standardfil SSL-certifikat** | \_.appservice.local.AzureStack.external.pfx |
    | **Filen för Apptjänst API SSL-certifikat** | api.appservice.local.AzureStack.external.pfx |
    | **App Service Publisher SSL certifikatfilen** | ftp.appservice.local.AzureStack.external.pfx |

    Om du har använt ett annat domänsuffix när du skapade certifikaten filnamn certifikatet inte använder *lokala. AzureStack.external*. I stället använda ditt anpassade domäner.

    ![Installationsprogrammet för App Service][10]

11. Ange SQL Server-information för server-instansen som används för att Apptjänst resource provider-databaser och klicka sedan på **nästa**. Installationsprogrammet verifierar egenskaper för SQL-anslutning.

    > [!NOTE]
    > Installationsprogrammet försöker att testa anslutningen till SQl Server innan du fortsätter.  Men om du har valt att distribuera i ett befintligt virtuellt nätverk kan installationsprogrammet kanske inte kan ansluta till SQL Server och en varning visas frågar om du vill fortsätta.  Kontrollera SQL Server-information och fortsätta om de är korrekta.
    >
    >

    ![Installationsprogrammet för App Service][11]

12. Granska rollinstansen och SKU-alternativ. Standardvärdena fylla med det minsta antalet instansen och minsta SKU: N för varje roll i en ASDK-distribution. En sammanfattning av vCPU och minneskrav tillhandahålls för att planera distributionen. När du har gjort dina val klickar du på **nästa**.

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

    ![Installationsprogrammet för App Service][13]

    > [!NOTE]
    > **Windows Server 2016 Core är inte en bild för plattform som stöds för användning med Azure App Service på Azure-stacken.  Använd inte utvärdering avbildningar för Produktionsdistribution.**

13. I den **Välj Plattformsavbildning** väljer din avbildning av virtuell dator för distribution av Windows Server 2016 från bilderna som finns i compute-resursprovidern för Apptjänst-molnet. Klicka på **Nästa**.

14. På nästa sida:
     1. Ange användarnamn för Worker-rollen virtuell dator administratör och lösenord.
     2. Ange andra roller virtuella administratörsanvändarnamn och lösenord.
     3. Klicka på **Nästa**.

    ![Installationsprogrammet för App Service][15]    

15. På sidan Sammanfattning:
    1. Kontrollera de val du gjort. Använd för att göra ändringar i **föregående** knappar för att besöka föregående sidor.
    2. Markera kryssrutan om konfigurationerna är korrekta.
    3. Starta distributionen, klicka på **nästa**.

    ![Installationsprogrammet för App Service][16]

16. På nästa sida:
    1. Spåra installationsförloppet. Apptjänst Azure stacken tar ungefär 60 minuter för att distribuera baserat på standardvalen.
    2. När installationsprogrammet slutförs, klickar du på **avsluta**.

    ![Installationsprogrammet för App Service][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validera App Service på Azure Stack-installation

1. Gå till i administrationsportalen för Azure-stacken **Administration - Apptjänst**.

2. I översikten under status, kontrollerar du att den **Status** visar **alla roller som är klara**.

    ![App Service-hantering](media/azure-stack-app-service-deploy/image12.png)
    
> [!NOTE]
> Om du vill distribuera till ett befintligt virtuellt nätverk och en intern IP-adress till conenct för din filserver, du måste lägga till utgående säkerhetsregel aktivera SMB-trafik mellan worker-undernät och filservern.  Gör detta genom att gå till WorkersNsg i Admin Portal och Lägg till utgående säkerhetsregel med följande egenskaper:
> * Källa: alla
> * Datakällan portintervall: *
> * Mål: IP-adresser
> * Mål-IP-adressintervall: intervall av IP-adresser för din filserver
> * Målportintervall: 445
> * Protokoll: TCP
> * Åtgärd: Tillåt
> * Prioritet: 700
> * Namn: Outbound_Allow_SMB445

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

1. Klicka i klientportalen Azure Stack **+**, gå till Azure Marketplace, distribuera en Django-webbplats och vänta tills åtgärden lyckades. Django webbplatform använder en databas med fil-system. Det kräver inte några ytterligare resurs-leverantörer, till exempel SQL eller MySQL.

2. Om du har distribuerat en MySQL-resursprovidern också kan du distribuera en WordPress-webbplats från Marketplace. När du uppmanas att ange parametrar anger du användarnamnet som *User1@Server1*, med användarnamn och servernamnet du väljer.

3. Om du också distribueras till en provider för SQL Server-resurs, kan du distribuera en DNN webbplats från Marketplace. När du uppmanas att ange parametrar kan du välja en databas i den dator som kör SQL Server som är ansluten till din resursprovidern.

## <a name="next-steps"></a>Nästa steg

Du kan också prova att använda andra [plattform som en tjänst (PaaS) services](azure-stack-tools-paas-services.md).

- [SQL Server-resursprovidern](azure-stack-sql-resource-provider-deploy.md)
- [MySQL-resursprovidern](azure-stack-mysql-resource-provider-deploy.md)

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
