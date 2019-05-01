---
title: Snabbstart – konfigurera slutpunkt till slutpunkt SSL-kryptering med Azure Application Gateway – Azure-portalen | Microsoft Docs
description: Lär dig hur du använder Azure-portalen för att skapa en Azure-Programgateway med slutpunkt till slutpunkt SSL-kryptering.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: bd165f81b45e3ae0c121fb8876ed88e68d493195
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64946804"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Konfigurera SSL för slutpunkt till slutpunkt med hjälp av Application Gateway med portalen

Den här artikeln visar hur du använder Azure-portalen för att konfigurera slutpunkt till slutpunkt SSL-kryptering med en Programgateway v1-SKU.  

> [!NOTE]
> Application Gateway v2-SKU kräver betrodda rotcertifikat för att aktivera slutpunkt till slutpunkt-konfigurationen. Portal-stöd för att lägga till betrodda rotcertifikat är inte tillgänglig ännu. Därför vid v2 SKU finns i [Konfigurera SSL för slutpunkt till slutpunkt med hjälp av PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill konfigurera SSL för slutpunkt till slutpunkt med application gateway, krävs ett certifikat för gateway och certifikat krävs för backend servrarna. Gateway-certifikatet används för att härleda en symmetrisk nyckel enligt specifikationen för SSL-protokollet. Den symmetriska nyckeln används sedan för att kryptera och dekryptera trafiken som skickas till gatewayen. För slutpunkt till slutpunkt SSL-kryptering måste backend-servern också vitlistas med application gateway. Gör du genom att ladda upp det offentliga certifikatet för backend-servrar, även kallat autentiseringscertifikat till application gateway. Lägger till certifikatet säkerställer att den application gatewayen kommunicerar bara med kända serverdelsinstanser. Detta skyddar ytterligare slutpunkt till slutpunkt-kommunikation.

Mer information finns i [SSL-avslutning och slutpunkt till slutpunkt SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Skapa en ny Programgateway med SSL för slutpunkt till slutpunkt

Om du vill skapa en ny Programgateway med slutpunkt till slutpunkt SSL-kryptering, måste du först aktivera SSL-avslutning när du skapar en ny Programgateway. Detta aktiverar SSL-kryptering för kommunikation mellan klient och application gateway. Sedan kan behöver du vitlista certifikat för backend-servrarna i HTTP-inställningarna att aktivera SSL-kryptering för kommunikation mellan application gateway och backend-servrarna, utföra slutpunkt till slutpunkt SSL-kryptering.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Aktivera SSL-avslutning när du skapar en ny Programgateway

I den här artikeln för att förstå hur du [aktivera SSL-avslutning när du skapar en ny Programgateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="whitelist-certificates-for-backend-servers"></a>Listan över godkända certifikat för backend-servrar

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **HTTP-inställningar** menyn till vänster. Azure skapas automatiskt en standardinställning HTTP, **appGatewayBackendHttpSettings**, när du skapade application gateway. 

3. Välj **appGatewayBackendHttpSettings**.

4. Under **protokollet**väljer **HTTPS**. Ett fönster för **Backend-certifikat för serverautentisering** visas. 

5. Under **Backend-certifikat för serverautentisering**, Välj **Skapa ny**.

6. Ange lämpliga **namn**.

7. Överför certifikatet med hjälp av den **ladda upp CER-certifikat** box.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Certifikatet som angavs i det här steget ska vara den offentliga nyckeln för .pfx-certifikat finns på serverdelen. Exportera certifikat (inte rotcertifikatet) installerat på backend-servern i anspråk, bevis och skäl till (CER)-format och använda den i det här steget. Det här steget vitlistor serverdelen med application gateway.

8. Välj **Spara**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Aktivera SSL för slutpunkt till slutpunkt för befintlig Programgateway

Om du vill konfigurera en befintlig Programgateway med slutpunkt till slutpunkt SSL-kryptering, måste du första aktivera SSL-avslutning i lyssnaren. Detta aktiverar SSL-kryptering för kommunikation mellan klient och application gateway. Sedan kan behöver du vitlista certifikat för backend-servrarna i HTTP-inställningarna att aktivera SSL-kryptering för kommunikation mellan application gateway och backend-servrarna, utföra slutpunkt till slutpunkt SSL-kryptering.

Du måste använda en lyssnare med HTTPS-protokollet och certifikat för att aktivera SSL-avslutning. Du kan inte ändra protokollet för en befintlig lyssnare. Därför kan du antingen välja att använda en befintlig lyssnare med HTTPS-protokollet och certifikat, eller skapa en ny lyssnare. Om du väljer tidigare kan du ignorera de nämns nedan steg för att **aktivera SSL-avslutning i befintlig Programgateway** och gå direkt till **listan över godkända certifikat för backend-servrarna** avsnittet. Följ stegen nedan om du väljer det senare.

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Aktivera SSL-avslutning i befintlig Programgateway

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **lyssnare** menyn till vänster.

3. Välj mellan **grundläggande** och **multisite** lyssnare enligt dina behov.

4. Under **protokollet**väljer **HTTPS**. Ett fönster för **certifikat** visas.

5. Ladda upp PFX-certifikatet som du vill använda för SSL-avslutning mellan klienten och programmet gatewayen.

   > [!NOTE]
   > I testsyfte kan du använda ett självsignerat certifikat. Du bör inte använda självsignerade certifikat för produktionsarbetsbelastningar. Lär dig hur du [skapa ett självsignerat certifikat](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Lägg till andra inställningar som krävs för den **lyssnare** enligt dina behov.

7. Välj **OK** för att spara.

### <a name="whitelist-certificates-for-backend-servers"></a>Listan över godkända certifikat för backend-servrar

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **HTTP-inställningar** menyn till vänster. Du kan antingen godkänna certifikat i en befintlig serverdel HTTP att eller skapa en ny HTTP-inställning. I den under steget ska vi listan över godkända certifikat för HTTP-standardinställningen **appGatewayBackendHttpSettings**.

3. Välj **appGatewayBackendHttpSettings**.

4. Under **protokollet**väljer **HTTPS**. Ett fönster för **Backend-certifikat för serverautentisering** visas. 

5. Under **Backend-certifikat för serverautentisering**, Välj **Skapa ny**.

6. Ange lämpliga **namn**.

7. Överför certifikatet med hjälp av den **ladda upp CER-certifikat** box.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Certifikatet som angavs i det här steget ska vara den offentliga nyckeln för .pfx-certifikat finns på serverdelen. Exportera certifikat (inte rotcertifikatet) installerat på backend-servern i anspråk, bevis och skäl till (CER)-format och använda den i det här steget. Det här steget vitlistor serverdelen med application gateway.

8. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)
