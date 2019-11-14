---
title: Konfigurera SSL-kryptering från slut punkt till slut punkt med portalen
titleSuffix: Azure Application Gateway
description: Lär dig hur du använder Azure Portal för att skapa en Programgateway med SSL-kryptering från slut punkt till slut punkt.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a878b966266bdd326db35d266bc14b2f81161e92
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075125"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Konfigurera SSL från slut punkt till slut punkt med hjälp av Application Gateway med portalen

Den här artikeln beskriver hur du använder Azure Portal för att konfigurera kryptering från slut punkt till slut Secure Sockets Layer punkt (SSL) via Azure Application Gateway v1 SKU.

> [!NOTE]
> Application Gateway v2-SKU: n kräver betrodda rot certifikat för att aktivera konfiguration från slut punkt till slut punkt.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill konfigurera SSL från slut punkt till slut punkt med en Programgateway behöver du ett certifikat för gatewayen. Certifikat krävs också för backend-servrarna. Gateway-certifikatet används för att härleda en symmetrisk nyckel i enlighet med SSL-protokollets specifikation. Den symmetriska nyckeln används sedan för att kryptera och dekryptera trafiken som skickas till gatewayen. 

För SSL-kryptering från slut punkt till slut punkt måste rätt backend-servrar vara tillåtna i Application Gateway. För att tillåta den här åtkomsten laddar du upp det offentliga certifikatet för backend-servrarna, även kallat autentiseringscertifikat (v1) eller betrodda rot certifikat (v2), till programgatewayen. Genom att lägga till certifikatet ser du till att Application Gateway endast kommunicerar med kända backend-instanser. Den här konfigurationen skyddar all kommunikation från slut punkt till slut punkt.

Läs mer i SSL- [terminering och slut punkt till slut punkt för SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Skapa en ny Application Gateway med SSL från slut punkt till slut punkt

Om du vill skapa en ny Programgateway med SSL-kryptering från slut punkt till slut punkt måste du först aktivera SSL-avslutning när du skapar en ny Application Gateway. Den här åtgärden aktiverar SSL-kryptering för kommunikation mellan klienten och programgatewayen. Sedan måste du lägga till en lista över certifikaten för backend-servrarna i HTTP-inställningarna i listan Betrodda mottagare. Den här konfigurationen aktiverar SSL-kryptering för kommunikation mellan programgatewayen och backend-servrarna. Som utför SSL-kryptering från slut punkt till slut punkt.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Aktivera SSL-avslutning när du skapar en ny Application Gateway

Läs mer i [Aktivera SSL-avslutning när du skapar en ny Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Lägg till autentiserings-/rot certifikat för backend-servrar

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **http-inställningar** på menyn på den vänstra sidan. Azure skapade automatiskt en standard-HTTP-inställning, **appGatewayBackendHttpSettings**, när du skapade Application Gateway. 

3. Välj **appGatewayBackendHttpSettings**.

4. Under **protokoll**väljer du **https**. Ett fönster för **certifikat från Server delens autentisering eller betrodda rot certifikat** visas.

5. Välj **Skapa ny**.

6. I fältet **namn** anger du ett lämpligt namn.

7. Välj certifikat filen i rutan **Ladda upp CER-certifikat** .

   För standard-och WAF-programgatewayer (v1) bör du överföra den offentliga nyckeln för Server certifikat för Server delen i CER-format.

   ![Lägg till certifikat](./media/end-to-end-ssl-portal/addcert.png)

   För Standard_v2 och WAF_v2 programgatewayer, bör du ladda upp rot certifikatet för backend-servercertifikatet i CER-format. Om backend-certifikatet utfärdas av en välkänd certifikat utfärdare (CA), kan du markera kryss rutan **Använd VÄLKÄND CA-certifikat** och sedan behöver du inte ladda upp ett certifikat.

   ![Lägg till betrott rot certifikat](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Rot certifikat](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Välj **Spara**.

## <a name="enable-end-to-end-ssl-for-an-existing-application-gateway"></a>Aktivera SSL från slut punkt till slut punkt för en befintlig Application Gateway

Om du vill konfigurera en befintlig Application Gateway med SSL-kryptering från slut punkt till slut punkt måste du först aktivera SSL-avslutning i lyssnaren. Den här åtgärden aktiverar SSL-kryptering för kommunikation mellan klienten och programgatewayen. Lägg sedan till dessa certifikat för backend-servrar i HTTP-inställningarna på listan Betrodda mottagare. Den här konfigurationen aktiverar SSL-kryptering för kommunikation mellan programgatewayen och backend-servrarna. Som utför SSL-kryptering från slut punkt till slut punkt.

Du måste använda en lyssnare med HTTPS-protokollet och ett certifikat för att aktivera SSL-avslutning. Du kan antingen använda en befintlig lyssnare som uppfyller dessa villkor eller skapa en ny lyssnare. Om du väljer det tidigare alternativet kan du ignorera följande avsnitt "Aktivera SSL-avslutning i en befintlig Application Gateway" och gå direkt till avsnittet "Lägg till autentisering/betrodda rot certifikat för backend-servrar".

Om du väljer det senare alternativet ska du följa stegen i följande procedur.
### <a name="enable-ssl-termination-in-an-existing-application-gateway"></a>Aktivera SSL-avslutning i en befintlig Application Gateway

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **lyssnare** på menyn på den vänstra sidan.

3. Välj antingen **grundläggande** eller **flera plats** lyssnare beroende på dina behov.

4. Under **protokoll**väljer du **https**. Ett fönster för **certifikat** visas.

5. Överför PFX-certifikatet som du tänker använda för SSL-terminering mellan klienten och programgatewayen.

   > [!NOTE]
   > I test syfte kan du använda ett självsignerat certifikat. Detta rekommenderas dock inte för produktions arbets belastningar eftersom de är svårare att hantera och inte är helt säkra. Mer information finns i [skapa ett självsignerat certifikat](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Lägg till andra nödvändiga inställningar för **lyssnaren**, beroende på dina behov.

7. Välj **OK** för att spara.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Lägg till autentisering/betrodda rot certifikat för backend-servrar

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **http-inställningar** på menyn på den vänstra sidan. Du kan antingen lägga till certifikat i en befintlig server dels-HTTP-inställning på listan Betrodda mottagare eller skapa en ny HTTP-inställning. (I nästa steg läggs certifikatet för standard-HTTP-inställningen, **appGatewayBackendHttpSettings**, till i listan över betrodda mottagare.)

3. Välj **appGatewayBackendHttpSettings**.

4. Under **protokoll**väljer du **https**. Ett fönster för **certifikat från Server delens autentisering eller betrodda rot certifikat** visas. 

5. Välj **Skapa ny**.

6. I fältet **namn** anger du ett lämpligt namn.

7. Välj certifikat filen i rutan **Ladda upp CER-certifikat** .

   För standard-och WAF-programgatewayer (v1) bör du överföra den offentliga nyckeln för Server certifikat för Server delen i CER-format.

   ![Lägg till certifikat](./media/end-to-end-ssl-portal/addcert.png)

   För Standard_v2 och WAF_v2 programgatewayer, bör du ladda upp rot certifikatet för backend-servercertifikatet i CER-format. Om backend-certifikatet utfärdas av en välkänd certifikat utfärdare, kan du markera kryss rutan **Använd VÄLKÄND CA-certifikat** och sedan behöver du inte ladda upp ett certifikat.

   ![Lägg till betrott rot certifikat](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway med hjälp av Azure CLI](./tutorial-manage-web-traffic-cli.md)
