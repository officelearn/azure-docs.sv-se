---
title: "Använder certifikat med Enterprise-Integrationspaket | Microsoft Docs"
description: "Lär dig hur du använder certifikat med Enterprise-Integrationspaket | Azure Logikappar"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 0570aab14283b38f9efcc50636f0c0c1c8e3ed13
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>Läs om certifikat och Enterprise-integreringspaket
## <a name="overview"></a>Översikt
Enterprise integration använder certifikat för säker B2B-kommunikation. Du kan använda två typer av certifikat i dina enterprise integration-appar:

* Offentliga certifikat, som måste köpas från en certifikatutfärdare (CA).
* Privata certifikat kan du utfärda själv. Dessa certifikat kallas ibland självsignerade certifikat.

## <a name="what-are-certificates"></a>Vad är certifikat?
Certifikat är digitalt dokument som verifieringen av identiteten för deltagarna i elektronisk kommunikation och som även säker elektronisk kommunikation.

## <a name="why-use-certificates"></a>Varför använda certifikat?
Ibland måste B2B-kommunikation vara konfidentiella. Enterprise integration använder certifikat för att skydda dessa meddelanden på två sätt:

* Genom att kryptera innehållet i meddelanden
* Genom att signera meddelanden  

## <a name="upload-a-public-certificate"></a>Ladda upp ett offentligt certifikat

Att använda en *certifikat med offentlig* i dina logic apps med B2B-funktioner, måste du först överföra certifikatet till ditt konto för integrering.  

När du har överfört ett certifikat är tillgängligt för att skydda dina B2B-meddelanden när du definierar deras egenskaper i den [avtal](logic-apps-enterprise-integration-agreements.md) som du skapar.  

Här följer detaljerade anvisningar för uppladdning av din offentliga certifikat till kontot integration när du loggar in på Azure-portalen:

1. Välj **fler tjänster** och ange **integrering** i sökrutan filtrera. Välj **Integrationskonton** från resultatlistan över     
![Välj Bläddra](media/logic-apps-enterprise-integration-certificates/overview-1.png)  
2. Välj integration kontot som du vill lägga till certifikatet.  
![Välj integration kontot som du vill lägga till certifikatet](media/logic-apps-enterprise-integration-certificates/overview-3.png)  
3. Välj den **certifikat** panelen.  
![Välj panelen certifikat](media/logic-apps-enterprise-integration-certificates/certificate-1.png)
4. I den **certifikat** bladet som öppnas väljer du den **Lägg till** knappen.   
![Klicka på knappen Lägg till](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
5. Ange en **namn** för certifikatet och välj sedan certifikatet skriver som **offentliga** i listrutan.  
6. Välj mappikonen på höger sida av den **certifikat** textruta. När filväljaren öppnas, söka efter och väljer den certifikatfil som du vill överföra till ditt konto för integrering.
7. Välj certifikatet och välj sedan **OK** i filväljaren. Detta verifierar och laddar upp certifikatet till ditt konto för integrering.
8. Slutligen tillbaka på den **Lägg till certifikat** bladet väljer den **OK** knappen.  
![Klicka på knappen OK](media/logic-apps-enterprise-integration-certificates/certificate-3.png)  
9. Välj den **certifikat** panelen. Du bör se det nya certifikatet.  
![Finns det nya certifikatet](media/logic-apps-enterprise-integration-certificates/certificate-4.png)  

## <a name="upload-a-private-certificate"></a>Ladda upp ett privat certifikat

Att använda en *privat certifikat* i dina logic apps med B2B-funktioner, du kan ladda upp ett privat certifikat till ditt konto integration genom att utföra följande åtgärder

1. [Ladda upp din privata nyckel till Nyckelvalvet](../key-vault/key-vault-get-started.md "Lär dig mer om Key Vault") och ger en **nyckelnamn** 
   
   > [!TIP]
   > Du måste auktorisera Logic Apps att utföra åtgärder på Nyckelvalvet. Du kan ge åtkomst till Logic Apps tjänstens huvudnamn med hjälp av följande PowerShell-kommando:`Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  
   > 
   > 

När du har gjort det föregående steget, lägger du till ett privat certifikat i integration konto.

Nedan följer detaljerade anvisningar för uppladdning av dina privata certifikat till kontot integration när du loggar in på Azure-portalen:  
 
1. Välj integration kontot som du vill lägga till certifikatet och välj den **certifikat** panelen.  
![Välj panelen certifikat](media/logic-apps-enterprise-integration-certificates/certificate-1.png)  
2. I den **certifikat** bladet som öppnas väljer du den **Lägg till** knappen.   
![Klicka på knappen Lägg till](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
3. Ange en **namn** för certifikatet och välj certifikatet skriver som **privata** i listrutan.   
4. Välj mappikonen på höger sida av den **certifikat** textruta. Hitta motsvarande offentliga certifikat som du vill överföra till ditt konto integration när filväljaren öppnas.   
   
   > [!Note]
   > När du lägger till ett privat certifikat är det viktigt att lägga till motsvarande offentliga certifikat ska visas i [AS2-avtal](logic-apps-enterprise-integration-as2.md) ta emot och skicka inställningarna för signering och kryptering av meddelanden.
   > 
   >   

5. Välj den **resursgruppen**, **Nyckelvalvet**, **nyckelnamn** och välj den **OK** knappen.  
![Lägg till certifikat](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)  
6. Välj den **certifikat** panelen. Du bör se det nya certifikatet.
![Finns det nya certifikatet](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)  



* [Skapa en B2B-avtal](logic-apps-enterprise-integration-agreements.md)  
* [Mer information om Key Vault](../key-vault/key-vault-get-started.md "Lär dig mer om Key Vault")  

