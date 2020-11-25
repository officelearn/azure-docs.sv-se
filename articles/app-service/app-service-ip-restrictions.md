---
title: Begränsningar för Azure App Service åtkomst
description: Lär dig hur du skyddar din app i Azure App Service genom att ställa in åtkomst begränsningar.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e1549dda367105db34272eab8a90c1760dd5bb5c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010188"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Konfigurera Azure App Service åtkomst begränsningar

Genom att ställa in åtkomst begränsningar kan du definiera en lista över tillåtna/nekade prioriteter som styr nätverks åtkomsten till din app. Listan kan innehålla IP-adresser eller Azure Virtual Network-undernät. Om det finns en eller flera poster finns en implicit *neka* i slutet av listan.

Funktionen för åtkomst begränsning fungerar med alla Azure App Service-värdbaserade arbets belastningar. Arbets belastningarna kan innehålla webbappar, API Apps, Linux-appar, Linux container Apps och functions.

När en begäran görs till din app utvärderas från-adressen mot IP-adresserna i listan med åtkomst begränsningar. Om från-adressen finns i ett undernät som har kon figurer ATS med tjänstens slut punkter till Microsoft. Web, jämförs käll under nätet med de virtuella nätverks reglerna i listan med åtkomst begränsningar. Om adressen inte tillåts åtkomst baserat på reglerna i listan, svarar tjänsten med en status kod för [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) .

Funktionen för åtkomst begränsning implementeras i App Service-frontend-roller som överordnas av de arbets värdar där koden körs. Åtkomst begränsningarna är därför effektiva nätverks åtkomst kontrol listor (ACL: er).

Möjligheten att begränsa åtkomsten till din webbapp från ett virtuellt Azure-nätverk har Aktiver ATS av [tjänst slut punkter][serviceendpoints]. Med tjänst slut punkter kan du begränsa åtkomsten till en tjänst för flera innehavare från valda undernät. Det fungerar inte för att begränsa trafik till appar som finns i en App Service-miljön. Om du befinner dig i ett App Service-miljön kan du kontrol lera åtkomsten till din app genom att använda IP-regler.

> [!NOTE]
> Tjänstens slut punkter måste vara aktiverade både på nätverks sidan och för den Azure-tjänst som de är aktiverade med. En lista över Azure-tjänster som stöder tjänst slut punkter finns i [Virtual Network tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md).
>

![Diagram över åtkomst begränsningarnas flöde.](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="add-or-edit-access-restriction-rules-in-the-portal"></a>Lägga till eller redigera regler för åtkomst begränsning i portalen

Gör följande om du vill lägga till en regel för begränsning av åtkomst till din app:

1. Logga in på Azure-portalen.

1. I den vänstra rutan väljer du **nätverk**.

1. I fönstret **nätverk** under **åtkomst begränsningar** väljer du **Konfigurera åtkomst begränsningar**.

   ![Skärm bild av fönstret App Service nätverks alternativ i Azure Portal.](media/app-service-ip-restrictions/access-restrictions.png)  

1. På sidan **åtkomst begränsningar** granskar du listan över regler för åtkomst begränsning som har definierats för din app.

   ![Skärm bild av sidan åtkomst begränsningar i Azure Portal, som visar listan över regler för åtkomst begränsning som definierats för den valda appen.](media/app-service-ip-restrictions/access-restrictions-browse.png)

   I listan visas alla aktuella begränsningar som tillämpas på appen. Om du har en begränsning för virtuella nätverk i appen visar tabellen om tjänst slut punkterna är aktiverade för Microsoft. Web. Om inga begränsningar har definierats för din app kan appen nås var som helst.  

### <a name="add-an-access-restriction-rule"></a>Lägg till en regel för begränsning av åtkomst

Om du vill lägga till en regel för begränsning av åtkomst till din app väljer du **Lägg till regel** i fönstret **åtkomst begränsningar** . När du har lagt till en regel börjar den gälla omedelbart. 

Reglerna tillämpas i prioritetsordning, med början från det lägsta antalet i kolumnen **prioritet** . En implicit *neka allt* gäller när du har lagt till en enda regel.

I fönstret **Lägg till IP-begränsning** när du skapar en regel gör du följande:

1. Under **åtgärd** väljer du antingen **Tillåt** eller **neka**.  

   ![Skärm bild av fönstret Lägg till IP-begränsning.](media/app-service-ip-restrictions/access-restrictions-ip-add.png)
   
1. Du kan också ange ett namn och en beskrivning av regeln.  
1. I list rutan **typ** väljer du typ av regel.  
1. I rutan **prioritet** anger du ett prioritets värde.  
1. I list rutorna **prenumeration**, **Virtual Network** och **undernät** väljer du vad du vill begränsa åtkomsten till.  

### <a name="set-an-ip-address-based-rule"></a>Ange en IP-adress-baserad regel

Följ proceduren som beskrivs i föregående avsnitt, men med följande variation:
* I list rutan **typ** i steg 3 väljer du **IPv4** eller **IPv6**. 

Ange IP-adressen i CIDR-notation (Classless Inter-Domain routing) för både IPv4-och IPv6-adresser. Om du vill ange en adress kan du använda något som *1.2.3.4/32*, där de första fyra oktetterna representerar din IP-adress och */32* är masken. IPv4 CIDR-noteringen för alla adresser är 0.0.0.0/0. Mer information om CIDR-notering finns i [klass lös Inter-Domain routning](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="use-service-endpoints"></a>Använda tjänstens slutpunkter

Genom att använda tjänst slut punkter kan du begränsa åtkomsten till de valda Azure-undernäten för virtuella nätverk. Om du vill begränsa åtkomsten till ett speciellt undernät skapar du en begränsnings regel med en **Virtual Network** typ. Du kan sedan välja den prenumeration, det virtuella nätverk och det undernät som du vill tillåta eller neka åtkomst till. 

Om tjänst slut punkter inte redan har Aktiver ATS med Microsoft. Web för det undernät som du har valt, aktive ras de automatiskt om du inte markerar kryss rutan **Ignorera saknade Microsoft. webb tjänst slut punkter** . Scenariot där du kanske vill aktivera tjänstens slut punkter i appen, men inte under nätet beror huvudsakligen på om du har behörighet att aktivera dem i under nätet. 

Om du behöver någon annan för att aktivera tjänstens slut punkter i under nätet markerar du kryss rutan **Ignorera saknade Microsoft. webb tjänst slut punkter** . Din app konfigureras för tjänst slut punkter i förväntat att de ska aktive ras senare i under nätet. 

![Skärm bild av fönstret "Lägg till IP-begränsning" med Virtual Network typen vald.](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Du kan inte använda tjänstens slut punkter för att begränsa åtkomsten till appar som körs i en App Service-miljön. När din app är i ett App Service-miljön kan du kontrol lera åtkomsten till den genom att använda regler för IP-åtkomst. 

Med tjänst slut punkter kan du konfigurera din app med programgatewayer eller andra WAF-enheter (Web Application Firewall). Du kan också konfigurera program på flera nivåer med säkra server delar. Mer information finns i [nätverksfunktionerna och App Service](networking-features.md) och [Application Gateway-integrering med tjänst slut punkter](networking/app-gateway-with-service-endpoints.md).

> [!NOTE]
> - Tjänst slut punkter stöds för närvarande inte för webb program som använder IP-Secure Sockets Layer (SSL) virtuell IP (VIP).
> - Det finns en gräns på 512 rader med IP-eller tjänst slut punkts begränsningar. Om du behöver fler än 512 rader med begränsningar rekommenderar vi att du installerar en fristående säkerhets produkt, till exempel Azure-frontend, Azure App gateway eller en WAF.
>

## <a name="manage-access-restriction-rules"></a>Hantera regler för åtkomst begränsning

Du kan redigera eller ta bort en befintlig regel för begränsning av åtkomst.

### <a name="edit-a-rule"></a>Redigera en regel

1. Om du vill börja redigera en befintlig regel för åtkomst begränsning dubbelklickar du på den regel som du vill redigera på sidan **åtkomst begränsningar** .

1. I fönstret **Redigera IP-begränsning** gör du ändringarna och väljer sedan **Uppdatera regel**. Redigeringar börjar gälla omedelbart, inklusive ändringar i prioritets ordning.

   ![Skärm bild av fönstret "Redigera IP-begränsning" i Azure Portal som visar fälten för en befintlig regel för begränsning av åtkomst.](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

   > [!NOTE]
   > När du redigerar en regel kan du inte växla mellan en IP-serveradress och en regel för virtuella nätverk. 

   ![Skärm bild av fönstret "Redigera IP-begränsning" i Azure Portal som visar inställningarna för en regel för virtuellt nätverk.](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

### <a name="delete-a-rule"></a>Ta bort en regel

Om du vill ta bort en regel väljer du ellipsen (**...**) bredvid den regel som du vill ta bort på sidan **åtkomst begränsningar** och väljer sedan **ta bort**.

![Skärm bild av sidan "åtkomst begränsningar" som visar ellipsen "ta bort" bredvid regeln för åtkomst begränsning som ska tas bort.](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="block-a-single-ip-address"></a>Blockera en enskild IP-adress

När du lägger till din första begränsnings regel för IP-adress lägger tjänsten till en explicit *Neka alla* -regel med prioritet 2147483647. I praktiken är den uttryckliga *Neka alla* -regeln att den slutgiltiga regeln körs och blockerar åtkomsten till en IP-adress som inte uttryckligen tillåts av en *Tillåt* -regel.

För ett scenario där du uttryckligen vill blockera en enskild IP-adress eller ett block med IP-adresser, men tillåta åtkomst till allt annat, lägger du till en explicit *Tillåt alla* regler.

![Skärm bild av sidan "åtkomst begränsningar" i Azure Portal som visar en enskild blockerad IP-adress.](media/app-service-ip-restrictions/block-single-address.png)

## <a name="restrict-access-to-an-scm-site"></a>Begränsa åtkomsten till en SCM-webbplats 

Förutom att kunna kontrol lera åtkomsten till din app kan du begränsa åtkomsten till den SCM-webbplats som används av din app. SCM-webbplatsen är både webb distributions slut punkten och kudu-konsolen. Du kan tilldela åtkomst begränsningar till SCM-webbplatsen från appen separat eller använda samma uppsättning begränsningar för både appen och SCM-platsen. När du väljer **samma begränsningar som \<app name>** kryss ruta, är allt tomt. Om du avmarkerar kryss rutan tillämpas inställningarna för SCM-platsen igen. 

![Skärm bild av sidan "åtkomst begränsningar" i Azure Portal, vilket visar att inga åtkomst begränsningar har angetts för SCM-platsen eller appen.](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="manage-access-restriction-rules-programatically"></a>Hantera regler för åtkomst begränsning program mässigt

Du kan lägga till åtkomst begränsningar program mässigt genom att göra något av följande: 

* Använd [Azure CLI](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true). Exempel:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
  --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* Använd [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0&preserve-view=true). Exempel:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```

Du kan också ange värden manuellt genom att göra något av följande:

* Använd en [Azure REST API](/rest/api/azure/) -åtgärd i appens konfiguration i Azure Resource Manager. Platsen för den här informationen i Azure Resource Manager:

  management.azure.com/subscriptions/**prenumerations-ID**/resourceGroups/**resurs grupper**/providers/Microsoft.Web/Sites/**Web App Name**/config/Web? API-version = 2018-02-01

* Använd en ARM-mall. Som exempel kan du använda resources.azure.com och redigera ipSecurityRestrictions-blocket för att lägga till den nödvändiga JSON-filen.

  JSON-syntaxen för det tidigare exemplet är:

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```

## <a name="set-up-azure-functions-access-restrictions"></a>Konfigurera Azure Functions åtkomst begränsningar

Åtkomst begränsningar är också tillgängliga för Function-appar med samma funktioner som App Service planer. När du aktiverar åtkomst begränsningar inaktiverar du också Azure Portal kod redigeraren för alla otillåtna IP-adresser.

## <a name="next-steps"></a>Nästa steg
[Åtkomst begränsningar för Azure Functions](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Application Gateway integrering med tjänst slut punkter](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
