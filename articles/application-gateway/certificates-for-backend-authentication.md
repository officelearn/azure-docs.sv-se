---
title: Certifikat som krävs för att tillåta serverdservrar
titleSuffix: Azure Application Gateway
description: Den här artikeln innehåller exempel på hur ett TLS/SSL-certifikat kan konverteras till autentiseringscertifikat och betrott rotcertifikat som krävs för att tillåta serverdinstanser i Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 20f588639c54b0a8b7cd304f33b5a9d633a73be6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133051"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Skapa certifikat som tillåter servering med Azure Application Gateway

För att göra slutet på TLS kräver Application Gateway att serverdelsinstanserna tillåts genom att överföra autentisering/betrodda rotcertifikat. För v1 SKU krävs autentiseringscertifikat, men för v2 SKU krävs betrodda rotcertifikat för att tillåta certifikaten.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> - Exportera autentiseringscertifikat från ett serverdcertifikat (för v1 SKU)
> - Exportera betrott rotcertifikat från ett serverdcertifikat (för v2 SKU)

## <a name="prerequisites"></a>Krav

Ett befintligt serverdcertifikat krävs för att generera autentiseringscertifikat eller betrodda rotcertifikat som krävs för att tillåta serverdinstanser med Application Gateway. Serveringscertifikatet kan vara samma som TLS/SSL-certifikatet eller annorlunda för ökad säkerhet. Application Gateway ger dig ingen mekanism för att skapa eller köpa ett TLS/SSL-certifikat. I testsyfte kan du skapa ett självsignerat certifikat, men du bör inte använda det för produktionsarbetsbelastningar. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Exportera autentiseringscertifikat (för v1 SKU)

Ett autentiseringscertifikat krävs för att tillåta serverdinstanser i Application Gateway v1 SKU. Autentiseringscertifikatet är den offentliga nyckeln för servercertifikat för server med server i Base-64-kodade X.509(. CER)-format. I det här exemplet ska du använda ett TLS/SSL-certifikat för serverdcertifikatet och exportera dess offentliga nyckel som ska användas som autentiseringscertifiering. I det här exemplet ska du också använda Windows Certificate Manager-verktyget för att exportera de certifikat som krävs. Du kan välja att använda något annat verktyg som är bekvämt.

Från ditt TLS/SSL-certifikat exporterar du den offentliga nyckeln .cer-filen (inte den privata nyckeln). Följande steg hjälper dig att exportera .cer-filen i Base-64-kodade X.509(. CER)-format för ditt certifikat:

1. Om du vill hämta en .cer-fil från certifikatet öppnar du **Hantera användarcertifikat**. Leta reda på certifikatet, vanligtvis i Certifikat - Aktuell användare\Personliga\Certifikat och högerklicka. Klicka på **Alla aktiviteter** och klicka sedan på **Exportera**. **Guiden Exportera certifikat** öppnas. Om du inte hittar certifikatet under Aktuell användare\Personliga\Certifikat kan du av misstag ha öppnat "Certifikat - lokal dator", i stället för "Certifikat - aktuell användare"). Om du vill öppna Certifikathanteraren i det aktuella användaromfattningen med PowerShell skriver du *certmgr* i konsolfönstret.

   ![Exportera](./media/certificates-for-backend-authentication/export.png)

2. Klicka på **Nästa** i guiden.

   ![Exportera certifikatet](./media/certificates-for-backend-authentication/exportwizard.png)

3. Välj **Nej, exportera inte den privata nyckeln** och klicka sedan på **Nästa**.

   ![Exportera inte den privata nyckeln](./media/certificates-for-backend-authentication/notprivatekey.png)

4. På sidan **Filformat för export** väljer du **Base 64-kodad X.509 (. CER).** och klickar sedan på **Nästa**.

   ![Bas-64 kodad](./media/certificates-for-backend-authentication/base64.png)

5. För **Fil att exportera** **bläddrar** du till den plats där du vill exportera certifikatet. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.

   ![Bläddra](./media/certificates-for-backend-authentication/browse.png)

6. Klicka på **Slutför** för att exportera certifikatet.

   ![Slutför](./media/certificates-for-backend-authentication/finish.png)

7. Certifikatet har exporterats.

   ![Lyckades](./media/certificates-for-backend-authentication/success.png)

   Det exporterade certifikatet ser ut ungefär så här:

   ![Exporteras](./media/certificates-for-backend-authentication/exported.png)

8. Om du öppnar det exporterade certifikatet med Anteckningar visas något som liknar det här exemplet. Avsnittet i blått innehåller den information som överförs till programgatewayen. Om du öppnar certifikatet med Anteckningar och det inte liknar detta innebär det vanligtvis att du inte exporterade det med bas-64-kodade X.509(. CER)-format. Om du vill använda en annan textredigerare förstår du dessutom att vissa redigerare kan införa oavsiktlig formatering i bakgrunden. Detta kan skapa problem när du överför texten från det här certifikatet till Azure.

   ![Öppna med Anteckningar](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Exportera betrott rotcertifikat (för v2 SKU)

Betrott rotcertifikat krävs för att vitlista serverdelsinstanser i programgateway v2 SKU. Rotcertifikatet är en Base-64-kodad X.509(. CER) formaterar rotcertifikat från servercertifikaten för serverservern. I det här exemplet använder vi ett TLS/SSL-certifikat för serverdelscertifikatet, exporterar dess offentliga nyckel och exporterar sedan rotcertifikatet för den betrodda certifikatutfärdaren från den offentliga nyckeln i base64-kodat format för att hämta det betrodda rotcertifikatet. Mellanliggande certifikat ska levereras med servercertifikat och installeras på serverservern.

Följande steg hjälper dig att exportera .cer-filen för ditt certifikat:

1. Följ stegen 1-9 som nämns i avsnittet **Exportera autentiseringscertifikat från ett serverdelscertifikat (för v1 SKU)** ovan för att exportera den offentliga nyckeln från serverdelscertifikatet.

2. När den offentliga nyckeln har exporterats öppnar du filen.

   ![Öppna auktoriseringscertifikat](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![om certifikat](./media/certificates-for-backend-authentication/general.png)

3. Gå till vyn Certifieringssökväg om du vill visa certifikatutfärdaren.

   ![information om certifikat](./media/certificates-for-backend-authentication/certdetails.png)

4. Markera rotcertifikatet och klicka på **Visa certifikat**.

   ![cert-sökväg](./media/certificates-for-backend-authentication/rootcert.png)

   Du bör se information om rotcertifikatet.

   ![cert info](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Flytta till **informationsvyn** och klicka på **Kopiera till fil...**

   ![kopiera rotcert](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Nu har du extraherat information om rotcertifikatet från serverdacertifikatet. Guiden **Exportera certifikat**visas . Använd nu steg 2-9 som nämns i avsnittet **Exportera autentiseringscertifikat från ett serverdelscertifikat (för v1 SKU)** ovan för att exportera det betrodda rotcertifikatet i Base-64-kodade X.509(. CER)-format.

## <a name="next-steps"></a>Nästa steg

Nu har du autentiseringscertifikatet/det betrodda rotcertifikatet i Base-64 kodat X.509(. CER)-format. Du kan lägga till detta i programgatewayen för att vitlista dina serverdelsservrar för slutpunkt till TLS-kryptering. Se [Konfigurera TLS från till med hjälp av Application Gateway med PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

