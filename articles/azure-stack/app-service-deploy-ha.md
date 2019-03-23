---
title: Distribuera Azure Stack App Service i en konfiguration med hög tillgänglighet | Microsoft Docs
description: Lär dig mer om att distribuera App Service i Azure Stack med en konfiguration med hög tillgänglighet.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: ''
ms.date: 03/23/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 1c105548f19994c4ca0ce161eedcfe11736864c7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370031"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>Distribuera App Service i en konfiguration med hög tillgänglighet

Den här artikeln visar hur du använder Azure Stack marketplace-objekt för att distribuera App Service för Azure Stack i en konfiguration med hög tillgänglighet. Förutom tillgängliga marketplace-objekt, den här lösningen använder även den [appservice-filresurs-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) snabbstartsmall för Azure Stack. Den här mallen skapar en infrastruktur med hög tillgänglighet som värd för App Service-resursprovider. App Service installeras på den här VM-infrastruktur med hög tillgänglighet. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>Distribuera högtillgängliga App Service-infrastrukturens virtuella datorer
Den [appservice-filresurs-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) snabbstartsmall för Azure Stack förenklar distribution av App Service i en konfiguration med hög tillgänglighet. Den bör distribueras i Providerprenumeration som standard. 

När den används för att skapa en anpassad resurs i Azure Stack, skapas:
- Ett virtuellt nätverk och undernät som krävs.
- Nätverkssäkerhetsgrupper för filserver, SQL Server och Active Directory Domain Services (AD DS)-undernät.
- Lagringskonton för VM-diskar och molnvittne för klustret.
- En intern belastningsutjämnare för virtuella SQL-datorer med privata IP-adress kopplad till SQL AlwaysOn-lyssnaren.
- Tre tillgänglighetsuppsättningar för AD DS, filserverklustret och SQL-klustret.
- SQL-kluster med två noder.
- Två noder filserverklustret.
- Två domänkontrollanter.

### <a name="required-azure-stack-marketplace-items"></a>Nödvändiga Azure Stack marketplace-objekt
Innan du använder den här mallen, kontrollera att följande [Azure Stack marketplace-objekt](azure-stack-marketplace-azure-items.md) är tillgängliga i Azure Stack-instans:

- Windows Server 2016 Datacenter Core-avbildning (för AD DS och file server virtuella datorer)
- SQL Server 2016 SP2 på Windows Server 2016 (företag)
- Senaste SQL IaaS-tillägget 
- Senaste versionen av PowerShell Desired State Configuration-tillägg 

> [!TIP]
> Granska [readme-filen för mallen](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) på GitHub för ytterligare information om kraven för mallen och standardvärden. 

### <a name="deploy-the-app-service-infrastructure"></a>Distribuera App Service-infrastrukturen
Använda stegen i det här avsnittet för att skapa en anpassad distribution med hjälp av den **appservice-filresurs-sqlserver-ha** snabbstartsmall för Azure Stack.

1. [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. Välj **\+** **skapa en resurs** > **anpassade**, och sedan **malldistributionen**.

   ![Anpassad malldistribution](media/app-service-deploy-ha/1.png)


3. På den **anpassad distribution** bladet väljer **redigera mallen** > **snabbstartsmall** och sedan använda listan över tillgängliga anpassade mallar till Välj den **appservice-filresurs-sqlserver-ha** mall, klickar du på **OK**, och sedan **spara**.

   ![Välj App Service-filresurs-sqlserver-hög tillgänglighet QuickStart-mallen](media/app-service-deploy-ha/2.png)

4. På den **anpassad distribution** bladet väljer **redigera parametrar** och rulla ned till Granska standardvärden för mallen. Ändra dessa värden som behövs för att ange alla nödvändiga parameterinformation och klicka sedan på **OK**.<br><br> Ange komplexa lösenord för parametrarna ADMINPASSWORD, FILESHAREOWNERPASSWORD, FILESHAREUSERPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD och SQLLOGINPASSWORD som ett minimum.
    
   ![Redigera anpassad distribution parametrar](media/app-service-deploy-ha/3.png)

5. På den **anpassad distribution** bladet Kontrollera **standard Providerprenumeration** väljs som prenumerationen som ska använda och sedan skapa en ny resursgrupp eller välj en befintlig resursgrupp för anpassat distribution.<br><br> Välj plats för resursgruppen (**lokala** för ASDK installationer) och klicka sedan på **skapa**. De anpassade distributionsinställningarna kommer att valideras innan malldistributionen startar.

    ![Skapa anpassad distribution](media/app-service-deploy-ha/4.png)

6. I administrationsportalen, Välj **resursgrupper** och sedan namnet på resursgruppen du skapade för anpassad distribution (**app-service-ha** i det här exemplet). Visa statusen för distributionen för att se till att alla distributioner har slutförts.

   > [!NOTE]
   > Malldistributionen tar ungefär en timme att slutföra.

   [![](media/app-service-deploy-ha/5-sm.png "Granska status för distribution av mallen")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Mall matar ut
När distributionen har slutförts, post har malldistributionen matar ut. Du måste ange den här informationen när du kör installationsprogrammet för App Service. 

Kontrollera att du anteckna var och en av dessa utdatavärden:
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

Följ dessa steg för att identifiera mallvärden för utdata:

1. [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. I administrationsportalen, Välj **resursgrupper** och sedan namnet på resursgruppen du skapade för anpassad distribution (**app-service-ha** i det här exemplet). 

3. Klicka på **distributioner** och välj **Microsoft.Template**.

    ![Microsoft.Template distribution](media/app-service-deploy-ha/6.png)

4. När du har valt den **Microsoft.Template** distribution, väljer **utdata** och registrera mallutdata för parametern. Den här informationen måste utföras när du distribuerar App Service.

    ![Parametern-utdata](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>Distribuera App Service i en konfiguration med hög tillgänglighet
Följ stegen i det här avsnittet för att distribuera App Service för Azure Stack i en lättillgänglig konfiguration baserat på den [appservice-filresurs-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) snabbstartsmall för Azure Stack. 

Efter installation av App Service-resursprovider kan inkludera du den i dina erbjudanden och planer. Användare kan prenumerera sedan för att hämta tjänsten och börja skapa program.

> [!IMPORTANT]
> Innan du kör installationsprogrammet för providern resurs ska du kontrollera att du har läst den viktiga informationen som åtföljer varje App Service-version så att du lär dig mer om nya funktioner och korrigeringar kända problem som kan påverka din distribution.

### <a name="prerequisites"></a>Förutsättningar
Innan du kan köra App Service-installationsprogrammet flera steg som krävs enligt beskrivningen i den [innan du sätter igång med App Service i Azure Stack-artikeln](azure-stack-app-service-before-you-get-started.md):

> [!TIP]
> Inte alla steg som beskrivs i den innan du sätter igång artikel krävs eftersom mallen deploymnet konfigurerar infrastrukturens virtuella datorer åt dig. 

- [Ladda ned installationsprogrammet och helper skripten för App Service](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts).
- [Ladda ned det senaste tillägget för anpassat skript i Azure Stack Marketplace](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace).
- [Generera certifikat som krävs](azure-stack-app-service-before-you-get-started.md#get-certificates).
- Skapa ID-program baserat på identifiera providern du har valt för Azure Stack. Ett ID-program kan göras för antingen [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application) eller [Active Directory Federation Services](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application) och anteckna programmets ID.
- Se till att du har lagt till Windows Server 2016 Datacenter-avbildning till Azure Stack marketplace. Detta krävs för installation av App Service.

### <a name="deploy-app-service-in-highly-available-configuration"></a>Distribuera App Service i konfiguration med hög tillgänglighet
Installera App Service-resursprovider tar minst en timme. Hur lång tid som behövs beror på hur många rollen instanser du distribuerar. Under distributionen så, körs installationsprogrammet följande uppgifter:

- Skapa en blobbehållare i det angivna Azure Stack-lagringskontot.
- Skapa en DNS-zon och DNS-poster för App Service.
- Registrera resursprovidern för App Service.
- Registrera galleriobjekt App Service.

Distribuera App Service-resursprovider genom att följa dessa steg:

1. Kör installationsprogrammet för tidigare hämtade App Service (**appservice.exe**) som administratör från en dator som har åtkomst till Azure Stack Admin Azure Resource Management-slutpunkten.

2. Välj **distribuera App Service eller uppgradera till den senaste versionen**.

    ![Distribuera eller uppgradera](media/app-service-deploy-ha/01.png)

3. Godkänn Microsoft licensvillkoren och klicka på **nästa**.

    ![Licensvillkor för Microsoft](media/app-service-deploy-ha/02.png)

4. Acceptera licensvillkoren för icke-Microsoft och klicka på **nästa**.

    ![Licensvillkor för icke-Microsoft](media/app-service-deploy-ha/03.png)

5. Ange App Service-slutpunkt molnkonfigurationen för Azure Stack-miljön.

    ![App Service-slutpunkt molnkonfigurationen](media/app-service-deploy-ha/04.png)

6. **Ansluta** till Azure Stack-prenumerationen som ska användas för installationen och välj platsen. 

    ![Ansluta till Azure Stack-prenumerationen](media/app-service-deploy-ha/05.png)

7. Välj **Använd befintligt virtuellt nätverk och undernät** och **resursgruppens namn** för resursgruppen som används för att distribuera mallen med hög tillgänglighet.<br><br>Därefter, Välj det virtuella nätverk som skapats som en del av malldistributionen och välj sedan rätt roll undernät från de nedrullningsbara Listalternativ. 

    ![Vnet-urval](media/app-service-deploy-ha/06.png)

8. Ange tidigare inspelade mallen matar ut information för sökvägen till filresursen och file share ägare-parametrar. När du är klar klickar du på **nästa**.

    ![Information om filresurs utdata](media/app-service-deploy-ha/07.png)

9. Eftersom den datorn som används för att installera App Service inte finns på samma virtuella nätverk som den filserver som används för att vara värd för App Service-filresurs, kommer det inte att matcha namnet. **Detta är förväntat**.<br><br>Kontrollera att informationen i filresursen UNC-sökväg och konton information är korrekt och tryck på **Ja** på varningsruta att fortsätta installationen av App Service.

    ![Förväntade feldialogruta](media/app-service-deploy-ha/08.png)

    Om du väljer att distribuera till ett befintligt virtuellt nätverk och en intern IP-adress för att ansluta till filservern, du måste lägga till en utgående säkerhetsregel att aktivera SMB-trafik mellan worker-undernät och filservern. Gå till WorkersNsg i administrationsportalen och lägga till en utgående säkerhetsregel med följande egenskaper:
    - Källa: Alla
    - Käll-portintervall: *
    - Mål: IP-adresser
    - Mål-IP-adressintervall: Intervall av IP-adresser för din filserver
    - Målportintervall: 445
    - Protokoll: TCP
    - Åtgärd: Tillåt
    - Prioritet: 700
    - Namn: Outbound_Allow_SMB445

10. Anger den Identitetsprogram-ID och sökvägen och lösenord för att certifikat som identitet och klickar på **nästa**:
    - Identitetsprogramcertifikat (i formatet **sso.appservice.local.azurestack.external.pfx**)
    - Azure Resource Manager-rotcertifikatet (**AzureStackCertificationAuthority.cer**)

    ![ID för programmet certifikatet och rotcertifikatet](media/app-service-deploy-ha/008.png)

10. Sedan ange återstående nödvändig information för följande certifikat och klicka på **nästa**:
    - Azure Stack SSL-standardcertifikatet (i formatet **_.appservice.local.azurestack.external.pfx**)
    - API-SSL-certifikat (i formatet **api.appservice.local.azurestack.external.pfx**)
    - Utgivarcertifikat (i form av **ftp.appservice.local.azurestack.external.pfx**) 

    ![Certifikat för ytterligare konfiguration](media/app-service-deploy-ha/09.png)

11. Ange den SQL Server-anslutningsinformation med hjälp av SQL Server-anslutningsinformationen från distributionsutdata för hög tillgänglighet mall:

    ![Anslutningsinformationen för SQL Server](media/app-service-deploy-ha/10.png)

12. Eftersom den datorn som används för att installera App Service inte finns på samma virtuella nätverk som SQL-servern som används för att vara värd för App Service-databaserna ska att du inte kunna matcha namnet.  **Detta är förväntat**.<br><br>Kontrollera att informationen i SQL Server-namn och konton information är korrekt och tryck på **Ja** att fortsätta installationen av App Service. Klicka på **Nästa**.

    ![Anslutningsinformationen för SQL Server](media/app-service-deploy-ha/11.png)

13. Godkänn standardvärdena för konfiguration av rollen eller ändra till rekommenderade värden och klickar på **nästa**.<br><br>Vi rekommenderar att standardvärden för rollinstanser för App Service-infrastrukturen ska ändras på följande sätt för konfigurationer med hög tillgänglighet:

    |Roll|Standard|Rekommendation för hög tillgänglighet|
    |-----|-----|-----|
    |Kontrollantrollen|2|2|
    |Hanteringsroll|1|3|
    |Utgivarroll|1|3|
    |Klientdelsroll|1|3|
    |Delad Arbetsroll|1|10|
    |     |     |     |

    ![Infrastrukturvärden rollen instans](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Ändra registernycklarnas standardvärden till de som rekommenderas i detta tutoral ökar maskinvarukraven för att installera App Service. Totalt 26 kärnor och 46,592 MB RAM-minne behövs för att stödja de rekommendera 21 virtuella datorerna i stället för standard 18 kärnor och 32 256 MB RAM för virtuella datorer i 15.

14. Välj plattformsavbildningen som ska användas för att installera App Service-infrastrukturens virtuella datorer och klicka på **nästa**:

    ![Valet av plattform](media/app-service-deploy-ha/13.png)

15. Ange App Service infrastruktur rollen autentiseringsuppgifter ska användas och klicka på **nästa**:

    ![Autentiseringsuppgifter för infrastruktur-roll](media/app-service-deploy-ha/14.png)

16. Granska informationen som används för att distribuera App Service och klicka på **nästa** att starta distributionen. 

    ![Granska Installationsöversikt](media/app-service-deploy-ha/15.png)

17. Granska förloppet för App Service-distributionen. Det kan ta över en timme beroende på dina specifika distributionskonfiguration och maskinvara. När installationsprogrammet har slutförts korrekt väljer **avsluta**.

    ![Installationen slutfördes](media/app-service-deploy-ha/16.png)


## <a name="next-steps"></a>Nästa steg

[Skala ut App Service](azure-stack-app-service-add-worker-roles.md). Du kan behöva lägga till fler App Service-infrastrukturen rollen arbeten för att möta efterfrågan förväntade program i din miljö. Som standard har kostnadsfria och delade arbetarnivåer stöd för App Service i Azure Stack. Om du vill lägga till andra arbetarnivåer, som du behöver lägga till flera worker-roller.

[Konfigurera distributionskällor](azure-stack-app-service-configure-deployment-sources.md). Det krävs ytterligare konfiguration för att stödja distribution av på begäran från flera källkontrollsprovidrar som GitHub, BitBucket, OneDrive och DropBox.

[Säkerhetskopiera Apptjänst](app-service-back-up.md). När du har distribuera och konfigurera App Service, bör du kontrollera att alla komponenter som behövs för haveriberedskap som säkerhetskopieras till att förhindra dataförlust och undvika onödiga service driftstopp under återställningsåtgärder.