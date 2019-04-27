---
title: Certifikat som krävs för lista över tillåtna serverdelar i Azure Application Gateway
description: Den här artikeln innehåller exempel på hur ett SSL-certifikat kan konverteras till certifikat för serverautentisering och betrodda rotcertifikat som krävs för serverdelsinstanser för listan över tillåtna program i Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/14/2019
ms.author: absha
ms.openlocfilehash: 72ee9123ad959c0c7240d4f7a906adc1a4dd1a93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831724"
---
# <a name="create-certificates-for-whitelisting-backend-with-azure-application-gateway"></a>Skapa certifikat för lista över tillåtna serverdelen med Azure Application Gateway

Om du vill utföra slutpunkt till slutpunkt SSL krävs serverdelsinstanser tillåtas genom att ladda upp autentisering/betrodda rotcertifikat. När det gäller v1-SKU, certifikat för klientautentisering krävs medan vid v2-SKU, betrodda rotcertifikat som krävs för att godkänna certifikaten

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> - Exportera certifikatet för serverautentisering från ett backend-certifikat (för v1-SKU)
> - Exportera betrodda rotcertifikat från ett backend-certifikat (för v2-SKU)

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver ett befintligt backend-certifikat för att generera certifikat för autentisering eller betrodda rotcertifikat som krävs för lista över tillåtna serverdelsinstanser med application gateway. Backend-certifikat kan vara samma som SSL-certifikat eller en annan för ökad säkerhet. Application gateway ger dig alla mekanism för att skapa eller köpa ett SSL-certifikat. Du kan skapa ett självsignerat certifikat för testning, men du bör inte använda den för produktionsarbetsbelastningar. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Exportera certifikatet för serverautentisering (för v1-SKU)

Certifikat för klientautentisering krävs för att vitlista serverdelsinstanser i application gateway v1 SKU. Certifikat för klientautentisering är den offentliga nyckeln för serverdels-servercertifikat i Base64-kodad X.509 (. CER)-format. I det här exemplet vi använder ett SSL-certifikat för backend-certifikat och exportera den offentliga nyckeln som ska användas som autentisering certifiering. I det här exemplet använder vi dessutom Certifikathanteraren för Windows-verktyget för att exportera certifikat som krävs. Du kan välja att använda andra verktyg används för att underlätta för dig.

Exportera den offentliga nyckeln .cer-fil (inte den privata nyckeln) från ditt SSL-certifikat. Följande steg hjälper dig att exportera .cer-fil i Base64-kodad X.509 (. CER)-format för ditt certifikat:

1. Om du vill hämta en .cer-fil från certifikatet öppnar du **Hantera användarcertifikat**. Leta upp certifikatet, vanligtvis under ”certifikat – aktuell användare\personligt\certifikat” och högerklicka. Klicka på **Alla aktiviteter** och klicka sedan på **Exportera**. **Guiden Exportera certifikat** öppnas. Om du inte kan hitta certifikatet under aktuell användare\personligt\certifikat, kan du av misstag öppnade ”certifikat - lokal Computer”, i stället för ”certifikat – aktuell användare”). Om du vill öppna Certifikathanteraren i aktuella omfång med hjälp av PowerShell kan du skriva *certmgr* i konsolfönstret.

   ![Exportera](./media/certificates-for-backend-authentication/export.png)

2. Klicka på **Nästa** i guiden.

   ![Exportera certifikatet](./media/certificates-for-backend-authentication/exportwizard.png)

3. Välj **Nej, exportera inte den privata nyckeln** och klicka sedan på **Nästa**.

   ![Exportera inte den privata nyckeln](./media/certificates-for-backend-authentication/notprivatekey.png)

4. På sidan **Filformat för export** väljer du **Base 64-kodad X.509 (. CER).** och klickar sedan på **Nästa**.

   ![Base 64-kodad](./media/certificates-for-backend-authentication/base64.png)

5. För **fil som ska exporteras**, **Bläddra** till den plats som du vill exportera certifikatet. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.

   ![Bläddra](./media/certificates-for-backend-authentication/browse.png)

6. Klicka på **Slutför** för att exportera certifikatet.

   ![Slutför](./media/certificates-for-backend-authentication/finish.png)

7. Ditt certifikat har exporterats.

   ![Lyckades](./media/certificates-for-backend-authentication/success.png)

   Det exporterade certifikatet ser ut ungefär så här:

   ![Exporterat](./media/certificates-for-backend-authentication/exported.png)

8. Om du öppnar det exporterade certifikatet med hjälp av anteckningar, visas något som liknar det här exemplet. I avsnittet i blått innehåller den information som överförs till application gateway. Om du vill öppna certifikatet med anteckningar och ser inte ut ungefär så här, vanligtvis det innebär att du inte exportera det med Base64-kodad X.509 (. CER)-format. Om du vill använda en annan textredigerare kan du också känna att vissa redigerare kan leda till oönskade formatering i bakgrunden. Detta kan skapa problem när laddat upp text från det här certifikatet till Azure.

   ![Öppna med anteckningar](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Exportera betrodda rotcertifikat (för v2-SKU)

Betrodda rotcertifikat som krävs för att vitlista serverdelsinstanser i application gateway v2 SKU. Rotcertifikatet är en Base 64-kodad X.509 (. CER) format rotcertifikat från serverdels-servercertifikat. I det här exemplet ska vi använda ett SSL-certifikat för backend-certifikatet, exportera den offentliga nyckeln och sedan exportera rotcertifikatet för den betrodda Certifikatutfärdaren från den offentliga nyckeln i base64-kodat format för att hämta det betrodda rotcertifikatet. 

Följande steg hjälper dig att exportera .cer-filen för certifikatet:

1. Följ steg 1 – 9 avsnittet **autentiseringscertifikat för Export från ett backend-certifikat (för v1-SKU)** ovan för att exportera den offentliga nyckeln från backend-certifikatet.

2. När den offentliga nyckeln har exporterats kan du öppna filen.

   ![Öppna auktorisering certifikat](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![Om certifikat](./media/certificates-for-backend-authentication/general.png)

3. Flytta till certifieringssökvägen vyn certifikatutfärdaren.

   ![information om certifikat](./media/certificates-for-backend-authentication/certdetails.png)

4. Välj rotcertifikat och klicka på **Visa certifikat**.

   ![CERT sökväg](./media/certificates-for-backend-authentication/rootcert.png)

   Du ska kunna visa rot-certifikatinformation.

   ![CERT-info](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Flytta till den **information** visa och klicka på **kopiera till fil...**

   ![Kopiera rotcertifikatet](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Du har nu extraherat information om rotcertifikatet från backend-certifikatet. Du kommer att se den **guiden Exportera certifikat**. Nu använda steg 2 – 9 avsnittet **autentiseringscertifikat för Export från ett backend-certifikat (för v1-SKU)** ovan för att exportera den betrodda roten certifikatet i Base-64-kodad X.509 (. CER)-format.

## <a name="next-steps"></a>Nästa steg

Nu har autentiseringen certifikatet/betrott rotcertifikat i Base64-kodad X.509 (. CER)-format. Du kan lägga till detta application gateway till listan över godkända backend-servrarna för slutpunkt till slutpunkt SSL-kryptering. Se [så här konfigurerar du från slutpunkt till slutpunkt SSL-kryptering](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).