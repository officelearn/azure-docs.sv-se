---
title: Snabb start – Konfigurera SSL-kryptering från slut punkt till slut punkt med Azure Application Gateway-Azure Portal | Microsoft Docs
description: Lär dig hur du använder Azure Portal för att skapa en Azure Application Gateway med SSL-kryptering från slut punkt till slut punkt.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a37b313bd808ee0441d84ac92050b087eba7ac9d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097199"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Konfigurera SSL från slut punkt till slut punkt med hjälp av Application Gateway med portalen

Den här artikeln visar hur du använder Azure Portal för att konfigurera SSL-kryptering från slut punkt till slut punkt med en Application Gateway v1 SKU.  

> [!NOTE]
> Application Gateway v2-SKU: n kräver betrodda rot certifikat för att aktivera konfiguration från slut punkt till slut punkt.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill konfigurera SSL från slut punkt till slut punkt med ett Application Gateway krävs ett certifikat för den gateway och de certifikat som krävs för backend-servrarna. Gateway-certifikatet används för att härleda en symmetrisk nyckel enligt specifikationen för SSL-protokoll. Den symmetriska nyckeln används sedan för att kryptera och dekryptera trafiken som skickas till gatewayen. För SSL-kryptering från slut punkt till slut punkt måste rätt backend-servrar vara tillåtna i Application Gateway. Om du vill göra detta laddar du upp det offentliga certifikatet för backend-servrarna, även kallat autentiseringscertifikat (v1) eller betrodda rot certifikat (v2), till Application Gateway. Genom att lägga till certifikatet ser du till att Application Gateway endast kommunicerar med kända backend-instanser. Detta skyddar all kommunikation från slut punkt till slut punkt.

Läs mer i SSL- [terminering och slut punkt till slut punkt för SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Skapa en ny Application Gateway med SSL från slut punkt till slut punkt

Om du vill skapa en ny Programgateway med SSL-kryptering från slut punkt till slut punkt måste du först aktivera SSL-avslutning när du skapar en ny Application Gateway. Detta aktiverar SSL-kryptering för kommunikationen mellan klienten och programgatewayen. Sedan måste du vitlista certifikat för backend-servrar i HTTP-inställningarna för att aktivera SSL-kryptering för kommunikationen mellan programgatewayen och backend-servrar, vilket gör SSL-kryptering från slut punkt till slut punkt.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Aktivera SSL-avslutning när du skapar en ny Application Gateway

Läs den här artikeln för att förstå hur du [aktiverar SSL-terminering när du skapar en ny Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificate-of-back-end-servers"></a>Lägg till autentisering/rot certifikat för backend-servrar

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **http-inställningar** på den vänstra menyn. Azure skapade automatiskt en standard-HTTP-inställning, **appGatewayBackendHttpSettings**, när du skapade Application Gateway. 

3. Välj **appGatewayBackendHttpSettings**.

4. Under **protokoll**väljer du **https**. Ett fönster för **certifikat från Server delens autentisering eller betrodda rot certifikat** visas. 

5. Välj **Skapa nytt**.

6. Ange ett lämpligt **namn**.

7. Välj certifikat filen i rutan **Ladda upp CER-certifikat** .

   För standard-och WAF-programgatewayer (v1) bör du överföra den offentliga nyckeln för Server delens Server certifikat i. cer-format.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   För Standard_v2-och WAF_v2-programgatewayer bör du ladda upp **rot certifikatet** för Server delens Server certifikat i CER-format. Om Server dels certifikatet har utfärdats av en välkänd certifikat utfärdare, kan du markera kryss rutan Använd välkänd CA-certifikat och det finns inget behov av att ladda upp ett certifikat.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![rootcert](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Välj **Spara**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Aktivera slutpunkt-till-slutpunkt-SSL för befintlig Application Gateway

Om du vill konfigurera en befintlig Application Gateway med SSL-kryptering från slut punkt till slut punkt måste du först aktivera SSL-avslutning i lyssnaren. Detta aktiverar SSL-kryptering för kommunikationen mellan klienten och programgatewayen. Sedan måste du vitlista certifikat för backend-servrar i HTTP-inställningarna för att aktivera SSL-kryptering för kommunikationen mellan programgatewayen och backend-servrar, vilket gör SSL-kryptering från slut punkt till slut punkt.

Du måste använda en lyssnare med HTTPS-protokoll och certifikat för att aktivera SSL-avslutning. Du kan välja att antingen använda en befintlig lyssnare med HTTPS-protokoll och certifikat, eller skapa en ny lyssnare. Om du väljer den tidigare kan du ignorera de nedan nämnda stegen för att **Aktivera SSL-avslutning i den befintliga programgatewayen** och flytta den direkt till avsnittet **Lägg till autentisering/betrodda rot certifikat för backend-servrar** . Använd de här stegen om du väljer den senare.

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Aktivera SSL-avslutning i den befintliga Application Gateway

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **lyssnare** på den vänstra menyn.

3. Välj mellan **grundläggande** och fler lyssnare för **flera platser** enligt ditt krav.

4. Under **protokoll**väljer du **https**. Ett fönster för **certifikat** kommer att visas.

5. Ladda upp det PFX-certifikat som du vill använda för SSL-terminering mellan klienten och programgatewayen.

   > [!NOTE]
   > I test syfte kan du använda ett självsignerat certifikat. men vi rekommenderar inte att du använder produktions arbets belastningar eftersom de är svårare att hantera och inte helt säkra. Lär dig hur du [skapar ett självsignerat certifikat](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Lägg till andra nödvändiga inställningar för **lyssnaren** enligt ditt krav.

7. Välj **OK** för att spara.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Lägg till autentisering/betrodda rot certifikat för backend-servrar

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **http-inställningar** på den vänstra menyn. Du kan antingen vitlista certifikat i en befintlig HTTP-inställning för Server delen eller skapa en ny HTTP-inställning. I steget nedan kommer vi att vitlista certifikat för standard-HTTP-inställningen, **appGatewayBackendHttpSettings**.

3. Välj **appGatewayBackendHttpSettings**.

4. Under **protokoll**väljer du **https**. Ett fönster för **certifikat från Server delens autentisering eller betrodda rot certifikat** visas. 

5. Välj **Skapa nytt**.

6. Ange lämpligt **namn**.

7. Välj certifikat filen i rutan **Ladda upp CER-certifikat** .

   För standard-och WAF-programgatewayer (v1) bör du överföra den offentliga nyckeln för Server delens Server certifikat i. cer-format.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   För Standard_v2-och WAF_v2-programgatewayer bör du ladda upp **rot certifikatet** för Server delens Server certifikat i CER-format. Om Server dels certifikatet har utfärdats av en välkänd certifikat utfärdare, kan du markera kryss rutan Använd välkänd CA-certifikat och det finns inget behov av att ladda upp ett certifikat.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)
