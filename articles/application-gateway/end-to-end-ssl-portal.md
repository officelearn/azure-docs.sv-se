---
title: Konfigurera TLS-kryptering från slutpunkt till slutpunkt med hjälp av portalen
titleSuffix: Azure Application Gateway
description: Lär dig hur du använder Azure-portalen för att skapa en programgateway med end-to-end TLS-kryptering.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6f86f32e64bbbe79ea5a403d04f7d6c29ee6b980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133019"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Konfigurera på slutna TLS med hjälp av Application Gateway med portalen

I den här artikeln beskrivs hur du använder Azure-portalen för att konfigurera TLS-kryptering (End-to-end Transport Layer Security), som tidigare kallades SSL-kryptering (Secure Sockets Layer), via Azure Application Gateway v1 SKU.

> [!NOTE]
> Application Gateway v2 SKU kräver betrodda rotcertifikat för att aktivera end-to-end-konfiguration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill konfigurera end-to-end TLS med en programgateway behöver du ett certifikat för gatewayen. Certifikat krävs också för backend-servrarna. Gateway-certifikatet används för att härleda en symmetrisk nyckel i enlighet med TLS-protokollspecifikationen. Den symmetriska nyckeln används sedan för att kryptera och dekryptera trafiken som skickas till gatewayen. 

För end-to-end TLS-kryptering måste höger backend-servrar tillåtas i programgatewayen. Om du vill tillåta den här åtkomsten överför du det offentliga certifikatet för backend-servrarna, även kallade autentiseringscertifikat (v1) eller betrodda rotcertifikat (v2), till programgatewayen. Genom att lägga till certifikatet säkerställs att programgatewayen endast kommunicerar med kända backend-instanser. Den här konfigurationen säkrar vidare kommunikation från på sluten tid.

Mer information finns i [Översikt över TLS-avslutning och till TLS med Application Gateway](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Skapa en ny programgateway med heltäckande TLS

Om du vill skapa en ny programgateway med heltäckande TLS-kryptering måste du först aktivera TLS-avslutning när du skapar en ny programgateway. Den här åtgärden möjliggör TLS-kryptering för kommunikation mellan klienten och programgatewayen. Sedan måste du placera certifikaten för backend-servrarna i HTTP-inställningarna i listan Över betrodda mottagare. Den här konfigurationen möjliggör TLS-kryptering för kommunikation mellan programgatewayen och backend-servrarna. Det åstadkommer end-to-end TLS-kryptering.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>Aktivera TLS-avslutning när du skapar en ny programgateway

Mer information finns i [Aktivera TLS-avslutning när du skapar en ny programgateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Lägga till autentiserings-/rotcertifikat för backend-servrar

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **HTTP-inställningar** på menyn till vänster. Azure skapade automatiskt en standard HTTP-inställning, **appGatewayBackendHttpSettings**, när du skapade programgatewayen. 

3. Välj **appGatewayBackendHttpSettings**.

4. Under **Protokoll**väljer du **HTTPS**. En ruta för **serverdautentiseringscertifikat eller betrodda rotcertifikat** visas.

5. Välj **Skapa ny**.

6. Ange ett lämpligt namn i fältet **Namn.**

7. Markera certifikatfilen i rutan **Ladda upp CER-certifikat.**

   För standard- och WAF-programgateways (v1) bör du ladda upp den offentliga nyckeln för servercertifikatet i .cer-format.

   ![Lägg till certifikat](./media/end-to-end-ssl-portal/addcert.png)

   För Standard_v2 och WAF_v2 programgateways bör du ladda upp rotcertifikatet för servercertifikatet i .cer-format. Om backend-certifikatet utfärdas av en välkänd certifikatutfärdare kan du markera kryssrutan **Använd väl känt certifikatutfärdarcertifikat** och sedan behöver du inte ladda upp ett certifikat.

   ![Lägga till betrott rotcertifikat](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Rotcertifikat](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Välj **Spara**.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Aktivera end-to-end TLS för en befintlig programgateway

Om du vill konfigurera en befintlig programgateway med heltäckande TLS-kryptering måste du först aktivera TLS-avslutning i lyssnaren. Den här åtgärden möjliggör TLS-kryptering för kommunikation mellan klienten och programgatewayen. Placera sedan dessa certifikat för backend-servrar i HTTP-inställningarna i listan Betrodda mottagare. Den här konfigurationen möjliggör TLS-kryptering för kommunikation mellan programgatewayen och backend-servrarna. Det åstadkommer end-to-end TLS-kryptering.

Du måste använda en lyssnare med HTTPS-protokollet och ett certifikat för att aktivera TLS-avslutning. Du kan antingen använda en befintlig lyssnare som uppfyller dessa villkor eller skapa en ny lyssnare. Om du väljer det tidigare alternativet kan du ignorera följande avsnittet Aktivera TLS-avslutning i en befintlig programgateway och gå direkt till avsnittet "Lägg till autentiserings-/betrodda rotcertifikat för serverdelsservrar".

Om du väljer det senare alternativet använder du stegen i följande procedur.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>Aktivera TLS-avslutning i en befintlig programgateway

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **Lyssnare** på menyn till vänster.

3. Välj antingen **Basic-** eller **Multi-site-lyssnare** beroende på dina behov.

4. Under **Protokoll**väljer du **HTTPS**. En ruta för **certifikat visas.**

5. Ladda upp PFX-certifikatet som du tänker använda för TLS-avslutning mellan klienten och programgatewayen.

   > [!NOTE]
   > I testsyfte kan du använda ett självsignerat certifikat. Detta rekommenderas dock inte för produktionsarbetsbelastningar, eftersom de är svårare att hantera och inte är helt säkra. Mer information finns i [skapa ett självsignerat certifikat](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Lägg till andra nödvändiga inställningar för **lyssnaren**, beroende på dina krav.

7. Välj **OK** för att spara.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Lägga till autentiserings-/betrodda rotcertifikat för backend-servrar

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **HTTP-inställningar** på menyn till vänster. Du kan antingen placera certifikat i en befintlig http-inställning för backend i listan Betrodda mottagare eller skapa en ny HTTP-inställning. (I nästa steg läggs certifikatet för standard-HTTP-inställningen, **appGatewayBackendHttpSettings**, till i listan Betrodda mottagare.)

3. Välj **appGatewayBackendHttpSettings**.

4. Under **Protokoll**väljer du **HTTPS**. En ruta för **serverdautentiseringscertifikat eller betrodda rotcertifikat** visas. 

5. Välj **Skapa ny**.

6. Ange ett lämpligt namn i fältet **Namn.**

7. Markera certifikatfilen i rutan **Ladda upp CER-certifikat.**

   För standard- och WAF-programgateways (v1) bör du ladda upp den offentliga nyckeln för servercertifikatet i .cer-format.

   ![Lägg till certifikat](./media/end-to-end-ssl-portal/addcert.png)

   För Standard_v2 och WAF_v2 programgateways bör du ladda upp rotcertifikatet för servercertifikatet i .cer-format. Om backend-certifikatet utfärdas av en välkänd certifikatutfärdare kan du markera kryssrutan **Använd väl känt certifikatutfärdarcertifikat** och sedan behöver du inte ladda upp ett certifikat.

   ![Lägga till betrott rotcertifikat](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)
