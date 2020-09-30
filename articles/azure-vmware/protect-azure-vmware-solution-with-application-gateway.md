---
title: Använd Azure Application Gateway för att skydda dina webbappar på Azure VMware-lösningen
description: Konfigurera Azure Application Gateway för att på ett säkert sätt exponera dina webbappar som körs på Azure VMware-lösningen.
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: ad7cd36d77da41d75ea9dcc18a51d0ffc5540d2a
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580568"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Använd Azure Application Gateway för att skydda dina webbappar på Azure VMware-lösningen

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) är en belastningsutjämnare för Layer 7-webbtrafik som gör att du kan hantera trafik till dina webb program. Den erbjuder många funktioner: cookie-baserad sessionsbaserade tillhörighet, URL-baserad Routning och brand vägg för webbaserade program (WAF) för att ge några namn. (En fullständig lista över funktioner finns i [Azure Application Gateway-funktioner](../application-gateway/features.md).) Den erbjuds i två versioner, v1 och v2. Båda har testats med webbappar som körs på Azure VMware-lösningen.

I den här artikeln går vi igenom ett vanligt scenario med Application Gateway framför en webb Server grupp med en uppsättning konfigurationer och rekommendationer för att skydda en webbapp som körs på Azure VMware-lösningen. 

## <a name="topology"></a>Topologi
Som du ser i följande bild kan Application Gateway användas för att skydda virtuella Azure IaaS-datorer, skalnings uppsättningar för virtuella Azure-datorer eller lokala servrar. Virtuella Azure VMware-lösningar kommer att behandlas som lokala servrar genom att Application Gateway.

![Application Gateway skyddar virtuella datorer i Azure VMware-lösningen.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Azure Application Gateway är för närvarande den enda metod som stöds för att exponera webb program som körs på virtuella Azure VMware-lösningar.

Följande diagram visar test scenariot som används för att verifiera Application Gateway med webb program för Azure VMware-lösningar.

![Application Gateway integrering med Azure VMware-lösning som kör webbappar.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-avs-scenario.png)

Application Gateway-instansen distribueras på hubben i ett dedikerat undernät. Den har en offentlig Azure-IP-adress; Det rekommenderas att du aktiverar standard DDoS-skydd för det virtuella nätverket. Webb servern finns i ett privat moln i Azure VMware-lösningen bakom NSX t0 och T1-routrar. Azure VMware-lösningen använder [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) för att aktivera kommunikationen med hubben och de lokala systemen.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration.
- Ett privat moln för VMware-lösningar i Azure distribueras och körs.

## <a name="deployment-and-configuration"></a>Distribution och konfiguration

1. Sök efter **Application Gateway** i Azure Portal och välj **skapa Application Gateway**.

2. Ange grundläggande information som i följande bild. Välj sedan **Nästa: frontend->**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Skapa Application Gateway":::

3. Välj IP-adress typen frontend. För offentlig väljer du en befintlig offentlig IP-adress eller skapar en ny. Välj **Nästa:>**.

    > [!NOTE]
    > Endast standard-och WAF-SKU: er (Web Application Firewall) stöds för privata klient versioner.

4. Lägg sedan till en backend-pool som beskriver en uppsättning instanser som är en del av programmet eller tjänsten (i det här fallet virtuella datorer som körs på Azures infrastruktur för VMware-lösning). Ange information om webb servrar som körs på Azure VMware-lösningen privat moln och välj **Lägg till**. Välj sedan **Nästa: konfigurations>**.

1. På fliken **konfiguration** väljer du **Lägg till en regel för routning**.

6. Ange information om lyssnaren på fliken **lyssnare** . Om HTTPS har valts måste ett certifikat tillhandahållas, antingen från en PFX-fil eller ett befintligt certifikat från Azure Key Vault. 

7. Välj fliken **Server dels mål** och välj den backend-pool som du skapade tidigare. I fältet **http-inställningar** väljer du **Lägg till ny**.

8. Konfigurera parametrarna för HTTP-inställningarna. Välj **Lägg till**.

9. Om du vill konfigurera Sök vägsbaserade regler väljer **du Lägg till flera mål för att skapa en Sök vägs baserad regel**. 

10. Lägg till en Sök vägs-baserad regel och välj **Lägg till**. Upprepa för att lägga till ytterligare Sök vägs regler. 

11. När du är färdig med att lägga till Sök vägs baserade regler väljer du **Lägg till** igen. Välj sedan **Nästa: taggar>**. 

12. Lägg till önskade taggar. Välj **Nästa: granska + skapa>**.

13. En verifiering körs på din Application Gateway; om det lyckas väljer du **skapa** för att distribuera.

## <a name="configuration-examples"></a>Konfigurations exempel

I det här avsnittet får du lära dig hur du konfigurerar Application Gateway med virtuella datorer i Azure VMware-lösningen som backend-pooler för följande användnings fall: 

- [Vara värd för flera platser](#hosting-multiple-sites)
- [Routning efter URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Vara värd för flera platser

Du kan använda Azure Portal för att konfigurera värd tjänster för flera webbplatser när du skapar en Programgateway. I den här självstudien definierar du Server dels adress grupper med hjälp av virtuella datorer som körs på ett privat Azure VMware-lösnings moln på en befintlig Application Gateway. Programgatewayen är en del av ett virtuellt hubb nätverk enligt beskrivningen i [integrera Azure VMware-lösning i en hubb och eker-arkitektur](concepts-hub-and-spoke.md). I den här självstudien förutsätter vi att du äger flera domäner och använder exempel på www.contoso.com och www.fabrikam.com.

1. Skapa de virtuella datorerna. Skapa två olika pooler för virtuella datorer i Azures privata moln för VMware-lösningen. en kommer att representera contoso och den andra Fabrikam. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs.png" alt-text="Skapa Application Gateway":::

    För att illustrera den här självstudien har vi använt Windows Server 2016 med Internet Information Services (IIS)-rollen installerad. När de virtuella datorerna har installerats kör du följande PowerShell-kommandon för att konfigurera IIS på var och en av de virtuella datorerna. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Lägg till backend-pooler. I en befintlig Application Gateway-instans väljer du **Server dels grupper** på den vänstra menyn, väljer  **Lägg till**och anger information om de nya poolerna. Välj **Lägg till** i den högra rutan.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png" alt-text="Skapa Application Gateway" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png":::

3. I avsnittet **lyssnare** skapar du en ny lyssnare för varje webbplats. Ange informationen för varje lyssnare och välj **Lägg till**.

4. I det vänstra navigerings fönstret väljer du **http-inställningar** och väljer **Lägg till** i den vänstra rutan. Fyll i informationen för att skapa en ny HTTP-inställning och välj **Spara**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png" alt-text="Skapa Application Gateway" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png":::

5. Skapa reglerna i avsnittet **Rules (regler** ) på den vänstra menyn. Koppla varje regel till motsvarande lyssnare. Välj **Lägg till**.

6. Konfigurera motsvarande backend-pool och HTTP-inställningar. Välj **Lägg till**.

7. Testa anslutningen. Öppna din önskade webbläsare och navigera till de olika webbplatser som finns i din Azure VMware-lösnings miljö, till exempel http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-07.png" alt-text="Skapa Application Gateway":::

### <a name="routing-by-url"></a>Routning efter URL

Du kan använda Azure Application Gateway för att konfigurera URL-sökvägar baserade regler för routning. I den här självstudien definierar du Server dels adress grupper med hjälp av virtuella datorer som körs på ett privat Azure VMware-lösnings moln på en befintlig Application Gateway. Programgatewayen är en del av ett virtuellt hubb nätverk enligt beskrivningen i [Azures interna integrerings dokumentation för VMware-lösningen](concepts-hub-and-spoke.md). Du skapar sedan routningsregler som kontrollerar att webb trafiken kommer till rätt servrar i poolerna.

1. Skapa de virtuella datorerna. Skapa en pool med virtuella datorer som representerar webb server gruppen i det privata molnet för Azure VMware-lösningen. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs.png" alt-text="Skapa Application Gateway"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Lägg till backend-pooler. Du måste lägga till tre nya backend-pooler i en befintlig Application Gateway-instans. Välj **Serverdelspooler** på den vänstra menyn. Välj **Lägg till** och ange information om den första poolen, **contoso-Web**. Lägg till en virtuell dator som mål. Välj **Lägg till**. Upprepa den här processen för **contoso-images** och **contoso-video**och Lägg till en unik virtuell dator till varje som mål. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Skapa Application Gateway" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png":::

3. I avsnittet **lyssnare** skapar du en ny lyssnare av typen Basic med Port 8080.

4. I det vänstra navigerings fönstret väljer du **http-inställningar** och väljer **Lägg till** i den vänstra rutan. Fyll i informationen för att skapa en ny HTTP-inställning och välj **Spara**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Skapa Application Gateway":::

5. Skapa reglerna i avsnittet **Rules (regler** ) på den vänstra menyn. Koppla varje regel till den tidigare skapade lyssnaren. Konfigurera sedan huvud server delen och HTTP-inställningarna. Välj **Lägg till**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Skapa Application Gateway":::

6. Testa konfigurationen. Öppna programgatewayen på Azure Portal och i avsnittet **Översikt** kopierar du den offentliga IP-adressen. Öppna sedan ett nytt webbläsarfönster och ange URL: en `http://<app-gw-ip-address>:8080` . 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Skapa Application Gateway":::

    Ändra URL:en till `http://<app-gw-ip-address>:8080/images/test.htm`.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Skapa Application Gateway":::

    Ändra URL: en igen till `http://<app-gw-ip-address>:8080/video/test.htm` .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Skapa Application Gateway":::

## <a name="next-steps"></a>Efterföljande moment

Läs [dokumentationen om Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) för fler konfigurations exempel.
