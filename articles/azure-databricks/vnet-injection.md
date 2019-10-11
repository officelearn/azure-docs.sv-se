---
title: Distribuera Azure Databricks i ditt virtuella nätverk
description: Den här artikeln beskriver hur du distribuerar Azure Databricks till ditt virtuella nätverk, även kallat VNet-injektering.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 07591517211d5334b9bf055d778f00b171e7056f
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263447"
---
# <a name="deploy-azure-databricks-in-your-virtual-network"></a>Distribuera Azure Databricks i ditt virtuella nätverk

Standard distributionen av Azure Databricks är en fullständigt hanterad tjänst på Azure: alla data Plans resurser, inklusive ett virtuellt nätverk (VNet), distribueras till en låst resurs grupp. Om du behöver anpassning av nätverk kan du dock Distribuera Azure Databricks-resurser i ditt eget virtuella nätverk (kallas även VNet-insprutning) när du gör det möjligt att:

* Anslut Azure Databricks till andra Azure-tjänster (till exempel Azure Storage) på ett säkrare sätt med hjälp av tjänstens slut punkter.
* Anslut till lokala data källor för användning med Azure Databricks med hjälp av användardefinierade vägar.
* Anslut Azure Databricks till en virtuell nätverks installation för att kontrol lera all utgående trafik och vidta åtgärder enligt reglerna för att tillåta och neka.
* Konfigurera Azure Databricks att använda anpassad DNS.
* Konfigurera regler för nätverks säkerhets grupper (NSG) för att ange begränsningar för utgående trafik.
* Distribuera Azure Databricks kluster i ditt befintliga virtuella nätverk.

Genom att distribuera Azure Databricks resurser till ditt eget virtuella nätverk kan du också använda flexibla CIDR-intervall (var som helst mellan/16-/24 för det virtuella nätverket och mellan/18-/26 för under näten).

  > [!NOTE]
  > Det går inte att ersätta det virtuella nätverket för en befintlig arbets yta. Om den aktuella arbets ytan inte kan hantera det begärda antalet aktiva klusternoder skapar du en annan arbets yta i ett större virtuellt nätverk. Följ [dessa detaljerade steg för migrering](howto-regional-disaster-recovery.md#detailed-migration-steps) för att kopiera resurser (antecknings böcker, klusterkonfigurationer, jobb) från den gamla till den nya arbets ytan.

## <a name="virtual-network-requirements"></a>Krav för virtuellt nätverk

Du kan använda distributions gränssnittet Azure Databricks arbets yta i Azure Portal för att automatiskt konfigurera ett befintligt virtuellt nätverk med nödvändiga undernät, nätverks säkerhets grupp och vit listning-inställningar, eller så kan du använda Azure Resource Manager mallar för att konfigurera ditt virtuella nätverk och distribuera din arbets yta.

Det virtuella nätverk som du distribuerar din Azure Databricks arbets yta till måste uppfylla följande krav:

### <a name="location"></a>Plats

Det virtuella nätverket måste finnas på samma plats som Azure Databricks-arbetsytan.

### <a name="subnets"></a>Undernät

Det virtuella nätverket måste innehålla två undernät som är dedikerade till Azure Databricks:

   1. Ett privat undernät med en konfigurerad nätverks säkerhets grupp som tillåter intern kommunikation i kluster

   2. Ett offentligt undernät med en konfigurerad nätverks säkerhets grupp som möjliggör kommunikation med Azure Databricks kontroll planet.

### <a name="address-space"></a>Adressutrymme

Ett CIDR-block mellan/16-/24 för det virtuella nätverket och ett CIDR-block mellan/18-/26 för privata och offentliga undernät.

### <a name="whitelisting"></a>Godkännande

All utgående och inkommande trafik mellan undernät och Azure Databricks kontroll planet måste vara vit listas.

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

I det här avsnittet beskrivs hur du skapar en Azure Databricks arbets yta i Azure Portal och distribuerar den i ditt eget befintliga virtuella nätverk. Azure Databricks uppdaterar det virtuella nätverket med två nya undernät och nätverks säkerhets grupper med CIDR-intervall som tillhandahålls av dig, whitelists inkommande och utgående under nät trafik och distribuerar arbets ytan till det uppdaterade virtuella nätverket.

## <a name="prerequisites"></a>Krav

Du måste ha ett virtuellt nätverk som du ska Distribuera Azure Databricks arbets ytan till. Du kan använda ett befintligt virtuellt nätverk eller skapa ett nytt, men det virtuella nätverket måste finnas i samma region som Azure Databricks arbets ytan som du planerar att skapa. Ett CIDR-intervall mellan/16-/24 krävs för det virtuella nätverket.

  > [!Warning]
  > En arbets yta med ett mindre virtuellt nätverk – det vill säga ett lägre CIDR-intervall – kan ta slut på IP-adresser (nätverks utrymme) snabbare än en arbets yta med ett större virtuellt nätverk. Till exempel kan en arbets yta med ett/24-undernät för virtuella nätverk och/26-undernät ha högst 64 noder aktiva i taget, medan en arbets yta med ett/20 virtuellt nätverk och/22-undernät kan innehålla högst 1024 noder.

  Dina undernät skapas automatiskt när du konfigurerar din arbets yta och du får möjlighet att tillhandahålla CIDR-intervallet för under näten under konfigurationen.

## <a name="configure-the-virtual-network"></a>Konfigurera det virtuella nätverket

1. I Azure Portal väljer du **+ skapa en resurs > analys > Azure Databricks** för att öppna dialog rutan Azure Databricks service.

2. Följ konfigurations stegen som beskrivs i steg 2: skapa en Azure Databricks arbets yta i Komma igång guiden och välj distributions Azure Databricks arbets ytan i Virtual Network alternativet.

   ![Skapa Azure Databricks tjänst](./media/vnet-injection/create-databricks-service.png)

3. Välj det virtuella nätverk som du vill använda.

   ![Alternativ för virtuellt nätverk](./media/vnet-injection/select-vnet.png)

4. Tillhandahåll CIDR-intervall i ett block mellan/18-/26 för två undernät, dedikerat till Azure Databricks:

   * Ett offentligt undernät kommer att skapas med en associerad nätverks säkerhets grupp som möjliggör kommunikation med Azure Databricks kontroll planet.
   * Ett privat undernät kommer att skapas med en associerad nätverks säkerhets grupp som tillåter intern kommunikation i kluster.

5. Klicka på **skapa** för att distribuera Azure Databricks arbets ytan till det virtuella nätverket.

## <a name="advanced-resource-manager-configurations"></a>Avancerade Resource Manager-konfigurationer

Om du vill ha mer kontroll över konfigurationen av det virtuella nätverket – till exempel om du vill använda befintliga undernät, använder du befintliga nätverks säkerhets grupper eller skapar egna säkerhets regler – du kan använda följande Azure Resource Manager mallar i stället för Portal konfiguration av virtuellt nätverk och arbets ytans distribution.

### <a name="all-in-one"></a>Allt-i-ett

Om du vill skapa ett virtuellt nätverk, nätverks säkerhets grupper och Azure Databricks arbets ytan alla i ett, använder du [alla-i-en-mallen för Databricks VNet-inmatade arbets ytor](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

När du använder den här mallen behöver du inte göra några manuella vit listning för under näts trafik.

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Om du vill skapa nätverks säkerhets grupper med de regler som krävs för ett befintligt virtuellt nätverk använder du [mallen nätverks säkerhets grupp för Databricks VNet-inmatning](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection).

När du använder den här mallen behöver du inte göra några manuella vit listning för under näts trafik.

### <a name="virtual-network"></a>Virtuellt nätverk

Om du vill skapa ett virtuellt nätverk med rätt offentliga och privata undernät använder du [Virtual Network-mallen för Databricks VNet-inmatning](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Om du använder den här mallen utan att använda mallen nätverks säkerhets grupper måste du manuellt lägga till vit listning-regler i de nätverks säkerhets grupper som du använder med det virtuella nätverket.

### <a name="azure-databricks-workspace"></a>Azure Databricks arbets yta

Om du vill distribuera en Azure Databricks arbets yta till ett befintligt virtuellt nätverk med offentliga och privata undernät och rätt konfigurerade nätverks säkerhets grupper redan har kon figurer ATS, använder du [arbets ytans mall för Databricks VNet-inmatning](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Om du använder den här mallen utan att använda mallen nätverks säkerhets grupper måste du manuellt lägga till vit listning-regler i de nätverks säkerhets grupper som du använder med det virtuella nätverket.

## <a name="whitelisting-subnet-traffic"></a>Vit listning under nät trafik

Om du inte använder [Azure Portal](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) -eller [Azure Resource Manager mallar](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) för att skapa nätverks säkerhets grupper, måste du manuellt vitlista följande trafik i dina undernät.

|Riktning|Protokoll|Källa|Källport|Mål|Målport|
|---------|--------|------|-----------|-----------|----------------|
|Inkommande|\*|VirtualNetwork|\*|\*|\*|
|Inkommande|\*|Kontroll plan NAT IP|\*|\*|22|
|Inkommande|\*|Kontroll plan NAT IP|\*|\*|5557|
|Utgående|\*|\*|\*|Webapp-IP|\*|
|Utgående|\*|\*|\*|SQL (service tag)|\*|
|Utgående|\*|\*|\*|Lagring (service tag)|\*|
|Utgående|\*|\*|\*|VirtualNetwork|\*|

Vitlista under nät trafik med följande IP-adresser. För SQL (metaarkiv) och lagring (artefakt-och logg lagring) bör du använda SQL-och Storage [service-taggarna](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

|Azure Databricks region|Tjänst|Offentlig IP-adress|
|-----------------------|-------|---------|
|Östra USA|NAT för kontroll plan </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Östra USA 2|NAT för kontroll plan </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Norra centrala USA|NAT för kontroll plan </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Centrala USA|NAT för kontroll plan </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Södra centrala USA|NAT för kontroll plan </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Västra USA|NAT för kontroll plan </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|USA, västra 2|NAT för kontroll plan </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Centrala Kanada|NAT för kontroll plan </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Östra Kanada|NAT för kontroll plan </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Storbritannien, västra|NAT för kontroll plan </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Storbritannien, södra|NAT för kontroll plan </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Västeuropa|NAT för kontroll plan </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Nordeuropa|NAT för kontroll plan </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Centrala Indien|NAT för kontroll plan </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Södra Indien|NAT för kontroll plan </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Västra Indien|NAT för kontroll plan </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Sydostasien|NAT för kontroll plan </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Östasien|NAT för kontroll plan </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Östra Australien|NAT för kontroll plan </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Sydöstra Australien|NAT för kontroll plan </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australien, centrala|NAT för kontroll plan </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Centrala Australien 2|NAT för kontroll plan </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Östra Japan|NAT för kontroll plan </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|
|Västra Japan|NAT för kontroll plan </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Felsöka

### <a name="workspace-launch-errors"></a>Fel vid start av arbets yta

Det går inte att starta en arbets yta i ett anpassat virtuellt nätverk på Azure Databricks inloggnings skärm med följande fel: **"Vi har påträffat ett fel när du skulle skapa din arbets yta. Kontrol lera att den anpassade nätverks konfigurationen är korrekt och försök igen. "**

Det här felet beror på att en nätverks konfiguration inte uppfyller kraven. Bekräfta att du har följt instruktionerna i det här avsnittet när du skapade arbets ytan.

### <a name="cluster-creation-errors"></a>Fel vid skapande av kluster

**Det går inte att hitta instanser: resurserna kunde inte uppnås via SSH.**

Möjlig orsak: trafik från kontroll planet till arbets tagarna är blockerad. Korrigera genom att se till att inkommande säkerhets regler uppfyller kraven. Om du distribuerar till ett befintligt virtuellt nätverk som är anslutet till ditt lokala nätverk, granskar du installationen med hjälp av informationen i ansluta din Azure Databricks arbets yta till ditt lokala nätverk.

**Oväntat startfel: ett oväntat fel inträffade när klustret konfigurerades. Försök igen och kontakta Azure Databricks om problemet kvarstår. Internt fel meddelande: tids gräns vid placering av nod.**

Möjlig orsak: trafik från arbetare till Azure Storage slut punkter blockeras. Korrigera genom att se till att utgående säkerhets regler uppfyller kraven. Om du använder anpassade DNS-servrar kontrollerar du också status för DNS-servrarna i det virtuella nätverket.

**Startfel för moln leverantör: ett fel uppstod när klustret skulle konfigureras. Mer information finns i Azure Databrickss guiden. Azure-felkod: AuthorizationFailed/InvalidResourceReference.**

Möjlig orsak: det virtuella nätverket eller under nätet finns inte längre. Kontrol lera att det virtuella nätverket och undernät finns.

**Klustret avslutades. Orsak: Spark-startfel: Spark kunde inte starta i tid. Det här problemet kan orsakas av fel Hive-metaarkiv, ogiltiga Spark-konfigurationer eller fel initierings skript. Felsök problemet med Spark-drivrutinen och kontakta Databricks om problemet kvarstår. Internt fel meddelande: Spark kunde inte starta: driv rutinen kunde inte starta i tid.**

Möjlig orsak: container kan inte kommunicera med instansen eller DBFS lagrings konto. Korrigera genom att lägga till en anpassad väg till under näten för DBFS lagrings konto med nästa hopp som Internet.

### <a name="notebook-command-errors"></a>Fel i Notebook-kommando

**Kommandot svarar inte**

Möjlig orsak: kommunikation mellan arbets tagare och arbets tagare har blockerats. Korrigera genom att se till att de inkommande säkerhets reglerna uppfyller kraven.

**Notebook-arbetsflödet Miss lyckas med undantaget: com. databricks. WorkflowException: org. apache. http. Notebook. ConnectTimeoutException**

Möjlig orsak: trafik från arbetare till Azure Databricks webapp blockeras. Korrigera genom att se till att de utgående säkerhets reglerna uppfyller kraven.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera, transformera och läsa in data med Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
